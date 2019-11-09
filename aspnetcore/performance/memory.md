---
title: Správa paměti a vzory v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak je paměť spravovaná v ASP.NET Core a jak funguje uvolňování paměti (GC).
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2019
uid: performance/memory
ms.openlocfilehash: 4c25c069aa2a6088c0549d786ecdd487ab7b9ea5
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896936"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Správa paměti a uvolňování paměti (GC) v ASP.NET Core

Od [Sébastien ROS](https://github.com/sebastienros) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Správa paměti je složitá, i ve spravovaném rozhraní, jako je .NET. Analýza a porozumění problémům s pamětí může být náročné. Tento článek:

* Bylo motivované mnoha *nevrácenými* chybami a uvolňováním paměti, které *nefunguje* . Většina těchto problémů byla způsobena tím, že nerozumíte tomu, jak spotřeba paměti funguje v rozhraní .NET Core, nebo pokud nerozumíte, jak se měří.
* Ukazuje problematické využití paměti a navrhuje alternativní přístupy.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Jak funguje uvolňování paměti (GC) v .NET Core

GC přiděluje segmenty haldy, kde jsou jednotlivé segmenty souvislým rozsahem paměti. Objekty umístěné v haldě jsou rozdělené do jedné ze 3 generací: 0, 1 nebo 2. Generace Určuje četnost, po kterou se GC pokusí uvolnit paměť na spravovaných objektech, na které už neodkazuje aplikace. Nižší očíslované generace jsou GC častěji.

Objekty jsou přesunuty z jedné generace do jiné na základě jejich životnosti. Jelikož objekty jsou po dobu provozu, jsou přesunuty do vyšší generace. Jak už bylo uvedeno výše, vyšší generace paměti GC jsou méně často. Krátkodobé dlouhodobé objekty vždy zůstávají v generaci 0. Například objekty, které jsou odkazovány během životnosti webové žádosti, jsou krátkodobé. [Jednoznačné](xref:fundamentals/dependency-injection#service-lifetimes) úrovně aplikace se obvykle migrují do generace 2.

Při spuštění aplikace ASP.NET Core se v GC:

* Rezervuje určitou paměť pro počáteční segmenty haldy.
* Po načtení modulu runtime potvrdí malou část paměti.

Předchozí přidělení paměti se provádí z důvodů výkonu. Výhoda výkonu přichází z segmentů haldy v souvislé paměti.

### <a name="call-gccollect"></a>Vyvolejte GC. Shromáždění

Volání [GC. Shromažďovat](xref:System.GC.Collect*) explicitně:

* By **se nemělo provádět v** produkčních ASP.NET Corech aplikacích.
* Je užitečné při zkoumání nevracení paměti.
* Při šetření ověří GC, že se z paměti odebraly všechny objekty dangling, takže je možné změřit paměť.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analýza využití paměti aplikací

Vyhrazené nástroje mohou pomoci analyzovat využití paměti:

- Počítání odkazů na objekty
- Měření vlivu GC na využití procesoru
- Měření paměťového prostoru používaného pro každou generaci

K analýze využití paměti použijte následující nástroje:

* [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): lze použít na produkčních počítačích.
* [Analýza využití paměti bez ladicího programu sady Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Využití paměti profilu v sadě Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Zjišťování problémů s pamětí

Správce úloh se dá použít k získání nápadu o tom, kolik paměti aplikace ASP.NET používá. Hodnota paměti Správce úloh:

* Představuje velikost paměti, která je používána procesem ASP.NET.
* Zahrnuje živé objekty aplikace a další uživatele paměti, jako je využití nativní paměti.

Pokud se hodnota paměti Správce úloh zvětšuje na neomezenou dobu a nikdy nesloučí, aplikace bude mít nevrácenou paměť. Následující části ukazují a vysvětlují několik vzorců využití paměti.

## <a name="sample-display-memory-usage-app"></a>Ukázková aplikace pro zobrazení využití paměti

[Ukázková aplikace MemoryLeak](https://github.com/sebastienros/memoryleak) je k dispozici na GitHubu. Aplikace MemoryLeak:

* Obsahuje diagnostický kontroler, který shromažďuje data paměti a GC v reálném čase pro aplikaci.
* Má stránku index, která zobrazuje data paměti a GC. Stránka index se aktualizuje každou sekundu.
* Obsahuje kontroler rozhraní API, který poskytuje různé vzory zatížení paměti.
* Nástroj není podporovaným nástrojem, ale dá se použít k zobrazení vzorců využití paměti aplikací ASP.NET Core.

Spusťte MemoryLeak. Přidělená paměť se pomalu zvyšuje, dokud nedojde k GC. Paměť roste, protože nástroj přiděluje vlastní objekt pro zachycení dat. Následující obrázek ukazuje stránku indexu MemoryLeak, když dojde k GC 0. generace. Graf zobrazuje 0 RPS (počet požadavků za sekundu), protože nebyly volány koncové body rozhraní API z řadiče rozhraní API.

![předchozí graf](memory/_static/0RPS.png)

Graf zobrazuje dvě hodnoty využití paměti:

- Přiděleno: množství paměti obsazené spravovanými objekty
- [Pracovní sada](/windows/win32/memory/working-set): sada stránek ve virtuálním adresním prostoru procesu, které jsou aktuálně rezidenty ve fyzické paměti. Zobrazená pracovní sada má stejnou hodnotu jako správce úloh.

### <a name="transient-objects"></a>Přechodné objekty

Následující rozhraní API vytvoří instanci řetězce 10 KB a vrátí ji klientovi. V každé žádosti je nový objekt přidělen v paměti a zapsán do odpovědi. Řetězce jsou uloženy v rozhraní .NET jako UTF-16 znaků, takže každý znak trvá 2 bajty paměti.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Následující graf se generuje s poměrně malým zatížením, aby se zobrazilo, jaký vliv má GC na přidělení paměti.

![předchozí graf](memory/_static/bigstring.png)

Předchozí graf znázorňuje:

* 4K RPS (počet požadavků za sekundu).
* K kolekcím GC generace 0 dochází každé dvě sekundy.
* Pracovní sada je konstanta o velikosti přibližně 500 MB.
* PROCESOR je 12%.
* Spotřeba paměti a verze (prostřednictvím GC) je stabilní.

Následující graf se seřizuje s maximální propustností, kterou může počítač zpracovat.

![předchozí graf](memory/_static/bigstring2.png)

Předchozí graf znázorňuje:

* 22K RPS
* Kolekce GC pro generaci 0 se probíhají několikrát za sekundu.
* Kolekce 1. generace se spouštějí, protože aplikace přidělila mnohem více paměti za sekundu.
* Pracovní sada je konstanta o velikosti přibližně 500 MB.
* PROCESOR je 33%.
* Spotřeba paměti a verze (prostřednictvím GC) je stabilní.
* PROCESOR (33%) není přetížený, takže uvolňování paměti může udržovat vysoký počet přidělení.

### <a name="workstation-gc-vs-server-gc"></a>GC pro pracovní stanice vs. GC serveru

Systém uvolňování paměti .NET má dva různé režimy:

* **GC pracovní stanice**: optimalizováno pro plochu.
* **GC serveru**. Výchozí GC pro aplikace ASP.NET Core. Optimalizováno pro server.

Režim GC se dá explicitně nastavit v souboru projektu nebo v souboru *runtimeconfig. JSON* publikované aplikace. Následující kód ukazuje nastavení `ServerGarbageCollection` v souboru projektu:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Změna `ServerGarbageCollection` v souboru projektu vyžaduje opětovné vytvoření aplikace.

**Poznámka:** Uvolňování paměti serveru **není k dispozici na** počítačích s jedním jádrem. Další informace najdete v tématu <xref:System.Runtime.GCSettings.IsServerGC>.

Následující obrázek ukazuje profil paměti v rámci 5K RPS pomocí GC pracovní stanice.

![předchozí graf](memory/_static/workstation.png)

Rozdíly mezi tímto grafem a verzí serveru jsou významné:

- Pracovní sada klesne z 500 MB na 70 MB.
- Uvolňování paměti v GC vyprší víckrát za sekundu místo každé dvě sekundy.
- Uvolňování paměti se zamítá z 300 MB na 10 MB.

V typických prostředích webového serveru je využití procesoru důležitější než paměť, proto je server GC lepší. Pokud je využití paměti vysoké a využití procesoru je poměrně nízké, může být UVOLŇOVÁNí paměti pracovní stanice více výkonné. Například vysoká hustota hostující několik webových aplikací, kde je paměť omezených.

### <a name="persistent-object-references"></a>Trvalé odkazy na objekty

GC nemůže uvolnit objekty, na které se odkazuje. Objekty, na které se odkazuje, ale které už nepotřebují, mají za následek nevracení paměti. Pokud aplikace často přiděluje objekty a neuvolní je až po jejich nepotřebení, využití paměti se v průběhu času zvýší.

Následující rozhraní API vytvoří instanci řetězce 10 KB a vrátí ji klientovi. Rozdíl s předchozím příkladem je, že tato instance je odkazována statickým členem, což znamená, že není nikdy k dispozici pro shromažďování.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

Předchozí kód:

* Je příkladem typické nevrácení paměti.
* S častými voláními způsobí, že se paměť aplikace zvětšuje, dokud proces neselže s výjimkou `OutOfMemory`.

![předchozí graf](memory/_static/eternal.png)

Na předchozím obrázku:

* Zátěžové testování `/api/staticstring` koncový bod způsobuje lineární zvětšení paměti.
* GC se pokusí uvolnit paměť, protože tlak paměti roste, voláním kolekce 2. generace.
* GC nemůže uvolnit nevrácenou paměť. Přidělená a pracovní sada se zvýšila s časem.

Některé scénáře, jako je například ukládání do mezipaměti, vyžadují, aby byly uloženy odkazy na objekty, dokud tlak vynutí uvolnění paměti. Třídu <xref:System.WeakReference> lze použít pro tento typ kódu pro ukládání do mezipaměti. Objekt `WeakReference` je shromážděn v části tlaky paměti. Výchozí implementace <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> používá `WeakReference`.

### <a name="native-memory"></a>Nativní paměť

Některé objekty .NET Core spoléhají na nativní paměť. UVOLŇOVÁNí paměti **nelze shromáždit** nativní paměť. Objekt .NET s použitím nativní paměti musí uvolnit pomocí nativního kódu.

Rozhraní .NET poskytuje rozhraní <xref:System.IDisposable>, které vývojářům umožňuje uvolnit nativní paměť. I v případě, že není volána <xref:System.IDisposable.Dispose*>, správně implementované třídy volají `Dispose` při spuštění [finalizační metody](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .

Vezměte v úvahu následující kód:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicaFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) je spravovaná třída, takže všechny instance budou shromážděny na konci žádosti.

Následující obrázek ukazuje profil paměti a průběžně vyvolal rozhraní API pro `fileprovider`.

![předchozí graf](memory/_static/fileprovider.png)

Předchozí graf znázorňuje zjevné problémy s implementací této třídy, protože zajišťuje zvýšení využití paměti. Jedná se o známý problém, který se sleduje v [tomto problému](https://github.com/aspnet/Home/issues/3110).

Stejná netěsnost by mohla být provedena v uživatelském kódu, a to jedním z následujících způsobů:

* Neuvolňuje třídu správně.
* Forgetting vyvolat metodu `Dispose`závislých objektů, které by měly být uvolněny.

### <a name="large-objects-heap"></a>Halda velkých objektů

Časté přiřazování paměti/bezplatné cykly můžou rozdělit paměť, zejména při přidělování velkých bloků paměti. Objekty jsou přidělovány v souvislém bloku paměti. Aby se zmírnila fragmentace, když GC uvolní paměť, trys ji defragmentovat. Tento proces se nazývá **komprimace**. Komprimace zahrnuje přesun objektů. Přesunutí velkých objektů ukládá snížení výkonu. Z tohoto důvodu GC vytvoří speciální zónu paměti pro _velké_ objekty označované jako [halda velkých objektů](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Objekty, které jsou větší než 85 000 bajtů (přibližně 83 KB), jsou:

* Umístit do LOH.
* Není zkomprimováno.
* Shromážděno během generace 2 GC.

Když je LOH plný, GC spustí kolekci 2. generace. Kolekce 2. generace:

* Jsou ve své podstatě pomalé.
* Navíc se účtují náklady na aktivaci kolekce na všech ostatních generacích.

Následující kód komprimuje LOH hned:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Informace o komprimaci LOH najdete v tématu <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>.

V kontejnerech pomocí .NET Core 3,0 a novějších se LOH automaticky zkomprimuje.

Toto chování ilustruje následující rozhraní API:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Následující graf znázorňuje profil paměti volajícího koncového bodu `/api/loh/84975` v rámci maximálního zatížení:

![předchozí graf](memory/_static/loh1.png)

Následující graf znázorňuje profil paměti pro volání `/api/loh/84976`ho koncového bodu a přidělení *pouze jednoho bajtu*:

![předchozí graf](memory/_static/loh2.png)

Poznámka: struktura `byte[]` má režijní bajty. To je důvod, proč 84 976 bajtů spouští limit 85 000.

Porovnání dvou předchozích grafů:

* Pracovní sada je pro oba scénáře podobná, přibližně 450 MB.
* V části požadavky LOH (84 975 bajtů) se zobrazí hlavně kolekce 0. generace.
* Požadavky over LOH generují kolekce konstant generace 2. Kolekce 2. generace jsou nákladné. Je potřeba více PROCESORů a propustnost se sníží téměř 50%.

Dočasné velké objekty jsou obzvláště problematické, protože způsobují Gen2 GC.

Pro maximální výkon by se mělo minimalizovat použití velkých objektů. Pokud je to možné, rozdělte velké objekty. Například middleware pro [ukládání odpovědí do mezipaměti](xref:performance/caching/response) v ASP.NET Core rozdělí položky mezipaměti do bloků menších než 85 000 bajtů.

Následující odkazy znázorňují ASP.NET Core přístup k udržení objektů pod limitem LOH:
- [ResponseCaching/Streams/StreamUtilities. cs](https://github.com/aspnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
- [ResponseCaching/MemoryResponseCache. cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Další informace naleznete v tématu:

* [Nekrytá halda Large Object](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Halda velkých objektů](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

Nesprávné použití <xref:System.Net.Http.HttpClient> může způsobit nevracení prostředků. Systémové prostředky, jako jsou databázová připojení, sokety, popisovače souborů atd.:

* Je více omezených než paměť.
* Jsou více problematické při nevracení paměti.

Zkušení vývojáři rozhraní .NET vědí, že volají <xref:System.IDisposable.Dispose*> objektů, které implementují <xref:System.IDisposable>. Nevyřazuje objekty, které implementují `IDisposable` obvykle mají za následek nevrácenou paměť nebo nevrácené systémové prostředky.

`HttpClient` implementuje `IDisposable`, **ale neměla by být** uvolněna při každém vyvolání. Místo toho je třeba znovu použít `HttpClient`.

Následující koncový bod vytvoří novou instanci `HttpClient` a odstraní ji na každý požadavek:

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

Při načtení se zaprotokolují následující chybové zprávy:

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

I když jsou instance `HttpClient` vyřazeny, vlastní síťové připojení bude nějakou dobu trvat, než operační systém uvolní. Průběžným vytvářením nových připojení dojde k _vyčerpání portů_ . Každé připojení klienta vyžaduje svůj vlastní port klienta.

Jedním ze způsobů, jak zabránit vyčerpání portů, je použít stejnou instanci `HttpClient`:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

Instance `HttpClient` se uvolní při zastavení aplikace. Tento příklad ukazuje, že po každém použití by mělo být uvolněno každý prostředek na jedno použití.

Pro lepší zpracování životnosti `HttpClient` instance se podívejte na následující:

* [Správa HttpClient a životního cyklu](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [Blog o továrně HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Sdružování objektů

Předchozí příklad ukázal, jak může být instance `HttpClient` vytvořená staticky a znovu použita všemi požadavky. Při opakovaném použití se nebudete moci dostat z prostředků.

Sdružování objektů:

* Používá vzor opakovaného použití.
* Je určený pro objekty, které je náročné vytvořit.

Fond je kolekce předem inicializovaných objektů, které mohou být rezervovány a vydány v rámci vláken. Fondy můžou definovat pravidla přidělování, jako jsou limity, předdefinované velikosti nebo míry růstu.

Balíček NuGet [Microsoft. Extensions. fond objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) obsahuje třídy, které vám pomůžou tyto fondy spravovat.

Následující koncový bod rozhraní API vytvoří instanci `byte` vyrovnávací paměti, která je vyplněna náhodnými čísly na každém požadavku:

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

Následující zobrazení grafu volá předchozí rozhraní API se středním zatížením:

![předchozí graf](memory/_static/array.png)

V předchozím grafu se kolekce generace 0 nevyskytují přibližně jednou za sekundu.

Předchozí kód může být optimalizován vysdružováním vyrovnávací paměti `byte` pomocí [`ArrayPool<T>`](xref:System.Buffers.ArrayPool`1). Statická instance se opakovaně používá napříč požadavky.

To se liší od tohoto přístupu je to, že objekt ve fondu je vrácen z rozhraní API. To znamená:

* Objekt je mimo váš ovládací prvek, jakmile se vrátíte z metody.
* Nemůžete uvolnit objekt.

Nastavení vyřazení objektu:

* Zapouzdřuje pole ve fondu na jedno a více objektů.
* Zaregistrujte objekt ve fondu s [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose` se postará o volání `Dispose`na cílovém objektu tak, aby se uvolnilo jenom po dokončení požadavku HTTP.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

Použití stejného zatížení jako nesdružené verze vede k následujícímu grafu:

![předchozí graf](memory/_static/pooledarray.png)

Hlavní rozdíl je přidělený bajtů a jako důsledek je to mnohem méně kolekcí 0. generace.

## <a name="additional-resources"></a>Další zdroje

* [Uvolňování paměti](/dotnet/standard/garbage-collection/)
* [Porozumění různým režimům GC s Vizualizérm souběžnosti](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Nekrytá halda Large Object](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Halda velkých objektů](/dotnet/standard/garbage-collection/large-object-heap)

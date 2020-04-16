---
title: Správa paměti a vzory v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak je paměť spravována v ASP.NET Core a jak funguje systém uvolňování paměti (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440945"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Správa paměti a uvolňování paměti (GC) v ASP.NET Core

[Sébastien Ros](https://github.com/sebastienros) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Správa paměti je složitá, a to i ve spravovaném rámci, jako je rozhraní .NET. Analýza a pochopení problémů s pamětí může být náročné. Tento článek:

* Byl motivován mnoha *nevracení paměti* a *GC nefunguje* problémy. Většina těchto problémů byla způsobena tím, že nechápu, jak funguje spotřeba paměti v .NET Core, nebo nepochopení, jak se měří.
* Ukazuje problematické využití paměti a navrhuje alternativní přístupy.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Jak funguje uvolňování paměti (GC) v rozhraní .NET Core

Gc přiděluje segmenty haldy, kde každý segment je souvislý rozsah paměti. Objekty umístěné v haldě jsou rozděleny do jedné ze 3 generací: 0, 1 nebo 2. Generování určuje frekvenci GC pokusí uvolnit paměť na spravované objekty, které jsou již odkazuje aplikace. Nižší číslované generace jsou GC'd častěji.

Objekty jsou přesunuty z jedné generace do druhé na základě jejich životnosti. Jak objekty žijí déle, jsou přesunuty do vyšší generace. Jak již bylo zmíněno dříve, vyšší generace jsou GC'd méně často. Krátkodobé žil objekty vždy zůstávají v generaci 0. Například objekty, které jsou odkazovány během životnosti webové žádosti jsou krátkodobé. [Singletons](xref:fundamentals/dependency-injection#service-lifetimes) na úrovni aplikace obvykle migrují do generace 2.

Když se spustí aplikace ASP.NET Core, gc:

* Rezervuje některé paměti pro počáteční segmenty haldy.
* Potvrdí malou část paměti při načtení běhu.

Předchozí přidělení paměti se provádí z důvodů výkonu. Výhoda výkonu pochází z haldy segmenty v souvislé paměti.

### <a name="call-gccollect"></a>Volejte GC. Shromažďovat

Volám [GC. Shromážděte](xref:System.GC.Collect*) explicitně:

* **Nemělo** by se to provádět produkčními aplikacemi ASP.NET Core.
* Je užitečné při zkoumání nevracení paměti.
* Při zkoumání ověří GC odstranil všechny visící objekty z paměti, takže paměť může být měřena.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analýza využití paměti aplikace

Vyhrazené nástroje mohou pomoci analyzovat využití paměti:

- Odkazy na objekty inventury
- Měření, jaký vliv má gc na využití procesoru
- Měření paměťového prostoru používaného pro každou generaci

K analýze využití paměti použijte následující nástroje:

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Lze použít na výrobních strojích.
* [Analýza využití paměti bez ladicího programu sady Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Využití paměti profilu v sadě Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Zjišťování problémů s pamětí

Správce úloh lze získat představu o tom, kolik paměti ASP.NET aplikace používá. Hodnota paměti Správce úloh:

* Představuje množství paměti, která se používá procesem ASP.NET.
* Zahrnuje živé objekty aplikace a další spotřebitele paměti, jako je například využití nativní paměti.

Pokud hodnota paměti Správce úloh se zvyšuje neomezeně dlouho a nikdy se nevylučuje, aplikace má nevracení paměti. V následujících částech demonstrovat a vysvětlit několik vzorů využití paměti.

## <a name="sample-display-memory-usage-app"></a>Ukázková aplikace pro využití paměti zobrazení

[Ukázková aplikace MemoryLeak](https://github.com/sebastienros/memoryleak) je k dispozici na GitHubu. Aplikace MemoryLeak:

* Obsahuje diagnostický řadič, který shromažďuje paměť v reálném čase a gc data pro aplikaci.
* Má index stránku, která zobrazuje paměť a GC data. Stránka Rejstřík je aktualizována každou sekundu.
* Obsahuje řadič rozhraní API, který poskytuje různé vzory načítání paměti.
* Není však podporovanýnástroj, lze jej však použít k zobrazení vzorců využití paměti aplikací ASP.NET Core.

Spusťte únik paměti. Přidělená paměť se pomalu zvyšuje, dokud nedojde k gc. Paměť se zvyšuje, protože nástroj přiděluje vlastní objekt pro sběr dat. Následující obrázek znázorňuje stránku MemoryLeak Index, když dojde k Gen 0 GC. Graf zobrazuje 0 RPS (Požadavky za sekundu), protože nebyly volány žádné koncové body rozhraní API z řadiče rozhraní API.

![předchozí graf](memory/_static/0RPS.png)

Graf zobrazuje dvě hodnoty využití paměti:

- Přiděleno: velikost paměti obsazené spravovanými objekty
- [Pracovní sada](/windows/win32/memory/working-set): Sada stránek ve virtuálním adresovém prostoru procesu, které jsou aktuálně rezidentní ve fyzické paměti. Zobrazená pracovní sada má stejnou hodnotu Správce úloh.

### <a name="transient-objects"></a>Přechodné objekty

Následující rozhraní API vytvoří instanci string 10 kB a vrátí ji klientovi. Při každém požadavku je nový objekt přidělen v paměti a zapsán do odpovědi. Řetězce jsou uloženy jako znaky UTF-16 v rozhraní .NET, takže každý znak trvá 2 bajty v paměti.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Následující graf je generován s relativně malé zatížení v ukázat, jak přidělení paměti jsou ovlivněny GC.

![předchozí graf](memory/_static/bigstring.png)

Předchozí graf ukazuje:

* 4K RPS (Požadavky za sekundu).
* Generace 0 GC kolekce dojít přibližně každé dvě sekundy.
* Pracovní sada je konstantní na přibližně 500 MB.
* CPU je 12%.
* Spotřeba paměti a uvolnění (prostřednictvím GC) je stabilní.

Následující graf je proveden na maximální propustnost, která může být zpracována strojem.

![předchozí graf](memory/_static/bigstring2.png)

Předchozí graf ukazuje:

* 22K RPS
* Generace 0 GC kolekce dojít několikrát za sekundu.
* Generace 1 kolekce jsou spuštěny, protože aplikace přiděleno výrazně více paměti za sekundu.
* Pracovní sada je konstantní na přibližně 500 MB.
* CPU je 33%.
* Spotřeba paměti a uvolnění (prostřednictvím GC) je stabilní.
* Procesor (33%) není nadměrně využívána, proto může uvolňování paměti držet krok s vysokým počtem přidělení.

### <a name="workstation-gc-vs-server-gc"></a>Pracovní stanice GC vs. Server GC

Uvolňování paměti .NET má dva různé režimy:

* **Pracovní stanice GC**: Optimalizováno pro stolní počítače.
* **Server GC**. Výchozí gc pro ASP.NET základní aplikace. Optimalizováno pro server.

Režim GC lze explicitně nastavit v souboru projektu nebo v souboru *runtimeconfig.json* publikované aplikace. V souboru projektu `ServerGarbageCollection` jsou zobrazeny následující značky:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Změna `ServerGarbageCollection` v souboru projektu vyžaduje, aby byla aplikace znovu sestavena.

**Poznámka:** Uvolňování paměti serveru **není** k dispozici na počítačích s jedním jádrem. Další informace naleznete v tématu <xref:System.Runtime.GCSettings.IsServerGC>.

Následující obrázek znázorňuje profil paměti v rámci 5K RPS pomocí pracovní stanice GC.

![předchozí graf](memory/_static/workstation.png)

Rozdíly mezi tímto grafem a verzí serveru jsou významné:

- Pracovní sada klesne z 500 MB na 70 MB.
- Gc dělá generace 0 kolekce vícekrát za sekundu namísto každé dvě sekundy.
- GC klesne z 300 MB na 10 MB.

V typickém prostředí webového serveru je využití procesoru důležitější než paměť, proto je lepší server GC. Pokud je využití paměti vysoké a využití procesoru je relativně nízká, pracovní stanice GC může být výkonnější. Například vysoká hustota hostování několika webových aplikací, kde je nedostatek paměti.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC pomocí Dockeru a malých kontejnerů

Pokud je v jednom počítači spuštěno více kontejnerizovaných aplikací, může být pracovní stanice GC více než server GC. Další informace naleznete [v tématu Spuštění s serverem GC v malém kontejneru](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) a [spuštěno se serverem GC v části 1 scénáře malého kontejneru – pevný limit pro haldu GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Trvalé odkazy na objekty

Gc nemůže uvolnit objekty, které jsou odkazovány. Objekty, které jsou odkazovány, ale již nejsou potřeba, mají za následek nevracení paměti. Pokud aplikace často přiděluje objekty a nepodaří je uvolnit poté, co již nejsou potřeba, využití paměti se časem zvýší.

Následující rozhraní API vytvoří instanci string 10 kB a vrátí ji klientovi. Rozdíl oproti předchozímu příkladu je, že tato instance je odkazována statickým členem, což znamená, že není nikdy k dispozici pro sběr.

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

Předcházející kód:

* Je příkladem typické nevracení paměti.
* Při častých voláních způsobí, že se `OutOfMemory` paměť aplikace zvětší, dokud se proces nezhroutí s výjimkou.

![předchozí graf](memory/_static/eternal.png)

Na předchozím obrázku:

* Zátěžové `/api/staticstring` testování koncového bodu způsobí lineární zvýšení paměti.
* Gc se pokusí uvolnit paměť jako tlak paměti roste voláním generace 2 kolekce.
* Gc nelze uvolnit nevrácené paměti. Přidělené a pracovní sady se časem zvyšují.

Některé scénáře, jako je například ukládání do mezipaměti, vyžadují, aby byly odkazy na objekt y drženy, dokud je tlak paměti nevynutí uvolnění. Třídu <xref:System.WeakReference> lze použít pro tento typ kódu ukládání do mezipaměti. Objekt `WeakReference` je shromažďována pod tlaky paměti. Výchozí implementace <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> použití `WeakReference`.

### <a name="native-memory"></a>Nativní paměť

Některé objekty .NET Core spoléhají na nativní paměť. Nativní paměť **nemůže** být shromažďována GC. Objekt .NET používající nativní paměť jej musí uvolnit pomocí nativního kódu.

Rozhraní .NET <xref:System.IDisposable> poskytuje rozhraní, které umožňuje vývojářům uvolnit nativní paměť. I <xref:System.IDisposable.Dispose*> když není volána, `Dispose` správně implementované třídy volání při spuštění [finalizační metody.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)

Uvažujte následující kód:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) je spravovaná třída, takže všechny instance budou shromažďovány na konci požadavku.

Následující obrázek znázorňuje profil paměti `fileprovider` při vyvolání rozhraní API nepřetržitě.

![předchozí graf](memory/_static/fileprovider.png)

Předchozí graf ukazuje zřejmý problém s implementací této třídy, protože stále zvyšuje využití paměti. Jedná se o známý problém, který je sledován v [tomto vydání](https://github.com/dotnet/aspnetcore/issues/3110).

Stejný únik může dojít v uživatelském kódu, jedním z následujících:

* Není uvolnění třídy správně.
* Zapomínání vyvolat `Dispose`metodu závislé objekty, které by měly být uvolněny.

### <a name="large-objects-heap"></a>Hromada velkých objektů

Časté přidělení paměti/volné cykly může fragmentovat paměť, zejména při přidělování velké bloky paměti. Objekty jsou přiděleny v souvislých blocích paměti. Chcete-li zmírnit fragmentaci, když GC uvolní paměť, pokusí se ji defragmentovat. Tento proces se nazývá **zhutnění**. Zhutnění zahrnuje přesouvání objektů. Přesunutí velkých objektů ukládá snížení výkonu. Z tohoto důvodu GC vytvoří speciální paměťovou zónu pro _velké_ objekty, nazývané [haldy velkých objektů](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Objekty, které jsou větší než 85 000 bajtů (přibližně 83 kB), jsou:

* Umístěnna na LOH.
* Není zhutněný.
* Shromažďovány během generace 2 GCs.

Když LOH je plná, GC spustí generace 2 kolekce. Kolekce Generace 2:

* Jsou ze své podstaty pomalé.
* Navíc vznikají náklady na aktivaci kolekce na všech ostatních generacích.

Následující kód okamžitě zkomprimuje LOH:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Viz <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> informace o zhutnění LOH.

V kontejnerech pomocí .NET Core 3.0 a novější loh je automaticky komprimován.

Následující rozhraní API, které ilustruje toto chování:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Následující graf zobrazuje profil paměti `/api/loh/84975` volání koncového bodu při maximálním zatížení:

![předchozí graf](memory/_static/loh1.png)

Následující graf znázorňuje profil `/api/loh/84976` paměti volání koncového bodu a přiděluje *ještě jeden bajt*:

![předchozí graf](memory/_static/loh2.png)

Poznámka: `byte[]` Struktura má režijní bajty. To je důvod, proč 84,976 bajtů aktivuje limit 85,000.

Porovnání dvou předchozích grafů:

* Pracovní sada je podobná pro oba scénáře, asi 450 MB.
* Pod Požadavky LOH (84,975 bajtů) zobrazuje většinou generace 0 kolekce.
* Přes Požadavky LOH generovat konstantní generace 2 kolekce. Generace 2 kolekce jsou drahé. Je zapotřebí více procesoru a propustnost klesá téměř o 50 %.

Dočasné velké objekty jsou obzvláště problematické, protože způsobují gen2 GC.

Pro maximální výkon by mělo být minimalizováno použití velkých objektů. Pokud je to možné, rozdělte velké objekty. Například [middleware pro ukládání do mezipaměti odpovědi](xref:performance/caching/response) v ASP.NET Core rozdělilo položky mezipaměti na bloky menší než 85 000 bajtů.

Následující odkazy ukazují ASP.NET základní přístup k udržování objektů pod limitem LOH:

* [ResponseCaching/Streams/StreamUtilities.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Další informace naleznete v tématu:

* [Velká objektová halda odkrytá](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Hromada velkých objektů](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

Nesprávné použití <xref:System.Net.Http.HttpClient> může mít za následek nevracení prostředků. Systémové prostředky, jako jsou připojení k databázi, sokety, popisovače souborů atd.:

* Jsou vzácnější než paměť.
* Jsou problematičtější při úniku než paměti.

Zkušení vývojáři rozhraní <xref:System.IDisposable.Dispose*> .NET vědí, že mají volat objekty, které implementují <xref:System.IDisposable>. Není likvidace objektů, `IDisposable` které implementují obvykle vede k nevracení paměti nebo nevracení systémových prostředků.

`HttpClient`vynucuje `IDisposable`, ale **neměl** by být zlikvidován při každém vyvolání. Spíše `HttpClient` by měly být znovu použity.

Následující koncový bod vytvoří a `HttpClient` zmaže novou instanci na každém požadavku:

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

Při načtení jsou zaznamenány následující chybové zprávy:

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

Přestože `HttpClient` jsou instance uvolněny, skutečné síťové připojení trvá nějakou dobu, než bude uvolněno operačním systémem. Neustálým vytvářením nových připojení dochází k _vyčerpání portů._ Každé připojení klienta vyžaduje vlastní port klienta.

Jedním ze způsobů, jak zabránit vyčerpání portu, je opětovné použití stejné `HttpClient` instance:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

Instance `HttpClient` se uvolní, když se aplikace zastaví. Tento příklad ukazuje, že ne každý jednorázový prostředek by měl být uvolněn po každém použití.

Lepší způsob zpracování životnosti `HttpClient` instance najdete v následujících tématech:

* [HttpClient a správa životnosti](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [Blog o výrobě klienta HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Sdružování objektů

Předchozí příklad ukázal, `HttpClient` jak může být instance statická a znovu použita všemi požadavky. Opakované použití zabraňuje vyčerpání prostředků.

Sdružování objektů:

* Použije vzorek opakovaného použití.
* Je určen pro objekty, které jsou nákladné vytvořit.

Fond je kolekce předem inicializovaných objektů, které mohou být vyhrazeny a uvolněny v podprocesech. Fondy mohou definovat pravidla přidělení, jako jsou omezení, předdefinované velikosti nebo rychlost růstu.

Balíček NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) obsahuje třídy, které pomáhají spravovat tyto fondy.

Následující koncový bod rozhraní API `byte` inkonzisuje vyrovnávací paměť, která je vyplněna náhodnými čísly na každém požadavku:

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

Následující graf zobrazuje volání předchozího rozhraní API s mírným zatížením:

![předchozí graf](memory/_static/array.png)

V předchozím grafu generace 0 kolekce dojít přibližně jednou za sekundu.

Předchozí kód lze optimalizovat sdružováním `byte` vyrovnávací paměti pomocí [>ArrayPool\<T ](xref:System.Buffers.ArrayPool`1). Statická instance je znovu použita napříč požadavky.

Co se liší s tímto přístupem je, že sdružený objekt je vrácena z rozhraní API. To znamená:

* Objekt je mimo vaši kontrolu, jakmile se vrátíte z metody.
* Objekt nelze uvolnit.

Chcete-li nastavit likvidaci objektu:

* Zapouzdřit sdružené pole v objektu na jedno použití.
* Zaregistrujte sdružený objekt pomocí [httpcontext.response.registerFordispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose`postará o volání `Dispose`cílového objektu tak, aby byl vydán pouze po dokončení požadavku HTTP.

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

Použití stejnézatížení jako nesdružená verze má za následek následující graf:

![předchozí graf](memory/_static/pooledarray.png)

Hlavní rozdíl je přidělena bajtů a v důsledku toho mnohem méně generace 0 kolekce.

## <a name="additional-resources"></a>Další zdroje

* [Uvolněné](/dotnet/standard/garbage-collection/)
* [Principy různých režimů GC pomocí vizualizéru souběžnosti](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Velká objektová halda odkrytá](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Hromada velkých objektů](/dotnet/standard/garbage-collection/large-object-heap)

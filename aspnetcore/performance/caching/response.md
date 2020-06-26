---
title: Ukládání odpovědí do mezipaměti v ASP.NET Core
author: rick-anderson
description: Naučte se, jak používat ukládání odpovědí do mezipaměti pro menší požadavky na šířku pásma a zvýšit výkon aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/response
ms.openlocfilehash: 25d6bdae0fce7821ec7b9195817dc07ef9aed40f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408185"
---
# <a name="response-caching-in-aspnet-core"></a>Ukládání odpovědí do mezipaměti v ASP.NET Core

[Jan Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Steve Smith](https://ardalis.com/)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukládání odpovědí do mezipaměti snižuje počet požadavků, které klient nebo proxy vytvoří na webový server. Ukládání odpovědí do mezipaměti také snižuje množství práce, které webový Server provede k vygenerování odpovědi. Ukládání odpovědí do mezipaměti je řízeno hlavičkami, které určují, jak má klient, proxy a middleware ukládat odpovědi do mezipaměti.

[Atribut ResponseCache](#responsecache-attribute) se účastní nastavení hlaviček ukládání odpovědí do mezipaměti. Klienti a zprostředkující proxy servery by měly dodržovat hlavičky pro ukládání odpovědí do mezipaměti v rámci [specifikace ukládání do mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234).

Pro ukládání do mezipaměti na straně serveru, které následuje za specifikací mezipaměti HTTP 1,1, použijte [middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware). Middleware může pomocí <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> vlastností ovlivnit chování ukládání do mezipaměti na straně serveru.

## <a name="http-based-response-caching"></a>Ukládání odpovědí na základě protokolu HTTP

[Specifikace ukládání do mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234) popisuje, jak se mají chovat internetové mezipaměti. Primární hlavičkou HTTP použitou pro ukládání do mezipaměti je [řízení mezipaměti](https://tools.ietf.org/html/rfc7234#section-5.2), které se používá k určení *direktiv*mezipaměti. Direktivy řídí chování ukládání do mezipaměti jako požadavky od klientů po servery a jako reakce způsobují od serverů zpátky klientům. Žádosti a odpovědi se pohybují přes proxy servery a proxy servery musí splňovat i specifikace mezipaměti HTTP 1,1.

`Cache-Control`V následující tabulce jsou uvedeny společné direktivy.

| Směrnici                                                       | Akce |
| --------------------------------------------------------------- | ------ |
| [republik](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Mezipaměť může ukládat odpověď. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Odpověď nesmí být uložena sdílenou mezipamětí. Soukromá mezipaměť může uložit a znovu použít odpověď. |
| [Maximální stáří](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Klient nepřijme odpověď, jejíž stáří je větší než zadaný počet sekund. Příklady: `max-age=60` (60 sekund), `max-age=2592000` (1 měsíc) |
| [No – mezipaměť](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **V případě požadavků**: mezipaměť nesmí k uspokojení požadavku použít uloženou odpověď. Zdrojový server znovu vygeneruje odpověď pro klienta a middleware aktualizuje uloženou odpověď v mezipaměti.<br><br>**Na odpovědích**: odpověď nesmí být použita pro následné žádosti bez ověření na zdrojovém serveru. |
| [bez uložení](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **V žádostech**: mezipaměť nesmí ukládat požadavek.<br><br>**V odpovědích**: mezipaměť nesmí ukládat žádnou část odpovědi. |

Další hlavičky mezipaměti, které hrají roli v mezipaměti, jsou uvedeny v následující tabulce.

| Záhlaví                                                     | Funkce |
| ---------------------------------------------------------- | -------- |
| [Věk](https://tools.ietf.org/html/rfc7234#section-5.1)     | Odhad doby v sekundách, po jejímž uplynutí byla odpověď generována nebo úspěšně ověřena na zdrojovém serveru. |
| [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | Čas, po jehož uplynutí je odpověď považována za zastaralou. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Existuje pro zpětnou kompatibilitu s mezipamětí HTTP/1.0 pro `no-cache` chování nastavení. Pokud `Cache-Control` je hlavička k dispozici, `Pragma` záhlaví je ignorováno. |
| [Toho](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Určuje, že odpověď uložená v mezipaměti nesmí být odeslána, pokud se všechna `Vary` pole hlaviček shodují v původní žádosti odpovědi v mezipaměti i v nové žádosti. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Požadavky na ukládání do mezipaměti založené na protokolu HTTP respektují direktivy řízení mezipaměti

[Specifikace mezipaměti HTTP 1,1 pro hlavičku Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) vyžaduje mezipaměť, aby bylo možné akceptovat platnou `Cache-Control` hlavičku odeslanou klientem. Klient může vytvořit požadavky s `no-cache` hodnotou hlavičky a vynutit, aby server vygeneroval novou odpověď pro každý požadavek.

Použití `Cache-Control` hlaviček požadavků klienta je vhodné, pokud považujete cíl ukládání http do mezipaměti. V rámci oficiální specifikace je ukládání do mezipaměti určeno ke snížení latence a zatížení sítě při požadavcích na požadavky v síti klientů, proxy serverů a serverů. To není nutně způsob, jak řídit zatížení na zdrojovém serveru.

Při použití [middleware pro ukládání odpovědí](xref:performance/caching/middleware) do mezipaměti není k dispozici žádné vývojářské řízení, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti. [Plánovaná vylepšení middlewaru](https://github.com/dotnet/AspNetCore/issues/2612) jsou příležitostí ke konfiguraci middlewaru pro ignorování `Cache-Control` hlavičky žádosti při rozhodování o obsluze odpovědi v mezipaměti. Plánovaná vylepšení poskytují možnost lepšího řízení zatížení serveru.

## <a name="other-caching-technology-in-aspnet-core"></a>Další technologie pro ukládání do mezipaměti v ASP.NET Core

### <a name="in-memory-caching"></a>Ukládání do mezipaměti webového serveru

Ukládání do mezipaměti v paměti používá paměť serveru k ukládání dat uložených v mezipaměti. Tento typ ukládání do mezipaměti je vhodný pro jeden server nebo víc serverů pomocí *relací s rychlým cvičením*. Rychlé relace znamenají, že požadavky klienta jsou vždycky směrovány na stejný server ke zpracování.

Další informace naleznete v tématu <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Distribuovaná mezipaměť

Pomocí distribuované mezipaměti můžete ukládat data v paměti, když je aplikace hostovaná v cloudové nebo serverové farmě. Mezipaměť se sdílí mezi servery, které zpracovávají požadavky. Klient může odeslat žádost, která je zpracována jakýmkoli serverem ve skupině, pokud jsou k dispozici data uložená v mezipaměti pro klienta. ASP.NET Core pracuje s mezipamětí SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)a [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distribuované mezipaměti.

Další informace naleznete v tématu <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Uložení pomocné rutiny značky do mezipaměti

Ukládání obsahu do mezipaměti pro zobrazení nebo Razor stránku MVC pomocí pomocníka značky mezipaměti Pomocník značek Cache používá ukládání dat do mezipaměti v paměti.

Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Pomocná rutina značek v distribuované mezipaměti

Ukládání obsahu do mezipaměti pro zobrazení nebo Razor stránku MVC v distribuovaném cloudu nebo ve scénářích webové farmy pomocí pomocníka značek distribuované mezipaměti. Pomocný pomocník značek distribuované mezipaměti používá k ukládání dat SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)nebo [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .

Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>ResponseCache – atribut

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Určuje parametry potřebné pro nastavení příslušných hlaviček v ukládání odpovědí do mezipaměti.

> [!WARNING]
> Zakáže ukládání do mezipaměti pro obsah, který obsahuje informace pro ověřené klienty. Ukládání do mezipaměti by mělo být povolené jenom pro obsah, který se nemění v závislosti na identitě uživatele nebo na tom, jestli je uživatel přihlášený.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>mění uloženou odpověď podle hodnot daného seznamu klíčů dotazů. Pokud `*` je k dispozici jedna hodnota, middleware se liší odezvy všemi parametry řetězce dotazu na žádosti.

Aby bylo možné nastavit vlastnost, musí být povoleno [middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> . V opačném případě je vyvolána výjimka modulu runtime. Pro vlastnost není k dispozici odpovídající hlavička protokolu HTTP <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> . Vlastnost je funkce HTTP, kterou zpracovává middleware pro ukládání odpovědí do mezipaměti. Aby middleware poskytovala odpověď uloženou v mezipaměti, řetězec dotazu a hodnota řetězce dotazu musí odpovídat předchozí žádosti. Zvažte například posloupnost požadavků a výsledků, které jsou uvedeny v následující tabulce.

| Žádost                          | Výsledek                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Vráceno ze serveru. |
| `http://example.com?key1=value1` | Vráceno z middlewaru. |
| `http://example.com?key1=value2` | Vráceno ze serveru. |

První požadavek vrací Server a ukládá do mezipaměti v middlewaru. Druhý požadavek vrací middleware, protože řetězec dotazu odpovídá předchozímu požadavku. Třetí žádost není v mezipaměti middlewaru, protože hodnota řetězce dotazu neodpovídá předchozí žádosti.

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Slouží ke konfiguraci a vytvoření (prostřednictvím) a <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` . `ResponseCacheFilter`Provede práci s aktualizací odpovídajících hlaviček protokolu HTTP a funkcí odpovědi. Filtr:

* Odebere všechna existující záhlaví pro `Vary` , `Cache-Control` a `Pragma` .
* Zapíše odpovídající hlavičky na základě vlastností nastavených v <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .
* Aktualizuje funkci ukládání do mezipaměti odpovědí HTTP, pokud <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> je nastavená.

### <a name="vary"></a>Toho

Tato hlavička je zapsána pouze v případě, že <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> je nastavena vlastnost. Vlastnost nastavená na `Vary` hodnotu vlastnosti. Následující příklad používá <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> vlastnost:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Pomocí ukázkové aplikace si prohlédněte hlavičky odpovědí pomocí síťových nástrojů v prohlížeči. Následující hlavičky odpovědí se odesílají s odpovědí na stránku Cache1:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>Úložiště a umístění. žádné

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>přepíše většinu ostatních vlastností. Je-li tato vlastnost nastavena na hodnotu `true` , `Cache-Control` záhlaví je nastaveno na `no-store` . Pokud <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> je nastaveno na `None` :

* `Cache-Control`je nastaven na `no-store,no-cache` .
* `Pragma`je nastaven na `no-cache` .

Pokud <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> je `false` a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> je `None` , `Cache-Control` , a `Pragma` jsou nastaveny na `no-cache` .

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>je obvykle nastaveno na `true` pro chybové stránky. Stránka Cache2 v ukázkové aplikaci vytváří hlavičky odpovědí, které klientovi neukládají odpověď.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

Ukázková aplikace vrátí stránku Cache2 s následujícími záhlavími:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Umístění a doba trvání

Aby bylo možné povolit ukládání do mezipaměti, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> musí být nastaveno na kladnou hodnotu a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> musí být buď `Any` (výchozí), nebo `Client` . Rozhraní nastaví `Cache-Control` hlavičku na hodnotu umístění následovanou `max-age` odpovědí.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>možnosti `Any` a `Client` překládat do `Cache-Control` hodnot záhlaví a v `public` `private` uvedeném pořadí. Jak je uvedeno v části [úložiště a umístění. None](#nostore-and-locationnone) , nastavení <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> pro nastavení `None` `Cache-Control` `Pragma` hlaviček na `no-cache` .

`Location.Any`( `Cache-Control` nastaveno na `public` ) znamená, že *klient nebo jakýkoli zprostředkující proxy server* může hodnotu ukládat do mezipaměti, včetně [middlewaru pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware).

`Location.Client`( `Cache-Control` nastaveno na `private` ) znamená, že hodnotu může ukládat *pouze klient* . Žádná mezimezipaměť by neměla hodnotu ukládat do mezipaměti, včetně [middlewaru pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware).

Řídicí hlavičky mezipaměti pouze poskytují pokyny klientům a zprostředkujícím proxy serverům, kdy a jak ukládat odpovědi do mezipaměti. Není nijak zaručeno, že klienti a proxy budou respektovat [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234). [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) bude vždycky postupovat podle pravidel pro ukládání do mezipaměti, která jsou stanovená specifikací

Následující příklad ukazuje model stránky Cache3 z ukázkové aplikace a záhlaví vytvořená nastavením <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> a ponecháním výchozí <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> hodnoty:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

Ukázková aplikace vrátí stránku Cache3 s následující hlavičkou:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Profily mezipaměti

Místo duplikace nastavení mezipaměti odpovědí u mnoha atributů akce kontroleru lze profily mezipaměti nakonfigurovat jako možnosti při nastavování MVC nebo Razor stránek v `Startup.ConfigureServices` . Hodnoty nalezené v profilu odkazované mezipaměti se používají jako výchozí hodnoty <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> a jsou potlačeny všemi vlastnostmi zadanými v atributu.

Nastavte profil mezipaměti. Následující příklad ukazuje 30 sekundový profil mezipaměti v ukázkové aplikaci `Startup.ConfigureServices` :

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

Model stránky Cache4 ukázkové aplikace odkazuje na `Default30` profil mezipaměti:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Lze použít pro:

* RazorObslužné rutiny stránky (třídy): atributy nelze použít na metody obslužné rutiny.
* Řadiče MVC (třídy).
* Akce MVC (metody): atributy na úrovni metody přepíšou nastavení zadaná v atributech na úrovni třídy.

Výsledná hlavička použitá pro odpověď stránky Cache4 `Default30` profil mezipaměti:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Další zdroje

* [Ukládání odpovědí do mezipamětí](https://tools.ietf.org/html/rfc7234#section-3)
* [Řízení mezipaměti](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

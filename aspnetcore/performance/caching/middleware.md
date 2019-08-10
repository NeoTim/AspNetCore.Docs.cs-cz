---
title: Ukládání do mezipaměti middlewaru v ASP.NET Core
author: guardrex
description: Naučte se konfigurovat a používat middleware pro ukládání odpovědí do mezipaměti v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: performance/caching/middleware
ms.openlocfilehash: 6371f42b100f70c6042064a6372c7b9e41fd5c73
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914990"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Ukládání do mezipaměti middlewaru v ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Jan Luo](https://github.com/JunTaoLuo)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([stažení](xref:index#how-to-download-a-sample))

Tento článek vysvětluje, jak v aplikaci ASP.NET Core nakonfigurovat middleware pro ukládání odpovědí do mezipaměti. Middleware určuje, kdy je možné odpovědi ukládat do mezipaměti, ukládá odpovědi a obsluhuje odpovědi z mezipaměti. Úvod do mezipaměti HTTP a atributu [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) najdete v tématu [ukládání odpovědí do mezipaměti](xref:performance/caching/response).

## <a name="configuration"></a>Konfiguraci

Použijte soubor [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

`Startup.ConfigureServices`Do kolekce služby přidejte middleware pro ukládání odpovědí do mezipaměti:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

::: moniker-end

Nakonfigurujte aplikaci tak, aby používala middlewaru <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> s metodou rozšíření, která přidá middleware do kanálu zpracování požadavků v `Startup.Configure`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

::: moniker-end

Ukázková aplikace přidá hlavičky pro řízení ukládání do mezipaměti pro následné žádosti:

* [Řízení mezipaměti](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Ukládá do mezipaměti odpovědi po dobu až 10 sekund.
* [Různé](https://tools.ietf.org/html/rfc7231#section-7.1.4) Nakonfiguruje middleware tak, aby poskytoval odpověď uloženou [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) v mezipaměti pouze v případě, že hlavička následujících požadavků odpovídá původní žádosti. &ndash;

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

::: moniker-end

Middleware pro ukládání odpovědí do mezipaměti ukládá pouze odpovědi serveru, jejichž výsledkem je stavový kód 200 (OK). Všechny ostatní odpovědi, včetně [chybových stránek](xref:fundamentals/error-handling), se middleware ignorují.

> [!WARNING]
> Odpovědi obsahující obsah pro ověřené klienty musí být označené jako neukládatelné do mezipaměti, aby middleware nemohli ukládat a obsluhovat tyto odpovědi. Podrobnosti o tom, jak middleware určuje, jestli je odpověď mezipaměť, najdete v tématu [podmínky pro ukládání do mezipaměti](#conditions-for-caching) .

## <a name="options"></a>Možnosti

Možnosti ukládání odpovědi do mezipaměti jsou uvedené v následující tabulce.

| Možnost | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Největší velikost paměti pro tělo odpovědi v bajtech. Výchozí hodnota je `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Omezení velikosti pro middleware mezipaměti odpovědí v bajtech. Výchozí hodnota je `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Určuje, zda jsou odpovědi uloženy v mezipaměti pro cesty citlivé na velká a malá písmena. Výchozí hodnota je `false`. |

Následující příklad nakonfiguruje middleware na:

* Odpovědi v mezipaměti s velikostí těla menší nebo rovnou 1 024 bajtů.
* Uložte odpovědi podle cest s rozlišováním velkých a malých písmen. Například `/page1` a`/Page1` jsou uloženy samostatně.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Při použití řadičů MVC/webového rozhraní API nebo modelů Razor Pagesch stránek určuje atribut [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) parametry potřebné pro nastavení příslušných hlaviček pro ukládání odpovědí do mezipaměti. Jediný parametr `[ResponseCache]` atributu, který striktně vyžaduje <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>middleware, který neodpovídá skutečné hlavičce HTTP. Další informace naleznete v tématu <xref:performance/caching/response#responsecache-attribute>.

Pokud `[ResponseCache]` atribut nepoužíváte, ukládání odpovědí do mezipaměti lze měnit `VaryByQueryKeys`pomocí. Použijte přímo z [vlastnosti HttpContext. Features:](xref:Microsoft.AspNetCore.Http.HttpContext.Features) <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Použití jedné hodnoty, která `*` se rovná, se liší v `VaryByQueryKeys` mezipaměti všemi parametry dotazu Request.

## <a name="http-headers-used-by-response-caching-middleware"></a>Hlavičky HTTP používané middlewarem pro ukládání odpovědí do mezipaměti

Následující tabulka poskytuje informace o hlavičkách HTTP, které ovlivňují ukládání odpovědí do mezipaměti.

| Záhlaví | Podrobnosti |
| ------ | ------- |
| `Authorization` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. |
| `Cache-Control` | Middleware považuje jenom odpovědi do mezipaměti označené `public` direktivou cache. Řízení ukládání do mezipaměti pomocí následujících parametrů:<ul><li>Maximální stáří</li><li>max-stale&#8224;</li><li>min – čerstvé</li><li>nutné – znovu ověřit</li><li>No – mezipaměť</li><li>bez uložení</li><li>pouze v mezipaměti</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;Pokud není zadán `max-stale`žádný limit, middleware neprovede žádnou akci.<br>&#8225;`proxy-revalidate`má stejný účinek jako `must-revalidate`.<br><br>Další informace najdete v [dokumentu RFC 7231: Vyžádat direktivy](https://tools.ietf.org/html/rfc7234#section-5.2.1)řízení mezipaměti. |
| `Pragma` | Hlavička v žádosti má stejný účinek jako `Cache-Control: no-cache`. `Pragma: no-cache` Tato hlavička je přepsána příslušnými direktivami v `Cache-Control` hlavičce, pokud je k dispozici. Brána se považuje za zpětnou kompatibilitu s HTTP/1.0. |
| `Set-Cookie` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. Jakýkoli middleware v kanálu zpracování požadavků, který nastavuje jeden nebo více souborů cookie, brání v ukládání do mezipaměti middleware ukládání odpovědi do mezipaměti (například [poskytovatele TempData založeného na souborech cookie](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary` Záhlaví se používá k odlišení odpovědi uložené v mezipaměti jinou hlavičkou. Například odpovědi ukládat do mezipaměti podle kódování zahrnutím `Vary: Accept-Encoding` hlavičky, která ukládá do mezipaměti odpovědi pro žádosti s hlavičkami `Accept-Encoding: text/plain` `Accept-Encoding: gzip` a samostatně. Odpověď s hodnotou `*` záhlaví se nikdy neukládá. |
| `Expires` | Odpověď považovaná za zastaralou touto hlavičkou není uložená nebo načtená, `Cache-Control` Pokud není přepsána jinými záhlavími. |
| `If-None-Match` | Úplná odpověď se obsluhuje z mezipaměti, pokud hodnota není `*` `ETag` a odpověď neodpovídá žádné z poskytnutých hodnot. V opačném případě je zpracována odpověď 304 (Neupraveno). |
| `If-Modified-Since` | Pokud hlavička `If-None-Match` není k dispozici, bude z mezipaměti poskytována úplná odpověď, pokud je datum odpovědi v mezipaměti novější než zadaná hodnota. Jinak se neupraví odpověď *304* . |
| `Date` | Při obsluze z mezipaměti je `Date` hlavička nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Content-Length` | Při obsluze z mezipaměti je `Content-Length` hlavička nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Age` | `Age` Hlavička poslaná v původní odpovědi je ignorována. Middleware vypočítá novou hodnotu při obsluze odpovědi v mezipaměti. |

## <a name="caching-respects-request-cache-control-directives"></a>Požadavky na ukládání do mezipaměti – direktivy řízení mezipaměti

Middleware respektuje pravidla [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Pravidla vyžadují mezipaměť, aby bylo možné akceptovat platnou `Cache-Control` hlavičku odeslanou klientem. V rámci specifikace může klient provádět žádosti s `no-cache` hodnotou hlavičky a vynutit, aby server vygeneroval novou odpověď pro každý požadavek. V současné době není při používání middleware k tomuto chování při ukládání do mezipaměti k dispozici žádná vývojářská kontrola, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti.

Pro lepší kontrolu nad chováním při ukládání do mezipaměti můžete prozkoumat další funkce pro ukládání do mezipaměti ASP.NET Core. Přečtěte si následující témata:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Poradce při potížích

Pokud chování při ukládání do mezipaměti není podle očekávání, zkontrolujte, že odpovědi jsou ukládat do mezipaměti a že je možné je obsluhovat z mezipaměti. Projděte si vstupní hlavičky žádosti a odchozí hlavičky odpovědi. Povolit [protokolování](xref:fundamentals/logging/index) pro usnadnění ladění.

Při testování a odstraňování potíží s chováním při ukládání do mezipaměti může prohlížeč nastavit hlavičky požadavků, které mají vliv na ukládání do mezipaměti nežádoucím způsobem. Prohlížeč může například nastavit `Cache-Control` hlavičku na `no-cache` nebo `max-age=0` při aktualizaci stránky. Následující nástroje mohou explicitně nastavit hlavičky požadavků a jsou upřednostňovány pro testování ukládání do mezipaměti:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Podmínky pro ukládání do mezipaměti

* Požadavek musí mít za následek odpověď serveru se stavovým kódem 200 (OK).
* Metoda požadavku musí být GET nebo HEAD.
* V `Startup.Configure`nástroji musí být middleware pro ukládání odpovědí do mezipaměti umístěn před middlewarem, který vyžaduje ukládání do mezipaměti. Další informace naleznete v tématu <xref:fundamentals/middleware/index>.
* Hlavička `Authorization` nesmí být k dispozici.
* `Cache-Control`parametry hlaviček musí být platné a odpověď musí být označená `public` a nesmí být označená. `private`
* Hlavička nesmí být k dispozici, pokud `Cache-Control` hlavička `Cache-Control` není k dispozici, `Pragma` protože záhlaví potlačí hlavičku, pokud je k dispozici. `Pragma: no-cache`
* Hlavička `Set-Cookie` nesmí být k dispozici.
* `Vary`parametry záhlaví musí být platné a nesmí být rovny `*`.
* Hodnota `Content-Length` hlavičky (Pokud je nastavena) musí odpovídat velikosti těla odpovědi.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> Není použit.
* Odpověď nesmí být zastaralá tak, jak je `Expires` určena hlavičkou `max-age` a `s-maxage` direktivami cache a.
* Ukládání odpovědí do vyrovnávací paměti musí být úspěšné. Velikost odpovědi musí být menší než nakonfigurovaná nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>. Velikost textu odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.
* Odpověď musí být mezipaměť v souladu se specifikacemi [RFC 7234](https://tools.ietf.org/html/rfc7234) . Například `no-store` direktiva nesmí existovat v polích hlavičky Request nebo Response. Viz *část 3: Ukládání odpovědí do* mezipamětí [dokumentu RFC 7234](https://tools.ietf.org/html/rfc7234) pro podrobnosti.

> [!NOTE]
> Systém ochrany proti padělání pro generování zabezpečených tokenů, který brání útokům přes CSRF (mezi lokalitami) `Cache-Control` , `Pragma` nastaví záhlaví `no-cache` a, aby se odpovědi neukládaly do mezipaměti. Informace o tom, jak zakázat tokeny antipadělání pro prvky formuláře HTML, <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>naleznete v tématu.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

---
title: Ukládání do mezipaměti middlewaru v ASP.NET Core
author: rick-anderson
description: Naučte se konfigurovat a používat middleware pro ukládání odpovědí do mezipaměti v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/caching/middleware
ms.openlocfilehash: 4deac15538d4607bd611c4e072daae39447681c1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655734"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Ukládání do mezipaměti middlewaru v ASP.NET Core

Od [Jan Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

Tento článek vysvětluje, jak v aplikaci ASP.NET Core nakonfigurovat middleware pro ukládání odpovědí do mezipaměti. Middleware určuje, kdy je možné odpovědi ukládat do mezipaměti, ukládá odpovědi a obsluhuje odpovědi z mezipaměti. Úvod do mezipaměti protokolu HTTP a atributu [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) naleznete v tématu [ukládání odpovědí do mezipaměti](xref:performance/caching/response).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Konfigurace

Middleware pro ukládání odpovědí do mezipaměti je implicitně k dispozici pro ASP.NET Core aplikace přes sdílené rozhraní.

V `Startup.ConfigureServices`přidejte middleware pro ukládání do mezipaměti do kolekce služeb:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Nakonfigurujte aplikaci tak, aby používala middlewaru s metodou rozšíření <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>, která do kanálu pro zpracování požadavků přidá middleware v `Startup.Configure`:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

Ukázková aplikace přidá hlavičky pro řízení ukládání do mezipaměti pro následné žádosti:

* Mezipaměť [– řízení mezipaměti](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; ukládá do mezipaměti odpovědi po dobu až 10 sekund.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; nakonfiguruje middleware tak, aby poskytoval odpověď uloženou v mezipaměti pouze v případě, že hlavička [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) dalších požadavků odpovídá původní žádosti.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

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
* Uložte odpovědi podle cest s rozlišováním velkých a malých písmen. Například `/page1` a `/Page1` se ukládají samostatně.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Při použití řadičů MVC/webového rozhraní API nebo modelů Razor Pagesch stránek určuje atribut [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) parametry potřebné pro nastavení příslušných hlaviček pro ukládání odpovědí do mezipaměti. Jediným parametrem atributu `[ResponseCache]`, který striktně vyžaduje, aby byl middleware <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, což neodpovídá skutečné hlavičce HTTP. Další informace naleznete v tématu <xref:performance/caching/response#responsecache-attribute>.

Pokud nepoužíváte atribut `[ResponseCache]`, ukládání odpovědí do mezipaměti lze v `VaryByQueryKeys`měnit. Použijte <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> přímo z [vlastnosti HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Použití jedné hodnoty rovnající se `*` v `VaryByQueryKeys` změní mezipaměť na všechny parametry dotazů na požadavky.

## <a name="http-headers-used-by-response-caching-middleware"></a>Hlavičky HTTP používané middlewarem pro ukládání odpovědí do mezipaměti

Následující tabulka poskytuje informace o hlavičkách HTTP, které ovlivňují ukládání odpovědí do mezipaměti.

| Hlavička | Podrobnosti |
| ------ | ------- |
| `Authorization` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. |
| `Cache-Control` | Middleware považuje jenom odpovědi do mezipaměti označené direktivou `public` cache. Řízení ukládání do mezipaměti pomocí následujících parametrů:<ul><li>Maximální stáří</li><li>max-stale&#8224;</li><li>min – čerstvé</li><li>nutné – znovu ověřit</li><li>No – mezipaměť</li><li>bez uložení</li><li>pouze v mezipaměti</li><li>privátní</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;Pokud není zadán žádný limit pro `max-stale`, middleware neprovede žádnou akci.<br>&#8225;`proxy-revalidate` má stejný účinek jako `must-revalidate`.<br><br>Další informace najdete v [dokumentu RFC 7231: Request-Control direktives cache](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache` hlavička v žádosti vytvoří stejný účinek jako `Cache-Control: no-cache`. Tato hlavička je přepsána příslušnými direktivami v hlavičce `Cache-Control`, pokud je k dispozici. Brána se považuje za zpětnou kompatibilitu s HTTP/1.0. |
| `Set-Cookie` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. Jakýkoli middleware v kanálu zpracování požadavků, který nastavuje jeden nebo více souborů cookie, brání v ukládání do mezipaměti middleware ukládání odpovědi do mezipaměti (například [poskytovatele TempData založeného na souborech cookie](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | Hlavička `Vary` slouží k odlišení odpovědi uložené v mezipaměti jinou hlavičkou. Například odpovědi ukládat do mezipaměti podle kódování zahrnutím hlavičky `Vary: Accept-Encoding`, která ukládá do mezipaměti odpovědi pro žádosti s hlavičkou `Accept-Encoding: gzip` a `Accept-Encoding: text/plain` samostatně. Odpověď s hodnotou `*` záhlaví není nikdy uložena. |
| `Expires` | Odpověď považovaná za zastaralou touto hlavičkou není uložená nebo načtená, pokud není přepsána jinými hlavičkami `Cache-Control`. |
| `If-None-Match` | Úplná odpověď je obsluhována z mezipaměti, pokud hodnota není `*` a `ETag` odpovědi neodpovídá žádné z poskytnutých hodnot. V opačném případě je zpracována odpověď 304 (Neupraveno). |
| `If-Modified-Since` | Pokud hlavička `If-None-Match` není k dispozici, bude z mezipaměti poskytována úplná odpověď, pokud je datum odpovědi v mezipaměti novější než zadaná hodnota. Jinak se *neupraví odpověď 304* . |
| `Date` | Při obsluze z mezipaměti je hlavička `Date` nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Content-Length` | Při obsluze z mezipaměti je hlavička `Content-Length` nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Age` | Hlavička `Age` odeslána v původní odpovědi je ignorována. Middleware vypočítá novou hodnotu při obsluze odpovědi v mezipaměti. |

## <a name="caching-respects-request-cache-control-directives"></a>Požadavky na ukládání do mezipaměti – direktivy řízení mezipaměti

Middleware respektuje pravidla [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Pravidla vyžadují mezipaměť, aby bylo možné akceptovat platnou hlavičku `Cache-Control` odeslanou klientem. V rámci specifikace může klient provádět žádosti s hodnotou hlavičky `no-cache` a vynutit, aby server vygeneroval novou odpověď pro každý požadavek. V současné době není při používání middleware k tomuto chování při ukládání do mezipaměti k dispozici žádná vývojářská kontrola, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti.

Pro lepší kontrolu nad chováním při ukládání do mezipaměti můžete prozkoumat další funkce pro ukládání do mezipaměti ASP.NET Core. Přečtěte si následující témata:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Řešení potíží

Pokud chování při ukládání do mezipaměti není podle očekávání, zkontrolujte, že odpovědi jsou ukládat do mezipaměti a že je možné je obsluhovat z mezipaměti. Projděte si vstupní hlavičky žádosti a odchozí hlavičky odpovědi. Povolit [protokolování](xref:fundamentals/logging/index) pro usnadnění ladění.

Při testování a odstraňování potíží s chováním při ukládání do mezipaměti může prohlížeč nastavit hlavičky požadavků, které mají vliv na ukládání do mezipaměti nežádoucím způsobem. Prohlížeč může například nastavit hlavičku `Cache-Control` `no-cache` nebo `max-age=0` při aktualizaci stránky. Následující nástroje mohou explicitně nastavit hlavičky požadavků a jsou upřednostňovány pro testování ukládání do mezipaměti:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Podmínky pro ukládání do mezipaměti

* Požadavek musí mít za následek odpověď serveru se stavovým kódem 200 (OK).
* Metoda požadavku musí být GET nebo HEAD.
* V `Startup.Configure`musí být middleware pro ukládání odpovědí do mezipaměti umístěn před middlewarem, který vyžaduje ukládání do mezipaměti. Další informace naleznete v tématu <xref:fundamentals/middleware/index>.
* Záhlaví `Authorization` nesmí být k dispozici.
* parametry hlaviček `Cache-Control` musí být platné a odpověď musí být označena `public` a nesmí být označena `private`.
* Hlavička `Pragma: no-cache` nesmí být přítomna, pokud `Cache-Control` hlavička není k dispozici, protože hlavička `Cache-Control` Přepisuje `Pragma` hlavičku, pokud je k dispozici.
* Záhlaví `Set-Cookie` nesmí být k dispozici.
* parametry hlaviček `Vary` musí být platné a nesmí se rovnat `*`.
* Hodnota hlavičky `Content-Length` (Pokud je nastavena) musí odpovídat velikosti těla odpovědi.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> se nepoužívá.
* Odpověď nesmí být zastaralá tak, jak je určená hlavičkou `Expires` a direktivami `max-age` a `s-maxage` cache.
* Ukládání odpovědí do vyrovnávací paměti musí být úspěšné. Velikost odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>. Velikost textu odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.
* Odpověď musí být mezipaměť v souladu se specifikacemi [RFC 7234](https://tools.ietf.org/html/rfc7234) . Například Direktiva `no-store` nesmí existovat v polích hlavičky Request nebo Response. Podrobnosti najdete *v části 3: ukládání odpovědí v mezipamětech* [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Systém ochrany proti padělání pro generování zabezpečených tokenů, aby nedocházelo k útokům přes CSRF (mezi weby), nastaví `Cache-Control` a `Pragma` záhlaví na `no-cache`, takže odpovědi nejsou ukládány do mezipaměti. Informace o tom, jak zakázat tokeny antipadělání pro prvky formuláře HTML, naleznete v tématu <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek vysvětluje, jak v aplikaci ASP.NET Core nakonfigurovat middleware pro ukládání odpovědí do mezipaměti. Middleware určuje, kdy je možné odpovědi ukládat do mezipaměti, ukládá odpovědi a obsluhuje odpovědi z mezipaměti. Úvod do mezipaměti protokolu HTTP a atributu [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) naleznete v tématu [ukládání odpovědí do mezipaměti](xref:performance/caching/response).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Konfigurace

Použijte soubor [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

V `Startup.ConfigureServices`přidejte middleware pro ukládání do mezipaměti do kolekce služeb:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Nakonfigurujte aplikaci tak, aby používala middlewaru s metodou rozšíření <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>, která do kanálu pro zpracování požadavků přidá middleware v `Startup.Configure`:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Ukázková aplikace přidá hlavičky pro řízení ukládání do mezipaměti pro následné žádosti:

* Mezipaměť [– řízení mezipaměti](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; ukládá do mezipaměti odpovědi po dobu až 10 sekund.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; nakonfiguruje middleware tak, aby poskytoval odpověď uloženou v mezipaměti pouze v případě, že hlavička [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) dalších požadavků odpovídá původní žádosti.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

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
* Uložte odpovědi podle cest s rozlišováním velkých a malých písmen. Například `/page1` a `/Page1` se ukládají samostatně.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Při použití řadičů MVC/webového rozhraní API nebo modelů Razor Pagesch stránek určuje atribut [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) parametry potřebné pro nastavení příslušných hlaviček pro ukládání odpovědí do mezipaměti. Jediným parametrem atributu `[ResponseCache]`, který striktně vyžaduje, aby byl middleware <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, což neodpovídá skutečné hlavičce HTTP. Další informace naleznete v tématu <xref:performance/caching/response#responsecache-attribute>.

Pokud nepoužíváte atribut `[ResponseCache]`, ukládání odpovědí do mezipaměti lze v `VaryByQueryKeys`měnit. Použijte <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> přímo z [vlastnosti HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Použití jedné hodnoty rovnající se `*` v `VaryByQueryKeys` změní mezipaměť na všechny parametry dotazů na požadavky.

## <a name="http-headers-used-by-response-caching-middleware"></a>Hlavičky HTTP používané middlewarem pro ukládání odpovědí do mezipaměti

Následující tabulka poskytuje informace o hlavičkách HTTP, které ovlivňují ukládání odpovědí do mezipaměti.

| Hlavička | Podrobnosti |
| ------ | ------- |
| `Authorization` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. |
| `Cache-Control` | Middleware považuje jenom odpovědi do mezipaměti označené direktivou `public` cache. Řízení ukládání do mezipaměti pomocí následujících parametrů:<ul><li>Maximální stáří</li><li>max-stale&#8224;</li><li>min – čerstvé</li><li>nutné – znovu ověřit</li><li>No – mezipaměť</li><li>bez uložení</li><li>pouze v mezipaměti</li><li>privátní</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;Pokud není zadán žádný limit pro `max-stale`, middleware neprovede žádnou akci.<br>&#8225;`proxy-revalidate` má stejný účinek jako `must-revalidate`.<br><br>Další informace najdete v [dokumentu RFC 7231: Request-Control direktives cache](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache` hlavička v žádosti vytvoří stejný účinek jako `Cache-Control: no-cache`. Tato hlavička je přepsána příslušnými direktivami v hlavičce `Cache-Control`, pokud je k dispozici. Brána se považuje za zpětnou kompatibilitu s HTTP/1.0. |
| `Set-Cookie` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. Jakýkoli middleware v kanálu zpracování požadavků, který nastavuje jeden nebo více souborů cookie, brání v ukládání do mezipaměti middleware ukládání odpovědi do mezipaměti (například [poskytovatele TempData založeného na souborech cookie](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | Hlavička `Vary` slouží k odlišení odpovědi uložené v mezipaměti jinou hlavičkou. Například odpovědi ukládat do mezipaměti podle kódování zahrnutím hlavičky `Vary: Accept-Encoding`, která ukládá do mezipaměti odpovědi pro žádosti s hlavičkou `Accept-Encoding: gzip` a `Accept-Encoding: text/plain` samostatně. Odpověď s hodnotou `*` záhlaví není nikdy uložena. |
| `Expires` | Odpověď považovaná za zastaralou touto hlavičkou není uložená nebo načtená, pokud není přepsána jinými hlavičkami `Cache-Control`. |
| `If-None-Match` | Úplná odpověď je obsluhována z mezipaměti, pokud hodnota není `*` a `ETag` odpovědi neodpovídá žádné z poskytnutých hodnot. V opačném případě je zpracována odpověď 304 (Neupraveno). |
| `If-Modified-Since` | Pokud hlavička `If-None-Match` není k dispozici, bude z mezipaměti poskytována úplná odpověď, pokud je datum odpovědi v mezipaměti novější než zadaná hodnota. Jinak se *neupraví odpověď 304* . |
| `Date` | Při obsluze z mezipaměti je hlavička `Date` nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Content-Length` | Při obsluze z mezipaměti je hlavička `Content-Length` nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Age` | Hlavička `Age` odeslána v původní odpovědi je ignorována. Middleware vypočítá novou hodnotu při obsluze odpovědi v mezipaměti. |

## <a name="caching-respects-request-cache-control-directives"></a>Požadavky na ukládání do mezipaměti – direktivy řízení mezipaměti

Middleware respektuje pravidla [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Pravidla vyžadují mezipaměť, aby bylo možné akceptovat platnou hlavičku `Cache-Control` odeslanou klientem. V rámci specifikace může klient provádět žádosti s hodnotou hlavičky `no-cache` a vynutit, aby server vygeneroval novou odpověď pro každý požadavek. V současné době není při používání middleware k tomuto chování při ukládání do mezipaměti k dispozici žádná vývojářská kontrola, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti.

Pro lepší kontrolu nad chováním při ukládání do mezipaměti můžete prozkoumat další funkce pro ukládání do mezipaměti ASP.NET Core. Přečtěte si následující témata:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Řešení potíží

Pokud chování při ukládání do mezipaměti není podle očekávání, zkontrolujte, že odpovědi jsou ukládat do mezipaměti a že je možné je obsluhovat z mezipaměti. Projděte si vstupní hlavičky žádosti a odchozí hlavičky odpovědi. Povolit [protokolování](xref:fundamentals/logging/index) pro usnadnění ladění.

Při testování a odstraňování potíží s chováním při ukládání do mezipaměti může prohlížeč nastavit hlavičky požadavků, které mají vliv na ukládání do mezipaměti nežádoucím způsobem. Prohlížeč může například nastavit hlavičku `Cache-Control` `no-cache` nebo `max-age=0` při aktualizaci stránky. Následující nástroje mohou explicitně nastavit hlavičky požadavků a jsou upřednostňovány pro testování ukládání do mezipaměti:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Podmínky pro ukládání do mezipaměti

* Požadavek musí mít za následek odpověď serveru se stavovým kódem 200 (OK).
* Metoda požadavku musí být GET nebo HEAD.
* V `Startup.Configure`musí být middleware pro ukládání odpovědí do mezipaměti umístěn před middlewarem, který vyžaduje ukládání do mezipaměti. Další informace naleznete v tématu <xref:fundamentals/middleware/index>.
* Záhlaví `Authorization` nesmí být k dispozici.
* parametry hlaviček `Cache-Control` musí být platné a odpověď musí být označena `public` a nesmí být označena `private`.
* Hlavička `Pragma: no-cache` nesmí být přítomna, pokud `Cache-Control` hlavička není k dispozici, protože hlavička `Cache-Control` Přepisuje `Pragma` hlavičku, pokud je k dispozici.
* Záhlaví `Set-Cookie` nesmí být k dispozici.
* parametry hlaviček `Vary` musí být platné a nesmí se rovnat `*`.
* Hodnota hlavičky `Content-Length` (Pokud je nastavena) musí odpovídat velikosti těla odpovědi.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> se nepoužívá.
* Odpověď nesmí být zastaralá tak, jak je určená hlavičkou `Expires` a direktivami `max-age` a `s-maxage` cache.
* Ukládání odpovědí do vyrovnávací paměti musí být úspěšné. Velikost odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>. Velikost textu odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.
* Odpověď musí být mezipaměť v souladu se specifikacemi [RFC 7234](https://tools.ietf.org/html/rfc7234) . Například Direktiva `no-store` nesmí existovat v polích hlavičky Request nebo Response. Podrobnosti najdete *v části 3: ukládání odpovědí v mezipamětech* [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Systém ochrany proti padělání pro generování zabezpečených tokenů, aby nedocházelo k útokům přes CSRF (mezi weby), nastaví `Cache-Control` a `Pragma` záhlaví na `no-cache`, takže odpovědi nejsou ukládány do mezipaměti. Informace o tom, jak zakázat tokeny antipadělání pro prvky formuláře HTML, naleznete v tématu <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

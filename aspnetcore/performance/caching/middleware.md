---
title: Ukládání do mezipaměti middlewaru v ASP.NET Core
author: rick-anderson
description: Naučte se konfigurovat a používat middleware pro ukládání odpovědí do mezipaměti v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/middleware
ms.openlocfilehash: 7e1463671323cddd2b95c03de994d497449d7884
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019089"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Ukládání do mezipaměti middlewaru v ASP.NET Core

Od [Jan Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

Tento článek vysvětluje, jak v aplikaci ASP.NET Core nakonfigurovat middleware pro ukládání odpovědí do mezipaměti. Middleware určuje, kdy je možné odpovědi ukládat do mezipaměti, ukládá odpovědi a obsluhuje odpovědi z mezipaměti. Úvod do mezipaměti protokolu HTTP a [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atributu najdete v tématu [ukládání odpovědí do mezipaměti](xref:performance/caching/response).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Konfigurace

Middleware pro ukládání odpovědí do mezipaměti je implicitně k dispozici pro ASP.NET Core aplikace přes sdílené rozhraní.

Do `Startup.ConfigureServices` kolekce služby přidejte middleware pro ukládání odpovědí do mezipaměti:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Nakonfigurujte aplikaci tak, aby používala middlewaru s <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> metodou rozšíření, která přidá middleware do kanálu zpracování požadavků v `Startup.Configure` :

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A>musí být před <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> použitím [middleware CORS](xref:security/cors)volána.

Ukázková aplikace přidá hlavičky pro řízení ukládání do mezipaměti pro následné žádosti:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): ukládá do mezipaměti odpovědi po dobu až 10 sekund.
* [Lišící](https://tools.ietf.org/html/rfc7231#section-7.1.4)se: konfiguruje middleware tak, aby poskytoval odpověď uloženou v mezipaměti pouze v případě, že hlavička [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) dalších požadavků odpovídá původní žádosti.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Předchozí hlavičky nejsou zapsány do odpovědi a jsou přepsány, když je kontroler, akce nebo Razor stránka:

* Má atribut [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . To platí i v případě, že vlastnost není nastavena. Například vynecháním vlastnosti [VaryByHeader](/aspnet/core/performance/caching/response#vary) dojde k odebrání odpovídajícího záhlaví z odpovědi.

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
* Uložte odpovědi podle cest s rozlišováním velkých a malých písmen. Například `/page1` a `/Page1` jsou uloženy samostatně.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Pokud používáte modely stránek MVC/Web API nebo stránky Razor , [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atribut určuje parametry potřebné pro nastavení příslušných hlaviček pro ukládání odpovědí do mezipaměti. Jediný parametr `[ResponseCache]` atributu, který striktně vyžaduje middleware <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , který neodpovídá skutečné hlavičce HTTP. Další informace naleznete v tématu <xref:performance/caching/response#responsecache-attribute>.

Pokud atribut nepoužíváte `[ResponseCache]` , ukládání odpovědí do mezipaměti lze měnit pomocí `VaryByQueryKeys` . Použijte <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> přímo z [vlastnosti HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Použití jedné hodnoty, která se rovná, se `*` `VaryByQueryKeys` liší v mezipaměti všemi parametry dotazu Request.

## <a name="http-headers-used-by-response-caching-middleware"></a>Hlavičky HTTP používané middlewarem pro ukládání odpovědí do mezipaměti

Následující tabulka poskytuje informace o hlavičkách HTTP, které ovlivňují ukládání odpovědí do mezipaměti.

| Záhlaví | Podrobnosti |
| ------ | ------- |
| `Authorization` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. |
| `Cache-Control` | Middleware považuje jenom odpovědi do mezipaměti označené `public` direktivou cache. Řízení ukládání do mezipaměti pomocí následujících parametrů:<ul><li>Maximální stáří</li><li>Max – zastaralé&#8224;</li><li>min – čerstvé</li><li>nutné – znovu ověřit</li><li>No – mezipaměť</li><li>bez uložení</li><li>pouze v mezipaměti</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-opětovné ověření&#8225;</li></ul>&#8224;, pokud není zadán žádný limit `max-stale` , middleware neprovede žádnou akci.<br>&#8225;`proxy-revalidate` má stejný účinek jako `must-revalidate` .<br><br>Další informace najdete v [dokumentu RFC 7231: Request-Control direktives cache](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache`Hlavička v žádosti má stejný účinek jako `Cache-Control: no-cache` . Tato hlavička je přepsána příslušnými direktivami v `Cache-Control` hlavičce, pokud je k dispozici. Brána se považuje za zpětnou kompatibilitu s HTTP/1.0. |
| `Set-Cookie` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. Jakýkoliv middleware v kanálu zpracování požadavků, který nastaví jednu nebo více s, brání v ukládání do cookie mezipaměti middleware ukládání odpovědi do mezipaměti (například [ cookie poskytovatel TempData na základě](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary`Záhlaví se používá k odlišení odpovědi uložené v mezipaměti jinou hlavičkou. Například odpovědi ukládat do mezipaměti podle kódování zahrnutím `Vary: Accept-Encoding` hlavičky, která ukládá do mezipaměti odpovědi pro žádosti s hlavičkami `Accept-Encoding: gzip` a `Accept-Encoding: text/plain` samostatně. Odpověď s hodnotou záhlaví `*` se nikdy neukládá. |
| `Expires` | Odpověď považovaná za zastaralou touto hlavičkou není uložená nebo načtená, pokud není přepsána jinými `Cache-Control` záhlavími. |
| `If-None-Match` | Úplná odpověď se obsluhuje z mezipaměti, pokud hodnota není `*` a `ETag` Odpověď neodpovídá žádné z poskytnutých hodnot. V opačném případě je zpracována odpověď 304 (Neupraveno). |
| `If-Modified-Since` | Pokud `If-None-Match` Hlavička není k dispozici, bude z mezipaměti poskytována úplná odpověď, pokud je datum odpovědi v mezipaměti novější než zadaná hodnota. Jinak se *neupraví odpověď 304* . |
| `Date` | Při obsluze z mezipaměti `Date` je hlavička nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Content-Length` | Při obsluze z mezipaměti `Content-Length` je hlavička nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Age` | `Age`Hlavička poslaná v původní odpovědi je ignorována. Middleware vypočítá novou hodnotu při obsluze odpovědi v mezipaměti. |

## <a name="caching-respects-request-cache-control-directives"></a>Požadavky na ukládání do mezipaměti – direktivy řízení mezipaměti

Middleware respektuje pravidla [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Pravidla vyžadují mezipaměť, aby bylo možné akceptovat platnou `Cache-Control` hlavičku odeslanou klientem. V rámci specifikace může klient provádět žádosti s `no-cache` hodnotou hlavičky a vynutit, aby server vygeneroval novou odpověď pro každý požadavek. V současné době není při používání middleware k tomuto chování při ukládání do mezipaměti k dispozici žádná vývojářská kontrola, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti.

Pro lepší kontrolu nad chováním při ukládání do mezipaměti můžete prozkoumat další funkce pro ukládání do mezipaměti ASP.NET Core. Přečtěte si následující témata:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Řešení potíží

Pokud chování při ukládání do mezipaměti není podle očekávání, zkontrolujte, že odpovědi jsou ukládat do mezipaměti a že je možné je obsluhovat z mezipaměti. Projděte si vstupní hlavičky žádosti a odchozí hlavičky odpovědi. Povolit [protokolování](xref:fundamentals/logging/index) pro usnadnění ladění.

Při testování a odstraňování potíží s chováním při ukládání do mezipaměti může prohlížeč nastavit hlavičky požadavků, které mají vliv na ukládání do mezipaměti nežádoucím způsobem. Prohlížeč může například nastavit `Cache-Control` hlavičku na `no-cache` nebo `max-age=0` při aktualizaci stránky. Následující nástroje mohou explicitně nastavit hlavičky požadavků a jsou upřednostňovány pro testování ukládání do mezipaměti:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Podmínky pro ukládání do mezipaměti

* Požadavek musí mít za následek odpověď serveru se stavovým kódem 200 (OK).
* Metoda požadavku musí být GET nebo HEAD.
* V nástroji `Startup.Configure` musí být middleware pro ukládání odpovědí do mezipaměti umístěn před middlewarem, který vyžaduje ukládání do mezipaměti. Další informace naleznete v tématu <xref:fundamentals/middleware/index>.
* `Authorization`Hlavička nesmí být k dispozici.
* `Cache-Control`parametry hlaviček musí být platné a odpověď musí být označená `public` a nesmí být označená `private` .
* `Pragma: no-cache`Hlavička nesmí být k dispozici, pokud `Cache-Control` Hlavička není k dispozici, protože záhlaví `Cache-Control` potlačí hlavičku, pokud je k `Pragma` dispozici.
* `Set-Cookie`Hlavička nesmí být k dispozici.
* `Vary`parametry záhlaví musí být platné a nesmí být rovny `*` .
* `Content-Length`Hodnota hlavičky (Pokud je nastavena) musí odpovídat velikosti těla odpovědi.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>Není použit.
* Odpověď nesmí být zastaralá tak, jak je určena `Expires` hlavičkou `max-age` a `s-maxage` direktivami cache a.
* Ukládání odpovědí do vyrovnávací paměti musí být úspěšné. Velikost odpovědi musí být menší než nakonfigurovaná nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Velikost textu odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Odpověď musí být mezipaměť v souladu se specifikacemi [RFC 7234](https://tools.ietf.org/html/rfc7234) . Například `no-store` direktiva nesmí existovat v polích hlavičky Request nebo Response. Podrobnosti najdete *v části 3: ukládání odpovědí v mezipamětech* [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Systém ochrany proti padělání pro generování zabezpečených tokenů, který brání útokům přes CSRF (mezi lokalitami) `Cache-Control` , nastaví záhlaví a, aby se `Pragma` `no-cache` odpovědi neukládaly do mezipaměti. Informace o tom, jak zakázat tokeny antipadělání pro prvky formuláře HTML, naleznete v tématu <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

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

Tento článek vysvětluje, jak v aplikaci ASP.NET Core nakonfigurovat middleware pro ukládání odpovědí do mezipaměti. Middleware určuje, kdy je možné odpovědi ukládat do mezipaměti, ukládá odpovědi a obsluhuje odpovědi z mezipaměti. Úvod do mezipaměti protokolu HTTP a [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atributu najdete v tématu [ukládání odpovědí do mezipaměti](xref:performance/caching/response).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Konfigurace

Použijte soubor [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

Do `Startup.ConfigureServices` kolekce služby přidejte middleware pro ukládání odpovědí do mezipaměti:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Nakonfigurujte aplikaci tak, aby používala middlewaru s <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> metodou rozšíření, která přidá middleware do kanálu zpracování požadavků v `Startup.Configure` :

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Ukázková aplikace přidá hlavičky pro řízení ukládání do mezipaměti pro následné žádosti:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): ukládá do mezipaměti odpovědi po dobu až 10 sekund.
* [Lišící](https://tools.ietf.org/html/rfc7231#section-7.1.4)se: konfiguruje middleware tak, aby poskytoval odpověď uloženou v mezipaměti pouze v případě, že hlavička [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) dalších požadavků odpovídá původní žádosti.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Předchozí hlavičky nejsou zapsány do odpovědi a jsou přepsány, když je kontroler, akce nebo Razor stránka:

* Má atribut [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . To platí i v případě, že vlastnost není nastavena. Například vynecháním vlastnosti [VaryByHeader](/aspnet/core/performance/caching/response#vary) dojde k odebrání odpovídajícího záhlaví z odpovědi.

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
* Uložte odpovědi podle cest s rozlišováním velkých a malých písmen. Například `/page1` a `/Page1` jsou uloženy samostatně.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Pokud používáte modely stránek MVC/Web API nebo stránky Razor , [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atribut určuje parametry potřebné pro nastavení příslušných hlaviček pro ukládání odpovědí do mezipaměti. Jediný parametr `[ResponseCache]` atributu, který striktně vyžaduje middleware <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , který neodpovídá skutečné hlavičce HTTP. Další informace naleznete v tématu <xref:performance/caching/response#responsecache-attribute>.

Pokud atribut nepoužíváte `[ResponseCache]` , ukládání odpovědí do mezipaměti lze měnit pomocí `VaryByQueryKeys` . Použijte <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> přímo z [vlastnosti HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Použití jedné hodnoty, která se rovná, se `*` `VaryByQueryKeys` liší v mezipaměti všemi parametry dotazu Request.

## <a name="http-headers-used-by-response-caching-middleware"></a>Hlavičky HTTP používané middlewarem pro ukládání odpovědí do mezipaměti

Následující tabulka poskytuje informace o hlavičkách HTTP, které ovlivňují ukládání odpovědí do mezipaměti.

| Záhlaví | Podrobnosti |
| ------ | ------- |
| `Authorization` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. |
| `Cache-Control` | Middleware považuje jenom odpovědi do mezipaměti označené `public` direktivou cache. Řízení ukládání do mezipaměti pomocí následujících parametrů:<ul><li>Maximální stáří</li><li>Max – zastaralé&#8224;</li><li>min – čerstvé</li><li>nutné – znovu ověřit</li><li>No – mezipaměť</li><li>bez uložení</li><li>pouze v mezipaměti</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-opětovné ověření&#8225;</li></ul>&#8224;, pokud není zadán žádný limit `max-stale` , middleware neprovede žádnou akci.<br>&#8225;`proxy-revalidate` má stejný účinek jako `must-revalidate` .<br><br>Další informace najdete v [dokumentu RFC 7231: Request-Control direktives cache](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache`Hlavička v žádosti má stejný účinek jako `Cache-Control: no-cache` . Tato hlavička je přepsána příslušnými direktivami v `Cache-Control` hlavičce, pokud je k dispozici. Brána se považuje za zpětnou kompatibilitu s HTTP/1.0. |
| `Set-Cookie` | Odpověď není uložena do mezipaměti, pokud hlavička existuje. Jakýkoliv middleware v kanálu zpracování požadavků, který nastaví jednu nebo více s, brání v ukládání do cookie mezipaměti middleware ukládání odpovědi do mezipaměti (například [ cookie poskytovatel TempData na základě](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary`Záhlaví se používá k odlišení odpovědi uložené v mezipaměti jinou hlavičkou. Například odpovědi ukládat do mezipaměti podle kódování zahrnutím `Vary: Accept-Encoding` hlavičky, která ukládá do mezipaměti odpovědi pro žádosti s hlavičkami `Accept-Encoding: gzip` a `Accept-Encoding: text/plain` samostatně. Odpověď s hodnotou záhlaví `*` se nikdy neukládá. |
| `Expires` | Odpověď považovaná za zastaralou touto hlavičkou není uložená nebo načtená, pokud není přepsána jinými `Cache-Control` záhlavími. |
| `If-None-Match` | Úplná odpověď se obsluhuje z mezipaměti, pokud hodnota není `*` a `ETag` Odpověď neodpovídá žádné z poskytnutých hodnot. V opačném případě je zpracována odpověď 304 (Neupraveno). |
| `If-Modified-Since` | Pokud `If-None-Match` Hlavička není k dispozici, bude z mezipaměti poskytována úplná odpověď, pokud je datum odpovědi v mezipaměti novější než zadaná hodnota. Jinak se *neupraví odpověď 304* . |
| `Date` | Při obsluze z mezipaměti `Date` je hlavička nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Content-Length` | Při obsluze z mezipaměti `Content-Length` je hlavička nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď. |
| `Age` | `Age`Hlavička poslaná v původní odpovědi je ignorována. Middleware vypočítá novou hodnotu při obsluze odpovědi v mezipaměti. |

## <a name="caching-respects-request-cache-control-directives"></a>Požadavky na ukládání do mezipaměti – direktivy řízení mezipaměti

Middleware respektuje pravidla [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Pravidla vyžadují mezipaměť, aby bylo možné akceptovat platnou `Cache-Control` hlavičku odeslanou klientem. V rámci specifikace může klient provádět žádosti s `no-cache` hodnotou hlavičky a vynutit, aby server vygeneroval novou odpověď pro každý požadavek. V současné době není při používání middleware k tomuto chování při ukládání do mezipaměti k dispozici žádná vývojářská kontrola, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti.

Pro lepší kontrolu nad chováním při ukládání do mezipaměti můžete prozkoumat další funkce pro ukládání do mezipaměti ASP.NET Core. Přečtěte si následující témata:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Řešení potíží

Pokud chování při ukládání do mezipaměti není podle očekávání, zkontrolujte, že odpovědi jsou ukládat do mezipaměti a že je možné je obsluhovat z mezipaměti. Projděte si vstupní hlavičky žádosti a odchozí hlavičky odpovědi. Povolit [protokolování](xref:fundamentals/logging/index) pro usnadnění ladění.

Při testování a odstraňování potíží s chováním při ukládání do mezipaměti může prohlížeč nastavit hlavičky požadavků, které mají vliv na ukládání do mezipaměti nežádoucím způsobem. Prohlížeč může například nastavit `Cache-Control` hlavičku na `no-cache` nebo `max-age=0` při aktualizaci stránky. Následující nástroje mohou explicitně nastavit hlavičky požadavků a jsou upřednostňovány pro testování ukládání do mezipaměti:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Podmínky pro ukládání do mezipaměti

* Požadavek musí mít za následek odpověď serveru se stavovým kódem 200 (OK).
* Metoda požadavku musí být GET nebo HEAD.
* V nástroji `Startup.Configure` musí být middleware pro ukládání odpovědí do mezipaměti umístěn před middlewarem, který vyžaduje ukládání do mezipaměti. Další informace naleznete v tématu <xref:fundamentals/middleware/index>.
* `Authorization`Hlavička nesmí být k dispozici.
* `Cache-Control`parametry hlaviček musí být platné a odpověď musí být označená `public` a nesmí být označená `private` .
* `Pragma: no-cache`Hlavička nesmí být k dispozici, pokud `Cache-Control` Hlavička není k dispozici, protože záhlaví `Cache-Control` potlačí hlavičku, pokud je k `Pragma` dispozici.
* `Set-Cookie`Hlavička nesmí být k dispozici.
* `Vary`parametry záhlaví musí být platné a nesmí být rovny `*` .
* `Content-Length`Hodnota hlavičky (Pokud je nastavena) musí odpovídat velikosti těla odpovědi.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>Není použit.
* Odpověď nesmí být zastaralá tak, jak je určena `Expires` hlavičkou `max-age` a `s-maxage` direktivami cache a.
* Ukládání odpovědí do vyrovnávací paměti musí být úspěšné. Velikost odpovědi musí být menší než nakonfigurovaná nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Velikost textu odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Odpověď musí být mezipaměť v souladu se specifikacemi [RFC 7234](https://tools.ietf.org/html/rfc7234) . Například `no-store` direktiva nesmí existovat v polích hlavičky Request nebo Response. Podrobnosti najdete *v části 3: ukládání odpovědí v mezipamětech* [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Systém ochrany proti padělání pro generování zabezpečených tokenů, který brání útokům přes CSRF (mezi lokalitami) `Cache-Control` , nastaví záhlaví a, aby se `Pragma` `no-cache` odpovědi neukládaly do mezipaměti. Informace o tom, jak zakázat tokeny antipadělání pro prvky formuláře HTML, naleznete v tématu <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

---
title: Odpověď do mezipaměti middlewaru v ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat a používat Middleware pro ukládání do mezipaměti odpovědi v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: performance/caching/middleware
ms.openlocfilehash: d6756ce16396133da643cc08ca0f48369479ad3a
ms.sourcegitcommit: b9e914ef274b5ec359582f299724af6234dce135
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596156"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Odpověď do mezipaměti middlewaru v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex) a [Luo Jan](https://github.com/JunTaoLuo)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([stažení](xref:index#how-to-download-a-sample))

Tento článek vysvětluje, jak pro konfiguraci middlewaru ukládání do mezipaměti odpovědi v aplikaci ASP.NET Core. Middleware určuje při odpovědi jsou možné ukládat do mezipaměti odpovědi úložišť a slouží odpovědi z mezipaměti. Úvod do protokolu HTTP, ukládání do mezipaměti a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atributu naleznete v tématu [ukládání odpovědí do mezipaměti](xref:performance/caching/response).

## <a name="configuration"></a>Konfiguraci

Použití [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) balíčku.

V `Startup.ConfigureServices`, přidejte Middleware pro ukládání odpovědí do kolekce služby:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Konfigurace aplikace pro použití middlewaru s <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> rozšiřující metoda, která přidá middleware v kanálu zpracování požadavku `Startup.Configure`:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Ukázkovou aplikaci přidá také hlavičky pro ovládací prvek ukládání do mezipaměti na následné žádosti:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; ukládá do mezipaměti možné ukládat do mezipaměti odpovědi až na 10 sekund.
* [Se liší](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; nakonfiguruje middlewaru, který má sloužit pouze pokud odpověď uložená v mezipaměti [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4) záhlaví následné žádosti se shoduje s původní požadavek.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Middleware pro ukládání do mezipaměti odpovědí pouze ukládá do mezipaměti odpovědi serveru, jejichž výsledkem je stavový kód 200 (OK). Žádné další odpovědi, včetně [chybové stránky](xref:fundamentals/error-handling), jsou ignorovány middlewarem.

> [!WARNING]
> Odpovědi s obsahem pro ověření klienti musí být označen jako není možné ukládat do mezipaměti zabránit middleware z ukládání a současné obsluhování těchto odpovědí. Zobrazit [podmínky pro ukládání do mezipaměti](#conditions-for-caching) podrobnosti o tom, jak middleware Určuje, zda je možné ukládat do mezipaměti odpovědi.

## <a name="options"></a>Možnosti

V následující tabulce jsou uvedeny možnosti ukládání do mezipaměti odpovědi.

| Možnost | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Největší možné ukládat do mezipaměti velikost datové části odpovědi v bajtech. Výchozí hodnota je `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Omezení velikosti pro middleware mezipaměti odpovědi v bajtech. Výchozí hodnota je `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Určuje, pokud jsou odpovědi ukládat do mezipaměti na malá a velká písmena cesty. Výchozí hodnota je `false`. |

Následující příklad nastaví middlewaru, který má být:

* Reakce mezipaměti s velikostí textu menší než nebo rovna 1024 bajtů.
* Store odpovědi pomocí cest malá a velká písmena. Například `/page1` a `/Page1` jsou uloženy odděleně.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Při použití MVC / webové rozhraní API kontrolerech a modelech stránky Razor Pages, [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) atribut určuje parametry, které jsou nezbytné k nastavení hlavičky vhodná pro ukládání odpovědí do mezipaměti. Parametr pouze `[ResponseCache]` je atribut, který přísně vyžaduje middleware <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, což neodpovídá skutečné hlavičky protokolu HTTP. Další informace naleznete v tématu <xref:performance/caching/response#responsecache-attribute>.

Pokud nepoužíváte `[ResponseCache]` atribut, ukládání odpovědí do mezipaměti může lišit s `VaryByQueryKeys`. Použití <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> přímo [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Pomocí jednoho hodnota se shoduje s `*` v `VaryByQueryKeys` mezipaměti se liší podle všech parametrech dotazu žádosti.

## <a name="http-headers-used-by-response-caching-middleware"></a>Hlaviček HTTP používané oborem Middleware pro ukládání do vyrovnávací paměti odpovědí

Následující tabulka obsahuje informace o hlavičky HTTP, které ovlivňují ukládání do mezipaměti odpovědi.

| Záhlaví | Podrobnosti |
| ------ | ------- |
| `Authorization` | Odpověď není v mezipaměti, pokud existuje záhlaví. |
| `Cache-Control` | Middleware uvažuje pouze ukládání do mezipaměti odpovědi označené `public` – direktiva cache. Řízení ukládání do mezipaměti s následujícími parametry:<ul><li>Maximální stáří</li><li>max-stale&#8224;</li><li>min – nové</li><li>musí revalidate</li><li>no-cache</li><li>no-store</li><li>only-if-cached</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;Pokud není zadáno žádné omezení na `max-stale`, middleware neprovede žádnou akci.<br>&#8225;`proxy-revalidate`má stejný účinek jako `must-revalidate`.<br><br>Další informace najdete v tématu [RFC 7231: Požádat o direktivy Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | A `Pragma: no-cache` záhlaví v žádosti o vytváří stejný účinek jako `Cache-Control: no-cache`. Tato hlavička přepsán směrnic v `Cache-Control` záhlaví, pokud jsou k dispozici. Považovat za kvůli zpětné kompatibilitě se verze HTTP 1.0. |
| `Set-Cookie` | Odpověď není v mezipaměti, pokud existuje záhlaví. Veškerý middleware v kanálu zpracování požadavků, který nastaví jeden nebo více souborů cookie brání Middleware pro ukládání odpovědí do mezipaměti odpovědi (třeba [založené na souborech cookie poskytovatele TempData](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary` Záhlaví se používá k odpověď uložená v mezipaměti se liší podle jiné záhlaví. Například pomocí kódování zahrnutím do mezipaměti odpovědi `Vary: Accept-Encoding` hlavičky, která ukládá do mezipaměti odpovědi pro požadavky s záhlaví `Accept-Encoding: gzip` a `Accept-Encoding: text/plain` samostatně. Odpověď s hodnotou hlavičky `*` se nikdy neukládají. |
| `Expires` | Odpověď této hlavičky považují za zastaralé není uložení nebo načtení, pokud nejsou přepsány jiná `Cache-Control` záhlaví. |
| `If-None-Match` | Úplné odpovědi se načítají z mezipaměti, pokud hodnota není `*` a `ETag` odpovědi neodpovídá žádné z zadanými hodnotami. V opačném případě je zpracovat v odpovědi 304 (Neupraveno). |
| `If-Modified-Since` | Pokud `If-None-Match` záhlaví není k dispozici, úplnou odpověď se načítají z mezipaměti, pokud je novější než hodnota zadaná data odpověď uložená v mezipaměti. V opačném případě *304 - nedojde ke změně* zpracování odpovědi. |
| `Date` | Při vykonávání z mezipaměti, `Date` není nastavena hlavička middleware, pokud nebyl zadán v původní odpovědi. |
| `Content-Length` | Při vykonávání z mezipaměti, `Content-Length` není nastavena hlavička middleware, pokud nebyl zadán v původní odpovědi. |
| `Age` | `Age` Záhlaví odeslaný v původní odpovědi se ignoruje. Middleware vypočítá novou hodnotu při vykonávání odpověď uložená v mezipaměti. |

## <a name="caching-respects-request-cache-control-directives"></a>Ukládání do mezipaměti respektuje direktivy Cache-Control žádosti

Middleware respektuje pravidla [specifikace HTTP 1.1 ukládání do mezipaměti](https://tools.ietf.org/html/rfc7234#section-5.2). Pravidla, která vyžadují mezipaměti případném dalším sdílení dodržovat platné `Cache-Control` záhlaví odesílaném klientem. Podle specifikace, klient mohou vytvářet požadavky s `no-cache` hodnota hlavičky a vynutit server vygenerovat novou odpověď pro každý požadavek. V současné době neexistuje žádný vývojáři řídit tohoto chování ukládání do mezipaměti při použití middleware, protože middleware dodržuje specifikaci oficiální ukládání do mezipaměti.

Pro větší kontrolu nad chování ukládání do mezipaměti prozkoumejte další funkce ukládání do mezipaměti ASP.NET Core. V následujících tématech:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Poradce při potížích

Pokud chování ukládání do mezipaměti není podle očekávání, ověřte, zda jsou odpovědi možné ukládat do mezipaměti a je schopný obsluhovány z mezipaměti. Prozkoumejte příchozí hlavičky požadavku a odpovědi na odchozí. Povolit [protokolování](xref:fundamentals/logging/index) k pomoci s laděním.

Při testování a řešení potíží s chování ukládání do mezipaměti, může prohlížeč nastavit hlavičky žádosti, které ovlivňují ukládání do mezipaměti nežádoucí způsoby. Například může nastavit prohlížeče `Cache-Control` záhlaví `no-cache` nebo `max-age=0` při aktualizaci stránky. Tyto nástroje můžete explicitně nastavit hlavičky požadavku a jsou upřednostněny testování ukládání do mezipaměti:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Podmínky pro ukládání do mezipaměti

* Výsledkem požadavku musí být server odpověď se stavovým kódem 200 (OK).
* Metoda žádosti musí být GET a HEAD.
* V `Startup.Configure`, Middleware pro ukládání do mezipaměti odpovědí musí být umístěna před middleware, které vyžadují ukládání do mezipaměti. Další informace naleznete v tématu <xref:fundamentals/middleware/index>.
* `Authorization` Hlavičky nesmí být k dispozici.
* `Cache-Control` parametry záhlaví musí být platná a musí být označena odpověď `public` a není označena jako `private`.
* `Pragma: no-cache` Hlavičky nesmí být k dispozici Pokud `Cache-Control` záhlaví není k dispozici, jako `Cache-Control` přepíše záhlaví `Pragma` záhlaví, pokud je k dispozici.
* `Set-Cookie` Hlavičky nesmí být k dispozici.
* `Vary` parametry záhlaví musí být platná a není rovno `*`.
* `Content-Length` Hodnota hlavičky (-li nastavit) musí odpovídat velikosti datové části odpovědi.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> Se nepoužívá.
* Odpověď nesmí být zastaralá, jak jsou určené `Expires` záhlaví a `max-age` a `s-maxage` mezipaměti direktivy.
* Ukládání odpovědí do vyrovnávací paměti musí být úspěšná. Velikost odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>. Musí být menší než nakonfigurované nebo výchozí velikost těla odpovědi <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.
* Odpovědi musí být možné ukládat do mezipaměti podle [RFC 7234](https://tools.ietf.org/html/rfc7234) specifikace. Například `no-store` – direktiva nesmí existovat v pole hlavičky požadavku nebo odpovědi. Zobrazit *část 3: Ukládání odpovědí do mezipaměti* z [RFC 7234](https://tools.ietf.org/html/rfc7234) podrobnosti.

> [!NOTE]
> Antiforgery systému generuje zabezpečené tokeny zabránit padělání žádosti mezi weby (CSRF) attacks sad `Cache-Control` a `Pragma` záhlaví `no-cache` tak, aby se neukládají do mezipaměti odpovědi. Informace o tom, jak zakázat antiforgery tokeny pro prvků formuláře HTML najdete v tématu <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

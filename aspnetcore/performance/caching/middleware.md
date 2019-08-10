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
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="818fd-103">Ukládání do mezipaměti middlewaru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="818fd-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="818fd-104">Od [Luke Latham](https://github.com/guardrex) a [Jan Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="818fd-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="818fd-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="818fd-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="818fd-106">Tento článek vysvětluje, jak v aplikaci ASP.NET Core nakonfigurovat middleware pro ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="818fd-107">Middleware určuje, kdy je možné odpovědi ukládat do mezipaměti, ukládá odpovědi a obsluhuje odpovědi z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="818fd-108">Úvod do mezipaměti HTTP a atributu [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) najdete v tématu [ukládání odpovědí do mezipaměti](xref:performance/caching/response).</span><span class="sxs-lookup"><span data-stu-id="818fd-108">For an introduction to HTTP caching and the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="configuration"></a><span data-ttu-id="818fd-109">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="818fd-109">Configuration</span></span>

<span data-ttu-id="818fd-110">Použijte soubor [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .</span><span class="sxs-lookup"><span data-stu-id="818fd-110">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="818fd-111">`Startup.ConfigureServices`Do kolekce služby přidejte middleware pro ukládání odpovědí do mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="818fd-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

::: moniker-end

<span data-ttu-id="818fd-112">Nakonfigurujte aplikaci tak, aby používala middlewaru <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> s metodou rozšíření, která přidá middleware do kanálu zpracování požadavků v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="818fd-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

::: moniker-end

<span data-ttu-id="818fd-113">Ukázková aplikace přidá hlavičky pro řízení ukládání do mezipaměti pro následné žádosti:</span><span class="sxs-lookup"><span data-stu-id="818fd-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="818fd-114">[Řízení mezipaměti](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Ukládá do mezipaměti odpovědi po dobu až 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="818fd-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="818fd-115">[Různé](https://tools.ietf.org/html/rfc7231#section-7.1.4) Nakonfiguruje middleware tak, aby poskytoval odpověď uloženou [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) v mezipaměti pouze v případě, že hlavička následujících požadavků odpovídá původní žádosti. &ndash;</span><span class="sxs-lookup"><span data-stu-id="818fd-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

::: moniker-end

<span data-ttu-id="818fd-116">Middleware pro ukládání odpovědí do mezipaměti ukládá pouze odpovědi serveru, jejichž výsledkem je stavový kód 200 (OK).</span><span class="sxs-lookup"><span data-stu-id="818fd-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="818fd-117">Všechny ostatní odpovědi, včetně [chybových stránek](xref:fundamentals/error-handling), se middleware ignorují.</span><span class="sxs-lookup"><span data-stu-id="818fd-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="818fd-118">Odpovědi obsahující obsah pro ověřené klienty musí být označené jako neukládatelné do mezipaměti, aby middleware nemohli ukládat a obsluhovat tyto odpovědi.</span><span class="sxs-lookup"><span data-stu-id="818fd-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="818fd-119">Podrobnosti o tom, jak middleware určuje, jestli je odpověď mezipaměť, najdete v tématu [podmínky pro ukládání do mezipaměti](#conditions-for-caching) .</span><span class="sxs-lookup"><span data-stu-id="818fd-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="818fd-120">Možnosti</span><span class="sxs-lookup"><span data-stu-id="818fd-120">Options</span></span>

<span data-ttu-id="818fd-121">Možnosti ukládání odpovědi do mezipaměti jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="818fd-121">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="818fd-122">Možnost</span><span class="sxs-lookup"><span data-stu-id="818fd-122">Option</span></span> | <span data-ttu-id="818fd-123">Popis</span><span class="sxs-lookup"><span data-stu-id="818fd-123">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="818fd-124">Největší velikost paměti pro tělo odpovědi v bajtech.</span><span class="sxs-lookup"><span data-stu-id="818fd-124">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="818fd-125">Výchozí hodnota je `64 * 1024 * 1024` (64 MB).</span><span class="sxs-lookup"><span data-stu-id="818fd-125">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="818fd-126">Omezení velikosti pro middleware mezipaměti odpovědí v bajtech.</span><span class="sxs-lookup"><span data-stu-id="818fd-126">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="818fd-127">Výchozí hodnota je `100 * 1024 * 1024` (100 MB).</span><span class="sxs-lookup"><span data-stu-id="818fd-127">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="818fd-128">Určuje, zda jsou odpovědi uloženy v mezipaměti pro cesty citlivé na velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="818fd-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="818fd-129">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="818fd-129">The default value is `false`.</span></span> |

<span data-ttu-id="818fd-130">Následující příklad nakonfiguruje middleware na:</span><span class="sxs-lookup"><span data-stu-id="818fd-130">The following example configures the middleware to:</span></span>

* <span data-ttu-id="818fd-131">Odpovědi v mezipaměti s velikostí těla menší nebo rovnou 1 024 bajtů.</span><span class="sxs-lookup"><span data-stu-id="818fd-131">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="818fd-132">Uložte odpovědi podle cest s rozlišováním velkých a malých písmen.</span><span class="sxs-lookup"><span data-stu-id="818fd-132">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="818fd-133">Například `/page1` a`/Page1` jsou uloženy samostatně.</span><span class="sxs-lookup"><span data-stu-id="818fd-133">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="818fd-134">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="818fd-134">VaryByQueryKeys</span></span>

<span data-ttu-id="818fd-135">Při použití řadičů MVC/webového rozhraní API nebo modelů Razor Pagesch stránek určuje atribut [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) parametry potřebné pro nastavení příslušných hlaviček pro ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-135">When using MVC / web API controllers or Razor Pages page models, the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="818fd-136">Jediný parametr `[ResponseCache]` atributu, který striktně vyžaduje <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>middleware, který neodpovídá skutečné hlavičce HTTP.</span><span class="sxs-lookup"><span data-stu-id="818fd-136">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="818fd-137">Další informace naleznete v tématu <xref:performance/caching/response#responsecache-attribute>.</span><span class="sxs-lookup"><span data-stu-id="818fd-137">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="818fd-138">Pokud `[ResponseCache]` atribut nepoužíváte, ukládání odpovědí do mezipaměti lze měnit `VaryByQueryKeys`pomocí.</span><span class="sxs-lookup"><span data-stu-id="818fd-138">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="818fd-139">Použijte přímo z [vlastnosti HttpContext. Features:](xref:Microsoft.AspNetCore.Http.HttpContext.Features) <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature></span><span class="sxs-lookup"><span data-stu-id="818fd-139">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="818fd-140">Použití jedné hodnoty, která `*` se rovná, se liší v `VaryByQueryKeys` mezipaměti všemi parametry dotazu Request.</span><span class="sxs-lookup"><span data-stu-id="818fd-140">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="818fd-141">Hlavičky HTTP používané middlewarem pro ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="818fd-141">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="818fd-142">Následující tabulka poskytuje informace o hlavičkách HTTP, které ovlivňují ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-142">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="818fd-143">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="818fd-143">Header</span></span> | <span data-ttu-id="818fd-144">Podrobnosti</span><span class="sxs-lookup"><span data-stu-id="818fd-144">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="818fd-145">Odpověď není uložena do mezipaměti, pokud hlavička existuje.</span><span class="sxs-lookup"><span data-stu-id="818fd-145">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="818fd-146">Middleware považuje jenom odpovědi do mezipaměti označené `public` direktivou cache.</span><span class="sxs-lookup"><span data-stu-id="818fd-146">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="818fd-147">Řízení ukládání do mezipaměti pomocí následujících parametrů:</span><span class="sxs-lookup"><span data-stu-id="818fd-147">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="818fd-148">Maximální stáří</span><span class="sxs-lookup"><span data-stu-id="818fd-148">max-age</span></span></li><li><span data-ttu-id="818fd-149">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="818fd-149">max-stale&#8224;</span></span></li><li><span data-ttu-id="818fd-150">min – čerstvé</span><span class="sxs-lookup"><span data-stu-id="818fd-150">min-fresh</span></span></li><li><span data-ttu-id="818fd-151">nutné – znovu ověřit</span><span class="sxs-lookup"><span data-stu-id="818fd-151">must-revalidate</span></span></li><li><span data-ttu-id="818fd-152">No – mezipaměť</span><span class="sxs-lookup"><span data-stu-id="818fd-152">no-cache</span></span></li><li><span data-ttu-id="818fd-153">bez uložení</span><span class="sxs-lookup"><span data-stu-id="818fd-153">no-store</span></span></li><li><span data-ttu-id="818fd-154">pouze v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="818fd-154">only-if-cached</span></span></li><li><span data-ttu-id="818fd-155">private</span><span class="sxs-lookup"><span data-stu-id="818fd-155">private</span></span></li><li><span data-ttu-id="818fd-156">public</span><span class="sxs-lookup"><span data-stu-id="818fd-156">public</span></span></li><li><span data-ttu-id="818fd-157">s-maxage</span><span class="sxs-lookup"><span data-stu-id="818fd-157">s-maxage</span></span></li><li><span data-ttu-id="818fd-158">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="818fd-158">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="818fd-159">&#8224;Pokud není zadán `max-stale`žádný limit, middleware neprovede žádnou akci.</span><span class="sxs-lookup"><span data-stu-id="818fd-159">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="818fd-160">&#8225;`proxy-revalidate`má stejný účinek jako `must-revalidate`.</span><span class="sxs-lookup"><span data-stu-id="818fd-160">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="818fd-161">Další informace najdete v [dokumentu RFC 7231: Vyžádat direktivy](https://tools.ietf.org/html/rfc7234#section-5.2.1)řízení mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-161">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="818fd-162">Hlavička v žádosti má stejný účinek jako `Cache-Control: no-cache`. `Pragma: no-cache`</span><span class="sxs-lookup"><span data-stu-id="818fd-162">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="818fd-163">Tato hlavička je přepsána příslušnými direktivami v `Cache-Control` hlavičce, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="818fd-163">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="818fd-164">Brána se považuje za zpětnou kompatibilitu s HTTP/1.0.</span><span class="sxs-lookup"><span data-stu-id="818fd-164">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="818fd-165">Odpověď není uložena do mezipaměti, pokud hlavička existuje.</span><span class="sxs-lookup"><span data-stu-id="818fd-165">The response isn't cached if the header exists.</span></span> <span data-ttu-id="818fd-166">Jakýkoli middleware v kanálu zpracování požadavků, který nastavuje jeden nebo více souborů cookie, brání v ukládání do mezipaměti middleware ukládání odpovědi do mezipaměti (například [poskytovatele TempData založeného na souborech cookie](xref:fundamentals/app-state#tempdata)).</span><span class="sxs-lookup"><span data-stu-id="818fd-166">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="818fd-167">`Vary` Záhlaví se používá k odlišení odpovědi uložené v mezipaměti jinou hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="818fd-167">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="818fd-168">Například odpovědi ukládat do mezipaměti podle kódování zahrnutím `Vary: Accept-Encoding` hlavičky, která ukládá do mezipaměti odpovědi pro žádosti s hlavičkami `Accept-Encoding: text/plain` `Accept-Encoding: gzip` a samostatně.</span><span class="sxs-lookup"><span data-stu-id="818fd-168">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="818fd-169">Odpověď s hodnotou `*` záhlaví se nikdy neukládá.</span><span class="sxs-lookup"><span data-stu-id="818fd-169">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="818fd-170">Odpověď považovaná za zastaralou touto hlavičkou není uložená nebo načtená, `Cache-Control` Pokud není přepsána jinými záhlavími.</span><span class="sxs-lookup"><span data-stu-id="818fd-170">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="818fd-171">Úplná odpověď se obsluhuje z mezipaměti, pokud hodnota není `*` `ETag` a odpověď neodpovídá žádné z poskytnutých hodnot.</span><span class="sxs-lookup"><span data-stu-id="818fd-171">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="818fd-172">V opačném případě je zpracována odpověď 304 (Neupraveno).</span><span class="sxs-lookup"><span data-stu-id="818fd-172">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="818fd-173">Pokud hlavička `If-None-Match` není k dispozici, bude z mezipaměti poskytována úplná odpověď, pokud je datum odpovědi v mezipaměti novější než zadaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="818fd-173">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="818fd-174">Jinak se neupraví odpověď *304* .</span><span class="sxs-lookup"><span data-stu-id="818fd-174">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="818fd-175">Při obsluze z mezipaměti je `Date` hlavička nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď.</span><span class="sxs-lookup"><span data-stu-id="818fd-175">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="818fd-176">Při obsluze z mezipaměti je `Content-Length` hlavička nastavena middlewarem, pokud nebyla poskytnuta na původní odpověď.</span><span class="sxs-lookup"><span data-stu-id="818fd-176">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="818fd-177">`Age` Hlavička poslaná v původní odpovědi je ignorována.</span><span class="sxs-lookup"><span data-stu-id="818fd-177">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="818fd-178">Middleware vypočítá novou hodnotu při obsluze odpovědi v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-178">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="818fd-179">Požadavky na ukládání do mezipaměti – direktivy řízení mezipaměti</span><span class="sxs-lookup"><span data-stu-id="818fd-179">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="818fd-180">Middleware respektuje pravidla [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="818fd-180">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="818fd-181">Pravidla vyžadují mezipaměť, aby bylo možné akceptovat platnou `Cache-Control` hlavičku odeslanou klientem.</span><span class="sxs-lookup"><span data-stu-id="818fd-181">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="818fd-182">V rámci specifikace může klient provádět žádosti s `no-cache` hodnotou hlavičky a vynutit, aby server vygeneroval novou odpověď pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="818fd-182">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="818fd-183">V současné době není při používání middleware k tomuto chování při ukládání do mezipaměti k dispozici žádná vývojářská kontrola, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-183">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="818fd-184">Pro lepší kontrolu nad chováním při ukládání do mezipaměti můžete prozkoumat další funkce pro ukládání do mezipaměti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="818fd-184">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="818fd-185">Přečtěte si následující témata:</span><span class="sxs-lookup"><span data-stu-id="818fd-185">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="818fd-186">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="818fd-186">Troubleshooting</span></span>

<span data-ttu-id="818fd-187">Pokud chování při ukládání do mezipaměti není podle očekávání, zkontrolujte, že odpovědi jsou ukládat do mezipaměti a že je možné je obsluhovat z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-187">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="818fd-188">Projděte si vstupní hlavičky žádosti a odchozí hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="818fd-188">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="818fd-189">Povolit [protokolování](xref:fundamentals/logging/index) pro usnadnění ladění.</span><span class="sxs-lookup"><span data-stu-id="818fd-189">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="818fd-190">Při testování a odstraňování potíží s chováním při ukládání do mezipaměti může prohlížeč nastavit hlavičky požadavků, které mají vliv na ukládání do mezipaměti nežádoucím způsobem.</span><span class="sxs-lookup"><span data-stu-id="818fd-190">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="818fd-191">Prohlížeč může například nastavit `Cache-Control` hlavičku na `no-cache` nebo `max-age=0` při aktualizaci stránky.</span><span class="sxs-lookup"><span data-stu-id="818fd-191">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="818fd-192">Následující nástroje mohou explicitně nastavit hlavičky požadavků a jsou upřednostňovány pro testování ukládání do mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="818fd-192">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="818fd-193">Fiddler</span><span class="sxs-lookup"><span data-stu-id="818fd-193">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="818fd-194">Postman</span><span class="sxs-lookup"><span data-stu-id="818fd-194">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="818fd-195">Podmínky pro ukládání do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="818fd-195">Conditions for caching</span></span>

* <span data-ttu-id="818fd-196">Požadavek musí mít za následek odpověď serveru se stavovým kódem 200 (OK).</span><span class="sxs-lookup"><span data-stu-id="818fd-196">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="818fd-197">Metoda požadavku musí být GET nebo HEAD.</span><span class="sxs-lookup"><span data-stu-id="818fd-197">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="818fd-198">V `Startup.Configure`nástroji musí být middleware pro ukládání odpovědí do mezipaměti umístěn před middlewarem, který vyžaduje ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-198">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="818fd-199">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="818fd-199">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="818fd-200">Hlavička `Authorization` nesmí být k dispozici.</span><span class="sxs-lookup"><span data-stu-id="818fd-200">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="818fd-201">`Cache-Control`parametry hlaviček musí být platné a odpověď musí být označená `public` a nesmí být označená. `private`</span><span class="sxs-lookup"><span data-stu-id="818fd-201">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="818fd-202">Hlavička nesmí být k dispozici, pokud `Cache-Control` hlavička `Cache-Control` není k dispozici, `Pragma` protože záhlaví potlačí hlavičku, pokud je k dispozici. `Pragma: no-cache`</span><span class="sxs-lookup"><span data-stu-id="818fd-202">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="818fd-203">Hlavička `Set-Cookie` nesmí být k dispozici.</span><span class="sxs-lookup"><span data-stu-id="818fd-203">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="818fd-204">`Vary`parametry záhlaví musí být platné a nesmí být rovny `*`.</span><span class="sxs-lookup"><span data-stu-id="818fd-204">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="818fd-205">Hodnota `Content-Length` hlavičky (Pokud je nastavena) musí odpovídat velikosti těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="818fd-205">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="818fd-206"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> Není použit.</span><span class="sxs-lookup"><span data-stu-id="818fd-206">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="818fd-207">Odpověď nesmí být zastaralá tak, jak je `Expires` určena hlavičkou `max-age` a `s-maxage` direktivami cache a.</span><span class="sxs-lookup"><span data-stu-id="818fd-207">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="818fd-208">Ukládání odpovědí do vyrovnávací paměti musí být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="818fd-208">Response buffering must be successful.</span></span> <span data-ttu-id="818fd-209">Velikost odpovědi musí být menší než nakonfigurovaná nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span><span class="sxs-lookup"><span data-stu-id="818fd-209">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="818fd-210">Velikost textu odpovědi musí být menší než nakonfigurované nebo výchozí <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span><span class="sxs-lookup"><span data-stu-id="818fd-210">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="818fd-211">Odpověď musí být mezipaměť v souladu se specifikacemi [RFC 7234](https://tools.ietf.org/html/rfc7234) .</span><span class="sxs-lookup"><span data-stu-id="818fd-211">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="818fd-212">Například `no-store` direktiva nesmí existovat v polích hlavičky Request nebo Response.</span><span class="sxs-lookup"><span data-stu-id="818fd-212">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="818fd-213">Viz *část 3: Ukládání odpovědí do* mezipamětí [dokumentu RFC 7234](https://tools.ietf.org/html/rfc7234) pro podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="818fd-213">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="818fd-214">Systém ochrany proti padělání pro generování zabezpečených tokenů, který brání útokům přes CSRF (mezi lokalitami) `Cache-Control` , `Pragma` nastaví záhlaví `no-cache` a, aby se odpovědi neukládaly do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="818fd-214">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="818fd-215">Informace o tom, jak zakázat tokeny antipadělání pro prvky formuláře HTML, <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>naleznete v tématu.</span><span class="sxs-lookup"><span data-stu-id="818fd-215">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="818fd-216">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="818fd-216">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

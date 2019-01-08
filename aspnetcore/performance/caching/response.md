---
title: Ukládání odpovědí do mezipaměti v ASP.NET Core
author: rick-anderson
description: Další informace o použití odpověď do mezipaměti pro nižší požadavky na šířku pásma a zvýšit výkon aplikace ASP.NET Core.
ms.author: riande
ms.date: 01/07/2018
uid: performance/caching/response
ms.openlocfilehash: 5fbcaddff6e53d01a19ba8a7455c719feb614326
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098945"
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="883e2-103">Ukládání odpovědí do mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="883e2-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="883e2-104">Podle [Jan Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="883e2-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Luke Latham](https://github.com/guardrex)</span></span>

> [!NOTE]
> <span data-ttu-id="883e2-105">Ukládání odpovědí do mezipaměti v Razor Pages je k dispozici v ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="883e2-105">Response caching in Razor Pages is available in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="883e2-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="883e2-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="883e2-107">Ukládání odpovědí do mezipaměti snižuje počet požadavků, které odešle klient nebo server proxy webový server.</span><span class="sxs-lookup"><span data-stu-id="883e2-107">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="883e2-108">Ukládání odpovědí do mezipaměti také snižuje množství práce provádí webového serveru pro generování odpovědi.</span><span class="sxs-lookup"><span data-stu-id="883e2-108">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="883e2-109">Ukládání odpovědí do mezipaměti se řídí hlavičky, které určují, jak chcete klienta, serveru proxy a middlewarem do mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="883e2-109">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="883e2-110">[ResponseCache atribut](#responsecache-attribute) účastní nastavení odpověď záhlaví, kteří klienti mohou případném dalším sdílení dodržovat při ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-110">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers, which clients may honor when caching responses.</span></span> <span data-ttu-id="883e2-111">[Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) je možné do mezipaměti odpovědi na serveru.</span><span class="sxs-lookup"><span data-stu-id="883e2-111">[Response Caching Middleware](xref:performance/caching/middleware) can be used to cache responses on the server.</span></span> <span data-ttu-id="883e2-112">Middleware lze použít `ResponseCache` atribut vlastnosti a ovlivnit chování ukládání do mezipaměti na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="883e2-112">The middleware can use `ResponseCache` attribute properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="883e2-113">Ukládání do mezipaměti založené na protokolu HTTP odpovědi</span><span class="sxs-lookup"><span data-stu-id="883e2-113">HTTP-based response caching</span></span>

<span data-ttu-id="883e2-114">[Specifikace HTTP 1.1 ukládání do mezipaměti](https://tools.ietf.org/html/rfc7234) popisuje chování mezipaměti Internet.</span><span class="sxs-lookup"><span data-stu-id="883e2-114">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="883e2-115">Primární záhlaví HTTP používá pro ukládání do mezipaměti je [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), který se používá k určení mezipaměti *direktivy*.</span><span class="sxs-lookup"><span data-stu-id="883e2-115">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="883e2-116">Direktivy řízení chování ukládání do mezipaměti podle požadavků dostanou od klientů na servery a jako odpověď dostanou ze serverů zpět klientům.</span><span class="sxs-lookup"><span data-stu-id="883e2-116">The directives control caching behavior as requests make their way from clients to servers and as reponses make their way from servers back to clients.</span></span> <span data-ttu-id="883e2-117">Požadavky a odpovědi procházet proxy servery a proxy servery musí také odpovídají specifikaci HTTP 1.1 ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-117">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="883e2-118">Běžné `Cache-Control` direktivy jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="883e2-118">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="883e2-119">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="883e2-119">Directive</span></span>                                                       | <span data-ttu-id="883e2-120">Akce</span><span class="sxs-lookup"><span data-stu-id="883e2-120">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="883e2-121">public</span><span class="sxs-lookup"><span data-stu-id="883e2-121">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="883e2-122">Mezipaměť může ukládat odpovědi.</span><span class="sxs-lookup"><span data-stu-id="883e2-122">A cache may store the response.</span></span> |
| [<span data-ttu-id="883e2-123">private</span><span class="sxs-lookup"><span data-stu-id="883e2-123">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="883e2-124">Odpověď nesmí být uloženy ve sdílené mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-124">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="883e2-125">Soukromé mezipaměti může ukládat a opakovaně používat odpovědi.</span><span class="sxs-lookup"><span data-stu-id="883e2-125">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="883e2-126">Maximální stáří</span><span class="sxs-lookup"><span data-stu-id="883e2-126">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="883e2-127">Klient nebude přijímat odpovědi, jejichž stáří je větší než zadaný počet sekund.</span><span class="sxs-lookup"><span data-stu-id="883e2-127">The client won't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="883e2-128">Příklady: `max-age=60` (60 sekund), `max-age=2592000` (1 měsíc)</span><span class="sxs-lookup"><span data-stu-id="883e2-128">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="883e2-129">no-cache</span><span class="sxs-lookup"><span data-stu-id="883e2-129">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="883e2-130">**U požadavků**: Mezipaměť nesmí používat uložené odpovědi, abyste vyhověli žádosti.</span><span class="sxs-lookup"><span data-stu-id="883e2-130">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="883e2-131">Poznámka: Zdrojový server znovu generuje odpovědi pro klienta a middleware aktualizuje odpověď na uložené v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-131">Note: The origin server re-generates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="883e2-132">**V odpovědi**: Odpověď nesmí se používat pro další požadavek bez ověřování na původním serveru.</span><span class="sxs-lookup"><span data-stu-id="883e2-132">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="883e2-133">no-store</span><span class="sxs-lookup"><span data-stu-id="883e2-133">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="883e2-134">**U požadavků**: Mezipaměť nesmí uložit žádost.</span><span class="sxs-lookup"><span data-stu-id="883e2-134">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="883e2-135">**V odpovědi**: Mezipaměť nesmí uložit libovolnou část odpovědi.</span><span class="sxs-lookup"><span data-stu-id="883e2-135">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="883e2-136">V následující tabulce jsou uvedeny další hlavičky mezipaměti, které hrají roli při ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-136">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="883e2-137">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="883e2-137">Header</span></span>                                                     | <span data-ttu-id="883e2-138">Funkce</span><span class="sxs-lookup"><span data-stu-id="883e2-138">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="883e2-139">Stáří</span><span class="sxs-lookup"><span data-stu-id="883e2-139">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="883e2-140">Odhad množství času v sekundách, protože odpověď byla vygenerována nebo úspěšně ověřen na původním serveru.</span><span class="sxs-lookup"><span data-stu-id="883e2-140">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="883e2-141">Vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="883e2-141">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="883e2-142">Datum a čas, po jejímž uplynutí se považuje za odpověď zastaralá.</span><span class="sxs-lookup"><span data-stu-id="883e2-142">The date/time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="883e2-143">Direktiva pragma</span><span class="sxs-lookup"><span data-stu-id="883e2-143">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="883e2-144">Pro zpětnou kompatibilitu s HTTP verze 1.0 ukládá do mezipaměti pro nastavení existuje `no-cache` chování.</span><span class="sxs-lookup"><span data-stu-id="883e2-144">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="883e2-145">Pokud `Cache-Control` záhlaví je k dispozici, `Pragma` záhlaví se ignoruje.</span><span class="sxs-lookup"><span data-stu-id="883e2-145">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="883e2-146">se liší</span><span class="sxs-lookup"><span data-stu-id="883e2-146">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="883e2-147">Určuje, že odpověď uložená v mezipaměti nesmí být odeslána, pokud všechny nástroje `Vary` záhlaví pole shodují v původní požadavek odpověď uložená v mezipaměti a nový požadavek.</span><span class="sxs-lookup"><span data-stu-id="883e2-147">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="883e2-148">Ukládání do mezipaměti respektuje založené na protokolu HTTP požadavku direktivy Cache-Control</span><span class="sxs-lookup"><span data-stu-id="883e2-148">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="883e2-149">[Specifikace HTTP 1.1 ukládání do mezipaměti pro hlavičku Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) vyžaduje mezipaměti případném dalším sdílení dodržovat platné `Cache-Control` záhlaví odesílaném klientem.</span><span class="sxs-lookup"><span data-stu-id="883e2-149">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="883e2-150">Klienta můžete vytvářet požadavky s `no-cache` hodnota hlavičky a vynutit server vygenerovat novou odpověď pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="883e2-150">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="883e2-151">Vždy dodržením klienta `Cache-Control` hlavičky žádosti dává smysl, pokud považujete za cíl protokolu HTTP, ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-151">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="883e2-152">V části specifikaci oficiální ukládání do mezipaměti smyslem je snížit latenci a sítě režii splňující požadavky napříč sítí klientů, proxy servery a servery.</span><span class="sxs-lookup"><span data-stu-id="883e2-152">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="883e2-153">Není nutně způsob, jak řídit zatížení na původním serveru.</span><span class="sxs-lookup"><span data-stu-id="883e2-153">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="883e2-154">Neexistuje žádné vývojáři řídit tohoto chování ukládání do mezipaměti při použití [Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) vzhledem k tomu, že dodržuje middleware official je přínosné pro ukládání do mezipaměti specifikace.</span><span class="sxs-lookup"><span data-stu-id="883e2-154">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="883e2-155">[Plánované vylepšení middleware](https://github.com/aspnet/AspNetCore/issues/2612) jsou příležitosti pro konfiguraci middlewaru ignorovat požadavku `Cache-Control` hlavičku při rozhodování o tom, která bude sloužit odpověď uložená v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-155">[Planned enhancements to the middleware](https://github.com/aspnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="883e2-156">Plánované vylepšení nabízejí možnost lepší řízení zatížení serveru.</span><span class="sxs-lookup"><span data-stu-id="883e2-156">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="883e2-157">Další technologie ukládání do mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="883e2-157">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="883e2-158">Ukládání do mezipaměti v paměti</span><span class="sxs-lookup"><span data-stu-id="883e2-158">In-memory caching</span></span>

<span data-ttu-id="883e2-159">Ukládání do mezipaměti v paměti používá k ukládání dat uložených v mezipaměti paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="883e2-159">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="883e2-160">Tento typ ukládání do mezipaměti je vhodný pro jeden nebo více servery pomocí *rychlé relace*.</span><span class="sxs-lookup"><span data-stu-id="883e2-160">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="883e2-161">Rychlé relace znamená, že požadavky od klienta jsou vždy směrovány na stejný server ke zpracování.</span><span class="sxs-lookup"><span data-stu-id="883e2-161">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="883e2-162">Další informace najdete v tématu [ukládat do mezipaměti v paměti](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="883e2-162">For more information, see [Cache in-memory](xref:performance/caching/memory).</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="883e2-163">Distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="883e2-163">Distributed Cache</span></span>

<span data-ttu-id="883e2-164">K ukládání dat v paměti, když je aplikace hostovaná v cloudu nebo serveru farmy pomocí distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-164">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="883e2-165">Mezipaměť je sdílen mezi servery, které zpracovávají požadavky.</span><span class="sxs-lookup"><span data-stu-id="883e2-165">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="883e2-166">Klient může odeslat žádost, kterou provádí služba jakýkoli server ve skupině, pokud je k dispozici data uložená v mezipaměti klienta.</span><span class="sxs-lookup"><span data-stu-id="883e2-166">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="883e2-167">ASP.NET Core nabízí systému SQL Server a mezipamětí Redis distribuovat.</span><span class="sxs-lookup"><span data-stu-id="883e2-167">ASP.NET Core offers SQL Server and Redis distributed caches.</span></span>

<span data-ttu-id="883e2-168">Další informace naleznete v tématu <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="883e2-168">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="883e2-169">Pomocné rutiny značky do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="883e2-169">Cache Tag Helper</span></span>

<span data-ttu-id="883e2-170">Vám může ukládat do mezipaměti obsah ze zobrazení MVC nebo stránky Razor s pomocné rutiny značky mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-170">You can cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="883e2-171">Pomocná rutina značek mezipaměti používá k ukládání dat do mezipaměti v paměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-171">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="883e2-172">Další informace najdete v tématu [pomocná rutina značek v mezipaměti v ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="883e2-172">For more information, see [Cache Tag Helper in ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="883e2-173">Pomocná rutina značek v distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="883e2-173">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="883e2-174">Obsah ze zobrazení MVC nebo stránky Razor v distribuovaných cloudových nebo webových farem můžete mezipaměti pomocí distribuované pomocná rutina značek mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-174">You can cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="883e2-175">Distribuované mezipaměti pomocná rutina značek v používá k ukládání dat serveru SQL Server nebo Redis.</span><span class="sxs-lookup"><span data-stu-id="883e2-175">The Distributed Cache Tag Helper uses SQL Server or Redis to store data.</span></span>

<span data-ttu-id="883e2-176">Další informace najdete v tématu [pomocné rutiny značky distribuované mezipaměti](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="883e2-176">For more information, see [Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="883e2-177">Atribut ResponseCache</span><span class="sxs-lookup"><span data-stu-id="883e2-177">ResponseCache attribute</span></span>

<span data-ttu-id="883e2-178">[ResponseCacheAttribute](/dotnet/api/Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) Určuje parametry, které jsou nezbytné pro nastavení příslušné záhlaví v ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-178">The [ResponseCacheAttribute](/dotnet/api/Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="883e2-179">Zakáže ukládání do mezipaměti pro obsah, který obsahuje informace o ověření klienti.</span><span class="sxs-lookup"><span data-stu-id="883e2-179">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="883e2-180">Ukládání do mezipaměti musí být povolené pouze pro obsah, který se nezmění na základě identity uživatele nebo určuje, zda je uživatel přihlášený.</span><span class="sxs-lookup"><span data-stu-id="883e2-180">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="883e2-181">[VaryByQueryKeys](/dotnet/api/microsoft.aspnetcore.mvc.responsecacheattribute.varybyquerykeys) uložené odpovědi se liší podle hodnoty daný seznam klíče dotazu.</span><span class="sxs-lookup"><span data-stu-id="883e2-181">[VaryByQueryKeys](/dotnet/api/microsoft.aspnetcore.mvc.responsecacheattribute.varybyquerykeys) varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="883e2-182">Když na jedinou hodnotu `*` je k dispozici, se liší middleware odpovědi na všechny žádosti parametrů řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="883e2-182">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span> <span data-ttu-id="883e2-183">`VaryByQueryKeys` vyžaduje ASP.NET Core 1.1 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="883e2-183">`VaryByQueryKeys` requires ASP.NET Core 1.1 or later.</span></span>

<span data-ttu-id="883e2-184">[Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) musí být povoleno nastavení `VaryByQueryKeys` vlastnosti; v opačném případě je vyvolána výjimka za běhu.</span><span class="sxs-lookup"><span data-stu-id="883e2-184">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the `VaryByQueryKeys` property; otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="883e2-185">Není k dispozici odpovídající hlavičku protokolu HTTP `VaryByQueryKeys` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="883e2-185">There isn't a corresponding HTTP header for the `VaryByQueryKeys` property.</span></span> <span data-ttu-id="883e2-186">Vlastnost je funkce protokolu HTTP zpracovávaných middlewarem odpovědi ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="883e2-186">The property is an HTTP feature handled by the Response Caching Middleware.</span></span> <span data-ttu-id="883e2-187">Pro daný middleware pro obsluhu odpověď uložená v mezipaměti řetězec dotazu a hodnotu řetězce dotazu musí odpovídat předchozí žádosti.</span><span class="sxs-lookup"><span data-stu-id="883e2-187">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="883e2-188">Představte si třeba pořadí požadavků a výsledky zobrazené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="883e2-188">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="883e2-189">Žádost</span><span class="sxs-lookup"><span data-stu-id="883e2-189">Request</span></span>                          | <span data-ttu-id="883e2-190">Výsledek</span><span class="sxs-lookup"><span data-stu-id="883e2-190">Result</span></span>                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | <span data-ttu-id="883e2-191">Server vrátil</span><span class="sxs-lookup"><span data-stu-id="883e2-191">Returned from server</span></span>     |
| `http://example.com?key1=value1` | <span data-ttu-id="883e2-192">Vrácená z middlewaru</span><span class="sxs-lookup"><span data-stu-id="883e2-192">Returned from middleware</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="883e2-193">Server vrátil</span><span class="sxs-lookup"><span data-stu-id="883e2-193">Returned from server</span></span>     |

<span data-ttu-id="883e2-194">První požadavek je vrácená serverem a uložili do mezipaměti v middlewaru.</span><span class="sxs-lookup"><span data-stu-id="883e2-194">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="883e2-195">Druhý požadavek je vrátit middlewarem, protože řetězec dotazu odpovídá předchozí žádosti.</span><span class="sxs-lookup"><span data-stu-id="883e2-195">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="883e2-196">Třetí žádost není v mezipaměti middleware, protože hodnotu řetězce dotazu neodpovídá předchozí žádosti.</span><span class="sxs-lookup"><span data-stu-id="883e2-196">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span> 

<span data-ttu-id="883e2-197">`ResponseCacheAttribute` Slouží ke konfiguraci a vytvořte (prostřednictvím `IFilterFactory`) [ResponseCacheFilter](/dotnet/api/microsoft.aspnetcore.mvc.internal.responsecachefilter).</span><span class="sxs-lookup"><span data-stu-id="883e2-197">The `ResponseCacheAttribute` is used to configure and create (via `IFilterFactory`) a [ResponseCacheFilter](/dotnet/api/microsoft.aspnetcore.mvc.internal.responsecachefilter).</span></span> <span data-ttu-id="883e2-198">`ResponseCacheFilter` Provádí aktualizace správné hlavičky HTTP a funkce odpovědi.</span><span class="sxs-lookup"><span data-stu-id="883e2-198">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="883e2-199">Filtr:</span><span class="sxs-lookup"><span data-stu-id="883e2-199">The filter:</span></span>

* <span data-ttu-id="883e2-200">Odebere všechny existující záhlaví pro `Vary`, `Cache-Control`, a `Pragma`.</span><span class="sxs-lookup"><span data-stu-id="883e2-200">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span> 
* <span data-ttu-id="883e2-201">Zapíše příslušné záhlaví podle vlastnosti nastavené `ResponseCacheAttribute`.</span><span class="sxs-lookup"><span data-stu-id="883e2-201">Writes out the appropriate headers based on the properties set in the `ResponseCacheAttribute`.</span></span> 
* <span data-ttu-id="883e2-202">Aktualizace ukládání do mezipaměti funkce protokolu HTTP, pokud odpověď `VaryByQueryKeys` nastavena.</span><span class="sxs-lookup"><span data-stu-id="883e2-202">Updates the response caching HTTP feature if `VaryByQueryKeys` is set.</span></span>

### <a name="vary"></a><span data-ttu-id="883e2-203">se liší</span><span class="sxs-lookup"><span data-stu-id="883e2-203">Vary</span></span>

<span data-ttu-id="883e2-204">Této hlavičky je zapsán, pouze když `VaryByHeader` je nastavena.</span><span class="sxs-lookup"><span data-stu-id="883e2-204">This header is only written when the `VaryByHeader` property is set.</span></span> <span data-ttu-id="883e2-205">Je nastaven na hodnotu `Vary` hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="883e2-205">It's set to the `Vary` property's value.</span></span> <span data-ttu-id="883e2-206">Následující ukázkový používá `VaryByHeader` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="883e2-206">The following sample uses the `VaryByHeader` property:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

<span data-ttu-id="883e2-207">Můžete zobrazit pomocí nástrojů v prohlížeči sítě hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="883e2-207">You can view the response headers with your browser's network tools.</span></span> <span data-ttu-id="883e2-208">Následující obrázek ukazuje F12 Edge výstupních **sítě** kartu, kdy `About2` aktualizaci metody akce:</span><span class="sxs-lookup"><span data-stu-id="883e2-208">The following image shows the Edge F12 output on the **Network** tab when the `About2` action method is refreshed:</span></span>

![Při volání metody akce About2 hraniční F12 výstup na síťové kartě](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a><span data-ttu-id="883e2-210">NoStore a Location.None</span><span class="sxs-lookup"><span data-stu-id="883e2-210">NoStore and Location.None</span></span>

<span data-ttu-id="883e2-211">`NoStore` přepíše většinu dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="883e2-211">`NoStore` overrides most of the other properties.</span></span> <span data-ttu-id="883e2-212">Pokud je tato vlastnost nastavena na `true`, `Cache-Control` záhlaví je nastavena na `no-store`.</span><span class="sxs-lookup"><span data-stu-id="883e2-212">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="883e2-213">Pokud `Location` je nastavena na `None`:</span><span class="sxs-lookup"><span data-stu-id="883e2-213">If `Location` is set to `None`:</span></span>

* <span data-ttu-id="883e2-214">`Cache-Control` je nastavena na `no-store,no-cache`.</span><span class="sxs-lookup"><span data-stu-id="883e2-214">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="883e2-215">`Pragma` je nastavena na `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="883e2-215">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="883e2-216">Pokud `NoStore` je `false` a `Location` je `None`, `Cache-Control` a `Pragma` jsou nastaveny na `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="883e2-216">If `NoStore` is `false` and `Location` is `None`, `Cache-Control` and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="883e2-217">Obvykle nastavena `NoStore` k `true` na chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="883e2-217">You typically set `NoStore` to `true` on error pages.</span></span> <span data-ttu-id="883e2-218">Příklad:</span><span class="sxs-lookup"><span data-stu-id="883e2-218">For example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

<span data-ttu-id="883e2-219">Výsledkem je následující hlavičky:</span><span class="sxs-lookup"><span data-stu-id="883e2-219">This results in the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="883e2-220">Umístění a doba trvání</span><span class="sxs-lookup"><span data-stu-id="883e2-220">Location and Duration</span></span>

<span data-ttu-id="883e2-221">Chcete-li povolit ukládání do mezipaměti, `Duration` musí být nastaven na kladnou hodnotu a `Location` musí být buď `Any` (výchozí) nebo `Client`.</span><span class="sxs-lookup"><span data-stu-id="883e2-221">To enable caching, `Duration` must be set to a positive value and `Location` must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="883e2-222">V takovém případě `Cache-Control` záhlaví je nastavena na hodnotu umístění, za nímž následuje `max-age` odpovědi.</span><span class="sxs-lookup"><span data-stu-id="883e2-222">In this case, the `Cache-Control` header is set to the location value followed by the `max-age` of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="883e2-223">`Location`v možnosti `Any` a `Client` překlad do `Cache-Control` hodnoty hlavičky `public` a `private`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="883e2-223">`Location`'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="883e2-224">Jak bylo uvedeno dříve, nastavení `Location` k `None` nastaví obě `Cache-Control` a `Pragma` záhlaví `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="883e2-224">As noted previously, setting `Location` to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="883e2-225">Níže je příklad ukazující hlavičky vytvořen tak, že nastavíte `Duration` a výchozí `Location` hodnotu:</span><span class="sxs-lookup"><span data-stu-id="883e2-225">Below is an example showing the headers produced by setting `Duration` and leaving the default `Location` value:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

<span data-ttu-id="883e2-226">Tímto se vytvoří následující hlavičky:</span><span class="sxs-lookup"><span data-stu-id="883e2-226">This produces the following header:</span></span>

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a><span data-ttu-id="883e2-227">Profily mezipaměti</span><span class="sxs-lookup"><span data-stu-id="883e2-227">Cache profiles</span></span>

<span data-ttu-id="883e2-228">Namísto duplikování `ResponseCache` nastavení na mnoho atributů akce kontroleru, mezipaměti profily se dají konfigurovat jako možnosti při nastavování MVC v `ConfigureServices` metoda ve `Startup`.</span><span class="sxs-lookup"><span data-stu-id="883e2-228">Instead of duplicating `ResponseCache` settings on many controller action attributes, cache profiles can be configured as options when setting up MVC in the `ConfigureServices` method in `Startup`.</span></span> <span data-ttu-id="883e2-229">Hodnoty nalezené v profilu odkazované mezipaměti se používají jako výchozí hodnoty podle `ResponseCache` atribut a jsou přepsány podle libovolných vlastností zadané v atributu.</span><span class="sxs-lookup"><span data-stu-id="883e2-229">Values found in a referenced cache profile are used as the defaults by the `ResponseCache` attribute and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="883e2-230">Nastavení profilu mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="883e2-230">Setting up a cache profile:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="883e2-231">Odkazování na profil mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="883e2-231">Referencing a cache profile:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

<span data-ttu-id="883e2-232">`ResponseCache` Atribut se dá použít k akce (metody) a kontrolerů (třídy).</span><span class="sxs-lookup"><span data-stu-id="883e2-232">The `ResponseCache` attribute can be applied both to actions (methods) and controllers (classes).</span></span> <span data-ttu-id="883e2-233">Atributy na úrovni metody přepsání nastavení uvedená v atributy na úrovni třídy.</span><span class="sxs-lookup"><span data-stu-id="883e2-233">Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="883e2-234">V příkladu výše Určuje atribut úroveň třídy v délce 30 sekund, přestože úroveň metody atribut odkazuje profil mezipaměti s určitou dobou trvání nastaven na 60 sekund.</span><span class="sxs-lookup"><span data-stu-id="883e2-234">In the above example, a class-level attribute specifies a duration of 30 seconds, while a method-level attribute references a cache profile with a duration set to 60 seconds.</span></span>

<span data-ttu-id="883e2-235">Výsledný záhlaví:</span><span class="sxs-lookup"><span data-stu-id="883e2-235">The resulting header:</span></span>

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a><span data-ttu-id="883e2-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="883e2-236">Additional resources</span></span>

* [<span data-ttu-id="883e2-237">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="883e2-237">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="883e2-238">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="883e2-238">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

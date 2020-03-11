---
title: Žádosti o funkce v ASP.NET Core
author: ardalis
description: Přečtěte si o podrobnostech implementace webového serveru souvisejících s požadavky HTTP a odpověďmi, které jsou definované v rozhraních pro ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659675"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="5de58-103">Žádosti o funkce v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5de58-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="5de58-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5de58-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5de58-105">Implementační detaily webového serveru související s HTTP požadavky a odpověďmi jsou definovány v rozhraních.</span><span class="sxs-lookup"><span data-stu-id="5de58-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="5de58-106">Tato rozhraní používá implementace serveru a middleware k vytváření a úpravám kanálu hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5de58-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="5de58-107">Rozhraní funkcí</span><span class="sxs-lookup"><span data-stu-id="5de58-107">Feature interfaces</span></span>

<span data-ttu-id="5de58-108">ASP.NET Core definuje počet rozhraní funkcí HTTP v `Microsoft.AspNetCore.Http.Features` používaných servery k identifikaci funkcí, které podporují.</span><span class="sxs-lookup"><span data-stu-id="5de58-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="5de58-109">Následující rozhraní funkcí řídí požadavky a návratové odpovědi:</span><span class="sxs-lookup"><span data-stu-id="5de58-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="5de58-110">`IHttpRequestFeature` definuje strukturu požadavku HTTP, včetně protokolu, cesty, řetězce dotazu, záhlaví a textu.</span><span class="sxs-lookup"><span data-stu-id="5de58-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="5de58-111">`IHttpResponseFeature` definuje strukturu odpovědi HTTP, včetně stavového kódu, hlaviček a textu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="5de58-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="5de58-112">`IHttpAuthenticationFeature` definuje podporu pro identifikaci uživatelů na základě `ClaimsPrincipal` a určení obslužné rutiny ověřování.</span><span class="sxs-lookup"><span data-stu-id="5de58-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="5de58-113">`IHttpUpgradeFeature` definuje podporu pro [upgrady http](https://tools.ietf.org/html/rfc2616.html#section-14.42). díky tomu může klient určit, které další protokoly chcete použít, pokud si server přeje přepínat protokoly.</span><span class="sxs-lookup"><span data-stu-id="5de58-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="5de58-114">`IHttpBufferingFeature` definuje metody pro zákaz ukládání požadavků do vyrovnávací paměti nebo odpovědí.</span><span class="sxs-lookup"><span data-stu-id="5de58-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="5de58-115">`IHttpConnectionFeature` definuje vlastnosti pro místní a vzdálené adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="5de58-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="5de58-116">`IHttpRequestLifetimeFeature` definuje podporu pro přerušení připojení nebo zjištění, zda byla žádost ukončena předčasně, například odpojením klienta.</span><span class="sxs-lookup"><span data-stu-id="5de58-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="5de58-117">`IHttpSendFileFeature` definuje metodu pro asynchronní posílání souborů.</span><span class="sxs-lookup"><span data-stu-id="5de58-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="5de58-118">`IHttpWebSocketFeature` definuje rozhraní API pro podpůrné webové sokety.</span><span class="sxs-lookup"><span data-stu-id="5de58-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="5de58-119">`IHttpRequestIdentifierFeature` přidá vlastnost, která může být implementována k jednoznačné identifikaci požadavků.</span><span class="sxs-lookup"><span data-stu-id="5de58-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="5de58-120">`ISessionFeature` definuje `ISessionFactory` a `ISession` abstrakce pro podporu uživatelských relací.</span><span class="sxs-lookup"><span data-stu-id="5de58-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="5de58-121">`ITlsConnectionFeature` definuje rozhraní API pro načítání klientských certifikátů.</span><span class="sxs-lookup"><span data-stu-id="5de58-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="5de58-122">`ITlsTokenBindingFeature` definuje metody pro práci s parametry vazby tokenu TLS.</span><span class="sxs-lookup"><span data-stu-id="5de58-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="5de58-123">`ISessionFeature` není funkcí serveru, ale je implementována `SessionMiddleware` (viz [Správa stavu aplikace](app-state.md)).</span><span class="sxs-lookup"><span data-stu-id="5de58-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="5de58-124">Kolekce funkcí</span><span class="sxs-lookup"><span data-stu-id="5de58-124">Feature collections</span></span>

<span data-ttu-id="5de58-125">Vlastnost `Features` `HttpContext` poskytuje rozhraní pro získání a nastavení dostupných funkcí protokolu HTTP pro aktuální požadavek.</span><span class="sxs-lookup"><span data-stu-id="5de58-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="5de58-126">Vzhledem k tomu, že je kolekce funkcí proměnlivá i v rámci požadavku, je možné použít middleware pro úpravu kolekce a přidání podpory pro další funkce.</span><span class="sxs-lookup"><span data-stu-id="5de58-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="5de58-127">Middleware a žádosti o funkce</span><span class="sxs-lookup"><span data-stu-id="5de58-127">Middleware and request features</span></span>

<span data-ttu-id="5de58-128">I když jsou servery zodpovědné za vytvoření kolekce funkcí, může middleware přidat do této kolekce a využívat funkce z kolekce.</span><span class="sxs-lookup"><span data-stu-id="5de58-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="5de58-129">Například `StaticFileMiddleware` přistupuje k funkci `IHttpSendFileFeature`.</span><span class="sxs-lookup"><span data-stu-id="5de58-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="5de58-130">Pokud tato funkce existuje, používá se k odeslání požadovaného statického souboru z jeho fyzické cesty.</span><span class="sxs-lookup"><span data-stu-id="5de58-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="5de58-131">V opačném případě se k odeslání souboru použije pomalejší alternativní metoda.</span><span class="sxs-lookup"><span data-stu-id="5de58-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="5de58-132">Pokud je k dispozici, `IHttpSendFileFeature` umožňuje operačnímu systému otevřít soubor a provést přímou kopii režimu jádra na síťové kartě.</span><span class="sxs-lookup"><span data-stu-id="5de58-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="5de58-133">Middleware může navíc přidat do kolekce funkcí zřízené serverem.</span><span class="sxs-lookup"><span data-stu-id="5de58-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="5de58-134">Existující funkce můžou být i nahrazovány middlewarem a umožňují middlewaru rozšířit funkčnost serveru.</span><span class="sxs-lookup"><span data-stu-id="5de58-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="5de58-135">Funkce přidané do kolekce jsou hned k dispozici pro další middleware nebo podkladové aplikace přímo v kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="5de58-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="5de58-136">Kombinací vlastních implementací serveru a konkrétních vylepšení middlewaru může být vytvořena přesně sada funkcí, které aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="5de58-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="5de58-137">To umožňuje, aby chybějící funkce byly přidány bez nutnosti změny v serveru a zajistila zpřístupnění pouze minimálního množství funkcí, čímž se omezuje prostor pro útoky a zvýšil se výkon.</span><span class="sxs-lookup"><span data-stu-id="5de58-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="5de58-138">Souhrn</span><span class="sxs-lookup"><span data-stu-id="5de58-138">Summary</span></span>

<span data-ttu-id="5de58-139">Rozhraní funkcí definují konkrétní funkce protokolu HTTP, které může daný požadavek podporovat.</span><span class="sxs-lookup"><span data-stu-id="5de58-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="5de58-140">Servery definují kolekce funkcí a počáteční sadu funkcí, které tento server podporuje, ale middleware je možné využít ke zlepšení těchto funkcí.</span><span class="sxs-lookup"><span data-stu-id="5de58-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5de58-141">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5de58-141">Additional resources</span></span>

* [<span data-ttu-id="5de58-142">Servery</span><span class="sxs-lookup"><span data-stu-id="5de58-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="5de58-143">Middleware</span><span class="sxs-lookup"><span data-stu-id="5de58-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="5de58-144">Otevřené webové rozhraní pro .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="5de58-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)

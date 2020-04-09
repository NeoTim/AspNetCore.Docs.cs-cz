---
title: Funkce požadavku v ASP.NET jádra
author: ardalis
description: Informace o podrobnostech implementace webového serveru souvisejících s požadavky HTTP a odpověďmi, které jsou definovány v rozhraních pro ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416224"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="c4b0f-103">Funkce požadavku v ASP.NET jádra</span><span class="sxs-lookup"><span data-stu-id="c4b0f-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="c4b0f-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c4b0f-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c4b0f-105">Podrobnosti implementace webového serveru související s požadavky HTTP a odpověďmi jsou definovány v rozhraních.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="c4b0f-106">Tato rozhraní jsou používány implementace serveru a middleware k vytvoření a úpravě hostitelského kanálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="c4b0f-107">Rozhraní funkcí</span><span class="sxs-lookup"><span data-stu-id="c4b0f-107">Feature interfaces</span></span>

<span data-ttu-id="c4b0f-108">ASP.NET Core definuje řadu rozhraní funkcí `Microsoft.AspNetCore.Http.Features` PROTOKOLU HTTP, ve kterých servery používají k identifikaci funkcí, které podporují.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="c4b0f-109">Následující rozhraní funkcí zpracovávají požadavky a vracejí odpovědi:</span><span class="sxs-lookup"><span data-stu-id="c4b0f-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="c4b0f-110">`IHttpRequestFeature`Definuje strukturu požadavku HTTP, včetně protokolu, cesty, řetězce dotazu, záhlaví a těla.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="c4b0f-111">`IHttpResponseFeature`Definuje strukturu odpovědi HTTP, včetně stavového kódu, záhlaví a těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="c4b0f-112">`IHttpAuthenticationFeature`Definuje podporu pro identifikaci uživatelů `ClaimsPrincipal` na základě a zadání obslužné rutiny ověřování.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="c4b0f-113">`IHttpUpgradeFeature`Definuje podporu [pro upgrady protokolu HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), které umožňují klientovi určit, které další protokoly chce použít, pokud chce server přepínat protokoly.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="c4b0f-114">`IHttpBufferingFeature`Definuje metody pro zakázání ukládání požadavků nebo odpovědí do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="c4b0f-115">`IHttpConnectionFeature`Definuje vlastnosti pro místní a vzdálené adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="c4b0f-116">`IHttpRequestLifetimeFeature`Definuje podporu pro přerušení připojení nebo zjištění, zda byl požadavek předčasně ukončen, například odpojením klienta.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="c4b0f-117">`IHttpSendFileFeature`Definuje metodu pro asynchronní odesílání souborů.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="c4b0f-118">`IHttpWebSocketFeature`Definuje rozhraní API pro podporu webových soketů.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="c4b0f-119">`IHttpRequestIdentifierFeature`Přidá vlastnost, která může být implementována k jednoznačné identifikaci požadavků.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="c4b0f-120">`ISessionFeature`Definuje `ISessionFactory` a `ISession` abstrakce pro podporu uživatelských relací.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="c4b0f-121">`ITlsConnectionFeature`Definuje rozhraní API pro načítání klientských certifikátů.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="c4b0f-122">`ITlsTokenBindingFeature`Definuje metody pro práci s parametry vazby tokenu TLS.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="c4b0f-123">`ISessionFeature`není funkce serveru, ale je implementována `SessionMiddleware` (viz Správa stavu [aplikace](app-state.md)).</span><span class="sxs-lookup"><span data-stu-id="c4b0f-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="c4b0f-124">Kolekce funkcí</span><span class="sxs-lookup"><span data-stu-id="c4b0f-124">Feature collections</span></span>

<span data-ttu-id="c4b0f-125">Vlastnost `Features` `HttpContext` poskytuje rozhraní pro získání a nastavení dostupných funkcí PROTOKOLU HTTP pro aktuální požadavek.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="c4b0f-126">Vzhledem k tomu, že kolekce funkcí je proměnlivá i v rámci požadavku, middlewar lze použít k úpravě kolekce a přidání podpory pro další funkce.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="c4b0f-127">Middleware a funkce požadavků</span><span class="sxs-lookup"><span data-stu-id="c4b0f-127">Middleware and request features</span></span>

<span data-ttu-id="c4b0f-128">Zatímco servery jsou zodpovědné za vytváření kolekce funkcí, middleware můžete přidat do této kolekce a využívat funkce z kolekce.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="c4b0f-129">Například přistupuje `StaticFileMiddleware` k `IHttpSendFileFeature` funkci.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="c4b0f-130">Pokud funkce existuje, slouží k odeslání požadovaného statického souboru z jeho fyzické cesty.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="c4b0f-131">V opačném případě se k odeslání souboru použije pomalejší alternativní metoda.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="c4b0f-132">Pokud `IHttpSendFileFeature` je k dispozici, umožňuje operačnímu systému otevřít soubor a provést přímou kopii režimu jádra na síťovou kartu.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="c4b0f-133">Kromě toho middleware můžete přidat do kolekce funkcí vytvořených serverem.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="c4b0f-134">Stávající funkce mohou být dokonce nahrazeny middleware, což middleware rozšířit funkčnost serveru.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="c4b0f-135">Funkce přidané do kolekce jsou okamžitě k dispozici pro jiné middleware nebo základní aplikace sama později v kanálu požadavku.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="c4b0f-136">Kombinací vlastních serverových implementací a specifických vylepšení middlewaru lze vytvořit přesnou sadu funkcí, které aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="c4b0f-137">To umožňuje přidat chybějící funkce bez nutnosti změny na serveru a zajišťuje pouze minimální množství funkcí, které jsou vystaveny, čímž se omezí plocha útoku a zlepší výkon.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="c4b0f-138">Souhrn</span><span class="sxs-lookup"><span data-stu-id="c4b0f-138">Summary</span></span>

<span data-ttu-id="c4b0f-139">Rozhraní funkcí definují specifické funkce protokolu HTTP, které může daný požadavek podporovat.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="c4b0f-140">Servery definují kolekce funkcí a počáteční sadu funkcí podporovaných tímto serverem, ale middleware lze použít k vylepšení těchto funkcí.</span><span class="sxs-lookup"><span data-stu-id="c4b0f-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4b0f-141">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c4b0f-141">Additional resources</span></span>

* [<span data-ttu-id="c4b0f-142">Servery</span><span class="sxs-lookup"><span data-stu-id="c4b0f-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="c4b0f-143">Middleware</span><span class="sxs-lookup"><span data-stu-id="c4b0f-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c4b0f-144">Otevřené webové rozhraní pro .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="c4b0f-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)

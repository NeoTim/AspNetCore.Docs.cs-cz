---
title: ASP.NET Core SignalR hostování a škálování v produkčním prostředí
author: bradygaster
description: Naučte se, jak se vyhnout problémům s výkonem a škálováním v aplikacích, které používají ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- SignalR
uid: signalr/scale
ms.openlocfilehash: 260e2f0c16288fec2e0a694d070f357529782d8d
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447331"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="3ffda-103">Hostování a škálování signalizace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ffda-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="3ffda-104">Autor [: Andrew Stanton – sestry](https://twitter.com/anurse), [Brady gastera](https://twitter.com/bradygaster)a [Dykstra](https://github.com/tdykstra);</span><span class="sxs-lookup"><span data-stu-id="3ffda-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="3ffda-105">Tento článek vysvětluje informace o hostování a škálování pro aplikace s vysokým provozem, které používají ASP.NET Core Signal.</span><span class="sxs-lookup"><span data-stu-id="3ffda-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="3ffda-106">Rychlé relace</span><span class="sxs-lookup"><span data-stu-id="3ffda-106">Sticky Sessions</span></span>

<span data-ttu-id="3ffda-107">Signál vyžaduje, aby všechny požadavky HTTP pro konkrétní připojení byly zpracovávány stejným procesem serveru.</span><span class="sxs-lookup"><span data-stu-id="3ffda-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="3ffda-108">Když je signál spuštěn na serverové farmě (více serverů), musí se použít rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="3ffda-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="3ffda-109">U některých nástrojů pro vyrovnávání zatížení se také nazývají spřažení relací.</span><span class="sxs-lookup"><span data-stu-id="3ffda-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="3ffda-110">Azure App Service pro směrování požadavků používá [Směrování žádostí na aplikace](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR).</span><span class="sxs-lookup"><span data-stu-id="3ffda-110">Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="3ffda-111">Povolením nastavení spřažení ARR v Azure App Service umožníte "rychlé relace".</span><span class="sxs-lookup"><span data-stu-id="3ffda-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="3ffda-112">Jediné okolnosti, kdy nejsou vyžadovány rychlé relace, jsou:</span><span class="sxs-lookup"><span data-stu-id="3ffda-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="3ffda-113">Při hostování na jednom serveru v jednom procesu.</span><span class="sxs-lookup"><span data-stu-id="3ffda-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="3ffda-114">Při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="3ffda-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="3ffda-115">Pokud jsou všichni klienti nakonfigurovaní tak, aby používali **jenom** objekty WebSockets, **a** v konfiguraci klienta je povolené [Nastavení SkipNegotiation](xref:signalr/configuration#configure-additional-options) .</span><span class="sxs-lookup"><span data-stu-id="3ffda-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="3ffda-116">Ve všech ostatních případech (včetně toho, kdy se používá Redis), musí být serverové prostředí nakonfigurované pro rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="3ffda-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="3ffda-117">Pokyny ke konfiguraci Azure App Service pro Signal najdete v tématu <xref:signalr/publish-to-azure-web-app>.</span><span class="sxs-lookup"><span data-stu-id="3ffda-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="3ffda-118">Prostředky připojení TCP</span><span class="sxs-lookup"><span data-stu-id="3ffda-118">TCP connection resources</span></span>

<span data-ttu-id="3ffda-119">Počet souběžných připojení TCP, která může webový server podporovat, je omezený.</span><span class="sxs-lookup"><span data-stu-id="3ffda-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="3ffda-120">Standardní klienti HTTP používají *dočasné* připojení.</span><span class="sxs-lookup"><span data-stu-id="3ffda-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="3ffda-121">Tato připojení se dají zavřít, když se klient přestane vycházet a znovu otevřít později.</span><span class="sxs-lookup"><span data-stu-id="3ffda-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="3ffda-122">Na druhé straně je připojení k signalizaci *trvalé*.</span><span class="sxs-lookup"><span data-stu-id="3ffda-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="3ffda-123">Připojení k signalizaci zůstávají otevřená i v případě, že klient přestane být činný.</span><span class="sxs-lookup"><span data-stu-id="3ffda-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="3ffda-124">V aplikaci s vysokým provozem, která obsluhuje mnoho klientů, můžou tato trvalá připojení způsobit, že by servery dosáhly maximálního počtu připojení.</span><span class="sxs-lookup"><span data-stu-id="3ffda-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="3ffda-125">Trvalá připojení také využívají určitou další paměť, aby bylo možné sledovat jednotlivá připojení.</span><span class="sxs-lookup"><span data-stu-id="3ffda-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="3ffda-126">Těžké používání prostředků souvisejících s připojením pomocí signálů může mít vliv na další webové aplikace, které jsou hostovány na stejném serveru.</span><span class="sxs-lookup"><span data-stu-id="3ffda-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="3ffda-127">Když se signál otevře a zobrazí poslední dostupná připojení TCP, další webové aplikace na stejném serveru také nemají k dispozici žádná další připojení.</span><span class="sxs-lookup"><span data-stu-id="3ffda-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="3ffda-128">Pokud server nemá připojení, zobrazí se chyby náhodného soketu a chyby resetování připojení.</span><span class="sxs-lookup"><span data-stu-id="3ffda-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="3ffda-129">Například:</span><span class="sxs-lookup"><span data-stu-id="3ffda-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="3ffda-130">Aby bylo možné zabránit využití prostředků signalizace v jiných webových aplikacích, spouštějte signály na různých serverech, než jsou vaše jiné webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="3ffda-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="3ffda-131">Aby bylo možné zabránit využití prostředků signalizace v aplikaci signalizace, nahorizontální navýšení kapacity a omezení počtu připojení, které server musí zpracovat.</span><span class="sxs-lookup"><span data-stu-id="3ffda-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="3ffda-132">Horizontální navýšení kapacity</span><span class="sxs-lookup"><span data-stu-id="3ffda-132">Scale out</span></span>

<span data-ttu-id="3ffda-133">Aplikace, která používá signalizaci, musí sledovat všechna připojení, což vytváří problémy pro serverovou farmu.</span><span class="sxs-lookup"><span data-stu-id="3ffda-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="3ffda-134">Přidejte server a získá nová připojení, o kterých ostatní servery nevědí.</span><span class="sxs-lookup"><span data-stu-id="3ffda-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="3ffda-135">Například signál na každém serveru v následujícím diagramu neznáte připojení na ostatních serverech.</span><span class="sxs-lookup"><span data-stu-id="3ffda-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="3ffda-136">Když signál na jednom ze serverů chce poslat zprávu všem klientům, zpráva se dostane jenom na klienty připojené k tomuto serveru.</span><span class="sxs-lookup"><span data-stu-id="3ffda-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Škálování signálu bez plánu](scale/_static/scale-no-backplane.png)

<span data-ttu-id="3ffda-138">Možnosti řešení tohoto problému jsou [služby signalizace Azure](#azure-signalr-service) a [Redis replánování](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="3ffda-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="3ffda-139">Služba Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="3ffda-139">Azure SignalR Service</span></span>

<span data-ttu-id="3ffda-140">Služba signalizace Azure je proxy místo pro plán.</span><span class="sxs-lookup"><span data-stu-id="3ffda-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="3ffda-141">Pokaždé, když klient inicializuje připojení k serveru, klient se přesměruje, aby se připojil ke službě.</span><span class="sxs-lookup"><span data-stu-id="3ffda-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="3ffda-142">Tento proces je znázorněný v následujícím diagramu:</span><span class="sxs-lookup"><span data-stu-id="3ffda-142">That process is illustrated in the following diagram:</span></span>

![Navázání připojení ke službě Azure Signal](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="3ffda-144">Výsledkem je, že služba spravuje všechna připojení klientů, zatímco každý server potřebuje jenom malý konstantní počet připojení ke službě, jak je znázorněno v následujícím diagramu:</span><span class="sxs-lookup"><span data-stu-id="3ffda-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Klienti připojení ke službě, servery připojené ke službě](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="3ffda-146">Tento přístup k horizontálnímu navýšení kapacity má oproti alternativním Redismu plánu několik výhod:</span><span class="sxs-lookup"><span data-stu-id="3ffda-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="3ffda-147">Rychlé relace, označované také jako [Spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), se nevyžadují, protože klienti se při připojení okamžitě přesměrují do služby Azure Signal.</span><span class="sxs-lookup"><span data-stu-id="3ffda-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="3ffda-148">Aplikace Signal se může škálovat na základě počtu odeslaných zpráv, zatímco služba signálu Azure se automaticky škáluje tak, aby zpracovávala libovolný počet připojení.</span><span class="sxs-lookup"><span data-stu-id="3ffda-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service automatically scales to handle any number of connections.</span></span> <span data-ttu-id="3ffda-149">Může se například jednat o tisíce klientů, ale pokud se pošle jenom pár zpráv za sekundu, aplikace Signal se nebude muset škálovat na víc serverů, aby se daly samotné připojení zvládnout.</span><span class="sxs-lookup"><span data-stu-id="3ffda-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="3ffda-150">Aplikace Signaler nepoužívá podstatně více prostředků připojení, než je webová aplikace bez signalizace.</span><span class="sxs-lookup"><span data-stu-id="3ffda-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="3ffda-151">Z těchto důvodů doporučujeme službu Azure Signal Service pro všechny aplikace signalizace ASP.NET Core hostované v Azure, včetně App Service, virtuálních počítačů a kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="3ffda-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="3ffda-152">Další informace najdete v [dokumentaci ke službě Azure Signal](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="3ffda-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="3ffda-153">Propojovací rozhraní Redis</span><span class="sxs-lookup"><span data-stu-id="3ffda-153">Redis backplane</span></span>

<span data-ttu-id="3ffda-154">[Redis](https://redis.io/) je úložiště klíč-hodnota v paměti, které podporuje systém zasílání zpráv s modelem publikování/odběru.</span><span class="sxs-lookup"><span data-stu-id="3ffda-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="3ffda-155">Redis pro vyřízení signálu používá funkci Pub/sub k posílání zpráv na jiné servery.</span><span class="sxs-lookup"><span data-stu-id="3ffda-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="3ffda-156">Když klient vytvoří připojení, informace o připojení se předávají do plánu.</span><span class="sxs-lookup"><span data-stu-id="3ffda-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="3ffda-157">Když server chce poslat zprávu všem klientům, pošle se do plánu.</span><span class="sxs-lookup"><span data-stu-id="3ffda-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="3ffda-158">Schéma pro naplánování zná všechny připojené klienty a servery, na kterých se nachází.</span><span class="sxs-lookup"><span data-stu-id="3ffda-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="3ffda-159">Pošle zprávu všem klientům přes jejich příslušné servery.</span><span class="sxs-lookup"><span data-stu-id="3ffda-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="3ffda-160">Tento proces je znázorněný v následujícím diagramu:</span><span class="sxs-lookup"><span data-stu-id="3ffda-160">This process is illustrated in the following diagram:</span></span>

![Redis replánování, zpráva odeslaná z jednoho serveru všem klientům](scale/_static/redis-backplane.png)

<span data-ttu-id="3ffda-162">Redis replánování je doporučený postup pro horizontální navýšení kapacity pro aplikace hostované ve vaší vlastní infrastruktuře.</span><span class="sxs-lookup"><span data-stu-id="3ffda-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="3ffda-163">Pokud je mezi datovým centrem a datovým centrem Azure významná latence připojení, služba Azure Signal Service nemusí být praktickou možností pro místní aplikace s požadavky na nízkou latenci nebo vysokou propustnost.</span><span class="sxs-lookup"><span data-stu-id="3ffda-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="3ffda-164">Výše zmíněné výhody služby signálů Azure jsou nevýhody pro Redis replánování:</span><span class="sxs-lookup"><span data-stu-id="3ffda-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="3ffda-165">S výjimkou případů, kdy je splněná **obě** z následujících podmínek, je třeba zadat také relace s rychlým vztahem, která se označuje jako [Spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity):</span><span class="sxs-lookup"><span data-stu-id="3ffda-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="3ffda-166">Všichni klienti jsou nakonfigurovaní tak, aby používali **jenom** objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3ffda-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="3ffda-167">[Nastavení SkipNegotiation](xref:signalr/configuration#configure-additional-options) je v konfiguraci klienta povoleno.</span><span class="sxs-lookup"><span data-stu-id="3ffda-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="3ffda-168">Po zahájení připojení na serveru musí připojení zůstat na tomto serveru.</span><span class="sxs-lookup"><span data-stu-id="3ffda-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="3ffda-169">SignalR aplikace musí škálovat na základě počtu klientů i v případě, že je odesíláno několik zpráv.</span><span class="sxs-lookup"><span data-stu-id="3ffda-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="3ffda-170">SignalR aplikace používá podstatně více prostředků připojení, než je webová aplikace bez SignalR.</span><span class="sxs-lookup"><span data-stu-id="3ffda-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="3ffda-171">Omezení služby IIS na klientském operačním systému Windows</span><span class="sxs-lookup"><span data-stu-id="3ffda-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="3ffda-172">Windows 10 a Windows 8. x jsou klientské operační systémy.</span><span class="sxs-lookup"><span data-stu-id="3ffda-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="3ffda-173">Služba IIS v klientských operačních systémech má omezení 10 souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="3ffda-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="3ffda-174">připojení SignalRjsou:</span><span class="sxs-lookup"><span data-stu-id="3ffda-174">SignalR's connections are:</span></span>

* <span data-ttu-id="3ffda-175">Přechodný a často znovu navázáno.</span><span class="sxs-lookup"><span data-stu-id="3ffda-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="3ffda-176">Neodstraněno okamžitě, pokud **se** už nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="3ffda-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="3ffda-177">Předchozí podmínky mají za to, že na klientském operačním systému budou mít limit 10 připojení.</span><span class="sxs-lookup"><span data-stu-id="3ffda-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="3ffda-178">Když se pro vývoj používá klientský operační systém, doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="3ffda-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="3ffda-179">Vyhněte se službě IIS.</span><span class="sxs-lookup"><span data-stu-id="3ffda-179">Avoid IIS.</span></span>
* <span data-ttu-id="3ffda-180">Jako cíle nasazení použijte Kestrel nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="3ffda-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="3ffda-181">Linux na serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="3ffda-181">Linux with Nginx</span></span>

<span data-ttu-id="3ffda-182">Pro SignalR WebSockets nastavte `Connection` a `Upgrade` hlavičku proxy serveru na následující:</span><span class="sxs-lookup"><span data-stu-id="3ffda-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="3ffda-183">Další informace najdete v tématu [Nginx jako proxy server WebSocket](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="3ffda-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="third-party-opno-locsignalr-backplane-providers"></a><span data-ttu-id="3ffda-184">Poskytovatelé přeplánování SignalR třetích stran</span><span class="sxs-lookup"><span data-stu-id="3ffda-184">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="3ffda-185">NCache</span><span class="sxs-lookup"><span data-stu-id="3ffda-185">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="3ffda-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="3ffda-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="3ffda-187">Další kroky</span><span class="sxs-lookup"><span data-stu-id="3ffda-187">Next steps</span></span>

<span data-ttu-id="3ffda-188">Další informace najdete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="3ffda-188">For more information, see the following resources:</span></span>

* <span data-ttu-id="3ffda-189">[Dokumentace ke službě Azure SignalR](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="3ffda-189">[Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview)</span></span>
* [<span data-ttu-id="3ffda-190">Nastavení Redisho plánu</span><span class="sxs-lookup"><span data-stu-id="3ffda-190">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)

---
title: ASP.NET Core SignalR hostování a škálování provozu
author: bradygaster
description: Naučte se, jak se vyhnout problémům s výkonem a škálováním v aplikacích, které používají ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/scale
ms.openlocfilehash: 2bfe05748e6740043be7f1ccc6dbe22ad4b0ca44
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722563"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a><span data-ttu-id="9f5f4-103">ASP.NET Core SignalR hostování a škálování</span><span class="sxs-lookup"><span data-stu-id="9f5f4-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="9f5f4-104">[Andrew Stanton-sestry](https://twitter.com/anurse), [Brady gastera](https://twitter.com/bradygaster)a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9f5f4-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="9f5f4-105">Tento článek vysvětluje, jaké jsou informace o hostování a škálování pro aplikace s vysokým provozem, které používají ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="9f5f4-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="9f5f4-106">Rychlé relace</span><span class="sxs-lookup"><span data-stu-id="9f5f4-106">Sticky Sessions</span></span>

<span data-ttu-id="9f5f4-107">SignalR vyžaduje, aby všechny požadavky HTTP pro konkrétní připojení byly zpracovávány stejným procesem serveru.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="9f5f4-108">Když SignalR je spuštěná na serverové farmě (víc serverů), musí se použít "rychlé relace".</span><span class="sxs-lookup"><span data-stu-id="9f5f4-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="9f5f4-109">U některých nástrojů pro vyrovnávání zatížení se také nazývají spřažení relací.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="9f5f4-110">Azure App Service pro směrování požadavků používá [Směrování žádostí na aplikace](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR).</span><span class="sxs-lookup"><span data-stu-id="9f5f4-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="9f5f4-111">Povolením nastavení spřažení ARR v Azure App Service umožníte "rychlé relace".</span><span class="sxs-lookup"><span data-stu-id="9f5f4-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="9f5f4-112">Jediné okolnosti, kdy nejsou vyžadovány rychlé relace, jsou:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="9f5f4-113">Při hostování na jednom serveru v jednom procesu.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="9f5f4-114">Při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="9f5f4-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="9f5f4-115">Pokud jsou všichni klienti nakonfigurovaní tak, aby používali **jenom** objekty WebSockets, **a** v konfiguraci klienta je povolené [Nastavení SkipNegotiation](xref:signalr/configuration#configure-additional-options) .</span><span class="sxs-lookup"><span data-stu-id="9f5f4-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="9f5f4-116">Ve všech ostatních případech (včetně toho, kdy se používá Redis), musí být serverové prostředí nakonfigurované pro rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="9f5f4-117">Pokyny ke konfiguraci Azure App Service pro SignalR najdete v tématu <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="9f5f4-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="9f5f4-118">Prostředky připojení TCP</span><span class="sxs-lookup"><span data-stu-id="9f5f4-118">TCP connection resources</span></span>

<span data-ttu-id="9f5f4-119">Počet souběžných připojení TCP, která může webový server podporovat, je omezený.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="9f5f4-120">Standardní klienti HTTP používají *dočasné* připojení.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="9f5f4-121">Tato připojení se dají zavřít, když se klient přestane vycházet a znovu otevřít později.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="9f5f4-122">Na druhé straně SignalR je připojení *trvalé*.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="9f5f4-123">SignalR připojení zůstávají otevřená i v případě, že klient přejde do nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="9f5f4-124">V aplikaci s vysokým provozem, která obsluhuje mnoho klientů, můžou tato trvalá připojení způsobit, že by servery dosáhly maximálního počtu připojení.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="9f5f4-125">Trvalá připojení také využívají určitou další paměť, aby bylo možné sledovat jednotlivá připojení.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="9f5f4-126">Těžké využívání prostředků souvisejících s připojením SignalR může ovlivnit jiné webové aplikace, které jsou hostovány na stejném serveru.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="9f5f4-127">Když SignalR se otevře a zobrazí poslední dostupná připojení TCP, další webové aplikace na stejném serveru také nemají k dispozici žádná další připojení.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="9f5f4-128">Pokud server nemá připojení, zobrazí se chyby náhodného soketu a chyby resetování připojení.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="9f5f4-129">Například:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="9f5f4-130">Aby SignalR využití prostředků mohlo způsobit chyby v jiných webových aplikacích, spouštějte SignalR na různých serverech, než jsou vaše jiné webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="9f5f4-131">Aby SignalR využití prostředků mohlo způsobovat chyby v SignalR aplikaci, horizontální navýšení kapacity a omezení počtu připojení, které server musí zpracovat</span><span class="sxs-lookup"><span data-stu-id="9f5f4-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="9f5f4-132">Horizontální navýšení kapacity</span><span class="sxs-lookup"><span data-stu-id="9f5f4-132">Scale out</span></span>

<span data-ttu-id="9f5f4-133">Aplikace, která využívá SignalR potřebnou evidenci všech připojení, která vytváří problémy pro serverovou farmu.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="9f5f4-134">Přidejte server a získá nová připojení, o kterých ostatní servery nevědí.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="9f5f4-135">Například SignalR na každém serveru v následujícím diagramu neznáte připojení na ostatních serverech.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="9f5f4-136">Když SignalR na jednom ze serverů přeje odeslat zprávu všem klientům, zpráva se dostane jenom na klienty připojené k tomuto serveru.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Škálování::: No – Loc (Signal)::: bez plánu](scale/_static/scale-no-backplane.png)

<span data-ttu-id="9f5f4-138">Možnosti řešení tohoto problému jsou [ SignalR služby Azure](#azure-signalr-service) a [Redis replánování](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="9f5f4-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-no-locsignalr-service"></a><span data-ttu-id="9f5f4-139">SignalRSlužba Azure</span><span class="sxs-lookup"><span data-stu-id="9f5f4-139">Azure SignalR Service</span></span>

<span data-ttu-id="9f5f4-140">Služba Azure SignalR je proxy místo pro plán.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="9f5f4-141">Pokaždé, když klient inicializuje připojení k serveru, klient se přesměruje, aby se připojil ke službě.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="9f5f4-142">Tento proces je znázorněný v následujícím diagramu:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-142">That process is illustrated in the following diagram:</span></span>

![Navazování připojení k Azure::: No-Loc (Signal)::: Service](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="9f5f4-144">Výsledkem je, že služba spravuje všechna připojení klientů, zatímco každý server potřebuje jenom malý konstantní počet připojení ke službě, jak je znázorněno v následujícím diagramu:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Klienti připojení ke službě, servery připojené ke službě](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="9f5f4-146">Tento přístup k horizontálnímu navýšení kapacity má oproti alternativním Redismu plánu několik výhod:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="9f5f4-147">Relace typu Sticky, označované také jako [Spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), nejsou vyžadovány, protože klienti jsou při připojení okamžitě přesměrováni na SignalR službu Azure.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="9f5f4-148">SignalRAplikace se může škálovat na základě počtu odeslaných zpráv, zatímco služba Azure se SignalR škáluje tak, aby zpracovávala libovolný počet připojení.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="9f5f4-149">Může se například jednat o tisíce klientů, ale pokud se pošle jenom pár zpráv za sekundu, SignalR aplikace nebude muset škálovat na víc serverů, aby bylo možné připojení sami zpracovat.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="9f5f4-150">SignalRAplikace nepoužívá podstatně víc prostředků připojení, než je webová aplikace SignalR .</span><span class="sxs-lookup"><span data-stu-id="9f5f4-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="9f5f4-151">Z těchto důvodů doporučujeme SignalR službu Azure pro všechny SignalR aplikace ASP.NET Core hostované v Azure, včetně App Service, virtuálních počítačů a kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="9f5f4-152">Další informace najdete v [dokumentaci ke SignalR službě Azure](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="9f5f4-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="9f5f4-153">Propojovací rozhraní Redis</span><span class="sxs-lookup"><span data-stu-id="9f5f4-153">Redis backplane</span></span>

<span data-ttu-id="9f5f4-154">[Redis](https://redis.io/) je úložiště klíč-hodnota v paměti, které podporuje systém zasílání zpráv s modelem publikování/odběru.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="9f5f4-155">SignalRRedis replánování používá funkci Pub/sub k posílání zpráv na jiné servery.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="9f5f4-156">Když klient vytvoří připojení, informace o připojení se předávají do plánu.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="9f5f4-157">Když server chce poslat zprávu všem klientům, pošle se do plánu.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="9f5f4-158">Schéma pro naplánování zná všechny připojené klienty a servery, na kterých se nachází.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="9f5f4-159">Pošle zprávu všem klientům přes jejich příslušné servery.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="9f5f4-160">Tento proces je znázorněný v následujícím diagramu:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-160">This process is illustrated in the following diagram:</span></span>

![Redis replánování, zpráva odeslaná z jednoho serveru všem klientům](scale/_static/redis-backplane.png)

<span data-ttu-id="9f5f4-162">Redis replánování je doporučený postup pro horizontální navýšení kapacity pro aplikace hostované ve vaší vlastní infrastruktuře.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="9f5f4-163">Pokud je mezi datovým centrem a datovým centrem Azure významná latence připojení, služba Azure SignalR nemusí být praktickou možností pro místní aplikace s požadavky na nízkou latenci nebo vysokou propustnost.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="9f5f4-164">SignalRVýše zmíněné výhody služby Azure jsou nevýhody pro Redis:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="9f5f4-165">S výjimkou případů, kdy je splněná **obě** z následujících podmínek, je třeba zadat také relace s rychlým vztahem, která se označuje jako [Spřažení klienta](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity):</span><span class="sxs-lookup"><span data-stu-id="9f5f4-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="9f5f4-166">Všichni klienti jsou nakonfigurovaní tak, aby používali **jenom** objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="9f5f4-167">[Nastavení SkipNegotiation](xref:signalr/configuration#configure-additional-options) je v konfiguraci klienta povoleno.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="9f5f4-168">Po zahájení připojení na serveru musí připojení zůstat na tomto serveru.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="9f5f4-169">SignalRAplikace musí škálovat na základě počtu klientů i v případě, že je odesíláno několik zpráv.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="9f5f4-170">SignalRAplikace používá podstatně více prostředků připojení než webová aplikace bez SignalR .</span><span class="sxs-lookup"><span data-stu-id="9f5f4-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="9f5f4-171">Omezení služby IIS na klientském operačním systému Windows</span><span class="sxs-lookup"><span data-stu-id="9f5f4-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="9f5f4-172">Windows 10 a Windows 8. x jsou klientské operační systémy.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="9f5f4-173">Služba IIS v klientských operačních systémech má omezení 10 souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="9f5f4-174">SignalRjsou připojení:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-174">SignalR's connections are:</span></span>

* <span data-ttu-id="9f5f4-175">Přechodný a často znovu navázáno.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="9f5f4-176">Neodstraněno okamžitě, pokud **se** už nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="9f5f4-177">Předchozí podmínky mají za to, že na klientském operačním systému budou mít limit 10 připojení.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="9f5f4-178">Když se pro vývoj používá klientský operační systém, doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="9f5f4-179">Vyhněte se službě IIS.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-179">Avoid IIS.</span></span>
* <span data-ttu-id="9f5f4-180">Jako cíle nasazení použijte Kestrel nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="9f5f4-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="9f5f4-181">Linux na serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="9f5f4-181">Linux with Nginx</span></span>

<span data-ttu-id="9f5f4-182">`Connection` `Upgrade` Pro objekty WebSockets nastavte proxy a záhlaví na následující SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f5f4-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="9f5f4-183">Další informace najdete v tématu [Nginx jako proxy server WebSocket](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="9f5f4-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="third-party-no-locsignalr-backplane-providers"></a><span data-ttu-id="9f5f4-184">SignalRPoskytovatelé pro replánování třetích stran</span><span class="sxs-lookup"><span data-stu-id="9f5f4-184">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="9f5f4-185">NCache</span><span class="sxs-lookup"><span data-stu-id="9f5f4-185">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="9f5f4-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="9f5f4-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="9f5f4-187">Další kroky</span><span class="sxs-lookup"><span data-stu-id="9f5f4-187">Next steps</span></span>

<span data-ttu-id="9f5f4-188">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="9f5f4-188">For more information, see the following resources:</span></span>

* [<span data-ttu-id="9f5f4-189">SignalRDokumentace ke službě Azure</span><span class="sxs-lookup"><span data-stu-id="9f5f4-189">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="9f5f4-190">Nastavení Redisho plánu</span><span class="sxs-lookup"><span data-stu-id="9f5f4-190">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
---
title: Redis propojovacího rozhraní pro horizontální navýšení kapacity funkce SignalR technologie ASP.NET Core
author: tdykstra
description: Zjistěte, jak nastavit propojovací rozhraní Redis umožňuje škálování aplikace SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: c8b09c0d482da344b54d167c0c9757167eaa6186
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52453011"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="5b27e-103">Nastavit propojovací rozhraní Redis pro horizontální navýšení kapacity funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b27e-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="5b27e-104">Podle [Andrew Stanton sestry](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), a [Petr Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="5b27e-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="5b27e-105">Tento článek vysvětluje aspekty SignalR konkrétní nastavení [Redis](https://redis.io/) server pro horizontální navýšení kapacity aplikace SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b27e-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="5b27e-106">Nastavit propojovací rozhraní Redis</span><span class="sxs-lookup"><span data-stu-id="5b27e-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="5b27e-107">Nasazení serveru Redis.</span><span class="sxs-lookup"><span data-stu-id="5b27e-107">Deploy a Redis server.</span></span>

  <span data-ttu-id="5b27e-108">Pro použití v produkčním prostředí propojovací rozhraní Redis je doporučeno pouze pro místní infrastrukturu.</span><span class="sxs-lookup"><span data-stu-id="5b27e-108">For production use, a Redis backplane is recommended only for on-premises infrastructure.</span></span> <span data-ttu-id="5b27e-109">Abyste minimalizovali latenci serveru Redis by měl být ve stejném datovém centru jako aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="5b27e-109">To minimize latency, the Redis server should be in the same data center as the SignalR app.</span></span> <span data-ttu-id="5b27e-110">Pokud vaše aplikace SignalR běží v cloudu Azure, doporučujeme namísto Redis propojovací rozhraní služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5b27e-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="5b27e-111">Můžete použít službu Azure Redis Cache pro vývojové a testovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="5b27e-111">You can use the Azure Redis Cache Service for development and test environments.</span></span> <span data-ttu-id="5b27e-112">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="5b27e-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="5b27e-113">Dokumentace ke službě redis</span><span class="sxs-lookup"><span data-stu-id="5b27e-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="5b27e-114">Dokumentace ke službě Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5b27e-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/en-us/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="5b27e-115">V aplikaci SignalR, nainstalujte `Microsoft.AspNetCore.SignalR.Redis` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="5b27e-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>

* <span data-ttu-id="5b27e-116">V `Startup.ConfigureServices` metody, volání `AddRedis` po `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="5b27e-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="5b27e-117">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="5b27e-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="5b27e-118">Většinu možností lze nastavit v připojovacím řetězci nebo v [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objektu.</span><span class="sxs-lookup"><span data-stu-id="5b27e-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="5b27e-119">Možnosti zadané v `ConfigurationOptions` přepsat ty nastavit v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="5b27e-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="5b27e-120">Následující příklad ukazuje, jak nastavit možnosti `ConfigurationOptions` objektu.</span><span class="sxs-lookup"><span data-stu-id="5b27e-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="5b27e-121">V tomto příkladu přidá předponu kanál tak, aby stejné instance Redis může sdílet více aplikací, jak je popsáno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="5b27e-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="5b27e-122">V předchozím kódu `options.Configuration` je inicializována s cokoli, co byl zadán v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="5b27e-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="5b27e-123">V aplikaci SignalR, nainstalujte `Microsoft.AspNetCore.SignalR.StackExchangeRedis` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="5b27e-123">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.StackExchangeRedis` NuGet package.</span></span>

* <span data-ttu-id="5b27e-124">V `Startup.ConfigureServices` metody, volání `AddStackExchangeRedis` po `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="5b27e-124">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="5b27e-125">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="5b27e-125">Configure options as needed:</span></span>
 
  <span data-ttu-id="5b27e-126">Většinu možností lze nastavit v připojovacím řetězci nebo v [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objektu.</span><span class="sxs-lookup"><span data-stu-id="5b27e-126">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="5b27e-127">Možnosti zadané v `ConfigurationOptions` přepsat ty nastavit v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="5b27e-127">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="5b27e-128">Následující příklad ukazuje, jak nastavit možnosti `ConfigurationOptions` objektu.</span><span class="sxs-lookup"><span data-stu-id="5b27e-128">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="5b27e-129">V tomto příkladu přidá předponu kanál tak, aby stejné instance Redis může sdílet více aplikací, jak je popsáno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="5b27e-129">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="5b27e-130">V předchozím kódu `options.Configuration` je inicializována s cokoli, co byl zadán v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="5b27e-130">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="5b27e-131">Informace o možnostech Redis, najdete v článku [StackExchange Redis dokumentaci](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="5b27e-131">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="5b27e-132">Pokud používáte jeden server Redis pro více aplikací SignalR, použijte předponu jiný kanál pro každou aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="5b27e-132">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="5b27e-133">Nastavení kanálu předponu izoluje jedna aplikace SignalR od ostatních, které používají jiný kanál předpony.</span><span class="sxs-lookup"><span data-stu-id="5b27e-133">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="5b27e-134">Pokud nechcete přiřadit odlišné předpony, zpráv odesílaných z jedné aplikace do všech svých vlastních klientů přejdete na všechny klienty ze všech aplikací, které používají Redis server jako propojovací rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5b27e-134">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="5b27e-135">Konfigurace vašeho serveru farmy Vyrovnávání zatížení softwaru pro rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="5b27e-135">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="5b27e-136">Tady je několik příkladů dokumentace o tom, jak to udělat:</span><span class="sxs-lookup"><span data-stu-id="5b27e-136">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="5b27e-137">SLUŽBA IIS</span><span class="sxs-lookup"><span data-stu-id="5b27e-137">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="5b27e-138">Haproxy:</span><span class="sxs-lookup"><span data-stu-id="5b27e-138">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="5b27e-139">Server Nginx</span><span class="sxs-lookup"><span data-stu-id="5b27e-139">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="5b27e-140">pfSense</span><span class="sxs-lookup"><span data-stu-id="5b27e-140">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="5b27e-141">Chyby serveru redis</span><span class="sxs-lookup"><span data-stu-id="5b27e-141">Redis server errors</span></span>

<span data-ttu-id="5b27e-142">Když Redis server přestane fungovat, SignalR vyvolá výjimky, které označují, že se nebudou doručovat zprávy.</span><span class="sxs-lookup"><span data-stu-id="5b27e-142">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="5b27e-143">Některé typické výjimka zprávy:</span><span class="sxs-lookup"><span data-stu-id="5b27e-143">Some typical exception messages:</span></span>

* <span data-ttu-id="5b27e-144">*Neúspěšné zapisované zprávě*</span><span class="sxs-lookup"><span data-stu-id="5b27e-144">*Failed writing message*</span></span>
* <span data-ttu-id="5b27e-145">*Nepovedlo se vyvolat metodu rozbočovače na "MethodName.*</span><span class="sxs-lookup"><span data-stu-id="5b27e-145">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="5b27e-146">*Připojení k Redis se nezdařilo*</span><span class="sxs-lookup"><span data-stu-id="5b27e-146">*Connection to Redis failed*</span></span>

<span data-ttu-id="5b27e-147">Funkce SignalR nemá vyrovnávací paměť zprávy k odeslání je při přechodu serveru zpět.</span><span class="sxs-lookup"><span data-stu-id="5b27e-147">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="5b27e-148">Všechny zprávy odeslané při odstávce serveru Redis se ztratí.</span><span class="sxs-lookup"><span data-stu-id="5b27e-148">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="5b27e-149">SignalR automaticky znovu připojí, když Redis server je opět k dispozici.</span><span class="sxs-lookup"><span data-stu-id="5b27e-149">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="5b27e-150">Vlastní chování pro chyby připojení</span><span class="sxs-lookup"><span data-stu-id="5b27e-150">Custom behavior for connection failures</span></span>

<span data-ttu-id="5b27e-151">Tady je příklad, který ukazuje, jak zpracovávat události selhání připojení Redis.</span><span class="sxs-lookup"><span data-stu-id="5b27e-151">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="clustering"></a><span data-ttu-id="5b27e-152">Vytváření clusterů</span><span class="sxs-lookup"><span data-stu-id="5b27e-152">Clustering</span></span>

<span data-ttu-id="5b27e-153">Clustering je metoda pro dosažení vysoké dostupnosti s využitím více serverů Redis.</span><span class="sxs-lookup"><span data-stu-id="5b27e-153">Clustering is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="5b27e-154">Vytváření clusterů není oficiálně podporován, ale může fungovat.</span><span class="sxs-lookup"><span data-stu-id="5b27e-154">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5b27e-155">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5b27e-155">Next steps</span></span>

<span data-ttu-id="5b27e-156">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="5b27e-156">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="5b27e-157">Dokumentace ke službě redis</span><span class="sxs-lookup"><span data-stu-id="5b27e-157">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="5b27e-158">Dokumentace ke službě StackExchange Redis</span><span class="sxs-lookup"><span data-stu-id="5b27e-158">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="5b27e-159">Dokumentace ke službě Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5b27e-159">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/en-us/azure/redis-cache/)

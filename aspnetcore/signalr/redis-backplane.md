---
title: Redis propojovacího rozhraní pro horizontální navýšení kapacity funkce SignalR technologie ASP.NET Core
author: bradygaster
description: Zjistěte, jak nastavit propojovací rozhraní Redis umožňuje škálování aplikace SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: 9d2a942dba6abe669126efee7f2b3cdd6560658e
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087670"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="058cf-103">Nastavit propojovací rozhraní Redis pro horizontální navýšení kapacity funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="058cf-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="058cf-104">Podle [Andrew Stanton sestry](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), a [Petr Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="058cf-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="058cf-105">Tento článek vysvětluje aspekty SignalR konkrétní nastavení [Redis](https://redis.io/) server pro horizontální navýšení kapacity aplikace SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="058cf-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="058cf-106">Nastavit propojovací rozhraní Redis</span><span class="sxs-lookup"><span data-stu-id="058cf-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="058cf-107">Nasazení serveru Redis.</span><span class="sxs-lookup"><span data-stu-id="058cf-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="058cf-108">Pro použití v produkčním prostředí se doporučuje propojovacího rozhraní Redis pouze v případě, že běží ve stejném datovém centru jako aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="058cf-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="058cf-109">V opačném případě latence sítě snižuje výkon.</span><span class="sxs-lookup"><span data-stu-id="058cf-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="058cf-110">Pokud vaše aplikace SignalR běží v cloudu Azure, doporučujeme namísto Redis propojovací rozhraní služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="058cf-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="058cf-111">Můžete použít službu Azure Redis Cache pro vývojové a testovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="058cf-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="058cf-112">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="058cf-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="058cf-113">Dokumentace ke službě redis</span><span class="sxs-lookup"><span data-stu-id="058cf-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="058cf-114">Dokumentace ke službě Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="058cf-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="058cf-115">V aplikaci SignalR, nainstalujte `Microsoft.AspNetCore.SignalR.Redis` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="058cf-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="058cf-116">(K dispozici je také `Microsoft.AspNetCore.SignalR.StackExchangeRedis` balíček, ale, že jeden je pro ASP.NET Core 2.2 a novější.)</span><span class="sxs-lookup"><span data-stu-id="058cf-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="058cf-117">V `Startup.ConfigureServices` metody, volání `AddRedis` po `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="058cf-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="058cf-118">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="058cf-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="058cf-119">Většinu možností lze nastavit v připojovacím řetězci nebo v [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objektu.</span><span class="sxs-lookup"><span data-stu-id="058cf-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="058cf-120">Možnosti zadané v `ConfigurationOptions` přepsat ty nastavit v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="058cf-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="058cf-121">Následující příklad ukazuje, jak nastavit možnosti `ConfigurationOptions` objektu.</span><span class="sxs-lookup"><span data-stu-id="058cf-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="058cf-122">V tomto příkladu přidá předponu kanál tak, aby stejné instance Redis může sdílet více aplikací, jak je popsáno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="058cf-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="058cf-123">V předchozím kódu `options.Configuration` je inicializována s cokoli, co byl zadán v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="058cf-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="058cf-124">V aplikaci SignalR nainstalujte některou z následujících balíčků NuGet:</span><span class="sxs-lookup"><span data-stu-id="058cf-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="058cf-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` -Závisí na StackExchange.Redis 2.X.X.</span><span class="sxs-lookup"><span data-stu-id="058cf-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="058cf-126">Toto je doporučený balíček pro ASP.NET Core 2.2 a novější.</span><span class="sxs-lookup"><span data-stu-id="058cf-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="058cf-127">`Microsoft.AspNetCore.SignalR.Redis` -Závisí na StackExchange.Redis 1.X.X.</span><span class="sxs-lookup"><span data-stu-id="058cf-127">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="058cf-128">Tento balíček nebudou přenosů v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="058cf-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="058cf-129">V `Startup.ConfigureServices` metody, volání `AddStackExchangeRedis` po `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="058cf-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="058cf-130">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="058cf-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="058cf-131">Většinu možností lze nastavit v připojovacím řetězci nebo v [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objektu.</span><span class="sxs-lookup"><span data-stu-id="058cf-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="058cf-132">Možnosti zadané v `ConfigurationOptions` přepsat ty nastavit v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="058cf-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="058cf-133">Následující příklad ukazuje, jak nastavit možnosti `ConfigurationOptions` objektu.</span><span class="sxs-lookup"><span data-stu-id="058cf-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="058cf-134">V tomto příkladu přidá předponu kanál tak, aby stejné instance Redis může sdílet více aplikací, jak je popsáno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="058cf-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="058cf-135">V předchozím kódu `options.Configuration` je inicializována s cokoli, co byl zadán v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="058cf-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="058cf-136">Informace o možnostech Redis, najdete v článku [StackExchange Redis dokumentaci](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="058cf-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="058cf-137">Pokud používáte jeden server Redis pro více aplikací SignalR, použijte předponu jiný kanál pro každou aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="058cf-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="058cf-138">Nastavení kanálu předponu izoluje jedna aplikace SignalR od ostatních, které používají jiný kanál předpony.</span><span class="sxs-lookup"><span data-stu-id="058cf-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="058cf-139">Pokud nechcete přiřadit odlišné předpony, zpráv odesílaných z jedné aplikace do všech svých vlastních klientů přejdete na všechny klienty ze všech aplikací, které používají Redis server jako propojovací rozhraní.</span><span class="sxs-lookup"><span data-stu-id="058cf-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="058cf-140">Konfigurace vašeho serveru farmy Vyrovnávání zatížení softwaru pro rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="058cf-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="058cf-141">Tady je několik příkladů dokumentace o tom, jak to udělat:</span><span class="sxs-lookup"><span data-stu-id="058cf-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="058cf-142">SLUŽBA IIS</span><span class="sxs-lookup"><span data-stu-id="058cf-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="058cf-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="058cf-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="058cf-144">Server Nginx</span><span class="sxs-lookup"><span data-stu-id="058cf-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="058cf-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="058cf-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="058cf-146">Chyby serveru redis</span><span class="sxs-lookup"><span data-stu-id="058cf-146">Redis server errors</span></span>

<span data-ttu-id="058cf-147">Když Redis server přestane fungovat, SignalR vyvolá výjimky, které označují, že se nebudou doručovat zprávy.</span><span class="sxs-lookup"><span data-stu-id="058cf-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="058cf-148">Některé typické výjimka zprávy:</span><span class="sxs-lookup"><span data-stu-id="058cf-148">Some typical exception messages:</span></span>

* <span data-ttu-id="058cf-149">*Neúspěšné zapisované zprávě*</span><span class="sxs-lookup"><span data-stu-id="058cf-149">*Failed writing message*</span></span>
* <span data-ttu-id="058cf-150">*Nepovedlo se vyvolat metodu rozbočovače na "MethodName.*</span><span class="sxs-lookup"><span data-stu-id="058cf-150">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="058cf-151">*Připojení k Redis se nezdařilo*</span><span class="sxs-lookup"><span data-stu-id="058cf-151">*Connection to Redis failed*</span></span>

<span data-ttu-id="058cf-152">Funkce SignalR nemá vyrovnávací paměť zprávy k odeslání je při přechodu serveru zpět.</span><span class="sxs-lookup"><span data-stu-id="058cf-152">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="058cf-153">Všechny zprávy odeslané při odstávce serveru Redis se ztratí.</span><span class="sxs-lookup"><span data-stu-id="058cf-153">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="058cf-154">SignalR automaticky znovu připojí, když Redis server je opět k dispozici.</span><span class="sxs-lookup"><span data-stu-id="058cf-154">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="058cf-155">Vlastní chování pro chyby připojení</span><span class="sxs-lookup"><span data-stu-id="058cf-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="058cf-156">Tady je příklad, který ukazuje, jak zpracovávat události selhání připojení Redis.</span><span class="sxs-lookup"><span data-stu-id="058cf-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="clustering"></a><span data-ttu-id="058cf-157">Vytváření clusterů</span><span class="sxs-lookup"><span data-stu-id="058cf-157">Clustering</span></span>

<span data-ttu-id="058cf-158">Clustering je metoda pro dosažení vysoké dostupnosti s využitím více serverů Redis.</span><span class="sxs-lookup"><span data-stu-id="058cf-158">Clustering is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="058cf-159">Vytváření clusterů není oficiálně podporován, ale může fungovat.</span><span class="sxs-lookup"><span data-stu-id="058cf-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="058cf-160">Další kroky</span><span class="sxs-lookup"><span data-stu-id="058cf-160">Next steps</span></span>

<span data-ttu-id="058cf-161">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="058cf-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="058cf-162">Dokumentace ke službě redis</span><span class="sxs-lookup"><span data-stu-id="058cf-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="058cf-163">Dokumentace ke službě StackExchange Redis</span><span class="sxs-lookup"><span data-stu-id="058cf-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="058cf-164">Dokumentace ke službě Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="058cf-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

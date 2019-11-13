---
title: Redis pro SignalR horizontální navýšení kapacity ASP.NET Core
author: bradygaster
description: Naučte se, jak nastavit Rediselné rozšíření pro ASP.NET Core SignalR aplikaci.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 379d46fcaabb8eb0d04e521a5ad698229f947b7c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963921"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a><span data-ttu-id="2eee0-103">Nastavení ASP.NET Coreho plánu pro SignalR škálování na více instancí Redis</span><span class="sxs-lookup"><span data-stu-id="2eee0-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="2eee0-104">Autor [: Andrew Stanton – sestry](https://twitter.com/anurse), [Brady gastera](https://twitter.com/bradygaster)a [Dykstra](https://github.com/tdykstra);</span><span class="sxs-lookup"><span data-stu-id="2eee0-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="2eee0-105">Tento článek vysvětluje SignalRspecifické aspekty nastavení serveru [Redis](https://redis.io/) pro použití pro horizontální navýšení SignalR aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2eee0-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="2eee0-106">Nastavení Redisho plánu</span><span class="sxs-lookup"><span data-stu-id="2eee0-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="2eee0-107">Nasazení serveru Redis</span><span class="sxs-lookup"><span data-stu-id="2eee0-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="2eee0-108">Pro produkční použití se doporučuje Redisý plán, který je spuštěný ve stejném datovém centru jako aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="2eee0-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="2eee0-109">V opačném případě latence sítě snižuje výkon.</span><span class="sxs-lookup"><span data-stu-id="2eee0-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="2eee0-110">Pokud je vaše aplikace SignalR spuštěná v cloudu Azure, doporučujeme místo plánu Redise použít službu Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="2eee0-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="2eee0-111">Službu Azure Redis Cache můžete použít pro vývojová a testovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="2eee0-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="2eee0-112">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="2eee0-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="2eee0-113">Dokumentace k Redis</span><span class="sxs-lookup"><span data-stu-id="2eee0-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="2eee0-114">Dokumentace k Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="2eee0-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="2eee0-115">V aplikaci SignalR nainstalujte balíček NuGet `Microsoft.AspNetCore.SignalR.Redis`.</span><span class="sxs-lookup"><span data-stu-id="2eee0-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="2eee0-116">(K dispozici je také balíček `Microsoft.AspNetCore.SignalR.StackExchangeRedis`, ale ten je určen pro ASP.NET Core 2,2 a novější.)</span><span class="sxs-lookup"><span data-stu-id="2eee0-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="2eee0-117">V metodě `Startup.ConfigureServices` volejte `AddRedis` po `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="2eee0-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="2eee0-118">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="2eee0-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="2eee0-119">Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="2eee0-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="2eee0-120">Možnosti zadané v `ConfigurationOptions` přepíší ty nastavené v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="2eee0-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="2eee0-121">Následující příklad ukazuje, jak nastavit možnosti v objektu `ConfigurationOptions`.</span><span class="sxs-lookup"><span data-stu-id="2eee0-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="2eee0-122">Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="2eee0-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="2eee0-123">V předchozím kódu je `options.Configuration` inicializován s jakýmkoli parametrem v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="2eee0-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="2eee0-124">V aplikaci SignalR nainstalujte jeden z následujících balíčků NuGet:</span><span class="sxs-lookup"><span data-stu-id="2eee0-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="2eee0-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` – závisí na StackExchange. Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="2eee0-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="2eee0-126">Toto je doporučený balíček pro ASP.NET Core 2,2 a novější.</span><span class="sxs-lookup"><span data-stu-id="2eee0-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="2eee0-127">`Microsoft.AspNetCore.SignalR.Redis` – závisí na StackExchange. Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="2eee0-127">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="2eee0-128">Tento balíček nebude expedován v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2eee0-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="2eee0-129">V metodě `Startup.ConfigureServices` volejte `AddStackExchangeRedis` po `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="2eee0-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="2eee0-130">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="2eee0-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="2eee0-131">Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="2eee0-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="2eee0-132">Možnosti zadané v `ConfigurationOptions` přepíší ty nastavené v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="2eee0-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="2eee0-133">Následující příklad ukazuje, jak nastavit možnosti v objektu `ConfigurationOptions`.</span><span class="sxs-lookup"><span data-stu-id="2eee0-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="2eee0-134">Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="2eee0-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="2eee0-135">V předchozím kódu je `options.Configuration` inicializován s jakýmkoli parametrem v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="2eee0-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="2eee0-136">Informace o možnostech Redis najdete v [dokumentaci k stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="2eee0-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="2eee0-137">Pokud pro více SignalR aplikací používáte jeden server Redis, použijte pro každou aplikaci SignalR jinou předponu kanálu.</span><span class="sxs-lookup"><span data-stu-id="2eee0-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="2eee0-138">Nastavení prefixu kanálu izoluje jednu SignalR aplikaci od ostatních, která používá jiné předpony kanálů.</span><span class="sxs-lookup"><span data-stu-id="2eee0-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="2eee0-139">Pokud nepřiřazujete jiné předpony, zpráva odeslaná z jedné aplikace všem svým vlastním klientům přejde ke všem klientům všech aplikací, které používají server Redis jako replánování.</span><span class="sxs-lookup"><span data-stu-id="2eee0-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="2eee0-140">Nakonfigurujte software pro vyrovnávání zatížení serverové farmy pro rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="2eee0-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="2eee0-141">Tady je několik příkladů dokumentace k tomu, jak to udělat:</span><span class="sxs-lookup"><span data-stu-id="2eee0-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="2eee0-142">SLUŽBU</span><span class="sxs-lookup"><span data-stu-id="2eee0-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="2eee0-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="2eee0-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="2eee0-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="2eee0-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="2eee0-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="2eee0-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="2eee0-146">Chyby serveru Redis</span><span class="sxs-lookup"><span data-stu-id="2eee0-146">Redis server errors</span></span>

<span data-ttu-id="2eee0-147">Když dojde k výpadku serveru Redis, SignalR vyvolá výjimky, které naznačují, že se zprávy nebudou doručovat.</span><span class="sxs-lookup"><span data-stu-id="2eee0-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="2eee0-148">Některé typické zprávy o výjimce:</span><span class="sxs-lookup"><span data-stu-id="2eee0-148">Some typical exception messages:</span></span>

* <span data-ttu-id="2eee0-149">*Zpráva se nepodařilo zapsat.*</span><span class="sxs-lookup"><span data-stu-id="2eee0-149">*Failed writing message*</span></span>
* <span data-ttu-id="2eee0-150">*Nepovedlo se vyvolat metodu centra ' MethodName '*</span><span class="sxs-lookup"><span data-stu-id="2eee0-150">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="2eee0-151">*Připojení k Redis se nezdařilo.*</span><span class="sxs-lookup"><span data-stu-id="2eee0-151">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="2eee0-152"> neukládá zprávy do vyrovnávací paměti k odeslání při zálohování serveru.</span><span class="sxs-lookup"><span data-stu-id="2eee0-152"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="2eee0-153">Všechny zprávy odeslané během výpadku serveru Redis jsou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="2eee0-153">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="2eee0-154"> se automaticky znovu připojí, když je server Redis k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2eee0-154"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="2eee0-155">Vlastní chování při selhání připojení</span><span class="sxs-lookup"><span data-stu-id="2eee0-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="2eee0-156">Tady je příklad, který ukazuje, jak zpracovávat události selhání připojení Redis.</span><span class="sxs-lookup"><span data-stu-id="2eee0-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="2eee0-157">Clustering Redis</span><span class="sxs-lookup"><span data-stu-id="2eee0-157">Redis Clustering</span></span>

<span data-ttu-id="2eee0-158">[Clustering Redis](https://redis.io/topics/cluster-spec) je metoda pro dosažení vysoké dostupnosti pomocí více serverů Redis.</span><span class="sxs-lookup"><span data-stu-id="2eee0-158">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="2eee0-159">Clustering není oficiálně podporovaný, ale může fungovat.</span><span class="sxs-lookup"><span data-stu-id="2eee0-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2eee0-160">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2eee0-160">Next steps</span></span>

<span data-ttu-id="2eee0-161">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="2eee0-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="2eee0-162">Dokumentace k Redis</span><span class="sxs-lookup"><span data-stu-id="2eee0-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="2eee0-163">Dokumentace k Redis pro StackExchange</span><span class="sxs-lookup"><span data-stu-id="2eee0-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="2eee0-164">Dokumentace k Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="2eee0-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

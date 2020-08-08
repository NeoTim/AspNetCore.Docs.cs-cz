---
title: Redis pro horizontální navýšení SignalR kapacity ASP.NET Core
author: bradygaster
description: Naučte se, jak nastavit Rediselné rozšíření pro zajištění horizontálního navýšení kapacity pro ASP.NET Core SignalR aplikaci.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: d80bce204b467d436a3580933718147bc3fbe75c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022482"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a><span data-ttu-id="3a074-103">Nastavení Redisho plánu pro horizontální navýšení SignalR kapacity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a074-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="3a074-104">Autor [: Andrew Stanton – sestry](https://twitter.com/anurse), [Brady gastera](https://twitter.com/bradygaster)a [Dykstra](https://github.com/tdykstra);</span><span class="sxs-lookup"><span data-stu-id="3a074-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="3a074-105">Tento článek vysvětluje SignalR konkrétní aspekty nastavení serveru [Redis](https://redis.io/) pro použití při škálování SignalR aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a074-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="3a074-106">Nastavení Redisho plánu</span><span class="sxs-lookup"><span data-stu-id="3a074-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="3a074-107">Nasazení serveru Redis</span><span class="sxs-lookup"><span data-stu-id="3a074-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="3a074-108">Pro produkční použití se doporučuje Redisý plán, který je spuštěný ve stejném datovém centru jako SignalR aplikace.</span><span class="sxs-lookup"><span data-stu-id="3a074-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="3a074-109">V opačném případě latence sítě snižuje výkon.</span><span class="sxs-lookup"><span data-stu-id="3a074-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="3a074-110">Pokud SignalR je vaše aplikace spuštěná v cloudu Azure, doporučujeme SignalR místo Redisho plánu použít službu Azure.</span><span class="sxs-lookup"><span data-stu-id="3a074-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="3a074-111">Službu Azure Redis Cache můžete použít pro vývojová a testovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="3a074-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="3a074-112">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="3a074-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="3a074-113">Dokumentace k Redis</span><span class="sxs-lookup"><span data-stu-id="3a074-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="3a074-114">Dokumentace k Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3a074-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="3a074-115">V SignalR aplikaci nainstalujte `Microsoft.AspNetCore.SignalR.Redis` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="3a074-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="3a074-116">V `Startup.ConfigureServices` metodě zavolejte `AddRedis` po `AddSignalR` :</span><span class="sxs-lookup"><span data-stu-id="3a074-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="3a074-117">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="3a074-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="3a074-118">Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="3a074-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="3a074-119">Možnosti, které jsou zadány v `ConfigurationOptions` přepsání, jsou nastaveny v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="3a074-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="3a074-120">Následující příklad ukazuje, jak nastavit možnosti v `ConfigurationOptions` objektu.</span><span class="sxs-lookup"><span data-stu-id="3a074-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="3a074-121">Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="3a074-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="3a074-122">V předchozím kódu `options.Configuration` je inicializován s jakýmkoli parametrem v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="3a074-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="3a074-123">V SignalR aplikaci nainstalujte jeden z následujících balíčků NuGet:</span><span class="sxs-lookup"><span data-stu-id="3a074-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="3a074-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`– Závisí na StackExchange. Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="3a074-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="3a074-125">Toto je doporučený balíček pro ASP.NET Core 2,2 a novější.</span><span class="sxs-lookup"><span data-stu-id="3a074-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="3a074-126">`Microsoft.AspNetCore.SignalR.Redis`– Závisí na StackExchange. Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="3a074-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="3a074-127">Tento balíček není zahrnutý v ASP.NET Core 3,0 a novějším.</span><span class="sxs-lookup"><span data-stu-id="3a074-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="3a074-128">V `Startup.ConfigureServices` metodě zavolejte <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="3a074-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="3a074-129">Při použití `Microsoft.AspNetCore.SignalR.Redis` volání metody <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="3a074-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="3a074-130">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="3a074-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="3a074-131">Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="3a074-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="3a074-132">Možnosti, které jsou zadány v `ConfigurationOptions` přepsání, jsou nastaveny v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="3a074-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="3a074-133">Následující příklad ukazuje, jak nastavit možnosti v `ConfigurationOptions` objektu.</span><span class="sxs-lookup"><span data-stu-id="3a074-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="3a074-134">Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="3a074-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="3a074-135">Při použití `Microsoft.AspNetCore.SignalR.Redis` volání metody <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="3a074-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="3a074-136">V předchozím kódu `options.Configuration` je inicializován s jakýmkoli parametrem v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="3a074-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="3a074-137">Informace o možnostech Redis najdete v [dokumentaci k stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="3a074-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="3a074-138">V SignalR aplikaci nainstalujte následující balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="3a074-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="3a074-139">V `Startup.ConfigureServices` metodě zavolejte <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="3a074-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="3a074-140">Podle potřeby nakonfigurujte možnosti:</span><span class="sxs-lookup"><span data-stu-id="3a074-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="3a074-141">Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="3a074-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="3a074-142">Možnosti, které jsou zadány v `ConfigurationOptions` přepsání, jsou nastaveny v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="3a074-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="3a074-143">Následující příklad ukazuje, jak nastavit možnosti v `ConfigurationOptions` objektu.</span><span class="sxs-lookup"><span data-stu-id="3a074-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="3a074-144">Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.</span><span class="sxs-lookup"><span data-stu-id="3a074-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="3a074-145">V předchozím kódu `options.Configuration` je inicializován s jakýmkoli parametrem v připojovacím řetězci.</span><span class="sxs-lookup"><span data-stu-id="3a074-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="3a074-146">Informace o možnostech Redis najdete v [dokumentaci k stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="3a074-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="3a074-147">Pokud pro více aplikací používáte jeden server Redis SignalR , použijte pro každou aplikaci jinou předponu kanálu SignalR .</span><span class="sxs-lookup"><span data-stu-id="3a074-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="3a074-148">Nastavení prefixu kanálu izoluje jednu SignalR aplikaci od ostatních, která používá jiné předpony kanálů.</span><span class="sxs-lookup"><span data-stu-id="3a074-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="3a074-149">Pokud nepřiřazujete jiné předpony, zpráva odeslaná z jedné aplikace všem svým vlastním klientům přejde ke všem klientům všech aplikací, které používají server Redis jako replánování.</span><span class="sxs-lookup"><span data-stu-id="3a074-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="3a074-150">Nakonfigurujte software pro vyrovnávání zatížení serverové farmy pro rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="3a074-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="3a074-151">Tady je několik příkladů dokumentace k tomu, jak to udělat:</span><span class="sxs-lookup"><span data-stu-id="3a074-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="3a074-152">IIS</span><span class="sxs-lookup"><span data-stu-id="3a074-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="3a074-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="3a074-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="3a074-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="3a074-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="3a074-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="3a074-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="3a074-156">Chyby serveru Redis</span><span class="sxs-lookup"><span data-stu-id="3a074-156">Redis server errors</span></span>

<span data-ttu-id="3a074-157">Když dojde k výpadku serveru Redis, SignalR vyvolá výjimku, která indikuje, že se nebudou doručovat zprávy.</span><span class="sxs-lookup"><span data-stu-id="3a074-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="3a074-158">Některé typické zprávy o výjimce:</span><span class="sxs-lookup"><span data-stu-id="3a074-158">Some typical exception messages:</span></span>

* <span data-ttu-id="3a074-159">*Zpráva se nepodařilo zapsat.*</span><span class="sxs-lookup"><span data-stu-id="3a074-159">*Failed writing message*</span></span>
* <span data-ttu-id="3a074-160">*Nepovedlo se vyvolat metodu centra ' MethodName '*</span><span class="sxs-lookup"><span data-stu-id="3a074-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="3a074-161">*Připojení k Redis se nezdařilo.*</span><span class="sxs-lookup"><span data-stu-id="3a074-161">*Connection to Redis failed*</span></span>

<span data-ttu-id="3a074-162">SignalRneukládá zprávy do vyrovnávací paměti pro odeslání při zálohování serveru.</span><span class="sxs-lookup"><span data-stu-id="3a074-162">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="3a074-163">Všechny zprávy odeslané během výpadku serveru Redis jsou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="3a074-163">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="3a074-164">SignalRautomaticky znovu připojí, jakmile bude server Redis k dispozici.</span><span class="sxs-lookup"><span data-stu-id="3a074-164">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="3a074-165">Vlastní chování při selhání připojení</span><span class="sxs-lookup"><span data-stu-id="3a074-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="3a074-166">Tady je příklad, který ukazuje, jak zpracovávat události selhání připojení Redis.</span><span class="sxs-lookup"><span data-stu-id="3a074-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="3a074-167">Clustering Redis</span><span class="sxs-lookup"><span data-stu-id="3a074-167">Redis Clustering</span></span>

<span data-ttu-id="3a074-168">[Clustering Redis](https://redis.io/topics/cluster-spec) je metoda pro dosažení vysoké dostupnosti pomocí více serverů Redis.</span><span class="sxs-lookup"><span data-stu-id="3a074-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="3a074-169">Clustering není oficiálně podporovaný, ale může fungovat.</span><span class="sxs-lookup"><span data-stu-id="3a074-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3a074-170">Další kroky</span><span class="sxs-lookup"><span data-stu-id="3a074-170">Next steps</span></span>

<span data-ttu-id="3a074-171">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="3a074-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="3a074-172">Dokumentace k Redis</span><span class="sxs-lookup"><span data-stu-id="3a074-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="3a074-173">Dokumentace k Redis pro StackExchange</span><span class="sxs-lookup"><span data-stu-id="3a074-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="3a074-174">Dokumentace k Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3a074-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

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
ms.openlocfilehash: 0461fc6a212ba78111bc2054cca74951721c5820
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289042"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a>Nastavení ASP.NET Coreho plánu pro SignalR škálování na více instancí Redis

Autor [: Andrew Stanton – sestry](https://twitter.com/anurse), [Brady gastera](https://twitter.com/bradygaster)a [Dykstra](https://github.com/tdykstra);

Tento článek vysvětluje SignalRspecifické aspekty nastavení serveru [Redis](https://redis.io/) pro použití pro horizontální navýšení SignalR aplikace ASP.NET Core.

## <a name="set-up-a-redis-backplane"></a>Nastavení Redisho plánu

* Nasazení serveru Redis

  > [!IMPORTANT] 
  > Pro produkční použití se doporučuje Redisý plán, který je spuštěný ve stejném datovém centru jako aplikace SignalR. V opačném případě latence sítě snižuje výkon. Pokud je vaše aplikace SignalR spuštěná v cloudu Azure, doporučujeme místo plánu Redise použít službu Azure SignalR. Službu Azure Redis Cache můžete použít pro vývojová a testovací prostředí.

  Další informace naleznete v následujících materiálech:

  * <xref:signalr/scale>
  * [Dokumentace k Redis](https://redis.io/)
  * [Dokumentace k Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* V aplikaci SignalR nainstalujte balíček NuGet `Microsoft.AspNetCore.SignalR.Redis`.
* V metodě `Startup.ConfigureServices` volejte `AddRedis` po `AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Podle potřeby nakonfigurujte možnosti:
 
  Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Možnosti zadané v `ConfigurationOptions` přepíší ty nastavené v připojovacím řetězci.

  Následující příklad ukazuje, jak nastavit možnosti v objektu `ConfigurationOptions`. Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  V předchozím kódu je `options.Configuration` inicializován s jakýmkoli parametrem v připojovacím řetězci.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* V aplikaci SignalR nainstalujte jeden z následujících balíčků NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` – závisí na StackExchange. Redis 2. X.X. Toto je doporučený balíček pro ASP.NET Core 2,2 a novější.
  * `Microsoft.AspNetCore.SignalR.Redis` – závisí na StackExchange. Redis 1. X.X. Tento balíček není zahrnutý v ASP.NET Core 3,0 a novějším.

* V metodě `Startup.ConfigureServices` volejte <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Při použití `Microsoft.AspNetCore.SignalR.Redis`volejte <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

* Podle potřeby nakonfigurujte možnosti:
 
  Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Možnosti zadané v `ConfigurationOptions` přepíší ty nastavené v připojovacím řetězci.

  Následující příklad ukazuje, jak nastavit možnosti v objektu `ConfigurationOptions`. Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Při použití `Microsoft.AspNetCore.SignalR.Redis`volejte <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

  V předchozím kódu je `options.Configuration` inicializován s jakýmkoli parametrem v připojovacím řetězci.

  Informace o možnostech Redis najdete v [dokumentaci k stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* V aplikaci SignalR nainstalujte následující balíček NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* V metodě `Startup.ConfigureServices` volejte <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Podle potřeby nakonfigurujte možnosti:
 
  Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Možnosti zadané v `ConfigurationOptions` přepíší ty nastavené v připojovacím řetězci.

  Následující příklad ukazuje, jak nastavit možnosti v objektu `ConfigurationOptions`. Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  V předchozím kódu je `options.Configuration` inicializován s jakýmkoli parametrem v připojovacím řetězci.

  Informace o možnostech Redis najdete v [dokumentaci k stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Pokud pro více SignalR aplikací používáte jeden server Redis, použijte pro každou aplikaci SignalR jinou předponu kanálu.

  Nastavení prefixu kanálu izoluje jednu SignalR aplikaci od ostatních, která používá jiné předpony kanálů. Pokud nepřiřazujete jiné předpony, zpráva odeslaná z jedné aplikace všem svým vlastním klientům přejde ke všem klientům všech aplikací, které používají server Redis jako replánování.

* Nakonfigurujte software pro vyrovnávání zatížení serverové farmy pro rychlé relace. Tady je několik příkladů dokumentace k tomu, jak to udělat:

  * [SLUŽBA IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Chyby serveru Redis

Když dojde k výpadku serveru Redis, SignalR vyvolá výjimky, které naznačují, že se zprávy nebudou doručovat. Některé typické zprávy o výjimce:

* *Zpráva se nepodařilo zapsat.*
* *Nepovedlo se vyvolat metodu centra ' MethodName '*
* *Připojení k Redis se nezdařilo.*

SignalR neukládá zprávy do vyrovnávací paměti k odeslání při zálohování serveru. Všechny zprávy odeslané během výpadku serveru Redis jsou ztraceny.

SignalR se automaticky znovu připojí, když je server Redis k dispozici.

### <a name="custom-behavior-for-connection-failures"></a>Vlastní chování při selhání připojení

Tady je příklad, který ukazuje, jak zpracovávat události selhání připojení Redis.

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

## <a name="redis-clustering"></a>Clustering Redis

[Clustering Redis](https://redis.io/topics/cluster-spec) je metoda pro dosažení vysoké dostupnosti pomocí více serverů Redis. Clustering není oficiálně podporovaný, ale může fungovat.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

* <xref:signalr/scale>
* [Dokumentace k Redis](https://redis.io/documentation)
* [Dokumentace k Redis pro StackExchange](https://stackexchange.github.io/StackExchange.Redis/)
* [Dokumentace k Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/)

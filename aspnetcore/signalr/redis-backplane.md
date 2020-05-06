---
title: Redis pro horizontální navýšení SignalR kapacity ASP.NET Core
author: bradygaster
description: Naučte se, jak nastavit Rediselné rozšíření pro zajištění horizontálního navýšení kapacity pro SignalR ASP.NET Core aplikaci.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 6068890f4089a13add05bf8cf8009367e343adce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775346"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a>Nastavení Redisho plánu pro horizontální navýšení SignalR kapacity ASP.NET Core

Autor [: Andrew Stanton – sestry](https://twitter.com/anurse), [Brady gastera](https://twitter.com/bradygaster)a [Dykstra](https://github.com/tdykstra);

Tento článek vysvětluje SignalRkonkrétní aspekty nastavení serveru [Redis](https://redis.io/) pro použití při škálování aplikace ASP.NET Core SignalR .

## <a name="set-up-a-redis-backplane"></a>Nastavení Redisho plánu

* Nasazení serveru Redis

  > [!IMPORTANT] 
  > Pro produkční použití se doporučuje Redisý plán, který je spuštěný ve stejném datovém centru jako SignalR aplikace. V opačném případě latence sítě snižuje výkon. Pokud je SignalR vaše aplikace spuštěná v cloudu Azure, doporučujeme místo SignalR Redisho plánu použít službu Azure. Službu Azure Redis Cache můžete použít pro vývojová a testovací prostředí.

  Další informace najdete v následujících materiálech:

  * <xref:signalr/scale>
  * [Dokumentace k Redis](https://redis.io/)
  * [Dokumentace k Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* V SignalR aplikaci nainstalujte balíček `Microsoft.AspNetCore.SignalR.Redis` NuGet.
* V `Startup.ConfigureServices` metodě zavolejte `AddRedis` po: `AddSignalR`

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Podle potřeby nakonfigurujte možnosti:
 
  Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Možnosti, které `ConfigurationOptions` jsou zadány v přepsání, jsou nastaveny v připojovacím řetězci.

  Následující příklad ukazuje, jak nastavit možnosti v `ConfigurationOptions` objektu. Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  V předchozím kódu `options.Configuration` je inicializován s jakýmkoli parametrem v připojovacím řetězci.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* V SignalR aplikaci nainstalujte jeden z následujících balíčků NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`– Závisí na StackExchange. Redis 2. X.X. Toto je doporučený balíček pro ASP.NET Core 2,2 a novější.
  * `Microsoft.AspNetCore.SignalR.Redis`– Závisí na StackExchange. Redis 1. X.X. Tento balíček není zahrnutý v ASP.NET Core 3,0 a novějším.

* V `Startup.ConfigureServices` metodě zavolejte <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Při použití `Microsoft.AspNetCore.SignalR.Redis`volání metody <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

* Podle potřeby nakonfigurujte možnosti:
 
  Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Možnosti, které `ConfigurationOptions` jsou zadány v přepsání, jsou nastaveny v připojovacím řetězci.

  Následující příklad ukazuje, jak nastavit možnosti v `ConfigurationOptions` objektu. Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Při použití `Microsoft.AspNetCore.SignalR.Redis`volání metody <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

  V předchozím kódu `options.Configuration` je inicializován s jakýmkoli parametrem v připojovacím řetězci.

  Informace o možnostech Redis najdete v [dokumentaci k stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* V SignalR aplikaci nainstalujte následující balíček NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* V `Startup.ConfigureServices` metodě zavolejte <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Podle potřeby nakonfigurujte možnosti:
 
  Většinu možností lze nastavit v připojovacím řetězci nebo v objektu [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Možnosti, které `ConfigurationOptions` jsou zadány v přepsání, jsou nastaveny v připojovacím řetězci.

  Následující příklad ukazuje, jak nastavit možnosti v `ConfigurationOptions` objektu. Tento příklad přidá předponu kanálu, aby více aplikací mohl sdílet stejnou instanci Redis, jak je vysvětleno v následujícím kroku.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  V předchozím kódu `options.Configuration` je inicializován s jakýmkoli parametrem v připojovacím řetězci.

  Informace o možnostech Redis najdete v [dokumentaci k stackexchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Pokud pro více SignalR aplikací používáte jeden server Redis, použijte pro každou SignalR aplikaci jinou předponu kanálu.

  Nastavení prefixu kanálu izoluje jednu SignalR aplikaci od ostatních, která používá jiné předpony kanálů. Pokud nepřiřazujete jiné předpony, zpráva odeslaná z jedné aplikace všem svým vlastním klientům přejde ke všem klientům všech aplikací, které používají server Redis jako replánování.

* Nakonfigurujte software pro vyrovnávání zatížení serverové farmy pro rychlé relace. Tady je několik příkladů dokumentace k tomu, jak to udělat:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Chyby serveru Redis

Když dojde k výpadku serveru Redis SignalR , vyvolá výjimku, která indikuje, že se nebudou doručovat zprávy. Některé typické zprávy o výjimce:

* *Zpráva se nepodařilo zapsat.*
* *Nepovedlo se vyvolat metodu centra ' MethodName '*
* *Připojení k Redis se nezdařilo.*

SignalRneukládá zprávy do vyrovnávací paměti pro odeslání při zálohování serveru. Všechny zprávy odeslané během výpadku serveru Redis jsou ztraceny.

SignalRautomaticky znovu připojí, jakmile bude server Redis k dispozici.

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

Další informace najdete v následujících materiálech:

* <xref:signalr/scale>
* [Dokumentace k Redis](https://redis.io/documentation)
* [Dokumentace k Redis pro StackExchange](https://stackexchange.github.io/StackExchange.Redis/)
* [Dokumentace k Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/)

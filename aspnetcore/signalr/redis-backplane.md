---
title: Redis propojovacího rozhraní pro horizontální navýšení kapacity funkce SignalR technologie ASP.NET Core
author: bradygaster
description: Zjistěte, jak nastavit propojovací rozhraní Redis umožňuje škálování aplikace SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: c02d8cd5fb3b6edbb21be4889da2e880099b731b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837439"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a>Nastavit propojovací rozhraní Redis pro horizontální navýšení kapacity funkce SignalR technologie ASP.NET Core

Podle [Andrew Stanton sestry](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), a [Petr Dykstra](https://github.com/tdykstra),

Tento článek vysvětluje aspekty SignalR konkrétní nastavení [Redis](https://redis.io/) server pro horizontální navýšení kapacity aplikace SignalR technologie ASP.NET Core.

## <a name="set-up-a-redis-backplane"></a>Nastavit propojovací rozhraní Redis

* Nasazení serveru Redis.

  > [!IMPORTANT] 
  > Pro použití v produkčním prostředí se doporučuje propojovacího rozhraní Redis pouze v případě, že běží ve stejném datovém centru jako aplikace SignalR. V opačném případě latence sítě snižuje výkon. Pokud vaše aplikace SignalR běží v cloudu Azure, doporučujeme namísto Redis propojovací rozhraní služby Azure SignalR. Můžete použít službu Azure Redis Cache pro vývojové a testovací prostředí.

  Další informace naleznete v následujících materiálech:

  * <xref:signalr/scale>
  * [Dokumentace ke službě redis](https://redis.io/)
  * [Dokumentace ke službě Azure Redis Cache](https://docs.microsoft.com/en-us/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* V aplikaci SignalR, nainstalujte `Microsoft.AspNetCore.SignalR.Redis` balíček NuGet. (K dispozici je také `Microsoft.AspNetCore.SignalR.StackExchangeRedis` balíček, ale, že jeden je pro ASP.NET Core 2.2 a novější.)

* V `Startup.ConfigureServices` metody, volání `AddRedis` po `AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Podle potřeby nakonfigurujte možnosti:
 
  Většinu možností lze nastavit v připojovacím řetězci nebo v [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objektu. Možnosti zadané v `ConfigurationOptions` přepsat ty nastavit v připojovacím řetězci.

  Následující příklad ukazuje, jak nastavit možnosti `ConfigurationOptions` objektu. V tomto příkladu přidá předponu kanál tak, aby stejné instance Redis může sdílet více aplikací, jak je popsáno v následujícím kroku.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  V předchozím kódu `options.Configuration` je inicializována s cokoli, co byl zadán v připojovacím řetězci.

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* V aplikaci SignalR nainstalujte některou z následujících balíčků NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` -Závisí na StackExchange.Redis 2.X.X. Toto je doporučený balíček pro ASP.NET Core 2.2 a novější.
  * `Microsoft.AspNetCore.SignalR.Redis` -Závisí na StackExchange.Redis 1.X.X. Tento balíček nebudou přenosů v ASP.NET Core 3.0.

* V `Startup.ConfigureServices` metody, volání `AddStackExchangeRedis` po `AddSignalR`:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* Podle potřeby nakonfigurujte možnosti:
 
  Většinu možností lze nastavit v připojovacím řetězci nebo v [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objektu. Možnosti zadané v `ConfigurationOptions` přepsat ty nastavit v připojovacím řetězci.

  Následující příklad ukazuje, jak nastavit možnosti `ConfigurationOptions` objektu. V tomto příkladu přidá předponu kanál tak, aby stejné instance Redis může sdílet více aplikací, jak je popsáno v následujícím kroku.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  V předchozím kódu `options.Configuration` je inicializována s cokoli, co byl zadán v připojovacím řetězci.

  Informace o možnostech Redis, najdete v článku [StackExchange Redis dokumentaci](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Pokud používáte jeden server Redis pro více aplikací SignalR, použijte předponu jiný kanál pro každou aplikaci SignalR.

  Nastavení kanálu předponu izoluje jedna aplikace SignalR od ostatních, které používají jiný kanál předpony. Pokud nechcete přiřadit odlišné předpony, zpráv odesílaných z jedné aplikace do všech svých vlastních klientů přejdete na všechny klienty ze všech aplikací, které používají Redis server jako propojovací rozhraní.

* Konfigurace vašeho serveru farmy Vyrovnávání zatížení softwaru pro rychlé relace. Tady je několik příkladů dokumentace o tom, jak to udělat:

  * [SLUŽBA IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Server Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Chyby serveru redis

Když Redis server přestane fungovat, SignalR vyvolá výjimky, které označují, že se nebudou doručovat zprávy. Některé typické výjimka zprávy:

* *Neúspěšné zapisované zprávě*
* *Nepovedlo se vyvolat metodu rozbočovače na "MethodName.*
* *Připojení k Redis se nezdařilo*

Funkce SignalR nemá vyrovnávací paměť zprávy k odeslání je při přechodu serveru zpět. Všechny zprávy odeslané při odstávce serveru Redis se ztratí.

SignalR automaticky znovu připojí, když Redis server je opět k dispozici.

### <a name="custom-behavior-for-connection-failures"></a>Vlastní chování pro chyby připojení

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

## <a name="clustering"></a>Vytváření clusterů

Clustering je metoda pro dosažení vysoké dostupnosti s využitím více serverů Redis. Vytváření clusterů není oficiálně podporován, ale může fungovat.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

* <xref:signalr/scale>
* [Dokumentace ke službě redis](https://redis.io/documentation)
* [Dokumentace ke službě StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/)
* [Dokumentace ke službě Azure Redis Cache](https://docs.microsoft.com/en-us/azure/redis-cache/)

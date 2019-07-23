---
title: Migrace služeb gRPC z C jádra ASP.NET Core
author: juntaoluo
description: Zjistěte, jak přesunout existující aplikaci na základě gRPC C jádra pro spuštění nad rámec zásobníku ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 47d74edd821124f0c8390d704ca7931b7eb6c4cd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901674"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrace služeb gRPC z C jádra ASP.NET Core

Podle [Luo Jan](https://github.com/juntaoluo)

Kvůli implementaci základní zásobníku, některé funkce fungovat stejně jako mezi [C-podle počtu jader gRPC](https://grpc.io/blog/grpc-stacks) aplikací a aplikací pro ASP.NET Core. Tento dokument popisuje klíčové rozdíly pro migraci mezi dvěma zásobníků.

## <a name="grpc-service-implementation-lifetime"></a>Doba platnosti implementace gRPC služby

V zásobníku ASP.NET Core gRPC services ve výchozím nastavení, se vytvoří s [s vymezeným oborem životnost](xref:fundamentals/dependency-injection#service-lifetimes). Naproti tomu gRPC C core ve výchozím nastavení vytvoří vazbu ke službě s [singleton životnost](xref:fundamentals/dependency-injection#service-lifetimes).

S vymezeným oborem životnost umožňuje implementaci služby k vyřešení dalších služeb pomocí s vymezeným oborem platnosti. Například můžete také vyřešit s vymezeným oborem životnost `DBContext` z kontejnerů DI prostřednictvím konstruktoru vkládání. Pomocí vymezených životnost:

* Nová instance implementace služby je vytvořen pro každý požadavek.
* Není možné sdílení stavu mezi žádostí prostřednictvím členy instance na typu implementace.
* Očekává se, k ukládání sdílené stavy do služby jednotlivý prvek v kontejneru DI. Uložené sdílené stavy jsou vyřešeny v konstruktoru gRPC implementaci služby.

Další informace o životnosti služby najdete v tématu <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Přidání služby typu singleton

Pro usnadnění přechodu od implementace C core gRPC k ASP.NET Core, je možné změnit životnost služby implementaci služby z oboru na jednotlivý prvek. To zahrnuje přidání do kontejnerů DI instance implementace služby:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Implementace služby s životností jednotlivý prvek je však již nemůže přeložit vymezené služby prostřednictvím konstruktoru vkládání.

## <a name="configure-grpc-services-options"></a>Nakonfigurujte možnosti služby gRPC

V aplikacích jazyka C-podle počtu jader, nastavení, jako `grpc.max_receive_message_length` a `grpc.max_send_message_length` jsou nakonfigurovány s `ChannelOption` při [vytváření instance serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

GRPC v ASP.NET Core, poskytuje konfiguraci prostřednictvím `GrpcServiceOptions` typu. Například gRPC služby maximální velikost příchozí zprávy můžete nakonfigurovat přes `AddGrpc`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16384; // 16 MB
    });
}
```

Další informace o konfiguraci najdete v tématu <xref:grpc/configuration>.

## <a name="logging"></a>Protokolování

Využívají aplikace s C-podle počtu jader `GrpcEnvironment` k [konfiguraci protokolovacího nástroje](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) pro účely ladění. Tuto funkci prostřednictvím poskytuje zásobníku ASP.NET Core [protokolování rozhraní API](xref:fundamentals/logging/index). Protokolovací nástroj lze například přidat ke službě gRPC prostřednictvím konstruktoru vkládání:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Aplikace s C-podle počtu jader konfigurace HTTPS prostřednictvím [Server.Ports vlastnost](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core. Například používá Kestrel [konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) pro tuto funkci.

## <a name="interceptors-and-middleware"></a>Sběrače a Middleware

ASP.NET Core [middleware](xref:fundamentals/middleware/index) nabízí podobné funkce, které jsou ve srovnání s sběrače v aplikacích jazyka C-podle počtu jader gRPC. Middleware a sběrače jsou koncepčně stejné jako se používá k vytvoření kanálu, který zpracovává požadavek gRPC. Oba umožňují práci prováděnou před nebo po další komponenta v kanálu. Však middleware ASP.NET Core funguje v základní zprávy HTTP/2, zatímco sběrače provádět gRPC vrstva abstrakce pomocí [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

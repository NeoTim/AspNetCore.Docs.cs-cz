---
title: Migrace služeb gRPC z C jádra ASP.NET Core
author: juntaoluo
description: Zjistěte, jak přesunout existující aplikaci na základě gRPC C jádra pro spuštění nad rámec zásobníku ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/migration
ms.openlocfilehash: 3c6e04694a33e953f6e1575f5ee9b0699cf1cdd3
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58207955"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrace služeb gRPC z C jádra ASP.NET Core

Podle [Luo Jan](https://github.com/juntaoluo)

Kvůli implementaci základní zásobníku, některé funkce fungovat stejně jako mezi [C jádra na základě gRPC](https://grpc.io/blog/grpc-stacks) aplikací založených na aplikace a ASP.NET Core. Tento dokument popisuje klíčové rozdíly si při migraci mezi dvěma zásobníků.

## <a name="grpc-service-implementation-lifetime"></a>Doba platnosti implementace gRPC služby

V zásobníku ASP.NET Core gRPC services ve výchozím nastavení, bude vytvořena s [obor životnost](xref:fundamentals/dependency-injection). Naproti tomu gRPC C core ve výchozím nastavení vytvoří vazbu na službu s životností typu Singleton.

Doba života obor umožňuje implementace služby k vyřešení dalším službám pomocí životnosti obor. Například můžete také vyřešit obor životnost `DBContext`, od kontejnerů DI prostřednictvím konstruktoru vkládání. Pomocí obor životnost:

* Nová instance implementace služby je vytvořen pro každý požadavek.
* Není možné sdílení stavu mezi žádostí prostřednictvím členy instance na typu implementace.
* Očekává se, k ukládání sdílené stavy do služby jednotlivý prvek v kontejneru DI. Uložené sdílené stavy jsou vyřešeny v konstruktoru gRPC implementaci služby. 

Další informace o životnost obor a Singleton, naleznete v tématu <xref:fundamentals/dependency-injection>.

### <a name="add-a-singleton-service"></a>Přidání služby typu singleton

Pro usnadnění přechodu od gRPC C základní implementace pro ASP.NET Core, je možné změnit životnost služby implementaci služby z obor na jednotlivý prvek. To zahrnuje přidání do kontejnerů DI instance implementace služby:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Však implementace služby s dobou života typu Singleton již bude schopen převést obor služeb prostřednictvím konstruktoru vkládání.

## <a name="configure-grpc-services-options"></a>Nakonfigurujte možnosti služby gRPC

V jazyce C core na základě aplikace, nastavení, jako `grpc.max_receive_message_length` a `grpc.max_send_message_length` jsou nakonfigurovány s `ChannelOption` při [vytváření `Server` instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

V ASP.NET Core `GrpcServiceOptions` poskytuje způsob, jak nakonfigurovat tato nastavení. Nastavení můžete použít globálně ke všem službám gRPC nebo typ implementace jednotlivých služeb. Možnosti zadané pro tyto typy implementace jednotlivých služeb se přepsat globální nastavení, pokud je nakonfigurované.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddGrpc(globalOptions =>
        {
            // Global settings
            globalOptions.SendMaxMessageSize = 4096
            globalOptions.ReceiveMaxMessageSize = 4096
        })
        .AddServiceOptions<GreeterService>(greeterOptions =>
        {
            // GreeterService settings. These will override global settings
            globalOptions.SendMaxMessageSize = 2048
            globalOptions.ReceiveMaxMessageSize = 2048
        })
}
```

## <a name="logging"></a>Protokolování

Na základě aplikací C core závisí na `GrpcEnvironment` k [konfiguraci protokolovacího nástroje](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) pro účely ladění. Tuto funkci prostřednictvím poskytuje zásobníku ASP.NET Core [protokolování rozhraní API](xref:fundamentals/logging/index). Protokolovací nástroj například mohou být přidány do služby gRPC prostřednictvím konstruktoru vkládání:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Na základě aplikací C – core konfigurace HTTPS prostřednictvím [ `Server.Ports` vlastnost](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core. Například používá Kestrel [konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) pro tuto funkci.

## <a name="interceptors-and-middlewares"></a>Sběrače a Middlewares

ASP.NET Core [middlewares](xref:fundamentals/middleware/index) gRPC aplikací založených na nabízí podobné funkce ve srovnání s zachycovacích dotazů v C core. Middlewares a sběrače jsou koncepčně stejné jako se používá k vytvoření kanálu, který zpracovává požadavek gRPC. Oba umožňují práci prováděnou před nebo po další komponenta v kanálu. Ale middlewares ASP.NET Core pracovat s základní zprávy HTTP/2, zatímco sběrače provádět gRPC vrstva abstrakce pomocí [ `ServerCallContext` ](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

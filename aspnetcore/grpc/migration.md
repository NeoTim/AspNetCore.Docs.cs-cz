---
title: Migrace služeb gRPC z C-Core na ASP.NET Core
author: juntaoluo
description: Naučte se, jak přesunout existující aplikaci gRPC založenou na jazyce C a spustit ji nad ASP.NET Core Stack.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 8f0d9dd980fa3281f30dc29d329d10ccd352ae72
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278701"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrace služeb gRPC z C-Core na ASP.NET Core

Od [Jan Luo](https://github.com/juntaoluo)

Z důvodu implementace podkladového zásobníku ne všechny funkce fungují stejným způsobem mezi aplikacemi [gRPC založenými na jazyce C](https://grpc.io/blog/grpc-stacks) a aplikacemi založenými na ASP.NET Core. Tento dokument popisuje klíčové rozdíly mezi těmito dvěma zásobníky.

## <a name="grpc-service-implementation-lifetime"></a>doba implementace služby gRPC

Ve výchozím nastavení jsou ve ASP.NET Core stacku služby gRPC vytvořeny s [vymezenou životností](xref:fundamentals/dependency-injection#service-lifetimes). Naproti tomu gRPC C-Core se ve výchozím nastavení váže ke službě s [životností singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Vymezená doba života umožňuje implementaci služby vyřešit další služby s vymezenými životnostmi. Například rozsah životnosti lze také vyřešit `DbContext` z kontejneru di prostřednictvím injektáže konstruktoru. Použití oboru platnosti:

* Nová instance implementace služby je vytvořena pro každý požadavek.
* Není možné sdílet stav mezi požadavky prostřednictvím členů instance v typu implementace.
* Očekává se, že se sdílené stavy ukládají do služby s jedním prvkem v kontejneru DI. Uložené sdílené stavy jsou vyřešeny v konstruktoru implementace služby gRPC.

Další informace o životních cyklech služby najdete <xref:fundamentals/dependency-injection#service-lifetimes>v tématu.

### <a name="add-a-singleton-service"></a>Přidání služby s jedním prvkem

Pro usnadnění přechodu z gRPC implementace C-Core do ASP.NET Core je možné změnit dobu života služby implementace služby z oboru na singleton. To zahrnuje přidání instance implementace služby do kontejneru DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Nicméně implementace služby s životností singleton již není schopna překládat oborové služby prostřednictvím injektáže konstruktoru.

## <a name="configure-grpc-services-options"></a>Konfigurace možností služeb gRPC Services

`grpc.max_receive_message_length` V aplikacích založených na jazyce C jsou nastavení, jako jsou a `grpc.max_send_message_length` , nakonfigurována `ChannelOption` při [vytváření instance serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

V ASP.NET Core poskytuje gRPC konfiguraci prostřednictvím `GrpcServiceOptions` typu. Například maximální velikost příchozích zpráv služby gRPC lze nakonfigurovat prostřednictvím `AddGrpc`. Následující příklad změní výchozí nastavení `ReceiveMaxMessageSize` 4 MB na 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Další informace o konfiguraci najdete v tématu <xref:grpc/configuration>.

## <a name="logging"></a>protokolování

Základní aplikace založené na jazyce C jsou závislé `GrpcEnvironment` na [konfiguraci protokolovacího](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) nástroje pro účely ladění. ASP.NET Core Stack tuto funkci poskytuje prostřednictvím [rozhraní API protokolování](xref:fundamentals/logging/index). Například protokolovací nástroj může být přidán do služby gRPC prostřednictvím injektáže konstruktoru:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Aplikace založené na základních jazycích konfigurují HTTPS prostřednictvím [vlastnosti Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core. Kestrel například pro tuto funkci používá [konfiguraci koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) .

## <a name="interceptors-and-middleware"></a>Zachycení a middleware

[Middleware](xref:fundamentals/middleware/index) ASP.NET Core nabízí podobné funkce v porovnání s zachycením v aplikacích gRPC založených na jazyce C. Middleware a jsou koncepčně stejné, jak jsou použity k vytvoření kanálu, který zpracovává gRPC požadavek. Obě umožňují provádět práci před nebo po další komponentě v kanálu. ASP.NET Core middleware ale funguje na podkladových zprávách HTTP/2, zatímco zachycení fungují na gRPC vrstvě abstrakce pomocí [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

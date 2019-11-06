---
title: Migrace služeb gRPC z C-Core na ASP.NET Core
author: juntaoluo
description: Naučte se, jak přesunout existující aplikaci gRPC založenou na jazyce C a spustit ji nad ASP.NET Core Stack.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: c4c07808540c9af370bfa253e8154a8a19f0f3de
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634071"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrace služeb gRPC z C-Core na ASP.NET Core

Od [Jan Luo](https://github.com/juntaoluo)

Z důvodu implementace podkladového zásobníku ne všechny funkce fungují stejným způsobem mezi aplikacemi [gRPC založenými na jazyce C](https://grpc.io/blog/grpc-stacks) a aplikacemi založenými na ASP.NET Core. Tento dokument popisuje klíčové rozdíly mezi těmito dvěma zásobníky.

## <a name="grpc-service-implementation-lifetime"></a>doba implementace služby gRPC

Ve výchozím nastavení jsou ve ASP.NET Core stacku služby gRPC vytvořeny s [vymezenou životností](xref:fundamentals/dependency-injection#service-lifetimes). Naproti tomu gRPC C-Core se ve výchozím nastavení váže ke službě s [životností singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Vymezená doba života umožňuje implementaci služby vyřešit další služby s vymezenými životnostmi. Například vymezená doba života může také vyřešit `DbContext` z kontejneru DI prostřednictvím injektáže konstruktoru. Použití oboru platnosti:

* Nová instance implementace služby je vytvořena pro každý požadavek.
* Není možné sdílet stav mezi požadavky prostřednictvím členů instance v typu implementace.
* Očekává se, že se sdílené stavy ukládají do služby s jedním prvkem v kontejneru DI. Uložené sdílené stavy jsou vyřešeny v konstruktoru implementace služby gRPC.

Další informace o životních cyklech služby najdete v tématu <xref:fundamentals/dependency-injection#service-lifetimes>.

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

V aplikacích založených na jazyce C jsou nastavení, jako je například `grpc.max_receive_message_length` a `grpc.max_send_message_length`, konfigurována s `ChannelOption` při [vytváření instance serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

V ASP.NET Core poskytuje gRPC konfiguraci prostřednictvím `GrpcServiceOptions` typu. Například maximální velikost příchozích zpráv služby gRPC je možné nakonfigurovat prostřednictvím `AddGrpc`. Následující příklad změní výchozí `MaxReceiveMessageSize` 4 MB na 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Další informace o konfiguraci najdete v tématu <xref:grpc/configuration>.

## <a name="logging"></a>protokolování

Aplikace založené na základních jazycích v jazyce C spoléhají na `GrpcEnvironment` ke [konfiguraci protokolovacího](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) nástroje pro účely ladění. ASP.NET Core Stack tuto funkci poskytuje prostřednictvím [rozhraní API protokolování](xref:fundamentals/logging/index). Například protokolovací nástroj může být přidán do služby gRPC prostřednictvím injektáže konstruktoru:

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

## <a name="grpc-interceptors-vs-middleware"></a>gRPC zachycení vs middleware

[Middleware](xref:fundamentals/middleware/index) ASP.NET Core nabízí podobné funkce v porovnání s zachycením v aplikacích gRPC založených na jazyce C. ASP.NET Core middlewarů a zachycení jsou koncepčně podobné. Protokoly

* Slouží k vytvoření kanálu, který zpracovává gRPC požadavek.
* Povolí provedení práce před nebo po další komponentě v kanálu.
* Poskytněte přístup k `HttpContext`:
  * V middleware je `HttpContext` parametr.
  * Ve zachycených modulech lze k `HttpContext` přistupovat pomocí parametru `ServerCallContext` s metodou rozšíření `ServerCallContext.GetHttpContext`. Všimněte si, že tato funkce je specifická pro zachycení běžící v ASP.NET Core.

rozdíly v gRPC zachytávací z ASP.NET Core middlewaru:

* Sběrače
  * Pracovat na gRPC vrstvě abstrakce s využitím [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Poskytněte přístup k:
    * Deserializovaná zpráva odeslaná volání.
    * Zpráva vracená voláním před tím, než je serializována.
* Jiné
  * Spustí se před zachycením gRPC.
  * Funguje se základními zprávami HTTP/2.
  * Může přistupovat jenom k bajtům z datových proudů požadavků a odpovědí.

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

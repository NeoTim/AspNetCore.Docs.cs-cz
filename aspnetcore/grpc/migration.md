---
title: Migrace služeb gRPC z C-core do ASP.NET Core
author: juntaoluo
description: Přečtěte si, jak přesunout existující aplikaci gRPC založenou na cjádru, která se má spouštět nad ASP.NET zásobníku Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664134"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrace služeb gRPC z C-core do ASP.NET Core

Podle [John Luo](https://github.com/juntaoluo)

Vzhledem k implementaci základního zásobníku ne fungují všechny funkce stejným způsobem mezi [aplikacemi gRPC založenými na jádru C](https://grpc.io/blog/grpc-stacks) a aplikacemi ASP.NET core. Tento dokument zvýrazní klíčové rozdíly při migraci mezi dvěma zásobníky.

## <a name="grpc-service-implementation-lifetime"></a>gRPC životnost implementace služby

V zásobníku ASP.NET Core jsou ve výchozím nastavení služby gRPC vytvořeny s [rozsahem životnosti](xref:fundamentals/dependency-injection#service-lifetimes). Naproti tomu gRPC C-core ve výchozím nastavení váže na službu s [životností singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Životnost oboru umožňuje implementaci služby vyřešit další služby s rozsahem životnosti. Například životnost oboru můžete také `DbContext` vyřešit z kontejneru DI prostřednictvím vstřikování konstruktoru. Použití životnosti s vymezenou oborem:

* Pro každý požadavek je vytvořena nová instance implementace služby.
* Není možné sdílet stav mezi požadavky prostřednictvím členů instance na typu implementace.
* Očekává se uložení sdílených stavů ve službě singleton v kontejneru DI. Uložené sdílené stavy jsou vyřešeny v konstruktoru implementace služby gRPC.

Další informace o životnosti <xref:fundamentals/dependency-injection#service-lifetimes>naleznete v tématu .

### <a name="add-a-singleton-service"></a>Přidání služby singleton

Pro usnadnění přechodu z implementace jádra gRPC C na ASP.NET core je možné změnit životnost implementace služby z rozsahu na singleton. To zahrnuje přidání instance implementace služby do kontejneru DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Implementace služby s životností singleton však již není schopna vyřešit služby s rozsahem prostřednictvím vkládání konstruktoru.

## <a name="configure-grpc-services-options"></a>Konfigurace možností služeb gRPC

V aplikacích založených na jádru `grpc.max_receive_message_length` `grpc.max_send_message_length` C jsou `ChannelOption` nastavení, jako je například a jsou konfigurovány s [při vytváření instance Serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

V ASP.NET Core, gRPC `GrpcServiceOptions` poskytuje konfiguraci prostřednictvím typu. Například maximální velikost příchozí zprávy služby gRPC lze `AddGrpc`konfigurovat pomocí aplikace . Následující příklad změní `MaxReceiveMessageSize` výchozí hodnotu 4 MB na 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Další informace o konfiguraci naleznete v tématu <xref:grpc/configuration>.

## <a name="logging"></a>Protokolování

C-core založené aplikace spoléhají `GrpcEnvironment` na [konfiguraci protokolování](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) pro účely ladění. ASP.NET core zásobníku poskytuje tuto funkci prostřednictvím [rozhraní API protokolování](xref:fundamentals/logging/index). Například logger lze přidat do služby gRPC prostřednictvím vstřikování konstruktoru:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Aplikace založené na jádru C konfigurují protokol HTTPS prostřednictvím [vlastnosti Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core. Kestrel například používá [konfiguraci koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) pro tuto funkci.

## <a name="grpc-interceptors-vs-middleware"></a>gRPC Interceptors vs Middleware

ASP.NET [Core middleware](xref:fundamentals/middleware/index) nabízí podobné funkce ve srovnání s interceptory v gRPC aplikacích založených na C-core. ASP.NET Core middleware a interceptory jsou koncepčně podobné. Oba:

* Používají se k vytvoření kanálu, který zpracovává požadavek gRPC.
* Povolit práci, která má být provedena před nebo po další součást v potrubí.
* Poskytnout přístup `HttpContext`k :
  * V middleware `HttpContext` je parametr.
  * V zachycovačích `HttpContext` lze `ServerCallContext` přistupovat `ServerCallContext.GetHttpContext` pomocí parametru s metodou rozšíření. Všimněte si, že tato funkce je specifická pro zachycovače spuštěné v ASP.NET core.

gRPC Interceptor rozdíly od ASP.NET Core Middleware:

* Zachycovače:
  * Pracujte na vrstvě abstrakce gRPC pomocí [služby ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Poskytněte přístup k:
    * Deserializovaná zpráva odeslaná volání.
    * Zpráva, která je vrácena z volání před jeho serializace.
  * Může zachytit a zpracovat výjimky vyvoláné ze služeb gRPC.
* Middleware:
  * Běží před gRPC zachycovače.
  * Pracuje na podkladové zprávy HTTP/2.
  * Přístup k bajtům lze získat pouze z datových proudů požadavků a odpovědí.

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

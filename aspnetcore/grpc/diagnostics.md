---
title: Protokolování a diagnostika v gRPC v .NET
author: jamesnk
description: Naučte se shromažďovat diagnostiku z vaší aplikace gRPC v .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: ce6ad96d9e26c9cd3844093536745f8f9bea4a76
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204343"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Protokolování a diagnostika v gRPC v .NET

Od [James Newton – král](https://twitter.com/jamesnk)

Tento článek poskytuje pokyny pro shromažďování diagnostických informací z vaší aplikace gRPC, které vám pomůžou při řešení problémů.

## <a name="grpc-services-logging"></a>protokolování služeb gRPC Services

> [!WARNING]
> Protokoly na straně serveru můžou obsahovat citlivé informace z vaší aplikace. **Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.

Vzhledem k tomu, že jsou služby gRPC hostované na ASP.NET Core, používá systém protokolování ASP.NET Core. Ve výchozí konfiguraci gRPC protokoluje velmi málo informací, ale může se nakonfigurovat. Podrobnosti o konfiguraci ASP.NET Core protokolování najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index#configuration) .

gRPC přidá do `Grpc` kategorie protokoly. Pokud chcete povolit podrobné protokoly z gRPC, nakonfigurujte `Grpc` `Debug` předpony na úroveň v souboru *appSettings. JSON* přidáním následujících položek do `LogLevel` dílčí části v: `Logging`

[!code-json[](diagnostics/logging-config.json?highlight=7)]

Můžete ho také nakonfigurovat v *Startup.cs* pomocí `ConfigureLogging`:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5)]

Pokud nepoužíváte konfiguraci založenou na formátu JSON, nastavte v konfiguračním systému následující hodnotu konfigurace:

* `Logging:LogLevel:Grpc` = `Debug`

Informace o tom, jak zadat hodnoty vnořených konfigurací, najdete v dokumentaci ke konfiguračnímu systému. Například při použití proměnných prostředí jsou místo `_` `:` (například `Logging__LogLevel__Grpc`) použity dva znaky (například).

Tuto `Debug` úroveň doporučujeme používat při shromažďování podrobnějších diagnostických nástrojů pro vaši aplikaci. `Trace` Úroveň přináší vysoce nízkou diagnostiku a je zřídka nutná pro diagnostiku problémů ve vaší aplikaci.

### <a name="sample-logging-output"></a>Ukázka výstupu protokolování

Tady je příklad výstupu konzoly na `Debug` úrovni služby gRPC:

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

## <a name="access-server-side-logs"></a>Přístup k protokolům na straně serveru

Způsob přístupu ke protokolům na straně serveru závisí na prostředí, ve kterém používáte.

### <a name="as-a-console-app"></a>Jako Konzolová aplikace

Pokud používáte konzolovou aplikaci, měl by být ve výchozím nastavení povolený [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console-provider) . protokoly gRPC se zobrazí v konzole nástroje.

### <a name="other-environments"></a>Další prostředí

Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows), najdete <xref:fundamentals/logging/index> Další informace o tom, jak nakonfigurovat zprostředkovatele protokolování vhodné pro prostředí.

## <a name="grpc-client-logging"></a>protokolování klienta gRPC

> [!WARNING]
> Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace. **Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.

Chcete-li získat protokoly z klienta rozhraní .NET, můžete nastavit `GrpcChannelOptions.LoggerFactory` vlastnost při vytvoření kanálu klienta. Pokud voláte službu gRPC z aplikace ASP.NET Core, bude objekt pro vytváření protokolovacího nástroje možné přeložit z injektáže závislosti (DI):

[!code-csharp[](diagnostics/net-client-dependency-injection.cs?highlight=7,16)]

Alternativním způsobem, jak povolit protokolování klienta, je použít objekt pro vytváření klientů [gRPC](xref:grpc/clientfactory) k vytvoření klienta. Klient gRPC zaregistrovaný ve službě klient Factory a vyřešený z typu DI bude automaticky používat nakonfigurované protokolování aplikace.

Pokud vaše aplikace nepoužívá di, můžete vytvořit novou `ILoggerFactory` instanci pomocí [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Pokud chcete získat přístup k této metodě, přidejte do své aplikace balíček [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) .

[!code-csharp[](diagnostics/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a>Ukázka výstupu protokolování

Tady je příklad výstupu konzoly na `Debug` úrovni klienta gRPC:

```
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>

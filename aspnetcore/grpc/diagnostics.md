---
title: Protokolování a diagnostika v gRPC v .NET
author: jamesnk
description: Naučte se shromažďovat diagnostiku z vaší aplikace gRPC v .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 7194e91b40a08c4a7ee619b8f207900af2683aa1
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250739"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="5839a-103">Protokolování a diagnostika v gRPC v .NET</span><span class="sxs-lookup"><span data-stu-id="5839a-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="5839a-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5839a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5839a-105">Tento článek poskytuje pokyny pro shromažďování diagnostických informací z vaší aplikace gRPC, které vám pomůžou při řešení problémů.</span><span class="sxs-lookup"><span data-stu-id="5839a-105">This article provides guidance for gathering diagnostics from your gRPC app to help troubleshoot issues.</span></span>

## <a name="grpc-services-logging"></a><span data-ttu-id="5839a-106">protokolování služeb gRPC Services</span><span class="sxs-lookup"><span data-stu-id="5839a-106">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="5839a-107">Protokoly na straně serveru můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="5839a-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="5839a-108">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="5839a-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="5839a-109">Vzhledem k tomu, že jsou služby gRPC hostované na ASP.NET Core, používá systém protokolování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5839a-109">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="5839a-110">Ve výchozí konfiguraci gRPC protokoluje velmi málo informací, ale může se nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="5839a-110">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="5839a-111">Podrobnosti o konfiguraci ASP.NET Core protokolování najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="5839a-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="5839a-112">gRPC přidá do `Grpc` kategorie protokoly.</span><span class="sxs-lookup"><span data-stu-id="5839a-112">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="5839a-113">Pokud chcete povolit podrobné protokoly z gRPC, nakonfigurujte `Grpc` `Debug` předpony na úroveň v souboru *appSettings. JSON* přidáním následujících položek do `LogLevel` dílčí části v: `Logging`</span><span class="sxs-lookup"><span data-stu-id="5839a-113">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="5839a-114">Můžete ho také nakonfigurovat v *Startup.cs* pomocí `ConfigureLogging`:</span><span class="sxs-lookup"><span data-stu-id="5839a-114">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="5839a-115">Pokud nepoužíváte konfiguraci založenou na formátu JSON, nastavte v konfiguračním systému následující hodnotu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="5839a-115">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="5839a-116">Informace o tom, jak zadat hodnoty vnořených konfigurací, najdete v dokumentaci ke konfiguračnímu systému.</span><span class="sxs-lookup"><span data-stu-id="5839a-116">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="5839a-117">Například při použití proměnných prostředí jsou místo `_` `:` (například `Logging__LogLevel__Grpc`) použity dva znaky (například).</span><span class="sxs-lookup"><span data-stu-id="5839a-117">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="5839a-118">Tuto `Debug` úroveň doporučujeme používat při shromažďování podrobnějších diagnostických nástrojů pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5839a-118">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="5839a-119">`Trace` Úroveň přináší vysoce nízkou diagnostiku a je zřídka nutná pro diagnostiku problémů ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5839a-119">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

### <a name="sample-logging-output"></a><span data-ttu-id="5839a-120">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="5839a-120">Sample logging output</span></span>

<span data-ttu-id="5839a-121">Tady je příklad výstupu konzoly na `Debug` úrovni služby gRPC:</span><span class="sxs-lookup"><span data-stu-id="5839a-121">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
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

## <a name="access-server-side-logs"></a><span data-ttu-id="5839a-122">Přístup k protokolům na straně serveru</span><span class="sxs-lookup"><span data-stu-id="5839a-122">Access server-side logs</span></span>

<span data-ttu-id="5839a-123">Způsob přístupu ke protokolům na straně serveru závisí na prostředí, ve kterém používáte.</span><span class="sxs-lookup"><span data-stu-id="5839a-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app"></a><span data-ttu-id="5839a-124">Jako Konzolová aplikace</span><span class="sxs-lookup"><span data-stu-id="5839a-124">As a console app</span></span>

<span data-ttu-id="5839a-125">Pokud používáte konzolovou aplikaci, měl by být ve výchozím nastavení povolený [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console-provider) .</span><span class="sxs-lookup"><span data-stu-id="5839a-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="5839a-126">protokoly gRPC se zobrazí v konzole nástroje.</span><span class="sxs-lookup"><span data-stu-id="5839a-126">gRPC logs will appear in the console.</span></span>

### <a name="other-environments"></a><span data-ttu-id="5839a-127">Další prostředí</span><span class="sxs-lookup"><span data-stu-id="5839a-127">Other environments</span></span>

<span data-ttu-id="5839a-128">Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows), najdete <xref:fundamentals/logging/index> Další informace o tom, jak nakonfigurovat zprostředkovatele protokolování vhodné pro prostředí.</span><span class="sxs-lookup"><span data-stu-id="5839a-128">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="grpc-client-logging"></a><span data-ttu-id="5839a-129">protokolování klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="5839a-129">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="5839a-130">Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="5839a-130">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="5839a-131">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="5839a-131">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="5839a-132">Chcete-li získat protokoly z klienta rozhraní .NET, můžete nastavit `GrpcChannelOptions.LoggerFactory` vlastnost při vytvoření kanálu klienta.</span><span class="sxs-lookup"><span data-stu-id="5839a-132">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="5839a-133">Pokud voláte službu gRPC z aplikace ASP.NET Core, bude objekt pro vytváření protokolovacího nástroje možné přeložit z injektáže závislosti (DI):</span><span class="sxs-lookup"><span data-stu-id="5839a-133">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="5839a-134">Alternativním způsobem, jak povolit protokolování klienta, je použít objekt pro vytváření klientů [gRPC](xref:grpc/clientfactory) k vytvoření klienta.</span><span class="sxs-lookup"><span data-stu-id="5839a-134">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="5839a-135">Klient gRPC zaregistrovaný ve službě klient Factory a vyřešený z typu DI bude automaticky používat nakonfigurované protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5839a-135">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="5839a-136">Pokud vaše aplikace nepoužívá di, můžete vytvořit novou `ILoggerFactory` instanci pomocí [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="5839a-136">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="5839a-137">Pokud chcete získat přístup k této metodě, přidejte do své aplikace balíček [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) .</span><span class="sxs-lookup"><span data-stu-id="5839a-137">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a><span data-ttu-id="5839a-138">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="5839a-138">Sample logging output</span></span>

<span data-ttu-id="5839a-139">Tady je příklad výstupu konzoly na `Debug` úrovni klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="5839a-139">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="additional-resources"></a><span data-ttu-id="5839a-140">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5839a-140">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>

---
title: BlazorProtokolování ASP.NET Core
author: guardrex
description: Přečtěte si o protokolování Blazor aplikací, včetně konfigurace na úrovni protokolu, a o tom, jak zapisovat zprávy protokolu z Razor komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: c2e8ee24c135b3c2f9b6be5b0e0efa54750c68b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014356"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="bdb6d-103">BlazorProtokolování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdb6d-103">ASP.NET Core Blazor logging</span></span>

## Blazor WebAssembly

<span data-ttu-id="bdb6d-104">Konfigurovat protokolování Blazor WebAssembly aplikací pomocí `WebAssemblyHostBuilder.Logging` vlastnosti v `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="bdb6d-104">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="bdb6d-105">`Logging`Vlastnost je typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , takže všechny metody rozšíření, které jsou k dispozici na, <xref:Microsoft.Extensions.Logging.ILoggingBuilder> jsou také k dispozici na `Logging` .</span><span class="sxs-lookup"><span data-stu-id="bdb6d-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="bdb6d-106">Konfigurace protokolování se dá načíst ze souborů s nastavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdb6d-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="bdb6d-107">Další informace naleznete v tématu <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bdb6d-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## Blazor Server

<span data-ttu-id="bdb6d-108">Obecné pokyny k protokolování ASP.NET Core najdete v tématu <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="bdb6d-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="bdb6d-109">Blazor WebAssemblySignalRProtokolování klienta .NET</span><span class="sxs-lookup"><span data-stu-id="bdb6d-109">Blazor WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="bdb6d-110">Vložením <xref:Microsoft.Extensions.Logging.ILoggerProvider> přidejte do `WebAssemblyConsoleLogger` zprostředkovatele protokolování předaného <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="bdb6d-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="bdb6d-111">Na rozdíl od tradiční <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> `WebAssemblyConsoleLogger` je to obálka kolem rozhraní API pro protokolování specifických pro prohlížeč (například `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="bdb6d-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="bdb6d-112">Použití `WebAssemblyConsoleLogger` Možnosti umožňuje protokolování v rámci mono v kontextu prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bdb6d-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="bdb6d-113">Komponenty přihlášení Razor</span><span class="sxs-lookup"><span data-stu-id="bdb6d-113">Log in Razor components</span></span>

<span data-ttu-id="bdb6d-114">Protokolovací nástroje respektují konfiguraci spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdb6d-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="bdb6d-115">`using`Direktiva pro <xref:Microsoft.Extensions.Logging> je nutná k podpoře dokončování technologie IntelliSense pro rozhraní API, například <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="bdb6d-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="bdb6d-116">Následující příklad ukazuje protokolování s <xref:Microsoft.Extensions.Logging.ILogger> Razor komponentami v nástroji:</span><span class="sxs-lookup"><span data-stu-id="bdb6d-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="bdb6d-117">Následující příklad ukazuje protokolování s <xref:Microsoft.Extensions.Logging.ILoggerFactory> Razor komponentami v nástroji:</span><span class="sxs-lookup"><span data-stu-id="bdb6d-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="bdb6d-118">Další materiály</span><span class="sxs-lookup"><span data-stu-id="bdb6d-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>

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
# <a name="aspnet-core-no-locblazor-logging"></a>BlazorProtokolování ASP.NET Core

## Blazor WebAssembly

Konfigurovat protokolování Blazor WebAssembly aplikací pomocí `WebAssemblyHostBuilder.Logging` vlastnosti v `Program.Main` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`Vlastnost je typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , takže všechny metody rozšíření, které jsou k dispozici na, <xref:Microsoft.Extensions.Logging.ILoggingBuilder> jsou také k dispozici na `Logging` .

Konfigurace protokolování se dá načíst ze souborů s nastavením aplikace. Další informace naleznete v tématu <xref:blazor/fundamentals/configuration#logging-configuration>.

## Blazor Server

Obecné pokyny k protokolování ASP.NET Core najdete v tématu <xref:fundamentals/logging/index> .

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a>Blazor WebAssemblySignalRProtokolování klienta .NET

Vložením <xref:Microsoft.Extensions.Logging.ILoggerProvider> přidejte do `WebAssemblyConsoleLogger` zprostředkovatele protokolování předaného <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Na rozdíl od tradiční <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> `WebAssemblyConsoleLogger` je to obálka kolem rozhraní API pro protokolování specifických pro prohlížeč (například `console.log` ). Použití `WebAssemblyConsoleLogger` Možnosti umožňuje protokolování v rámci mono v kontextu prohlížeče.

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a>Komponenty přihlášení Razor

Protokolovací nástroje respektují konfiguraci spuštění aplikace.

`using`Direktiva pro <xref:Microsoft.Extensions.Logging> je nutná k podpoře dokončování technologie IntelliSense pro rozhraní API, například <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .

Následující příklad ukazuje protokolování s <xref:Microsoft.Extensions.Logging.ILogger> Razor komponentami v nástroji:

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

Následující příklad ukazuje protokolování s <xref:Microsoft.Extensions.Logging.ILoggerFactory> Razor komponentami v nástroji:

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

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/logging/index>

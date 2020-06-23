---
title: BlazorProtokolování ASP.NET Core
author: guardrex
description: Přečtěte si o protokolování Blazor aplikací, včetně konfigurace na úrovni protokolu, a o tom, jak zapisovat zprávy protokolu z Razor komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: 841c4021d9217312b2601b0e775542c6455cca82
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240880"
---
# <a name="aspnet-core-blazor-logging"></a>BlazorProtokolování ASP.NET Core

## <a name="blazor-webassembly"></a>BlazorWebAssembly

Nakonfigurujte protokolování v Blazor aplikacích pro WebAssembly pomocí `WebAssemblyHostBuilder.Logging` vlastnosti v `Program.Main` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`Vlastnost je typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , takže všechny metody rozšíření, které jsou k dispozici na, <xref:Microsoft.Extensions.Logging.ILoggingBuilder> jsou také k dispozici na `Logging` .

Konfigurace protokolování se dá načíst ze souborů s nastavením aplikace. Další informace naleznete v tématu <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="blazor-server"></a>BlazorWebServer

Obecné pokyny k protokolování ASP.NET Core najdete v tématu <xref:fundamentals/logging/index> .

## <a name="blazor-webassembly-signalr-net-client-logging"></a>BlazorSignalRProtokolování klienta .NET pro WebAssembly

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

## <a name="log-in-razor-components"></a>Komponenty přihlášení Razor

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

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/index>

---
title: Migrace z Microsoft. Extensions. Logging 2,1 na 2,2 nebo 3,0
author: pakrym
description: Naučte se migrovat non-ASP.NET základní aplikaci, která používá Microsoft. Extensions. Logging od 2,1 do 2,2 nebo 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 0c85ca637c1e93bbde93c7d5d12408637476558e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399787"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrace z Microsoft. Extensions. Logging 2,1 na 2,2 nebo 3,0

V tomto článku najdete popis běžných kroků migrace aplikace non-ASP.NET Core, která používá `Microsoft.Extensions.Logging` od 2,1 do 2,2 nebo 3,0.

## <a name="21-to-22"></a>Z verze 2.1 do verze 2.2

Ručně vytvořte `ServiceCollection` a zavolejte `AddLogging` .

Příklad 2,1:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

Příklad 2,2:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2,1 až 3,0

V 3,0 použijte `LoggingFactory.Create` .

Příklad 2,1:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

Příklad 3,0:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Další zdroje

* [Balíček NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>

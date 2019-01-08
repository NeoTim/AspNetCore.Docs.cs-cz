---
title: Migrace z Microsoft.Extensions.Logging 2.1 k 2.2 nebo 3.0.
author: pakrym
description: Zjistěte, jak migrovat aplikace ASP.NET Core, která používá Microsoft.Extensions.Logging z 2.1 2.2 nebo 3.0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099536"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrace z Microsoft.Extensions.Logging 2.1 k 2.2 nebo 3.0.

Tento článek popisuje běžné kroky při migraci aplikace ASP.NET Core, která používá `Microsoft.Extensions.Logging` z 2.1 2.2 nebo 3.0.

## <a name="21-to-22"></a>2.1 – 2.2

Ruční vytvoření `ServiceCollection` a volat `AddLogging`.

2.1 Příklad:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2,2 příklad:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2.1 až 3.0

3.0, použijte `LoggingFactory.Create`.

2.1 Příklad:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3.0 Příklad:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Další zdroje

<xref:fundamentals/logging/index>
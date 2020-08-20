---
title: Migrace z Microsoft. Extensions. Logging 2,1 na 2,2 nebo 3,0
author: pakrym
description: Naučte se migrovat non-ASP.NET základní aplikaci, která používá Microsoft. Extensions. Logging od 2,1 do 2,2 nebo 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 8ef07118e3a885e41221402bdfc2d7e942c6dd73
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634095"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="ba09c-103">Migrace z Microsoft. Extensions. Logging 2,1 na 2,2 nebo 3,0</span><span class="sxs-lookup"><span data-stu-id="ba09c-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="ba09c-104">V tomto článku najdete popis běžných kroků migrace aplikace non-ASP.NET Core, která používá `Microsoft.Extensions.Logging` od 2,1 do 2,2 nebo 3,0.</span><span class="sxs-lookup"><span data-stu-id="ba09c-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="ba09c-105">Z verze 2.1 do verze 2.2</span><span class="sxs-lookup"><span data-stu-id="ba09c-105">2.1 to 2.2</span></span>

<span data-ttu-id="ba09c-106">Ručně vytvořte `ServiceCollection` a zavolejte `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="ba09c-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="ba09c-107">Příklad 2,1:</span><span class="sxs-lookup"><span data-stu-id="ba09c-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="ba09c-108">Příklad 2,2:</span><span class="sxs-lookup"><span data-stu-id="ba09c-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="ba09c-109">2,1 až 3,0</span><span class="sxs-lookup"><span data-stu-id="ba09c-109">2.1 to 3.0</span></span>

<span data-ttu-id="ba09c-110">V 3,0 použijte `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="ba09c-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="ba09c-111">Příklad 2,1:</span><span class="sxs-lookup"><span data-stu-id="ba09c-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="ba09c-112">Příklad 3,0:</span><span class="sxs-lookup"><span data-stu-id="ba09c-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="ba09c-113">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="ba09c-113">Additional resources</span></span>

* <span data-ttu-id="ba09c-114">[Balíček NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="ba09c-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>

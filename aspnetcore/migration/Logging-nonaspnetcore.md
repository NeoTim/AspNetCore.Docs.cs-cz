---
title: Migrace z Microsoft. Extensions. Logging 2,1 na 2,2 nebo 3,0
author: pakrym
description: Naučte se migrovat non-ASP.NET základní aplikaci, která používá Microsoft. Extensions. Logging od 2,1 do 2,2 nebo 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: edb1c6456d0cbbac57d739f61b4c159f146e4f7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015111"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="af1ce-103">Migrace z Microsoft. Extensions. Logging 2,1 na 2,2 nebo 3,0</span><span class="sxs-lookup"><span data-stu-id="af1ce-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="af1ce-104">V tomto článku najdete popis běžných kroků migrace aplikace non-ASP.NET Core, která používá `Microsoft.Extensions.Logging` od 2,1 do 2,2 nebo 3,0.</span><span class="sxs-lookup"><span data-stu-id="af1ce-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="af1ce-105">Z verze 2.1 do verze 2.2</span><span class="sxs-lookup"><span data-stu-id="af1ce-105">2.1 to 2.2</span></span>

<span data-ttu-id="af1ce-106">Ručně vytvořte `ServiceCollection` a zavolejte `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="af1ce-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="af1ce-107">Příklad 2,1:</span><span class="sxs-lookup"><span data-stu-id="af1ce-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="af1ce-108">Příklad 2,2:</span><span class="sxs-lookup"><span data-stu-id="af1ce-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="af1ce-109">2,1 až 3,0</span><span class="sxs-lookup"><span data-stu-id="af1ce-109">2.1 to 3.0</span></span>

<span data-ttu-id="af1ce-110">V 3,0 použijte `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="af1ce-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="af1ce-111">Příklad 2,1:</span><span class="sxs-lookup"><span data-stu-id="af1ce-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="af1ce-112">Příklad 3,0:</span><span class="sxs-lookup"><span data-stu-id="af1ce-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="af1ce-113">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="af1ce-113">Additional resources</span></span>

* <span data-ttu-id="af1ce-114">[Balíček NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="af1ce-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>

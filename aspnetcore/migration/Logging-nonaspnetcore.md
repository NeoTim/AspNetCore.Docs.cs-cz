---
<span data-ttu-id="d6adc-101">title: migrace z Microsoft. Extensions. Logging 2,1 na 2,2 nebo 3,0 autor: pakrym Description: Naučte se migrovat aplikaci non-ASP.NET Core, která používá Microsoft. Extensions. protokolování z 2,1 do 2,2 nebo 3,0.</span><span class="sxs-lookup"><span data-stu-id="d6adc-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="d6adc-102">MS. Author: pakrym MS. Custom: MVC MS. Date: 01/04/2019 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d6adc-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="d6adc-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6adc-103">'Blazor'</span></span>
- <span data-ttu-id="d6adc-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6adc-104">'Identity'</span></span>
- <span data-ttu-id="d6adc-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6adc-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d6adc-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6adc-106">'Razor'</span></span>
- <span data-ttu-id="d6adc-107">SignalRUID: migrace/protokolování – nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="d6adc-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="d6adc-108">Migrace z Microsoft. Extensions. Logging 2,1 na 2,2 nebo 3,0</span><span class="sxs-lookup"><span data-stu-id="d6adc-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="d6adc-109">V tomto článku najdete popis běžných kroků migrace aplikace non-ASP.NET Core, která používá `Microsoft.Extensions.Logging` od 2,1 do 2,2 nebo 3,0.</span><span class="sxs-lookup"><span data-stu-id="d6adc-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="d6adc-110">Z verze 2.1 do verze 2.2</span><span class="sxs-lookup"><span data-stu-id="d6adc-110">2.1 to 2.2</span></span>

<span data-ttu-id="d6adc-111">Ručně vytvořte `ServiceCollection` a zavolejte `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="d6adc-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="d6adc-112">Příklad 2,1:</span><span class="sxs-lookup"><span data-stu-id="d6adc-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="d6adc-113">Příklad 2,2:</span><span class="sxs-lookup"><span data-stu-id="d6adc-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="d6adc-114">2,1 až 3,0</span><span class="sxs-lookup"><span data-stu-id="d6adc-114">2.1 to 3.0</span></span>

<span data-ttu-id="d6adc-115">V 3,0 použijte `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="d6adc-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="d6adc-116">Příklad 2,1:</span><span class="sxs-lookup"><span data-stu-id="d6adc-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="d6adc-117">Příklad 3,0:</span><span class="sxs-lookup"><span data-stu-id="d6adc-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="d6adc-118">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d6adc-118">Additional resources</span></span>

* <span data-ttu-id="d6adc-119">[Balíček NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="d6adc-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>

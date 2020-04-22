---
title: Přihlášení do jádra rozhraní .NET core a ASP.NET core
author: rick-anderson
description: Zjistěte, jak používat architekturu protokolování poskytovanou balíčkem Microsoft.Extensions.Logging NuGet.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: b897d0d775da62a11f01a64f39b47b6c5abebc8b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791564"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="5873e-103">Přihlášení do jádra rozhraní .NET core a ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="5873e-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5873e-104">Tom [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5873e-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5873e-105">Rozhraní .NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="5873e-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="5873e-106">Tento článek ukazuje, jak používat rozhraní API protokolování s integrovanými zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="5873e-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="5873e-107">Většina příkladů kódu uvedených v tomto článku pochází z ASP.NET základních aplikací.</span><span class="sxs-lookup"><span data-stu-id="5873e-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="5873e-108">Části těchto fragmentů kódu specifické pro protokolování platí pro všechny aplikace .NET Core, která používá [obecný hostitel](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="5873e-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="5873e-109">Příklad použití obecného hostitele v aplikaci, která není webovou konzolou, najdete<xref:fundamentals/host/hosted-services>v *Program.cs* soubor [ukázkové aplikace Úlohy na pozadí](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( ).</span><span class="sxs-lookup"><span data-stu-id="5873e-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="5873e-110">Protokolování kódu pro aplikace bez obecného hostitele se liší ve způsobu, jakým [jsou přidány zprostředkovatele](#add-providers) a [jsou vytvořeny úhozy kláves](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="5873e-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="5873e-111">Příklady kódu bez hostitele jsou uvedeny v těchto částech článku.</span><span class="sxs-lookup"><span data-stu-id="5873e-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="5873e-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5873e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="5873e-113">Přidání zprostředkovatelů</span><span class="sxs-lookup"><span data-stu-id="5873e-113">Add providers</span></span>

<span data-ttu-id="5873e-114">Zprostředkovatel protokolování zobrazí nebo uloží protokoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="5873e-115">Například zprostředkovatel konzoly zobrazí protokoly na konzoli a poskytovatel Azure Application Insights je uloží do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5873e-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="5873e-116">Protokoly lze odeslat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="5873e-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="5873e-117">Pokud chcete přidat zprostředkovatele do aplikace, která používá `Add{provider name}` obecný hostitel, zavolejte metodu rozšíření poskytovatele v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5873e-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="5873e-118">V aplikaci konzoly bez hostitele volejte `Add{provider name}` metodu rozšíření `LoggerFactory`zprostředkovatele při vytváření :</span><span class="sxs-lookup"><span data-stu-id="5873e-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="5873e-119">`LoggerFactory`a `AddConsole` vyžadují `using` prohlášení `Microsoft.Extensions.Logging`pro .</span><span class="sxs-lookup"><span data-stu-id="5873e-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="5873e-120">Výchozí ASP.NET volání výchozích <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>šablon projektu jádra , které přidá následující poskytovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="5873e-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="5873e-121">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="5873e-122">Ladění</span><span class="sxs-lookup"><span data-stu-id="5873e-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="5873e-123">Zdroj událostí</span><span class="sxs-lookup"><span data-stu-id="5873e-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="5873e-124">[EventLog](#windows-eventlog-provider) (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="5873e-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="5873e-125">Výchozí zprostředkovatele můžete nahradit vlastními volbami.</span><span class="sxs-lookup"><span data-stu-id="5873e-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="5873e-126">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte požadované zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="5873e-127">Další informace o [předdefinovaných zprostředkovatelích protokolování](#built-in-logging-providers) a [poskytovatelích protokolování třetích stran](#third-party-logging-providers) dále v článku.</span><span class="sxs-lookup"><span data-stu-id="5873e-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="5873e-128">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="5873e-128">Create logs</span></span>

<span data-ttu-id="5873e-129">Chcete-li vytvořit protokoly, použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="5873e-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="5873e-130">Ve webové aplikaci nebo hostované `ILogger` službě získáte z vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="5873e-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="5873e-131">V aplikacích konzoly, `LoggerFactory` které nejsou `ILogger`hostitelskou konzolou, použijte k vytvoření aplikace .</span><span class="sxs-lookup"><span data-stu-id="5873e-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="5873e-132">Následující příklad ASP.NET Core vytvoří `TodoApiSample.Pages.AboutModel` protokolovací nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="5873e-133">*Kategorie* protokolu je řetězec, který je přidružen ke každému protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="5873e-134">Instance `ILogger<T>` poskytované DI vytvoří protokoly, které mají plně `T` kvalifikovaný název typu jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="5873e-135">Následující příklad aplikace konzoly bez hostitele `LoggingConsoleApp.Program` vytvoří protokolovací nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="5873e-136">V následujících příkladech ASP.NET core a konzolové aplikace, `Information` protokolování se používá k vytvoření protokolů s jako úroveň.</span><span class="sxs-lookup"><span data-stu-id="5873e-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="5873e-137">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="5873e-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="5873e-138">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="5873e-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="5873e-139">Vytvoření protokolů ve třídě Program</span><span class="sxs-lookup"><span data-stu-id="5873e-139">Create logs in the Program class</span></span>

<span data-ttu-id="5873e-140">Chcete-li zapsat `Program` protokoly ve třídě aplikace `ILogger` ASP.NET Core, získejte instanci z DI po vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="5873e-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="5873e-141">Protokolování během výstavby hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="5873e-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="5873e-142">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="5873e-142">However, a separate logger can be used.</span></span> <span data-ttu-id="5873e-143">V následujícím příkladu se protokolovací nástroj [Serilog](https://serilog.net/) používá k přihlášení `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5873e-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="5873e-144">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="5873e-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="5873e-145">Vytvoření protokolů ve třídě Startup</span><span class="sxs-lookup"><span data-stu-id="5873e-145">Create logs in the Startup class</span></span>

<span data-ttu-id="5873e-146">Chcete-li zapsat `Startup.Configure` protokoly v metodě `ILogger` aplikace ASP.NET Core, zahrňte parametr do podpisu metody:</span><span class="sxs-lookup"><span data-stu-id="5873e-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="5873e-147">Zápis protokolů před dokončením nastavení kontejneru DI v metodě `Startup.ConfigureServices` není podporován:</span><span class="sxs-lookup"><span data-stu-id="5873e-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="5873e-148">Vkládání protokolů `Startup` do konstruktoru není podporováno.</span><span class="sxs-lookup"><span data-stu-id="5873e-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="5873e-149">Vkládání protokolů `Startup.ConfigureServices` do podpisu metody není podporováno.</span><span class="sxs-lookup"><span data-stu-id="5873e-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="5873e-150">Důvodem pro toto omezení je, že protokolování závisí na DI a na konfiguraci, která zase závisí na DI.</span><span class="sxs-lookup"><span data-stu-id="5873e-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="5873e-151">Kontejner DI není nastaven, `ConfigureServices` dokud nebude dokončena.</span><span class="sxs-lookup"><span data-stu-id="5873e-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="5873e-152">Konstruktor vkládání úhozů do `Startup` funguje v dřívějších verzích ASP.NET Core, protože samostatný kontejner DI je vytvořen pro web hostitele.</span><span class="sxs-lookup"><span data-stu-id="5873e-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="5873e-153">Informace o tom, proč je pro obecný hostitel vytvořen pouze jeden kontejner, naleznete v [oznámení o změně rozdělení](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="5873e-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="5873e-154">Pokud potřebujete nakonfigurovat službu, `ILogger<T>`která závisí na , můžete to stále provést pomocí vkládání konstruktoru nebo poskytnutím metody výroby.</span><span class="sxs-lookup"><span data-stu-id="5873e-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="5873e-155">Přístup metody výroby se doporučuje pouze v případě, že neexistuje žádná jiná možnost.</span><span class="sxs-lookup"><span data-stu-id="5873e-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="5873e-156">Předpokládejme například, že potřebujete vyplnit vlastnost službou z DI:</span><span class="sxs-lookup"><span data-stu-id="5873e-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="5873e-157">Předchozí zvýrazněný kód `Func` je, který běží poprvé kontejner UD potřebuje `MyService`k vytvoření instance .</span><span class="sxs-lookup"><span data-stu-id="5873e-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="5873e-158">Tímto způsobem můžete přistupovat k libovolné registrované službě.</span><span class="sxs-lookup"><span data-stu-id="5873e-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor-webassembly"></a><span data-ttu-id="5873e-159">Vytváření protokolů v Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5873e-159">Create logs in Blazor WebAssembly</span></span>

<span data-ttu-id="5873e-160">Konfigurace protokolování v aplikacích Blazor WebAssembly s vlastností `WebAssemblyHostBuilder.Logging` v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5873e-160">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="5873e-161">Vlastnost `Logging` je typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, takže všechny metody <xref:Microsoft.Extensions.Logging.ILoggingBuilder> rozšíření jsou `Logging`k dispozici také na .</span><span class="sxs-lookup"><span data-stu-id="5873e-161">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="5873e-162">Žádné asynchronní metody protokolování</span><span class="sxs-lookup"><span data-stu-id="5873e-162">No asynchronous logger methods</span></span>

<span data-ttu-id="5873e-163">Protokolování by mělo být tak rychlé, že nestojí za náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-163">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="5873e-164">Pokud je úložiště dat protokolování pomalé, nezapisujte do něj přímo.</span><span class="sxs-lookup"><span data-stu-id="5873e-164">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="5873e-165">Zvažte zápis zpráv protokolu do rychlého úložiště zpočátku a potom je přesunout do pomalé úložiště později.</span><span class="sxs-lookup"><span data-stu-id="5873e-165">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="5873e-166">Například pokud se přihlašujete k serveru SQL Server, `Log` nechcete `Log` to provést přímo v metodě, protože metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="5873e-166">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="5873e-167">Místo toho synchronně přidat zprávy protokolu do fronty v paměti a mají pracovník na pozadí vytáhnout zprávy z fronty provést asynchronní práci odesílání dat na SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5873e-167">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="5873e-168">Další informace naleznete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHub.</span><span class="sxs-lookup"><span data-stu-id="5873e-168">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="5873e-169">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5873e-169">Configuration</span></span>

<span data-ttu-id="5873e-170">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="5873e-170">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="5873e-171">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="5873e-171">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="5873e-172">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5873e-172">Command-line arguments.</span></span>
* <span data-ttu-id="5873e-173">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5873e-173">Environment variables.</span></span>
* <span data-ttu-id="5873e-174">Objekty .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="5873e-174">In-memory .NET objects.</span></span>
* <span data-ttu-id="5873e-175">Nešifrované úložiště [Správce tajných](xref:security/app-secrets) služeb.</span><span class="sxs-lookup"><span data-stu-id="5873e-175">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="5873e-176">Šifrované úložiště uživatelů, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="5873e-176">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="5873e-177">Vlastní zprostředkovatelé (nainstalované nebo vytvořené).</span><span class="sxs-lookup"><span data-stu-id="5873e-177">Custom providers (installed or created).</span></span>

<span data-ttu-id="5873e-178">Například konfigurace protokolování je běžně `Logging` poskytována v části souborů nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-178">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="5873e-179">Následující příklad ukazuje obsah typické *nastavení aplikace. Development.json* soubor:</span><span class="sxs-lookup"><span data-stu-id="5873e-179">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="5873e-180">Vlastnost `Logging` může `LogLevel` mít a protokolovat vlastnosti zprostředkovatele (je zobrazena konzola).</span><span class="sxs-lookup"><span data-stu-id="5873e-180">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="5873e-181">Vlastnost `LogLevel` v `Logging` části určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-181">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="5873e-182">V příkladu `System` `Microsoft` a kategorie `Information` protokolu na úrovni `Debug` a všechny ostatní protokolu na úrovni.</span><span class="sxs-lookup"><span data-stu-id="5873e-182">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="5873e-183">Další vlastnosti v části `Logging` zadejte zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-183">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="5873e-184">Příklad je pro zprostředkovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-184">The example is for the Console provider.</span></span> <span data-ttu-id="5873e-185">Pokud zprostředkovatel podporuje [obory protokolu](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="5873e-185">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="5873e-186">Vlastnost zprostředkovatele (například `Console` v příkladu) `LogLevel` může také určit vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5873e-186">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="5873e-187">`LogLevel`v rámci zprostředkovatele určuje úrovně pro protokolování pro tohoto zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-187">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="5873e-188">Pokud jsou úrovně `Logging.{providername}.LogLevel`zadány v , `Logging.LogLevel`přepíší vše, co je nastaveno v .</span><span class="sxs-lookup"><span data-stu-id="5873e-188">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="5873e-189">Rozhraní API protokolování neobsahuje scénář pro změnu úrovní protokolu, když je spuštěná aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-189">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="5873e-190">Někteří poskytovatelé konfigurace jsou však schopni znovu načíst konfiguraci, která má okamžitý vliv na konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-190">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="5873e-191">Například [zprostředkovatel konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider), který `CreateDefaultBuilder` je přidán do čtení souborů nastavení, znovu načte konfiguraci protokolování ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="5873e-191">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="5873e-192">Pokud se konfigurace změní v kódu, když je aplikace spuštěná, aplikace může volat [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) a aktualizovat konfiguraci protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-192">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="5873e-193">Informace o implementaci zprostředkovatelů <xref:fundamentals/configuration/index>konfigurace naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="5873e-193">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="5873e-194">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="5873e-194">Sample logging output</span></span>

<span data-ttu-id="5873e-195">S ukázkovým kódem zobrazeným v předchozí části se protokoly zobrazí v konzole při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5873e-195">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="5873e-196">Zde je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="5873e-196">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="5873e-197">Předchozí protokoly byly generovány provedením požadavku HTTP Get `http://localhost:5000/api/todo/0`do ukázkové aplikace na adrese .</span><span class="sxs-lookup"><span data-stu-id="5873e-197">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="5873e-198">Tady je příklad stejných protokolů, které se zobrazují v okně Ladění při spuštění ukázkové aplikace v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5873e-198">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="5873e-199">Protokoly, které jsou `ILogger` vytvořeny volání zobrazené v předchozí části začínají "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="5873e-199">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="5873e-200">Protokoly, které začínají kategorie "Microsoft" jsou z ASP.NET kódu core framework.</span><span class="sxs-lookup"><span data-stu-id="5873e-200">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="5873e-201">ASP.NET Core a kód aplikace používají stejné protokolování ROZHRANÍ API a zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="5873e-201">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="5873e-202">Zbývající část tohoto článku vysvětluje některé podrobnosti a možnosti pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-202">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="5873e-203">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="5873e-203">NuGet packages</span></span>

<span data-ttu-id="5873e-204">Rozhraní `ILogger` `ILoggerFactory` a jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="5873e-204">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="5873e-205">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-205">Log category</span></span>

<span data-ttu-id="5873e-206">Při `ILogger` vytvoření objektu je pro něj *zadána kategorie.*</span><span class="sxs-lookup"><span data-stu-id="5873e-206">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="5873e-207">Tato kategorie je součástí každé zprávy protokolu `ILogger`vytvořené danou instancí .</span><span class="sxs-lookup"><span data-stu-id="5873e-207">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="5873e-208">Kategorie může být libovolný řetězec, ale konvence je použít název třídy, například "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="5873e-208">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="5873e-209">Slouží `ILogger<T>` k `ILogger` získání instance, která používá `T` plně kvalifikovaný název typu jako kategorie:</span><span class="sxs-lookup"><span data-stu-id="5873e-209">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="5873e-210">Chcete-li kategorii explicitně `ILoggerFactory.CreateLogger`určit, volejte :</span><span class="sxs-lookup"><span data-stu-id="5873e-210">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="5873e-211">`ILogger<T>`je ekvivalentní `CreateLogger` volání s plně kvalifikovaným typem názvu . `T`</span><span class="sxs-lookup"><span data-stu-id="5873e-211">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="5873e-212">Úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-212">Log level</span></span>

<span data-ttu-id="5873e-213">Každý protokol určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5873e-213">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="5873e-214">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="5873e-214">The log level indicates the severity or importance.</span></span> <span data-ttu-id="5873e-215">Můžete například zapsat `Information` protokol, když metoda `Warning` končí normálně a protokol, když metoda vrátí stavový kód *404 Not Found.*</span><span class="sxs-lookup"><span data-stu-id="5873e-215">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="5873e-216">Následující kód `Information` vytvoří `Warning` a protokoly:</span><span class="sxs-lookup"><span data-stu-id="5873e-216">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="5873e-217">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="5873e-217">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="5873e-218">Druhým parametrem je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytnuté zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="5873e-218">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="5873e-219">Parametry metody jsou vysvětleny v [části šablony zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="5873e-219">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="5873e-220">Metody protokolu, které zahrnují úroveň v názvu `LogInformation` `LogWarning`metody (například a ) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="5873e-220">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="5873e-221">Tyto metody `Log` volání metody, `LogLevel` která přebírá parametr.</span><span class="sxs-lookup"><span data-stu-id="5873e-221">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="5873e-222">Můžete volat `Log` metodu přímo, nikoli jednu z těchto metod rozšíření, ale syntaxe je poměrně složité.</span><span class="sxs-lookup"><span data-stu-id="5873e-222">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="5873e-223">Další informace naleznete <xref:Microsoft.Extensions.Logging.ILogger> v tématu a [rozšíření protokolů zdrojový kód](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="5873e-223">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="5873e-224">ASP.NET Core definuje následující úrovně protokolu, seřazené zde od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="5873e-224">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="5873e-225">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="5873e-225">Trace = 0</span></span>

  <span data-ttu-id="5873e-226">Informace, které je obvykle cenné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="5873e-226">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="5873e-227">Tyto zprávy mohou obsahovat citlivá data aplikace, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5873e-227">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="5873e-228">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="5873e-228">*Disabled by default.*</span></span>

* <span data-ttu-id="5873e-229">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="5873e-229">Debug = 1</span></span>

  <span data-ttu-id="5873e-230">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="5873e-230">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="5873e-231">Příklad: `Entering method Configure with flag set to true.` `Debug` Povolit protokoly úrovně v produkčním prostředí pouze při řešení potíží, z důvodu velkého objemu protokolů.</span><span class="sxs-lookup"><span data-stu-id="5873e-231">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="5873e-232">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="5873e-232">Information = 2</span></span>

  <span data-ttu-id="5873e-233">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-233">For tracking the general flow of the app.</span></span> <span data-ttu-id="5873e-234">Tyto protokoly mají obvykle některé dlouhodobé hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5873e-234">These logs typically have some long-term value.</span></span> <span data-ttu-id="5873e-235">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="5873e-235">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="5873e-236">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="5873e-236">Warning = 3</span></span>

  <span data-ttu-id="5873e-237">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-237">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="5873e-238">Mohou mezi ně patřit chyby nebo jiné podmínky, které nezpůsobují zastavení aplikace, ale může být nutné ji prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="5873e-238">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="5873e-239">Zpracované výjimky jsou běžné místo `Warning` pro použití na úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-239">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="5873e-240">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="5873e-240">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="5873e-241">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="5873e-241">Error = 4</span></span>

  <span data-ttu-id="5873e-242">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="5873e-242">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="5873e-243">Tyto zprávy označují selhání v aktuální aktivity nebo operace (například aktuální požadavek HTTP), není selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-243">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="5873e-244">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="5873e-244">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="5873e-245">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="5873e-245">Critical = 5</span></span>

  <span data-ttu-id="5873e-246">Pro poruchy, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="5873e-246">For failures that require immediate attention.</span></span> <span data-ttu-id="5873e-247">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="5873e-247">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="5873e-248">Pomocí úrovně protokolu můžete určit, kolik výstupu protokolu je zapsáno do určitého paměťového média nebo okna zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5873e-248">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="5873e-249">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5873e-249">For example:</span></span>

* <span data-ttu-id="5873e-250">Ve výrobě:</span><span class="sxs-lookup"><span data-stu-id="5873e-250">In production:</span></span>
  * <span data-ttu-id="5873e-251">Protokolování `Trace` na `Information` úrovních pro vytváří velký objem podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-251">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="5873e-252">Chcete-li řídit náklady a nepřekročit limity úložiště dat, zařazujte `Trace` zprávy `Information` na úrovni do úložiště dat s vysokým objemem a nízkými náklady.</span><span class="sxs-lookup"><span data-stu-id="5873e-252">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="5873e-253">Protokolování `Warning` `Critical` na úrovních obvykle vytváří méně, menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-253">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="5873e-254">Proto náklady a limity úložiště obvykle nejsou problém, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="5873e-254">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="5873e-255">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="5873e-255">During development:</span></span>
  * <span data-ttu-id="5873e-256">`Warning` Protokolovat `Critical` prostřednictvím zpráv do konzoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-256">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="5873e-257">Při `Trace` `Information` potížích přidávejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="5873e-257">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="5873e-258">Oddíl [Filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovatel zpracovává.</span><span class="sxs-lookup"><span data-stu-id="5873e-258">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="5873e-259">ASP.NET Core zapisuje protokoly pro rámcové události.</span><span class="sxs-lookup"><span data-stu-id="5873e-259">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="5873e-260">Příklady protokolu dříve v tomto článku `Information` vyloučeny protokoly pod úrovní, takže byly vytvořeny žádné `Debug` protokoly nebo `Trace` úroveň.</span><span class="sxs-lookup"><span data-stu-id="5873e-260">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="5873e-261">Tady je příklad protokolů konzoly vytvořených spuštěním `Debug` ukázkové aplikace nakonfigurované tak, aby zobrazovala protokoly:</span><span class="sxs-lookup"><span data-stu-id="5873e-261">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="5873e-262">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-262">Log event ID</span></span>

<span data-ttu-id="5873e-263">Každý protokol může určit *ID události*.</span><span class="sxs-lookup"><span data-stu-id="5873e-263">Each log can specify an *event ID*.</span></span> <span data-ttu-id="5873e-264">Ukázková aplikace to provádí pomocí `LoggingEvents` místně definované třídy:</span><span class="sxs-lookup"><span data-stu-id="5873e-264">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="5873e-265">ID události přidruží sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="5873e-265">An event ID associates a set of events.</span></span> <span data-ttu-id="5873e-266">Všechny protokoly související se zobrazením seznamu položek na stránce mohou být například 1001.</span><span class="sxs-lookup"><span data-stu-id="5873e-266">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="5873e-267">Zprostředkovatel protokolování může uložit ID události v poli ID, ve zprávě protokolování nebo vůbec.</span><span class="sxs-lookup"><span data-stu-id="5873e-267">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="5873e-268">Poskytovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="5873e-268">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="5873e-269">Zprostředkovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="5873e-269">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="5873e-270">Šablona protokolu zpráv</span><span class="sxs-lookup"><span data-stu-id="5873e-270">Log message template</span></span>

<span data-ttu-id="5873e-271">Každý protokol určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="5873e-271">Each log specifies a message template.</span></span> <span data-ttu-id="5873e-272">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="5873e-272">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="5873e-273">Používejte názvy pro zástupné symboly, ne čísla.</span><span class="sxs-lookup"><span data-stu-id="5873e-273">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="5873e-274">Pořadí zástupných symbolů, nikoli jejich názvy, určuje, které parametry se používají k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="5873e-274">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="5873e-275">V následujícím kódu si všimněte, že názvy parametrů jsou mimo pořadí v šabloně zprávy:</span><span class="sxs-lookup"><span data-stu-id="5873e-275">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="5873e-276">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:</span><span class="sxs-lookup"><span data-stu-id="5873e-276">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="5873e-277">Architektura protokolování funguje tak, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5873e-277">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="5873e-278">Samotné argumenty jsou předány systému protokolování, nikoli pouze formátované šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="5873e-278">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="5873e-279">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="5873e-279">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="5873e-280">Předpokládejme například, že volání metody logger vypadají takto:</span><span class="sxs-lookup"><span data-stu-id="5873e-280">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="5873e-281">Pokud odesíláte protokoly do Azure Table Storage, každá `ID` `RequestTime` entita tabulky Azure může mít a vlastnosti, což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-281">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="5873e-282">Dotaz může najít všechny protokoly `RequestTime` v rámci určité oblasti bez analýzy doby mimo textovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="5873e-282">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="5873e-283">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="5873e-283">Logging exceptions</span></span>

<span data-ttu-id="5873e-284">Metody protokolování mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5873e-284">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="5873e-285">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="5873e-285">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="5873e-286">Tady je příklad výstupu zprostředkovatele ladění z výše uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-286">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="5873e-287">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="5873e-287">Log filtering</span></span>

<span data-ttu-id="5873e-288">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny zprostředkovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-288">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="5873e-289">Všechny protokoly pod minimální úroveň nejsou předány tomuto zprostředkovateli, takže se nezobrazí ani neuloží.</span><span class="sxs-lookup"><span data-stu-id="5873e-289">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="5873e-290">Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-290">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="5873e-291">Celá hodnota `LogLevel.None` je 6, která je `LogLevel.Critical` vyšší než (5).</span><span class="sxs-lookup"><span data-stu-id="5873e-291">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="5873e-292">Vytvoření pravidel filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="5873e-292">Create filter rules in configuration</span></span>

<span data-ttu-id="5873e-293">Kód šablony projektu `CreateDefaultBuilder` volá k nastavení protokolování pro zprostředkovatele konzoly, ladění a zdroje událostí (ASP.NET Core 2.2 nebo novějším).</span><span class="sxs-lookup"><span data-stu-id="5873e-293">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="5873e-294">Metoda `CreateDefaultBuilder` nastaví protokolování hledat konfiguraci v `Logging` části, jak je vysvětleno dříve v tomto [článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="5873e-294">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="5873e-295">Konfigurační data určují minimální úrovně protokolu podle zprostředkovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5873e-295">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="5873e-296">Tento JSON vytvoří šest pravidel filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a jeden pro všechny zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-296">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="5873e-297">Jedno pravidlo je vybráno pro `ILogger` každého zprostředkovatele při vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="5873e-297">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="5873e-298">Filtrování pravidel v kódu</span><span class="sxs-lookup"><span data-stu-id="5873e-298">Filter rules in code</span></span>

<span data-ttu-id="5873e-299">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="5873e-299">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="5873e-300">Druhý `AddFilter` určuje zprostředkovatele ladění pomocí jeho název typu.</span><span class="sxs-lookup"><span data-stu-id="5873e-300">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="5873e-301">První `AddFilter` platí pro všechny zprostředkovatele, protože neurčuje typ zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-301">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="5873e-302">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="5873e-302">How filtering rules are applied</span></span>

<span data-ttu-id="5873e-303">Konfigurační `AddFilter` data a kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="5873e-303">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="5873e-304">Prvních šest pocházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-304">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="5873e-305">Číslo</span><span class="sxs-lookup"><span data-stu-id="5873e-305">Number</span></span> | <span data-ttu-id="5873e-306">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="5873e-306">Provider</span></span>      | <span data-ttu-id="5873e-307">Kategorie, které začínají ...</span><span class="sxs-lookup"><span data-stu-id="5873e-307">Categories that begin with ...</span></span>          | <span data-ttu-id="5873e-308">Minimální úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-308">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="5873e-309">1</span><span class="sxs-lookup"><span data-stu-id="5873e-309">1</span></span>      | <span data-ttu-id="5873e-310">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-310">Debug</span></span>         | <span data-ttu-id="5873e-311">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="5873e-311">All categories</span></span>                          | <span data-ttu-id="5873e-312">Informace</span><span class="sxs-lookup"><span data-stu-id="5873e-312">Information</span></span>       |
| <span data-ttu-id="5873e-313">2</span><span class="sxs-lookup"><span data-stu-id="5873e-313">2</span></span>      | <span data-ttu-id="5873e-314">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-314">Console</span></span>       | <span data-ttu-id="5873e-315">Microsoft.AspNetCore.Mvc.Razor.Interní</span><span class="sxs-lookup"><span data-stu-id="5873e-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="5873e-316">Upozornění</span><span class="sxs-lookup"><span data-stu-id="5873e-316">Warning</span></span>           |
| <span data-ttu-id="5873e-317">3</span><span class="sxs-lookup"><span data-stu-id="5873e-317">3</span></span>      | <span data-ttu-id="5873e-318">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-318">Console</span></span>       | <span data-ttu-id="5873e-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="5873e-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="5873e-320">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-320">Debug</span></span>             |
| <span data-ttu-id="5873e-321">4</span><span class="sxs-lookup"><span data-stu-id="5873e-321">4</span></span>      | <span data-ttu-id="5873e-322">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-322">Console</span></span>       | <span data-ttu-id="5873e-323">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="5873e-323">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="5873e-324">Chyba</span><span class="sxs-lookup"><span data-stu-id="5873e-324">Error</span></span>             |
| <span data-ttu-id="5873e-325">5</span><span class="sxs-lookup"><span data-stu-id="5873e-325">5</span></span>      | <span data-ttu-id="5873e-326">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-326">Console</span></span>       | <span data-ttu-id="5873e-327">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="5873e-327">All categories</span></span>                          | <span data-ttu-id="5873e-328">Informace</span><span class="sxs-lookup"><span data-stu-id="5873e-328">Information</span></span>       |
| <span data-ttu-id="5873e-329">6</span><span class="sxs-lookup"><span data-stu-id="5873e-329">6</span></span>      | <span data-ttu-id="5873e-330">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="5873e-330">All providers</span></span> | <span data-ttu-id="5873e-331">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="5873e-331">All categories</span></span>                          | <span data-ttu-id="5873e-332">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-332">Debug</span></span>             |
| <span data-ttu-id="5873e-333">7</span><span class="sxs-lookup"><span data-stu-id="5873e-333">7</span></span>      | <span data-ttu-id="5873e-334">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="5873e-334">All providers</span></span> | <span data-ttu-id="5873e-335">Systém</span><span class="sxs-lookup"><span data-stu-id="5873e-335">System</span></span>                                  | <span data-ttu-id="5873e-336">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-336">Debug</span></span>             |
| <span data-ttu-id="5873e-337">8</span><span class="sxs-lookup"><span data-stu-id="5873e-337">8</span></span>      | <span data-ttu-id="5873e-338">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-338">Debug</span></span>         | <span data-ttu-id="5873e-339">Microsoft</span><span class="sxs-lookup"><span data-stu-id="5873e-339">Microsoft</span></span>                               | <span data-ttu-id="5873e-340">Trasování</span><span class="sxs-lookup"><span data-stu-id="5873e-340">Trace</span></span>             |

<span data-ttu-id="5873e-341">Při `ILogger` vytvoření objektu, `ILoggerFactory` objekt vybere jedno pravidlo pro zprostředkovatele použít na tento protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-341">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="5873e-342">Všechny zprávy napsané instancí `ILogger` jsou filtrovány na základě vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="5873e-342">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="5873e-343">Nejkonkrétnější pravidlo možné pro každého poskytovatele a dvojice kategorií je vybránz dostupných pravidel.</span><span class="sxs-lookup"><span data-stu-id="5873e-343">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="5873e-344">Následující algoritmus se používá pro `ILogger` každého zprostředkovatele při vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="5873e-344">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="5873e-345">Vyberte všechna pravidla, která odpovídají poskytovateli nebo jeho aliasu.</span><span class="sxs-lookup"><span data-stu-id="5873e-345">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="5873e-346">Pokud není nalezena žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="5873e-346">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="5873e-347">Z výsledku předchozího kroku vyberte pravidla s nejdelší předponou kategorie shody.</span><span class="sxs-lookup"><span data-stu-id="5873e-347">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="5873e-348">Pokud není nalezena žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="5873e-348">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="5873e-349">Pokud je vybráno více pravidel, vezměte **poslední.**</span><span class="sxs-lookup"><span data-stu-id="5873e-349">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="5873e-350">Pokud nejsou vybrána `MinimumLevel`žádná pravidla, použijte .</span><span class="sxs-lookup"><span data-stu-id="5873e-350">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="5873e-351">S předchozím seznamem pravidel předpokládejme, `ILogger` že vytvoříte objekt pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="5873e-351">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="5873e-352">Pro zprostředkovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="5873e-352">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="5873e-353">Pravidlo 8 je nejkonkrétnější, takže to je ten, který byl vybrán.</span><span class="sxs-lookup"><span data-stu-id="5873e-353">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="5873e-354">Pro zprostředkovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="5873e-354">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="5873e-355">Pravidlo 3 je nejkonkrétnější.</span><span class="sxs-lookup"><span data-stu-id="5873e-355">Rule 3 is most specific.</span></span>

<span data-ttu-id="5873e-356">Výsledná `ILogger` instance odešle `Trace` protokoly úrovně a výše zprostředkovateli ladění.</span><span class="sxs-lookup"><span data-stu-id="5873e-356">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="5873e-357">Protokoly `Debug` úrovně a výše jsou odesílány zprostředkovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-357">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="5873e-358">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="5873e-358">Provider aliases</span></span>

<span data-ttu-id="5873e-359">Každý zprostředkovatel definuje *alias,* který lze použít v konfiguraci namísto plně kvalifikovaný název typu.</span><span class="sxs-lookup"><span data-stu-id="5873e-359">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="5873e-360">Pro předdefinované zprostředkovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="5873e-360">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="5873e-361">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-361">Console</span></span>
* <span data-ttu-id="5873e-362">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-362">Debug</span></span>
* <span data-ttu-id="5873e-363">EventSource</span><span class="sxs-lookup"><span data-stu-id="5873e-363">EventSource</span></span>
* <span data-ttu-id="5873e-364">Eventlog</span><span class="sxs-lookup"><span data-stu-id="5873e-364">EventLog</span></span>
* <span data-ttu-id="5873e-365">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5873e-365">TraceSource</span></span>
* <span data-ttu-id="5873e-366">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="5873e-366">AzureAppServicesFile</span></span>
* <span data-ttu-id="5873e-367">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="5873e-367">AzureAppServicesBlob</span></span>
* <span data-ttu-id="5873e-368">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="5873e-368">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="5873e-369">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="5873e-369">Default minimum level</span></span>

<span data-ttu-id="5873e-370">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že pro daného zprostředkovatele a kategorii neplatí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-370">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="5873e-371">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="5873e-371">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="5873e-372">Pokud explicitně nenastavíte minimální úroveň, `Information`výchozí hodnota `Trace` je `Debug` , což znamená, že a protokoly jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="5873e-372">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="5873e-373">Funkce filtrování</span><span class="sxs-lookup"><span data-stu-id="5873e-373">Filter functions</span></span>

<span data-ttu-id="5873e-374">Funkce filtru je vyvolána pro všechny zprostředkovatele a kategorie, které nemají pravidla přiřazená podle konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-374">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="5873e-375">Kód ve funkci má přístup k typu zprostředkovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-375">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="5873e-376">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5873e-376">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="5873e-377">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="5873e-377">System categories and levels</span></span>

<span data-ttu-id="5873e-378">Zde jsou některé kategorie používané ASP.NET jádrem a jádrem entity s poznámkami o tom, jaké protokoly od nich lze očekávat:</span><span class="sxs-lookup"><span data-stu-id="5873e-378">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="5873e-379">Kategorie</span><span class="sxs-lookup"><span data-stu-id="5873e-379">Category</span></span>                            | <span data-ttu-id="5873e-380">Poznámky</span><span class="sxs-lookup"><span data-stu-id="5873e-380">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="5873e-381">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="5873e-381">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="5873e-382">Obecná diagnostika ASP.NET jádra.</span><span class="sxs-lookup"><span data-stu-id="5873e-382">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="5873e-383">Ochrana Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="5873e-383">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="5873e-384">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="5873e-384">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="5873e-385">Filtrování Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="5873e-385">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="5873e-386">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="5873e-386">Hosts allowed.</span></span> |
| <span data-ttu-id="5873e-387">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="5873e-387">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="5873e-388">Jak dlouho trvalo dokončení požadavků HTTP a v jakém čase byly spuštěny.</span><span class="sxs-lookup"><span data-stu-id="5873e-388">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="5873e-389">Která hostitelská spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="5873e-389">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="5873e-390">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="5873e-390">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="5873e-391">Diagnostika MVC a Razor.</span><span class="sxs-lookup"><span data-stu-id="5873e-391">MVC and Razor diagnostics.</span></span> <span data-ttu-id="5873e-392">Vazba modelu, spuštění filtru, kompilace zobrazení, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="5873e-392">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="5873e-393">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="5873e-393">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="5873e-394">Informace o porovnávání tras.</span><span class="sxs-lookup"><span data-stu-id="5873e-394">Route matching information.</span></span> |
| <span data-ttu-id="5873e-395">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="5873e-395">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="5873e-396">Připojení spustit, zastavit a udržet naživu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="5873e-396">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="5873e-397">informace o certifikátu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5873e-397">HTTPS certificate information.</span></span> |
| <span data-ttu-id="5873e-398">Soubory Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="5873e-398">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="5873e-399">Doručené složky.</span><span class="sxs-lookup"><span data-stu-id="5873e-399">Files served.</span></span> |
| <span data-ttu-id="5873e-400">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="5873e-400">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="5873e-401">Diagnostika jádra obecnéentity frameworku.</span><span class="sxs-lookup"><span data-stu-id="5873e-401">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="5873e-402">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="5873e-402">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="5873e-403">Obory protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-403">Log scopes</span></span>

 <span data-ttu-id="5873e-404">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="5873e-404">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="5873e-405">Toto seskupení lze použít k připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="5873e-405">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="5873e-406">Například každý protokol vytvořený jako součást zpracování transakce může obsahovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="5873e-406">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="5873e-407">Obor je `IDisposable` typ, který je <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> vrácen metodou a trvá, dokud je uvolněn.</span><span class="sxs-lookup"><span data-stu-id="5873e-407">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="5873e-408">Použití oboru zabalením volání `using` protokolování v bloku:</span><span class="sxs-lookup"><span data-stu-id="5873e-408">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="5873e-409">Následující kód umožňuje obory pro zprostředkovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="5873e-409">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="5873e-410">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5873e-410">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="5873e-411">Konfigurace možnosti `IncludeScopes` protokolování konzoly je nutná k povolení protokolování založeného na oboru.</span><span class="sxs-lookup"><span data-stu-id="5873e-411">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="5873e-412">Informace o konfiguraci naleznete v části [Konfigurace.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="5873e-412">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="5873e-413">Každá zpráva protokolu obsahuje informace s vymezenou oborem:</span><span class="sxs-lookup"><span data-stu-id="5873e-413">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="5873e-414">Vestavění poskytovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="5873e-414">Built-in logging providers</span></span>

<span data-ttu-id="5873e-415">ASP.NET Core dodává tyto poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="5873e-415">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="5873e-416">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-416">Console</span></span>](#console-provider)
* [<span data-ttu-id="5873e-417">Ladění</span><span class="sxs-lookup"><span data-stu-id="5873e-417">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="5873e-418">Zdroj událostí</span><span class="sxs-lookup"><span data-stu-id="5873e-418">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="5873e-419">Eventlog</span><span class="sxs-lookup"><span data-stu-id="5873e-419">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="5873e-420">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5873e-420">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="5873e-421">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="5873e-421">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="5873e-422">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="5873e-422">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="5873e-423">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="5873e-423">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="5873e-424">Informace o protokolování stdout a ladění pomocí <xref:test/troubleshoot-azure-iis> ASP.NET <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>core modulem naleznete v tématu a .</span><span class="sxs-lookup"><span data-stu-id="5873e-424">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="5873e-425">Zprostředkovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="5873e-425">Console provider</span></span>

<span data-ttu-id="5873e-426">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-426">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="5873e-427">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5873e-427">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="5873e-428">Zprostředkovatel ladění</span><span class="sxs-lookup"><span data-stu-id="5873e-428">Debug provider</span></span>

<span data-ttu-id="5873e-429">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí`Debug.WriteLine` [třídy System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (volání metody).</span><span class="sxs-lookup"><span data-stu-id="5873e-429">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="5873e-430">V systému Linux tento zprostředkovatel zapisuje protokoly do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="5873e-430">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="5873e-431">Zprostředkovatel zdroje událostí</span><span class="sxs-lookup"><span data-stu-id="5873e-431">Event Source provider</span></span>

<span data-ttu-id="5873e-432">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na `Microsoft-Extensions-Logging`křížovou platformu Zdroje událostí s názvem .</span><span class="sxs-lookup"><span data-stu-id="5873e-432">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="5873e-433">V systému Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="5873e-433">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="5873e-434">Zprostředkovatel zdroje událostí je `CreateDefaultBuilder` přidán automaticky, když je volána k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="5873e-434">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="5873e-435">nástroje pro trasování dotnet</span><span class="sxs-lookup"><span data-stu-id="5873e-435">dotnet trace tooling</span></span>

<span data-ttu-id="5873e-436">[Dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) nástroj je globální nástroj rozhraní se sídlem v rychléplatformě, který umožňuje shromažďování trasování .NET Core spuštěného procesu.</span><span class="sxs-lookup"><span data-stu-id="5873e-436">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="5873e-437">Nástroj shromažďuje <xref:Microsoft.Extensions.Logging.EventSource> data zprostředkovatele <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>pomocí .</span><span class="sxs-lookup"><span data-stu-id="5873e-437">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="5873e-438">Nainstalujte nástroje trasování dotnet pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="5873e-438">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="5873e-439">Pomocí nástrojů sledování dotnet shromažďujte trasování z aplikace:</span><span class="sxs-lookup"><span data-stu-id="5873e-439">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="5873e-440">Pokud aplikace nevytváří hostitele s `CreateDefaultBuilder`, přidejte [poskytovatele zdroje událostí](#event-source-provider) do konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-440">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="5873e-441">Spusťte aplikaci pomocí příkazu. `dotnet run`</span><span class="sxs-lookup"><span data-stu-id="5873e-441">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="5873e-442">Určete identifikátor procesu (PID) aplikace .NET Core:</span><span class="sxs-lookup"><span data-stu-id="5873e-442">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="5873e-443">V systému Windows použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="5873e-443">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="5873e-444">Správce úloh (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="5873e-444">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="5873e-445">seznam úloh, příkaz</span><span class="sxs-lookup"><span data-stu-id="5873e-445">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="5873e-446">Příkaz Získat proces powershellu</span><span class="sxs-lookup"><span data-stu-id="5873e-446">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="5873e-447">Na Linuxu použijte [příkaz pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="5873e-447">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="5873e-448">Najděte PID pro proces, který má stejný název jako sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-448">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="5873e-449">Spusťte `dotnet trace` příkaz.</span><span class="sxs-lookup"><span data-stu-id="5873e-449">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="5873e-450">Obecná syntaxe příkazu:</span><span class="sxs-lookup"><span data-stu-id="5873e-450">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="5873e-451">Při použití příkazového prostředí Prostředí `--providers` PowerShell uzavřete hodnotu do jednoduchých uvozovek (`'`):</span><span class="sxs-lookup"><span data-stu-id="5873e-451">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="5873e-452">Na platformách jiných než `-f speedscope` Windows přidejte možnost změnit formát `speedscope`výstupního trasovacího souboru na .</span><span class="sxs-lookup"><span data-stu-id="5873e-452">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="5873e-453">Klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="5873e-453">Keyword</span></span> | <span data-ttu-id="5873e-454">Popis</span><span class="sxs-lookup"><span data-stu-id="5873e-454">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="5873e-455">1</span><span class="sxs-lookup"><span data-stu-id="5873e-455">1</span></span>       | <span data-ttu-id="5873e-456">Protokolovat meta `LoggingEventSource`události o .</span><span class="sxs-lookup"><span data-stu-id="5873e-456">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="5873e-457">Nezaznamenává události z `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="5873e-457">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="5873e-458">2</span><span class="sxs-lookup"><span data-stu-id="5873e-458">2</span></span>       | <span data-ttu-id="5873e-459">Zapne událost, `Message` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="5873e-459">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="5873e-460">Poskytuje informace programovým (neformátovaným) způsobem.</span><span class="sxs-lookup"><span data-stu-id="5873e-460">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="5873e-461">4</span><span class="sxs-lookup"><span data-stu-id="5873e-461">4</span></span>       | <span data-ttu-id="5873e-462">Zapne událost, `FormatMessage` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="5873e-462">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="5873e-463">Poskytuje formátovanou verzi řetězce informací.</span><span class="sxs-lookup"><span data-stu-id="5873e-463">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="5873e-464">8</span><span class="sxs-lookup"><span data-stu-id="5873e-464">8</span></span>       | <span data-ttu-id="5873e-465">Zapne událost, `MessageJson` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="5873e-465">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="5873e-466">Poskytuje json reprezentaci argumentů.</span><span class="sxs-lookup"><span data-stu-id="5873e-466">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="5873e-467">Úroveň události</span><span class="sxs-lookup"><span data-stu-id="5873e-467">Event Level</span></span> | <span data-ttu-id="5873e-468">Popis</span><span class="sxs-lookup"><span data-stu-id="5873e-468">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="5873e-469">0</span><span class="sxs-lookup"><span data-stu-id="5873e-469">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="5873e-470">1</span><span class="sxs-lookup"><span data-stu-id="5873e-470">1</span></span>           | `Critical`      |
   | <span data-ttu-id="5873e-471">2</span><span class="sxs-lookup"><span data-stu-id="5873e-471">2</span></span>           | `Error`         |
   | <span data-ttu-id="5873e-472">3</span><span class="sxs-lookup"><span data-stu-id="5873e-472">3</span></span>           | `Warning`       |
   | <span data-ttu-id="5873e-473">4</span><span class="sxs-lookup"><span data-stu-id="5873e-473">4</span></span>           | `Informational` |
   | <span data-ttu-id="5873e-474">5</span><span class="sxs-lookup"><span data-stu-id="5873e-474">5</span></span>           | `Verbose`       |

   <span data-ttu-id="5873e-475">`FilterSpecs`položky `{Logger Category}` `{Event Level}` pro a představují další podmínky filtrování protokolů.</span><span class="sxs-lookup"><span data-stu-id="5873e-475">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="5873e-476">Samostatné `FilterSpecs` položky s`;`středníkem ( ).</span><span class="sxs-lookup"><span data-stu-id="5873e-476">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="5873e-477">Příklad použití příkazového prostředí systému `--providers` Windows **(žádné** jednoduché uvozovky kolem hodnoty):</span><span class="sxs-lookup"><span data-stu-id="5873e-477">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="5873e-478">Předchozí příkaz aktivuje:</span><span class="sxs-lookup"><span data-stu-id="5873e-478">The preceding command activates:</span></span>

   * <span data-ttu-id="5873e-479">Protokolovací nástroj Zdroje událostí pro`4`vytvoření formátovaných`2`řetězců ( ) pro chyby ( ).</span><span class="sxs-lookup"><span data-stu-id="5873e-479">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="5873e-480">`Microsoft.AspNetCore.Hosting`protokolování `Informational` na úrovni`4`protokolování ( ).</span><span class="sxs-lookup"><span data-stu-id="5873e-480">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="5873e-481">Zastavení nástroje trasování dotnet stisknutím klávesy Enter nebo Ctrl+C.</span><span class="sxs-lookup"><span data-stu-id="5873e-481">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="5873e-482">Trasování je uložens s názvem *trace.nettrace* ve složce, `dotnet trace` kde je příkaz proveden.</span><span class="sxs-lookup"><span data-stu-id="5873e-482">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="5873e-483">Otevřete trasování pomocí [perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="5873e-483">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="5873e-484">Otevřete soubor *trace.nettrace* a prozkoumejte události trasování.</span><span class="sxs-lookup"><span data-stu-id="5873e-484">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="5873e-485">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="5873e-485">For more information, see:</span></span>

* <span data-ttu-id="5873e-486">[Trasování pro nástroj pro analýzu výkonu (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentace jádra.NET)</span><span class="sxs-lookup"><span data-stu-id="5873e-486">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="5873e-487">[Trasování pro nástroj pro analýzu výkonu (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentace úložiště dotnet/diagnostics GitHub)</span><span class="sxs-lookup"><span data-stu-id="5873e-487">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="5873e-488">[Třída LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (prohlížeč rozhraní API.NET)</span><span class="sxs-lookup"><span data-stu-id="5873e-488">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="5873e-489">[Název reference LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Chcete-li získat referenční zdroj `release/{Version}`pro `{Version}` jinou verzi, změňte větev na , kde je požadovaná verze ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5873e-489">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="5873e-490">[Perfview](#perfview) &ndash; Užitečné pro zobrazení trasování zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="5873e-490">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="5873e-491">Zobrazení Perfview</span><span class="sxs-lookup"><span data-stu-id="5873e-491">Perfview</span></span>

<span data-ttu-id="5873e-492">Pomocí [nástroje PerfView](https://github.com/Microsoft/perfview) sbírat a zobrazovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-492">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="5873e-493">Existují další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi ETW vyzařované ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5873e-493">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="5873e-494">Chcete-li nakonfigurovat PerfView pro shromažďování událostí `*Microsoft-Extensions-Logging` protokolovaných tímto zprostředkovatelem, přidejte řetězec do seznamu **Další zprostředkovatelé.**</span><span class="sxs-lookup"><span data-stu-id="5873e-494">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="5873e-495">(Nenechte si ujít hvězdičku na začátku řetězce.)</span><span class="sxs-lookup"><span data-stu-id="5873e-495">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="5873e-497">Zprostředkovatel služby Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="5873e-497">Windows EventLog provider</span></span>

<span data-ttu-id="5873e-498">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5873e-498">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="5873e-499">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="5873e-499">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="5873e-500">Pokud `null` není zadáno, použije se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="5873e-500">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="5873e-501">`LogName`&ndash; "Aplikace"</span><span class="sxs-lookup"><span data-stu-id="5873e-501">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="5873e-502">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="5873e-502">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="5873e-503">`MachineName`&ndash; místní počítač</span><span class="sxs-lookup"><span data-stu-id="5873e-503">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="5873e-504">Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level).</span><span class="sxs-lookup"><span data-stu-id="5873e-504">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="5873e-505">Chcete-li protokolovat události nižší než `Warning`, explicitně nastavte úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-505">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="5873e-506">Do souboru *appsettings.json* například přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="5873e-506">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="5873e-507">Zprostředkovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="5873e-507">TraceSource provider</span></span>

<span data-ttu-id="5873e-508">Balíček [zprostředkovatele Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-508">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="5873e-509">[AddTraceSource přetížení](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="5873e-509">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="5873e-510">Chcete-li použít tohoto zprostředkovatele, aplikace musí běžet na rozhraní .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="5873e-510">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="5873e-511">Zprostředkovatel může směrovat zprávy do různých [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použité v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5873e-511">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="5873e-512">Poskytovatel služeb Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5873e-512">Azure App Service provider</span></span>

<span data-ttu-id="5873e-513">Balíček zprostředkovatele [Microsoft.Extensions.Log.Log.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Služby Azure App Service a do [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="5873e-513">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="5873e-514">Balíček zprostředkovatele není součástí sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5873e-514">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="5873e-515">Chcete-li použít zprostředkovatele, přidejte balíček zprostředkovatele do projektu.</span><span class="sxs-lookup"><span data-stu-id="5873e-515">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="5873e-516">Chcete-li konfigurovat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> nastavení <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>zprostředkovatele, použijte a , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5873e-516">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="5873e-517">Když se nasadíte do aplikace App Service, aplikace respektuje nastavení v části [protokoly služby App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stránce **Služby aplikací** na portálu Azure.</span><span class="sxs-lookup"><span data-stu-id="5873e-517">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="5873e-518">Při aktualizaci následujících nastavení se změny projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-518">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="5873e-519">**Protokolování aplikací (souborový systém)**</span><span class="sxs-lookup"><span data-stu-id="5873e-519">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="5873e-520">**Protokolování aplikací (objekt blob)**</span><span class="sxs-lookup"><span data-stu-id="5873e-520">**Application Logging (Blob)**</span></span>

<span data-ttu-id="5873e-521">Výchozí umístění souborů protokolu je ve složce *D:\\\\home LogFiles\\Application* a výchozí název souboru je *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="5873e-521">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="5873e-522">Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="5873e-522">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="5873e-523">Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="5873e-523">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="5873e-524">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="5873e-524">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="5873e-525">Nemá žádný vliv, když je&mdash;projekt spuštěn místně, nezapisuje do místních souborů nebo místní vývojové úložiště pro objekty BLOB.</span><span class="sxs-lookup"><span data-stu-id="5873e-525">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="5873e-526">Streamování protokolu Azure</span><span class="sxs-lookup"><span data-stu-id="5873e-526">Azure log streaming</span></span>

<span data-ttu-id="5873e-527">Streamování protokolu Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="5873e-527">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="5873e-528">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="5873e-528">The app server</span></span>
* <span data-ttu-id="5873e-529">Webový server</span><span class="sxs-lookup"><span data-stu-id="5873e-529">The web server</span></span>
* <span data-ttu-id="5873e-530">Trasování neúspěšných požadavků</span><span class="sxs-lookup"><span data-stu-id="5873e-530">Failed request tracing</span></span>

<span data-ttu-id="5873e-531">Konfigurace streamování protokolu Azure:</span><span class="sxs-lookup"><span data-stu-id="5873e-531">To configure Azure log streaming:</span></span>

* <span data-ttu-id="5873e-532">Přejděte na stránku **protokolů služby App Service** na stránce portálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-532">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="5873e-533">Nastavení **protokolování aplikací (souborový systém)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="5873e-533">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="5873e-534">Zvolte **úroveň**protokolu .</span><span class="sxs-lookup"><span data-stu-id="5873e-534">Choose the log **Level**.</span></span> <span data-ttu-id="5873e-535">Toto nastavení platí jenom pro streamování protokolu Azure, ne pro jiné poskytovatele protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5873e-535">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="5873e-536">Přejděte na stránku **Log Stream** a zobrazte zprávy aplikací.</span><span class="sxs-lookup"><span data-stu-id="5873e-536">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="5873e-537">Jsou zaznamenány aplikací prostřednictvím `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5873e-537">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="5873e-538">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="5873e-538">Azure Application Insights trace logging</span></span>

<span data-ttu-id="5873e-539">Balíček zprostředkovatele [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5873e-539">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="5873e-540">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu telemetrických dat.</span><span class="sxs-lookup"><span data-stu-id="5873e-540">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="5873e-541">Pokud používáte tohoto zprostředkovatele, můžete dotazovat a analyzovat protokoly pomocí nástrojů Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5873e-541">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="5873e-542">Zprostředkovatel protokolování je součástí jako závislost [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje všechny dostupné telemetrie pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5873e-542">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="5873e-543">Pokud používáte tento balíček, není nutné instalovat balíček zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-543">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="5873e-544">Nepoužívejte balíček&mdash; [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) pro ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="5873e-544">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="5873e-545">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="5873e-545">For more information, see the following resources:</span></span>

* [<span data-ttu-id="5873e-546">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="5873e-546">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="5873e-547">[Application Insights pro ASP.NET základní aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou škálu telemetrie Application Insights spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="5873e-547">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="5873e-548">[ApplicationInsightsLoggerProvider pro protokoly ILogger jádra .NET](/azure/azure-monitor/app/ilogger) – začněte zde, pokud chcete implementovat poskytovatele protokolování bez zbytku telemetrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5873e-548">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="5873e-549">[Adaptéry pro protokolování Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="5873e-549">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="5873e-550">[Nainstalujte, nakonfigurujte a inicializujte kurz Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="5873e-550">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="5873e-551">Poskytovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="5873e-551">Third-party logging providers</span></span>

<span data-ttu-id="5873e-552">Architektury protokolování třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5873e-552">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="5873e-553">[elmah.io](https://elmah.io/) ([Úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5873e-553">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="5873e-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="5873e-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="5873e-555">[JSNLog](https://jsnlog.com/) ([Úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="5873e-555">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="5873e-556">[KissLog.net](https://kisslog.net/) ([Úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="5873e-556">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="5873e-557">[Log4Net](https://logging.apache.org/log4net/) ([Úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="5873e-557">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="5873e-558">[Loggr](https://loggr.net/) ([Úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5873e-558">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="5873e-559">[NLog](https://nlog-project.org/) ([Úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5873e-559">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="5873e-560">[Sentry](https://sentry.io/welcome/) ([Úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="5873e-560">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="5873e-561">[Serilog](https://serilog.net/) ([Úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="5873e-561">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="5873e-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Úložiště Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="5873e-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="5873e-563">Některé architektury jiných výrobců můžete provádět [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5873e-563">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="5873e-564">Použití rozhraní třetí strany je podobné použití jednoho z předdefinovaných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="5873e-564">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="5873e-565">Přidejte balíček NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="5873e-565">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="5873e-566">Volání `ILoggerFactory` metody rozšíření poskytované rozhraní protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-566">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="5873e-567">Další informace naleznete v dokumentaci každého zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-567">For more information, see each provider's documentation.</span></span> <span data-ttu-id="5873e-568">Společnost Microsoft nepodporuje poskytovatele protokolování jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="5873e-568">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5873e-569">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5873e-569">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5873e-570">Tom [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5873e-570">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5873e-571">Rozhraní .NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="5873e-571">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="5873e-572">Tento článek ukazuje, jak používat rozhraní API protokolování s integrovanými zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="5873e-572">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="5873e-573">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5873e-573">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="5873e-574">Přidání zprostředkovatelů</span><span class="sxs-lookup"><span data-stu-id="5873e-574">Add providers</span></span>

<span data-ttu-id="5873e-575">Zprostředkovatel protokolování zobrazí nebo uloží protokoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-575">A logging provider displays or stores logs.</span></span> <span data-ttu-id="5873e-576">Například zprostředkovatel konzoly zobrazí protokoly na konzoli a poskytovatel Azure Application Insights je uloží do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5873e-576">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="5873e-577">Protokoly lze odeslat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="5873e-577">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="5873e-578">Chcete-li přidat zprostředkovatele, `Add{provider name}` volejte metodu rozšíření zprostředkovatele v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5873e-578">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="5873e-579">Předchozí kód vyžaduje odkazy `Microsoft.Extensions.Logging` na `Microsoft.Extensions.Configuration`a .</span><span class="sxs-lookup"><span data-stu-id="5873e-579">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="5873e-580">Výchozí šablona <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>projektu volá , která přidá následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="5873e-580">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="5873e-581">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-581">Console</span></span>
* <span data-ttu-id="5873e-582">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-582">Debug</span></span>
* <span data-ttu-id="5873e-583">EventSource (počínaje ASP.NET jádrem 2.2)</span><span class="sxs-lookup"><span data-stu-id="5873e-583">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="5873e-584">Pokud používáte `CreateDefaultBuilder`, můžete nahradit výchozí zprostředkovatele vlastními volbami.</span><span class="sxs-lookup"><span data-stu-id="5873e-584">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="5873e-585">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte požadované zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-585">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="5873e-586">Další informace o [předdefinovaných zprostředkovatelích protokolování](#built-in-logging-providers) a [poskytovatelích protokolování třetích stran](#third-party-logging-providers) dále v článku.</span><span class="sxs-lookup"><span data-stu-id="5873e-586">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="5873e-587">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="5873e-587">Create logs</span></span>

<span data-ttu-id="5873e-588">Chcete-li vytvořit protokoly, použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="5873e-588">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="5873e-589">Ve webové aplikaci nebo hostované `ILogger` službě získáte z vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="5873e-589">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="5873e-590">V aplikacích konzoly, `LoggerFactory` které nejsou `ILogger`hostitelskou konzolou, použijte k vytvoření aplikace .</span><span class="sxs-lookup"><span data-stu-id="5873e-590">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="5873e-591">Následující příklad ASP.NET Core vytvoří `TodoApiSample.Pages.AboutModel` protokolovací nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-591">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="5873e-592">*Kategorie* protokolu je řetězec, který je přidružen ke každému protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-592">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="5873e-593">Instance `ILogger<T>` poskytované DI vytvoří protokoly, které mají plně `T` kvalifikovaný název typu jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-593">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="5873e-594">V následujících příkladech ASP.NET core a konzolové aplikace, `Information` protokolování se používá k vytvoření protokolů s jako úroveň.</span><span class="sxs-lookup"><span data-stu-id="5873e-594">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="5873e-595">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="5873e-595">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="5873e-596">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="5873e-596">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="5873e-597">Vytvoření protokolů při spuštění</span><span class="sxs-lookup"><span data-stu-id="5873e-597">Create logs in Startup</span></span>

<span data-ttu-id="5873e-598">Chcete-li zapsat `Startup` protokoly `ILogger` do třídy, zahrňte parametr do podpisu konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="5873e-598">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="5873e-599">Vytvoření protokolů ve třídě Program</span><span class="sxs-lookup"><span data-stu-id="5873e-599">Create logs in the Program class</span></span>

<span data-ttu-id="5873e-600">Chcete-li zapsat `Program` protokoly `ILogger` ve třídě, získejte instanci z DI:</span><span class="sxs-lookup"><span data-stu-id="5873e-600">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="5873e-601">Protokolování během výstavby hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="5873e-601">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="5873e-602">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="5873e-602">However, a separate logger can be used.</span></span> <span data-ttu-id="5873e-603">V následujícím příkladu se protokolovací nástroj [Serilog](https://serilog.net/) používá k přihlášení `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5873e-603">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="5873e-604">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="5873e-604">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="5873e-605">Žádné asynchronní metody protokolování</span><span class="sxs-lookup"><span data-stu-id="5873e-605">No asynchronous logger methods</span></span>

<span data-ttu-id="5873e-606">Protokolování by mělo být tak rychlé, že nestojí za náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-606">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="5873e-607">Pokud je úložiště dat protokolování pomalé, nezapisujte do něj přímo.</span><span class="sxs-lookup"><span data-stu-id="5873e-607">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="5873e-608">Zvažte zápis zpráv protokolu do rychlého úložiště zpočátku a potom je přesunout do pomalé úložiště později.</span><span class="sxs-lookup"><span data-stu-id="5873e-608">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="5873e-609">Například pokud se přihlašujete k serveru SQL Server, `Log` nechcete `Log` to provést přímo v metodě, protože metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="5873e-609">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="5873e-610">Místo toho synchronně přidat zprávy protokolu do fronty v paměti a mají pracovník na pozadí vytáhnout zprávy z fronty provést asynchronní práci odesílání dat na SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5873e-610">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="5873e-611">Další informace naleznete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHub.</span><span class="sxs-lookup"><span data-stu-id="5873e-611">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="5873e-612">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5873e-612">Configuration</span></span>

<span data-ttu-id="5873e-613">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="5873e-613">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="5873e-614">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="5873e-614">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="5873e-615">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5873e-615">Command-line arguments.</span></span>
* <span data-ttu-id="5873e-616">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5873e-616">Environment variables.</span></span>
* <span data-ttu-id="5873e-617">Objekty .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="5873e-617">In-memory .NET objects.</span></span>
* <span data-ttu-id="5873e-618">Nešifrované úložiště [Správce tajných](xref:security/app-secrets) služeb.</span><span class="sxs-lookup"><span data-stu-id="5873e-618">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="5873e-619">Šifrované úložiště uživatelů, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="5873e-619">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="5873e-620">Vlastní zprostředkovatelé (nainstalované nebo vytvořené).</span><span class="sxs-lookup"><span data-stu-id="5873e-620">Custom providers (installed or created).</span></span>

<span data-ttu-id="5873e-621">Například konfigurace protokolování je běžně `Logging` poskytována v části souborů nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-621">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="5873e-622">Následující příklad ukazuje obsah typické *nastavení aplikace. Development.json* soubor:</span><span class="sxs-lookup"><span data-stu-id="5873e-622">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="5873e-623">Vlastnost `Logging` může `LogLevel` mít a protokolovat vlastnosti zprostředkovatele (je zobrazena konzola).</span><span class="sxs-lookup"><span data-stu-id="5873e-623">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="5873e-624">Vlastnost `LogLevel` v `Logging` části určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-624">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="5873e-625">V příkladu `System` `Microsoft` a kategorie `Information` protokolu na úrovni `Debug` a všechny ostatní protokolu na úrovni.</span><span class="sxs-lookup"><span data-stu-id="5873e-625">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="5873e-626">Další vlastnosti v části `Logging` zadejte zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-626">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="5873e-627">Příklad je pro zprostředkovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-627">The example is for the Console provider.</span></span> <span data-ttu-id="5873e-628">Pokud zprostředkovatel podporuje [obory protokolu](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="5873e-628">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="5873e-629">Vlastnost zprostředkovatele (například `Console` v příkladu) `LogLevel` může také určit vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5873e-629">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="5873e-630">`LogLevel`v rámci zprostředkovatele určuje úrovně pro protokolování pro tohoto zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-630">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="5873e-631">Pokud jsou úrovně `Logging.{providername}.LogLevel`zadány v , `Logging.LogLevel`přepíší vše, co je nastaveno v .</span><span class="sxs-lookup"><span data-stu-id="5873e-631">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="5873e-632">Rozhraní API protokolování neobsahuje scénář pro změnu úrovní protokolu, když je spuštěná aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-632">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="5873e-633">Někteří poskytovatelé konfigurace jsou však schopni znovu načíst konfiguraci, která má okamžitý vliv na konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-633">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="5873e-634">Například [zprostředkovatel konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider), který `CreateDefaultBuilder` je přidán do čtení souborů nastavení, znovu načte konfiguraci protokolování ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="5873e-634">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="5873e-635">Pokud se konfigurace změní v kódu, když je aplikace spuštěná, aplikace může volat [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) a aktualizovat konfiguraci protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-635">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="5873e-636">Informace o implementaci zprostředkovatelů <xref:fundamentals/configuration/index>konfigurace naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="5873e-636">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="5873e-637">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="5873e-637">Sample logging output</span></span>

<span data-ttu-id="5873e-638">S ukázkovým kódem zobrazeným v předchozí části se protokoly zobrazí v konzole při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5873e-638">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="5873e-639">Zde je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="5873e-639">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="5873e-640">Předchozí protokoly byly generovány provedením požadavku HTTP Get `http://localhost:5000/api/todo/0`do ukázkové aplikace na adrese .</span><span class="sxs-lookup"><span data-stu-id="5873e-640">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="5873e-641">Tady je příklad stejných protokolů, které se zobrazují v okně Ladění při spuštění ukázkové aplikace v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5873e-641">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="5873e-642">Protokoly, které jsou `ILogger` vytvořeny volání zobrazené v předchozí části začínají "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="5873e-642">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="5873e-643">Protokoly, které začínají kategorie "Microsoft" jsou z ASP.NET kódu core framework.</span><span class="sxs-lookup"><span data-stu-id="5873e-643">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="5873e-644">ASP.NET Core a kód aplikace používají stejné protokolování ROZHRANÍ API a zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="5873e-644">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="5873e-645">Zbývající část tohoto článku vysvětluje některé podrobnosti a možnosti pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-645">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="5873e-646">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="5873e-646">NuGet packages</span></span>

<span data-ttu-id="5873e-647">Rozhraní `ILogger` `ILoggerFactory` a jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="5873e-647">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="5873e-648">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-648">Log category</span></span>

<span data-ttu-id="5873e-649">Při `ILogger` vytvoření objektu je pro něj *zadána kategorie.*</span><span class="sxs-lookup"><span data-stu-id="5873e-649">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="5873e-650">Tato kategorie je součástí každé zprávy protokolu `ILogger`vytvořené danou instancí .</span><span class="sxs-lookup"><span data-stu-id="5873e-650">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="5873e-651">Kategorie může být libovolný řetězec, ale konvence je použít název třídy, například "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="5873e-651">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="5873e-652">Slouží `ILogger<T>` k `ILogger` získání instance, která používá `T` plně kvalifikovaný název typu jako kategorie:</span><span class="sxs-lookup"><span data-stu-id="5873e-652">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="5873e-653">Chcete-li kategorii explicitně `ILoggerFactory.CreateLogger`určit, volejte :</span><span class="sxs-lookup"><span data-stu-id="5873e-653">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="5873e-654">`ILogger<T>`je ekvivalentní `CreateLogger` volání s plně kvalifikovaným typem názvu . `T`</span><span class="sxs-lookup"><span data-stu-id="5873e-654">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="5873e-655">Úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-655">Log level</span></span>

<span data-ttu-id="5873e-656">Každý protokol určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5873e-656">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="5873e-657">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="5873e-657">The log level indicates the severity or importance.</span></span> <span data-ttu-id="5873e-658">Můžete například zapsat `Information` protokol, když metoda `Warning` končí normálně a protokol, když metoda vrátí stavový kód *404 Not Found.*</span><span class="sxs-lookup"><span data-stu-id="5873e-658">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="5873e-659">Následující kód `Information` vytvoří `Warning` a protokoly:</span><span class="sxs-lookup"><span data-stu-id="5873e-659">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="5873e-660">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="5873e-660">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="5873e-661">Druhým parametrem je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytnuté zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="5873e-661">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="5873e-662">Parametry metody jsou vysvětleny v [části šablony zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="5873e-662">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="5873e-663">Metody protokolu, které zahrnují úroveň v názvu `LogInformation` `LogWarning`metody (například a ) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="5873e-663">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="5873e-664">Tyto metody `Log` volání metody, `LogLevel` která přebírá parametr.</span><span class="sxs-lookup"><span data-stu-id="5873e-664">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="5873e-665">Můžete volat `Log` metodu přímo, nikoli jednu z těchto metod rozšíření, ale syntaxe je poměrně složité.</span><span class="sxs-lookup"><span data-stu-id="5873e-665">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="5873e-666">Další informace naleznete <xref:Microsoft.Extensions.Logging.ILogger> v tématu a [rozšíření protokolů zdrojový kód](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="5873e-666">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="5873e-667">ASP.NET Core definuje následující úrovně protokolu, seřazené zde od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="5873e-667">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="5873e-668">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="5873e-668">Trace = 0</span></span>

  <span data-ttu-id="5873e-669">Informace, které je obvykle cenné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="5873e-669">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="5873e-670">Tyto zprávy mohou obsahovat citlivá data aplikace, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5873e-670">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="5873e-671">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="5873e-671">*Disabled by default.*</span></span>

* <span data-ttu-id="5873e-672">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="5873e-672">Debug = 1</span></span>

  <span data-ttu-id="5873e-673">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="5873e-673">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="5873e-674">Příklad: `Entering method Configure with flag set to true.` `Debug` Povolit protokoly úrovně v produkčním prostředí pouze při řešení potíží, z důvodu velkého objemu protokolů.</span><span class="sxs-lookup"><span data-stu-id="5873e-674">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="5873e-675">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="5873e-675">Information = 2</span></span>

  <span data-ttu-id="5873e-676">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-676">For tracking the general flow of the app.</span></span> <span data-ttu-id="5873e-677">Tyto protokoly mají obvykle některé dlouhodobé hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5873e-677">These logs typically have some long-term value.</span></span> <span data-ttu-id="5873e-678">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="5873e-678">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="5873e-679">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="5873e-679">Warning = 3</span></span>

  <span data-ttu-id="5873e-680">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-680">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="5873e-681">Mohou mezi ně patřit chyby nebo jiné podmínky, které nezpůsobují zastavení aplikace, ale může být nutné ji prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="5873e-681">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="5873e-682">Zpracované výjimky jsou běžné místo `Warning` pro použití na úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-682">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="5873e-683">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="5873e-683">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="5873e-684">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="5873e-684">Error = 4</span></span>

  <span data-ttu-id="5873e-685">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="5873e-685">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="5873e-686">Tyto zprávy označují selhání v aktuální aktivity nebo operace (například aktuální požadavek HTTP), není selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-686">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="5873e-687">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="5873e-687">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="5873e-688">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="5873e-688">Critical = 5</span></span>

  <span data-ttu-id="5873e-689">Pro poruchy, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="5873e-689">For failures that require immediate attention.</span></span> <span data-ttu-id="5873e-690">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="5873e-690">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="5873e-691">Pomocí úrovně protokolu můžete určit, kolik výstupu protokolu je zapsáno do určitého paměťového média nebo okna zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5873e-691">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="5873e-692">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5873e-692">For example:</span></span>

* <span data-ttu-id="5873e-693">Ve výrobě:</span><span class="sxs-lookup"><span data-stu-id="5873e-693">In production:</span></span>
  * <span data-ttu-id="5873e-694">Protokolování `Trace` na `Information` úrovních pro vytváří velký objem podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-694">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="5873e-695">Chcete-li řídit náklady a nepřekročit limity úložiště dat, zařazujte `Trace` zprávy `Information` na úrovni do úložiště dat s vysokým objemem a nízkými náklady.</span><span class="sxs-lookup"><span data-stu-id="5873e-695">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="5873e-696">Protokolování `Warning` `Critical` na úrovních obvykle vytváří méně, menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-696">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="5873e-697">Proto náklady a limity úložiště obvykle nejsou problém, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="5873e-697">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="5873e-698">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="5873e-698">During development:</span></span>
  * <span data-ttu-id="5873e-699">`Warning` Protokolovat `Critical` prostřednictvím zpráv do konzoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-699">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="5873e-700">Při `Trace` `Information` potížích přidávejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="5873e-700">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="5873e-701">Oddíl [Filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovatel zpracovává.</span><span class="sxs-lookup"><span data-stu-id="5873e-701">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="5873e-702">ASP.NET Core zapisuje protokoly pro rámcové události.</span><span class="sxs-lookup"><span data-stu-id="5873e-702">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="5873e-703">Příklady protokolu dříve v tomto článku `Information` vyloučeny protokoly pod úrovní, takže byly vytvořeny žádné `Debug` protokoly nebo `Trace` úroveň.</span><span class="sxs-lookup"><span data-stu-id="5873e-703">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="5873e-704">Tady je příklad protokolů konzoly vytvořených spuštěním `Debug` ukázkové aplikace nakonfigurované tak, aby zobrazovala protokoly:</span><span class="sxs-lookup"><span data-stu-id="5873e-704">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="5873e-705">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-705">Log event ID</span></span>

<span data-ttu-id="5873e-706">Každý protokol může určit *ID události*.</span><span class="sxs-lookup"><span data-stu-id="5873e-706">Each log can specify an *event ID*.</span></span> <span data-ttu-id="5873e-707">Ukázková aplikace to provádí pomocí `LoggingEvents` místně definované třídy:</span><span class="sxs-lookup"><span data-stu-id="5873e-707">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="5873e-708">ID události přidruží sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="5873e-708">An event ID associates a set of events.</span></span> <span data-ttu-id="5873e-709">Všechny protokoly související se zobrazením seznamu položek na stránce mohou být například 1001.</span><span class="sxs-lookup"><span data-stu-id="5873e-709">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="5873e-710">Zprostředkovatel protokolování může uložit ID události v poli ID, ve zprávě protokolování nebo vůbec.</span><span class="sxs-lookup"><span data-stu-id="5873e-710">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="5873e-711">Poskytovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="5873e-711">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="5873e-712">Zprostředkovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="5873e-712">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="5873e-713">Šablona protokolu zpráv</span><span class="sxs-lookup"><span data-stu-id="5873e-713">Log message template</span></span>

<span data-ttu-id="5873e-714">Každý protokol určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="5873e-714">Each log specifies a message template.</span></span> <span data-ttu-id="5873e-715">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="5873e-715">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="5873e-716">Používejte názvy pro zástupné symboly, ne čísla.</span><span class="sxs-lookup"><span data-stu-id="5873e-716">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="5873e-717">Pořadí zástupných symbolů, nikoli jejich názvy, určuje, které parametry se používají k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="5873e-717">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="5873e-718">V následujícím kódu si všimněte, že názvy parametrů jsou mimo pořadí v šabloně zprávy:</span><span class="sxs-lookup"><span data-stu-id="5873e-718">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="5873e-719">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:</span><span class="sxs-lookup"><span data-stu-id="5873e-719">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="5873e-720">Architektura protokolování funguje tak, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5873e-720">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="5873e-721">Samotné argumenty jsou předány systému protokolování, nikoli pouze formátované šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="5873e-721">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="5873e-722">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="5873e-722">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="5873e-723">Předpokládejme například, že volání metody logger vypadají takto:</span><span class="sxs-lookup"><span data-stu-id="5873e-723">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="5873e-724">Pokud odesíláte protokoly do Azure Table Storage, každá `ID` `RequestTime` entita tabulky Azure může mít a vlastnosti, což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-724">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="5873e-725">Dotaz může najít všechny protokoly `RequestTime` v rámci určité oblasti bez analýzy doby mimo textovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="5873e-725">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="5873e-726">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="5873e-726">Logging exceptions</span></span>

<span data-ttu-id="5873e-727">Metody protokolování mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5873e-727">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="5873e-728">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="5873e-728">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="5873e-729">Tady je příklad výstupu zprostředkovatele ladění z výše uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-729">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="5873e-730">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="5873e-730">Log filtering</span></span>

<span data-ttu-id="5873e-731">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny zprostředkovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="5873e-731">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="5873e-732">Všechny protokoly pod minimální úroveň nejsou předány tomuto zprostředkovateli, takže se nezobrazí ani neuloží.</span><span class="sxs-lookup"><span data-stu-id="5873e-732">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="5873e-733">Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-733">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="5873e-734">Celá hodnota `LogLevel.None` je 6, která je `LogLevel.Critical` vyšší než (5).</span><span class="sxs-lookup"><span data-stu-id="5873e-734">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="5873e-735">Vytvoření pravidel filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="5873e-735">Create filter rules in configuration</span></span>

<span data-ttu-id="5873e-736">Kód šablony projektu `CreateDefaultBuilder` volá k nastavení protokolování pro zprostředkovatele konzoly, ladění a zdroje událostí (ASP.NET Core 2.2 nebo novějším).</span><span class="sxs-lookup"><span data-stu-id="5873e-736">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="5873e-737">Metoda `CreateDefaultBuilder` nastaví protokolování hledat konfiguraci v `Logging` části, jak je vysvětleno dříve v tomto [článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="5873e-737">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="5873e-738">Konfigurační data určují minimální úrovně protokolu podle zprostředkovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5873e-738">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="5873e-739">Tento JSON vytvoří šest pravidel filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a jeden pro všechny zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-739">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="5873e-740">Jedno pravidlo je vybráno pro `ILogger` každého zprostředkovatele při vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="5873e-740">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="5873e-741">Filtrování pravidel v kódu</span><span class="sxs-lookup"><span data-stu-id="5873e-741">Filter rules in code</span></span>

<span data-ttu-id="5873e-742">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="5873e-742">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="5873e-743">Druhý `AddFilter` určuje zprostředkovatele ladění pomocí jeho název typu.</span><span class="sxs-lookup"><span data-stu-id="5873e-743">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="5873e-744">První `AddFilter` platí pro všechny zprostředkovatele, protože neurčuje typ zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-744">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="5873e-745">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="5873e-745">How filtering rules are applied</span></span>

<span data-ttu-id="5873e-746">Konfigurační `AddFilter` data a kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="5873e-746">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="5873e-747">Prvních šest pocházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-747">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="5873e-748">Číslo</span><span class="sxs-lookup"><span data-stu-id="5873e-748">Number</span></span> | <span data-ttu-id="5873e-749">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="5873e-749">Provider</span></span>      | <span data-ttu-id="5873e-750">Kategorie, které začínají ...</span><span class="sxs-lookup"><span data-stu-id="5873e-750">Categories that begin with ...</span></span>          | <span data-ttu-id="5873e-751">Minimální úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-751">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="5873e-752">1</span><span class="sxs-lookup"><span data-stu-id="5873e-752">1</span></span>      | <span data-ttu-id="5873e-753">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-753">Debug</span></span>         | <span data-ttu-id="5873e-754">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="5873e-754">All categories</span></span>                          | <span data-ttu-id="5873e-755">Informace</span><span class="sxs-lookup"><span data-stu-id="5873e-755">Information</span></span>       |
| <span data-ttu-id="5873e-756">2</span><span class="sxs-lookup"><span data-stu-id="5873e-756">2</span></span>      | <span data-ttu-id="5873e-757">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-757">Console</span></span>       | <span data-ttu-id="5873e-758">Microsoft.AspNetCore.Mvc.Razor.Interní</span><span class="sxs-lookup"><span data-stu-id="5873e-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="5873e-759">Upozornění</span><span class="sxs-lookup"><span data-stu-id="5873e-759">Warning</span></span>           |
| <span data-ttu-id="5873e-760">3</span><span class="sxs-lookup"><span data-stu-id="5873e-760">3</span></span>      | <span data-ttu-id="5873e-761">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-761">Console</span></span>       | <span data-ttu-id="5873e-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="5873e-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="5873e-763">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-763">Debug</span></span>             |
| <span data-ttu-id="5873e-764">4</span><span class="sxs-lookup"><span data-stu-id="5873e-764">4</span></span>      | <span data-ttu-id="5873e-765">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-765">Console</span></span>       | <span data-ttu-id="5873e-766">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="5873e-766">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="5873e-767">Chyba</span><span class="sxs-lookup"><span data-stu-id="5873e-767">Error</span></span>             |
| <span data-ttu-id="5873e-768">5</span><span class="sxs-lookup"><span data-stu-id="5873e-768">5</span></span>      | <span data-ttu-id="5873e-769">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-769">Console</span></span>       | <span data-ttu-id="5873e-770">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="5873e-770">All categories</span></span>                          | <span data-ttu-id="5873e-771">Informace</span><span class="sxs-lookup"><span data-stu-id="5873e-771">Information</span></span>       |
| <span data-ttu-id="5873e-772">6</span><span class="sxs-lookup"><span data-stu-id="5873e-772">6</span></span>      | <span data-ttu-id="5873e-773">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="5873e-773">All providers</span></span> | <span data-ttu-id="5873e-774">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="5873e-774">All categories</span></span>                          | <span data-ttu-id="5873e-775">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-775">Debug</span></span>             |
| <span data-ttu-id="5873e-776">7</span><span class="sxs-lookup"><span data-stu-id="5873e-776">7</span></span>      | <span data-ttu-id="5873e-777">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="5873e-777">All providers</span></span> | <span data-ttu-id="5873e-778">Systém</span><span class="sxs-lookup"><span data-stu-id="5873e-778">System</span></span>                                  | <span data-ttu-id="5873e-779">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-779">Debug</span></span>             |
| <span data-ttu-id="5873e-780">8</span><span class="sxs-lookup"><span data-stu-id="5873e-780">8</span></span>      | <span data-ttu-id="5873e-781">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-781">Debug</span></span>         | <span data-ttu-id="5873e-782">Microsoft</span><span class="sxs-lookup"><span data-stu-id="5873e-782">Microsoft</span></span>                               | <span data-ttu-id="5873e-783">Trasování</span><span class="sxs-lookup"><span data-stu-id="5873e-783">Trace</span></span>             |

<span data-ttu-id="5873e-784">Při `ILogger` vytvoření objektu, `ILoggerFactory` objekt vybere jedno pravidlo pro zprostředkovatele použít na tento protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-784">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="5873e-785">Všechny zprávy napsané instancí `ILogger` jsou filtrovány na základě vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="5873e-785">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="5873e-786">Nejkonkrétnější pravidlo možné pro každého poskytovatele a dvojice kategorií je vybránz dostupných pravidel.</span><span class="sxs-lookup"><span data-stu-id="5873e-786">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="5873e-787">Následující algoritmus se používá pro `ILogger` každého zprostředkovatele při vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="5873e-787">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="5873e-788">Vyberte všechna pravidla, která odpovídají poskytovateli nebo jeho aliasu.</span><span class="sxs-lookup"><span data-stu-id="5873e-788">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="5873e-789">Pokud není nalezena žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="5873e-789">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="5873e-790">Z výsledku předchozího kroku vyberte pravidla s nejdelší předponou kategorie shody.</span><span class="sxs-lookup"><span data-stu-id="5873e-790">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="5873e-791">Pokud není nalezena žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="5873e-791">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="5873e-792">Pokud je vybráno více pravidel, vezměte **poslední.**</span><span class="sxs-lookup"><span data-stu-id="5873e-792">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="5873e-793">Pokud nejsou vybrána `MinimumLevel`žádná pravidla, použijte .</span><span class="sxs-lookup"><span data-stu-id="5873e-793">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="5873e-794">S předchozím seznamem pravidel předpokládejme, `ILogger` že vytvoříte objekt pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="5873e-794">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="5873e-795">Pro zprostředkovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="5873e-795">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="5873e-796">Pravidlo 8 je nejkonkrétnější, takže to je ten, který byl vybrán.</span><span class="sxs-lookup"><span data-stu-id="5873e-796">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="5873e-797">Pro zprostředkovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="5873e-797">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="5873e-798">Pravidlo 3 je nejkonkrétnější.</span><span class="sxs-lookup"><span data-stu-id="5873e-798">Rule 3 is most specific.</span></span>

<span data-ttu-id="5873e-799">Výsledná `ILogger` instance odešle `Trace` protokoly úrovně a výše zprostředkovateli ladění.</span><span class="sxs-lookup"><span data-stu-id="5873e-799">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="5873e-800">Protokoly `Debug` úrovně a výše jsou odesílány zprostředkovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-800">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="5873e-801">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="5873e-801">Provider aliases</span></span>

<span data-ttu-id="5873e-802">Každý zprostředkovatel definuje *alias,* který lze použít v konfiguraci namísto plně kvalifikovaný název typu.</span><span class="sxs-lookup"><span data-stu-id="5873e-802">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="5873e-803">Pro předdefinované zprostředkovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="5873e-803">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="5873e-804">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-804">Console</span></span>
* <span data-ttu-id="5873e-805">Ladit</span><span class="sxs-lookup"><span data-stu-id="5873e-805">Debug</span></span>
* <span data-ttu-id="5873e-806">EventSource</span><span class="sxs-lookup"><span data-stu-id="5873e-806">EventSource</span></span>
* <span data-ttu-id="5873e-807">Eventlog</span><span class="sxs-lookup"><span data-stu-id="5873e-807">EventLog</span></span>
* <span data-ttu-id="5873e-808">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5873e-808">TraceSource</span></span>
* <span data-ttu-id="5873e-809">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="5873e-809">AzureAppServicesFile</span></span>
* <span data-ttu-id="5873e-810">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="5873e-810">AzureAppServicesBlob</span></span>
* <span data-ttu-id="5873e-811">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="5873e-811">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="5873e-812">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="5873e-812">Default minimum level</span></span>

<span data-ttu-id="5873e-813">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že pro daného zprostředkovatele a kategorii neplatí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-813">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="5873e-814">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="5873e-814">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="5873e-815">Pokud explicitně nenastavíte minimální úroveň, `Information`výchozí hodnota `Trace` je `Debug` , což znamená, že a protokoly jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="5873e-815">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="5873e-816">Funkce filtrování</span><span class="sxs-lookup"><span data-stu-id="5873e-816">Filter functions</span></span>

<span data-ttu-id="5873e-817">Funkce filtru je vyvolána pro všechny zprostředkovatele a kategorie, které nemají pravidla přiřazená podle konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="5873e-817">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="5873e-818">Kód ve funkci má přístup k typu zprostředkovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-818">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="5873e-819">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5873e-819">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="5873e-820">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="5873e-820">System categories and levels</span></span>

<span data-ttu-id="5873e-821">Zde jsou některé kategorie používané ASP.NET jádrem a jádrem entity s poznámkami o tom, jaké protokoly od nich lze očekávat:</span><span class="sxs-lookup"><span data-stu-id="5873e-821">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="5873e-822">Kategorie</span><span class="sxs-lookup"><span data-stu-id="5873e-822">Category</span></span>                            | <span data-ttu-id="5873e-823">Poznámky</span><span class="sxs-lookup"><span data-stu-id="5873e-823">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="5873e-824">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="5873e-824">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="5873e-825">Obecná diagnostika ASP.NET jádra.</span><span class="sxs-lookup"><span data-stu-id="5873e-825">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="5873e-826">Ochrana Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="5873e-826">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="5873e-827">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="5873e-827">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="5873e-828">Filtrování Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="5873e-828">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="5873e-829">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="5873e-829">Hosts allowed.</span></span> |
| <span data-ttu-id="5873e-830">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="5873e-830">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="5873e-831">Jak dlouho trvalo dokončení požadavků HTTP a v jakém čase byly spuštěny.</span><span class="sxs-lookup"><span data-stu-id="5873e-831">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="5873e-832">Která hostitelská spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="5873e-832">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="5873e-833">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="5873e-833">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="5873e-834">Diagnostika MVC a Razor.</span><span class="sxs-lookup"><span data-stu-id="5873e-834">MVC and Razor diagnostics.</span></span> <span data-ttu-id="5873e-835">Vazba modelu, spuštění filtru, kompilace zobrazení, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="5873e-835">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="5873e-836">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="5873e-836">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="5873e-837">Informace o porovnávání tras.</span><span class="sxs-lookup"><span data-stu-id="5873e-837">Route matching information.</span></span> |
| <span data-ttu-id="5873e-838">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="5873e-838">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="5873e-839">Připojení spustit, zastavit a udržet naživu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="5873e-839">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="5873e-840">informace o certifikátu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5873e-840">HTTPS certificate information.</span></span> |
| <span data-ttu-id="5873e-841">Soubory Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="5873e-841">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="5873e-842">Doručené složky.</span><span class="sxs-lookup"><span data-stu-id="5873e-842">Files served.</span></span> |
| <span data-ttu-id="5873e-843">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="5873e-843">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="5873e-844">Diagnostika jádra obecnéentity frameworku.</span><span class="sxs-lookup"><span data-stu-id="5873e-844">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="5873e-845">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="5873e-845">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="5873e-846">Obory protokolu</span><span class="sxs-lookup"><span data-stu-id="5873e-846">Log scopes</span></span>

 <span data-ttu-id="5873e-847">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="5873e-847">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="5873e-848">Toto seskupení lze použít k připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="5873e-848">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="5873e-849">Například každý protokol vytvořený jako součást zpracování transakce může obsahovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="5873e-849">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="5873e-850">Obor je `IDisposable` typ, který je <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> vrácen metodou a trvá, dokud je uvolněn.</span><span class="sxs-lookup"><span data-stu-id="5873e-850">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="5873e-851">Použití oboru zabalením volání `using` protokolování v bloku:</span><span class="sxs-lookup"><span data-stu-id="5873e-851">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="5873e-852">Následující kód umožňuje obory pro zprostředkovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="5873e-852">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="5873e-853">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5873e-853">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="5873e-854">Konfigurace možnosti `IncludeScopes` protokolování konzoly je nutná k povolení protokolování založeného na oboru.</span><span class="sxs-lookup"><span data-stu-id="5873e-854">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="5873e-855">Informace o konfiguraci naleznete v části [Konfigurace.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="5873e-855">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="5873e-856">Každá zpráva protokolu obsahuje informace s vymezenou oborem:</span><span class="sxs-lookup"><span data-stu-id="5873e-856">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="5873e-857">Vestavění poskytovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="5873e-857">Built-in logging providers</span></span>

<span data-ttu-id="5873e-858">ASP.NET Core dodává tyto poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="5873e-858">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="5873e-859">Konzola</span><span class="sxs-lookup"><span data-stu-id="5873e-859">Console</span></span>](#console-provider)
* [<span data-ttu-id="5873e-860">Ladění</span><span class="sxs-lookup"><span data-stu-id="5873e-860">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="5873e-861">Zdroj událostí</span><span class="sxs-lookup"><span data-stu-id="5873e-861">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="5873e-862">Eventlog</span><span class="sxs-lookup"><span data-stu-id="5873e-862">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="5873e-863">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5873e-863">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="5873e-864">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="5873e-864">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="5873e-865">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="5873e-865">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="5873e-866">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="5873e-866">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="5873e-867">Informace o protokolování stdout a ladění pomocí <xref:test/troubleshoot-azure-iis> ASP.NET <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>core modulem naleznete v tématu a .</span><span class="sxs-lookup"><span data-stu-id="5873e-867">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="5873e-868">Zprostředkovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="5873e-868">Console provider</span></span>

<span data-ttu-id="5873e-869">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-869">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="5873e-870">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5873e-870">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="5873e-871">Zprostředkovatel ladění</span><span class="sxs-lookup"><span data-stu-id="5873e-871">Debug provider</span></span>

<span data-ttu-id="5873e-872">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí`Debug.WriteLine` [třídy System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (volání metody).</span><span class="sxs-lookup"><span data-stu-id="5873e-872">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="5873e-873">V systému Linux tento zprostředkovatel zapisuje protokoly do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="5873e-873">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="5873e-874">Zprostředkovatel zdroje událostí</span><span class="sxs-lookup"><span data-stu-id="5873e-874">Event Source provider</span></span>

<span data-ttu-id="5873e-875">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na `Microsoft-Extensions-Logging`křížovou platformu Zdroje událostí s názvem .</span><span class="sxs-lookup"><span data-stu-id="5873e-875">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="5873e-876">V systému Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="5873e-876">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="5873e-877">Zprostředkovatel zdroje událostí je `CreateDefaultBuilder` přidán automaticky, když je volána k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="5873e-877">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="5873e-878">Pomocí [nástroje PerfView](https://github.com/Microsoft/perfview) sbírat a zobrazovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="5873e-878">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="5873e-879">Existují další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi ETW vyzařované ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5873e-879">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="5873e-880">Chcete-li nakonfigurovat PerfView pro shromažďování událostí `*Microsoft-Extensions-Logging` protokolovaných tímto zprostředkovatelem, přidejte řetězec do seznamu **Další zprostředkovatelé.**</span><span class="sxs-lookup"><span data-stu-id="5873e-880">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="5873e-881">(Nenechte si ujít hvězdičku na začátku řetězce.)</span><span class="sxs-lookup"><span data-stu-id="5873e-881">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="5873e-883">Zprostředkovatel služby Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="5873e-883">Windows EventLog provider</span></span>

<span data-ttu-id="5873e-884">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="5873e-884">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="5873e-885">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="5873e-885">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="5873e-886">Pokud `null` není zadáno, použije se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="5873e-886">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="5873e-887">`LogName`&ndash; "Aplikace"</span><span class="sxs-lookup"><span data-stu-id="5873e-887">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="5873e-888">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="5873e-888">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="5873e-889">`MachineName`&ndash; místní počítač</span><span class="sxs-lookup"><span data-stu-id="5873e-889">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="5873e-890">Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level).</span><span class="sxs-lookup"><span data-stu-id="5873e-890">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="5873e-891">Chcete-li protokolovat události nižší než `Warning`, explicitně nastavte úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="5873e-891">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="5873e-892">Do souboru *appsettings.json* například přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="5873e-892">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="5873e-893">Zprostředkovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="5873e-893">TraceSource provider</span></span>

<span data-ttu-id="5873e-894">Balíček [zprostředkovatele Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-894">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="5873e-895">[AddTraceSource přetížení](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="5873e-895">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="5873e-896">Chcete-li použít tohoto zprostředkovatele, aplikace musí běžet na rozhraní .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="5873e-896">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="5873e-897">Zprostředkovatel může směrovat zprávy do různých [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použité v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5873e-897">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="5873e-898">Poskytovatel služeb Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5873e-898">Azure App Service provider</span></span>

<span data-ttu-id="5873e-899">Balíček zprostředkovatele [Microsoft.Extensions.Log.Log.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Služby Azure App Service a do [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="5873e-899">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="5873e-900">Balíček zprostředkovatele není součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5873e-900">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="5873e-901">Při cílení na rozhraní .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` metabalíček přidejte balíček zprostředkovatele do projektu.</span><span class="sxs-lookup"><span data-stu-id="5873e-901">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="5873e-902">Přetížení <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> umožňuje projít v <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="5873e-902">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="5873e-903">Objekt nastavení může přepsat výchozí nastavení, například šablonu výstupu protokolování, název objektu blob a omezení velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="5873e-903">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="5873e-904">(Výstupní*šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici s voláním `ILogger` metody.)</span><span class="sxs-lookup"><span data-stu-id="5873e-904">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="5873e-905">Když se nasadíte do aplikace App Service, aplikace respektuje nastavení v části [protokoly služby App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stránce **Služby aplikací** na portálu Azure.</span><span class="sxs-lookup"><span data-stu-id="5873e-905">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="5873e-906">Při aktualizaci následujících nastavení se změny projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-906">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="5873e-907">**Protokolování aplikací (souborový systém)**</span><span class="sxs-lookup"><span data-stu-id="5873e-907">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="5873e-908">**Protokolování aplikací (objekt blob)**</span><span class="sxs-lookup"><span data-stu-id="5873e-908">**Application Logging (Blob)**</span></span>

<span data-ttu-id="5873e-909">Výchozí umístění souborů protokolu je ve složce *D:\\\\home LogFiles\\Application* a výchozí název souboru je *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="5873e-909">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="5873e-910">Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="5873e-910">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="5873e-911">Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="5873e-911">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="5873e-912">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="5873e-912">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="5873e-913">Nemá žádný vliv, když je&mdash;projekt spuštěn místně, nezapisuje do místních souborů nebo místní vývojové úložiště pro objekty BLOB.</span><span class="sxs-lookup"><span data-stu-id="5873e-913">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="5873e-914">Streamování protokolu Azure</span><span class="sxs-lookup"><span data-stu-id="5873e-914">Azure log streaming</span></span>

<span data-ttu-id="5873e-915">Streamování protokolu Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="5873e-915">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="5873e-916">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="5873e-916">The app server</span></span>
* <span data-ttu-id="5873e-917">Webový server</span><span class="sxs-lookup"><span data-stu-id="5873e-917">The web server</span></span>
* <span data-ttu-id="5873e-918">Trasování neúspěšných požadavků</span><span class="sxs-lookup"><span data-stu-id="5873e-918">Failed request tracing</span></span>

<span data-ttu-id="5873e-919">Konfigurace streamování protokolu Azure:</span><span class="sxs-lookup"><span data-stu-id="5873e-919">To configure Azure log streaming:</span></span>

* <span data-ttu-id="5873e-920">Přejděte na stránku **protokolů služby App Service** na stránce portálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="5873e-920">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="5873e-921">Nastavení **protokolování aplikací (souborový systém)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="5873e-921">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="5873e-922">Zvolte **úroveň**protokolu .</span><span class="sxs-lookup"><span data-stu-id="5873e-922">Choose the log **Level**.</span></span> <span data-ttu-id="5873e-923">Toto nastavení platí jenom pro streamování protokolu Azure, ne pro jiné poskytovatele protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5873e-923">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="5873e-924">Přejděte na stránku **Log Stream** a zobrazte zprávy aplikací.</span><span class="sxs-lookup"><span data-stu-id="5873e-924">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="5873e-925">Jsou zaznamenány aplikací prostřednictvím `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5873e-925">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="5873e-926">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="5873e-926">Azure Application Insights trace logging</span></span>

<span data-ttu-id="5873e-927">Balíček zprostředkovatele [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5873e-927">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="5873e-928">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu telemetrických dat.</span><span class="sxs-lookup"><span data-stu-id="5873e-928">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="5873e-929">Pokud používáte tohoto zprostředkovatele, můžete dotazovat a analyzovat protokoly pomocí nástrojů Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5873e-929">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="5873e-930">Zprostředkovatel protokolování je součástí jako závislost [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje všechny dostupné telemetrie pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5873e-930">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="5873e-931">Pokud používáte tento balíček, není nutné instalovat balíček zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-931">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="5873e-932">Nepoužívejte balíček&mdash; [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) pro ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="5873e-932">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="5873e-933">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="5873e-933">For more information, see the following resources:</span></span>

* [<span data-ttu-id="5873e-934">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="5873e-934">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="5873e-935">[Application Insights pro ASP.NET základní aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou škálu telemetrie Application Insights spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="5873e-935">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="5873e-936">[ApplicationInsightsLoggerProvider pro protokoly ILogger jádra .NET](/azure/azure-monitor/app/ilogger) – začněte zde, pokud chcete implementovat poskytovatele protokolování bez zbytku telemetrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="5873e-936">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="5873e-937">[Adaptéry pro protokolování Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="5873e-937">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="5873e-938">[Nainstalujte, nakonfigurujte a inicializujte kurz Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="5873e-938">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="5873e-939">Poskytovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="5873e-939">Third-party logging providers</span></span>

<span data-ttu-id="5873e-940">Architektury protokolování třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5873e-940">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="5873e-941">[elmah.io](https://elmah.io/) ([Úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5873e-941">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="5873e-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="5873e-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="5873e-943">[JSNLog](https://jsnlog.com/) ([Úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="5873e-943">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="5873e-944">[KissLog.net](https://kisslog.net/) ([Úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="5873e-944">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="5873e-945">[Log4Net](https://logging.apache.org/log4net/) ([Úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="5873e-945">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="5873e-946">[Loggr](https://loggr.net/) ([Úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5873e-946">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="5873e-947">[NLog](https://nlog-project.org/) ([Úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="5873e-947">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="5873e-948">[Sentry](https://sentry.io/welcome/) ([Úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="5873e-948">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="5873e-949">[Serilog](https://serilog.net/) ([Úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="5873e-949">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="5873e-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Úložiště Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="5873e-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="5873e-951">Některé architektury jiných výrobců můžete provádět [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5873e-951">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="5873e-952">Použití rozhraní třetí strany je podobné použití jednoho z předdefinovaných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="5873e-952">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="5873e-953">Přidejte balíček NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="5873e-953">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="5873e-954">Volání `ILoggerFactory` metody rozšíření poskytované rozhraní protokolování.</span><span class="sxs-lookup"><span data-stu-id="5873e-954">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="5873e-955">Další informace naleznete v dokumentaci každého zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="5873e-955">For more information, see each provider's documentation.</span></span> <span data-ttu-id="5873e-956">Společnost Microsoft nepodporuje poskytovatele protokolování jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="5873e-956">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5873e-957">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5873e-957">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end

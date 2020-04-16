---
title: Přihlášení do jádra rozhraní .NET core a ASP.NET core
author: rick-anderson
description: Zjistěte, jak používat architekturu protokolování poskytovanou balíčkem Microsoft.Extensions.Logging NuGet.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440737"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="c6eea-103">Přihlášení do jádra rozhraní .NET core a ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="c6eea-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6eea-104">Tom [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c6eea-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c6eea-105">Rozhraní .NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="c6eea-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="c6eea-106">Tento článek ukazuje, jak používat rozhraní API protokolování s integrovanými zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="c6eea-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="c6eea-107">Většina příkladů kódu uvedených v tomto článku pochází z ASP.NET základních aplikací.</span><span class="sxs-lookup"><span data-stu-id="c6eea-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="c6eea-108">Části těchto fragmentů kódu specifické pro protokolování platí pro všechny aplikace .NET Core, která používá [obecný hostitel](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="c6eea-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="c6eea-109">Příklad použití obecného hostitele v aplikaci, která není webovou konzolou, najdete<xref:fundamentals/host/hosted-services>v *Program.cs* soubor [ukázkové aplikace Úlohy na pozadí](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( ).</span><span class="sxs-lookup"><span data-stu-id="c6eea-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="c6eea-110">Protokolování kódu pro aplikace bez obecného hostitele se liší ve způsobu, jakým [jsou přidány zprostředkovatele](#add-providers) a [jsou vytvořeny úhozy kláves](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="c6eea-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="c6eea-111">Příklady kódu bez hostitele jsou uvedeny v těchto částech článku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="c6eea-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c6eea-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="c6eea-113">Přidání zprostředkovatelů</span><span class="sxs-lookup"><span data-stu-id="c6eea-113">Add providers</span></span>

<span data-ttu-id="c6eea-114">Zprostředkovatel protokolování zobrazí nebo uloží protokoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="c6eea-115">Například zprostředkovatel konzoly zobrazí protokoly na konzoli a poskytovatel Azure Application Insights je uloží do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c6eea-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="c6eea-116">Protokoly lze odeslat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="c6eea-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="c6eea-117">Pokud chcete přidat zprostředkovatele do aplikace, která používá `Add{provider name}` obecný hostitel, zavolejte metodu rozšíření poskytovatele v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c6eea-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="c6eea-118">V aplikaci konzoly bez hostitele volejte `Add{provider name}` metodu rozšíření `LoggerFactory`zprostředkovatele při vytváření :</span><span class="sxs-lookup"><span data-stu-id="c6eea-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="c6eea-119">`LoggerFactory`a `AddConsole` vyžadují `using` prohlášení `Microsoft.Extensions.Logging`pro .</span><span class="sxs-lookup"><span data-stu-id="c6eea-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="c6eea-120">Výchozí ASP.NET volání výchozích <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>šablon projektu jádra , které přidá následující poskytovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="c6eea-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="c6eea-121">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="c6eea-122">Ladění</span><span class="sxs-lookup"><span data-stu-id="c6eea-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="c6eea-123">Zdroj událostí</span><span class="sxs-lookup"><span data-stu-id="c6eea-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="c6eea-124">[EventLog](#windows-eventlog-provider) (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="c6eea-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="c6eea-125">Výchozí zprostředkovatele můžete nahradit vlastními volbami.</span><span class="sxs-lookup"><span data-stu-id="c6eea-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="c6eea-126">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte požadované zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="c6eea-127">Další informace o [předdefinovaných zprostředkovatelích protokolování](#built-in-logging-providers) a [poskytovatelích protokolování třetích stran](#third-party-logging-providers) dále v článku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="c6eea-128">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="c6eea-128">Create logs</span></span>

<span data-ttu-id="c6eea-129">Chcete-li vytvořit protokoly, použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="c6eea-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="c6eea-130">Ve webové aplikaci nebo hostované `ILogger` službě získáte z vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="c6eea-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="c6eea-131">V aplikacích konzoly, `LoggerFactory` které nejsou `ILogger`hostitelskou konzolou, použijte k vytvoření aplikace .</span><span class="sxs-lookup"><span data-stu-id="c6eea-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="c6eea-132">Následující příklad ASP.NET Core vytvoří `TodoApiSample.Pages.AboutModel` protokolovací nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="c6eea-133">*Kategorie* protokolu je řetězec, který je přidružen ke každému protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="c6eea-134">Instance `ILogger<T>` poskytované DI vytvoří protokoly, které mají plně `T` kvalifikovaný název typu jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="c6eea-135">Následující příklad aplikace konzoly bez hostitele `LoggingConsoleApp.Program` vytvoří protokolovací nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="c6eea-136">V následujících příkladech ASP.NET core a konzolové aplikace, `Information` protokolování se používá k vytvoření protokolů s jako úroveň.</span><span class="sxs-lookup"><span data-stu-id="c6eea-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="c6eea-137">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="c6eea-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="c6eea-138">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="c6eea-139">Vytvoření protokolů ve třídě Program</span><span class="sxs-lookup"><span data-stu-id="c6eea-139">Create logs in the Program class</span></span>

<span data-ttu-id="c6eea-140">Chcete-li zapsat `Program` protokoly ve třídě aplikace `ILogger` ASP.NET Core, získejte instanci z DI po vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="c6eea-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="c6eea-141">Protokolování během výstavby hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="c6eea-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="c6eea-142">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="c6eea-142">However, a separate logger can be used.</span></span> <span data-ttu-id="c6eea-143">V následujícím příkladu se protokolovací nástroj [Serilog](https://serilog.net/) používá k přihlášení `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c6eea-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="c6eea-144">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="c6eea-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="c6eea-145">Vytvoření protokolů ve třídě Startup</span><span class="sxs-lookup"><span data-stu-id="c6eea-145">Create logs in the Startup class</span></span>

<span data-ttu-id="c6eea-146">Chcete-li zapsat `Startup.Configure` protokoly v metodě `ILogger` aplikace ASP.NET Core, zahrňte parametr do podpisu metody:</span><span class="sxs-lookup"><span data-stu-id="c6eea-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="c6eea-147">Zápis protokolů před dokončením nastavení kontejneru DI v metodě `Startup.ConfigureServices` není podporován:</span><span class="sxs-lookup"><span data-stu-id="c6eea-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="c6eea-148">Vkládání protokolů `Startup` do konstruktoru není podporováno.</span><span class="sxs-lookup"><span data-stu-id="c6eea-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="c6eea-149">Vkládání protokolů `Startup.ConfigureServices` do podpisu metody není podporováno.</span><span class="sxs-lookup"><span data-stu-id="c6eea-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="c6eea-150">Důvodem pro toto omezení je, že protokolování závisí na DI a na konfiguraci, která zase závisí na DI.</span><span class="sxs-lookup"><span data-stu-id="c6eea-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="c6eea-151">Kontejner DI není nastaven, `ConfigureServices` dokud nebude dokončena.</span><span class="sxs-lookup"><span data-stu-id="c6eea-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="c6eea-152">Konstruktor vkládání úhozů do `Startup` funguje v dřívějších verzích ASP.NET Core, protože samostatný kontejner DI je vytvořen pro web hostitele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="c6eea-153">Informace o tom, proč je pro obecný hostitel vytvořen pouze jeden kontejner, naleznete v [oznámení o změně rozdělení](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="c6eea-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="c6eea-154">Pokud potřebujete nakonfigurovat službu, `ILogger<T>`která závisí na , můžete to stále provést pomocí vkládání konstruktoru nebo poskytnutím metody výroby.</span><span class="sxs-lookup"><span data-stu-id="c6eea-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="c6eea-155">Přístup metody výroby se doporučuje pouze v případě, že neexistuje žádná jiná možnost.</span><span class="sxs-lookup"><span data-stu-id="c6eea-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="c6eea-156">Předpokládejme například, že potřebujete vyplnit vlastnost službou z DI:</span><span class="sxs-lookup"><span data-stu-id="c6eea-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="c6eea-157">Předchozí zvýrazněný kód `Func` je, který běží poprvé kontejner UD potřebuje `MyService`k vytvoření instance .</span><span class="sxs-lookup"><span data-stu-id="c6eea-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="c6eea-158">Tímto způsobem můžete přistupovat k libovolné registrované službě.</span><span class="sxs-lookup"><span data-stu-id="c6eea-158">You can access any of the registered services in this way.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="c6eea-159">Žádné asynchronní metody protokolování</span><span class="sxs-lookup"><span data-stu-id="c6eea-159">No asynchronous logger methods</span></span>

<span data-ttu-id="c6eea-160">Protokolování by mělo být tak rychlé, že nestojí za náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-160">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="c6eea-161">Pokud je úložiště dat protokolování pomalé, nezapisujte do něj přímo.</span><span class="sxs-lookup"><span data-stu-id="c6eea-161">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="c6eea-162">Zvažte zápis zpráv protokolu do rychlého úložiště zpočátku a potom je přesunout do pomalé úložiště později.</span><span class="sxs-lookup"><span data-stu-id="c6eea-162">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="c6eea-163">Například pokud se přihlašujete k serveru SQL Server, `Log` nechcete `Log` to provést přímo v metodě, protože metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="c6eea-163">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="c6eea-164">Místo toho synchronně přidat zprávy protokolu do fronty v paměti a mají pracovník na pozadí vytáhnout zprávy z fronty provést asynchronní práci odesílání dat na SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c6eea-164">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="c6eea-165">Další informace naleznete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHub.</span><span class="sxs-lookup"><span data-stu-id="c6eea-165">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="c6eea-166">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="c6eea-166">Configuration</span></span>

<span data-ttu-id="c6eea-167">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="c6eea-167">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="c6eea-168">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="c6eea-168">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="c6eea-169">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-169">Command-line arguments.</span></span>
* <span data-ttu-id="c6eea-170">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-170">Environment variables.</span></span>
* <span data-ttu-id="c6eea-171">Objekty .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="c6eea-171">In-memory .NET objects.</span></span>
* <span data-ttu-id="c6eea-172">Nešifrované úložiště [Správce tajných](xref:security/app-secrets) služeb.</span><span class="sxs-lookup"><span data-stu-id="c6eea-172">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="c6eea-173">Šifrované úložiště uživatelů, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="c6eea-173">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="c6eea-174">Vlastní zprostředkovatelé (nainstalované nebo vytvořené).</span><span class="sxs-lookup"><span data-stu-id="c6eea-174">Custom providers (installed or created).</span></span>

<span data-ttu-id="c6eea-175">Například konfigurace protokolování je běžně `Logging` poskytována v části souborů nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-175">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="c6eea-176">Následující příklad ukazuje obsah typické *nastavení aplikace. Development.json* soubor:</span><span class="sxs-lookup"><span data-stu-id="c6eea-176">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="c6eea-177">Vlastnost `Logging` může `LogLevel` mít a protokolovat vlastnosti zprostředkovatele (je zobrazena konzola).</span><span class="sxs-lookup"><span data-stu-id="c6eea-177">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="c6eea-178">Vlastnost `LogLevel` v `Logging` části určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-178">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="c6eea-179">V příkladu `System` `Microsoft` a kategorie `Information` protokolu na úrovni `Debug` a všechny ostatní protokolu na úrovni.</span><span class="sxs-lookup"><span data-stu-id="c6eea-179">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="c6eea-180">Další vlastnosti v části `Logging` zadejte zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-180">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="c6eea-181">Příklad je pro zprostředkovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-181">The example is for the Console provider.</span></span> <span data-ttu-id="c6eea-182">Pokud zprostředkovatel podporuje [obory protokolu](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="c6eea-182">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="c6eea-183">Vlastnost zprostředkovatele (například `Console` v příkladu) `LogLevel` může také určit vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c6eea-183">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="c6eea-184">`LogLevel`v rámci zprostředkovatele určuje úrovně pro protokolování pro tohoto zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-184">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="c6eea-185">Pokud jsou úrovně `Logging.{providername}.LogLevel`zadány v , `Logging.LogLevel`přepíší vše, co je nastaveno v .</span><span class="sxs-lookup"><span data-stu-id="c6eea-185">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="c6eea-186">Rozhraní API protokolování neobsahuje scénář pro změnu úrovní protokolu, když je spuštěná aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-186">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="c6eea-187">Někteří poskytovatelé konfigurace jsou však schopni znovu načíst konfiguraci, která má okamžitý vliv na konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-187">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="c6eea-188">Například [zprostředkovatel konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider), který `CreateDefaultBuilder` je přidán do čtení souborů nastavení, znovu načte konfiguraci protokolování ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="c6eea-188">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="c6eea-189">Pokud se konfigurace změní v kódu, když je aplikace spuštěná, aplikace může volat [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) a aktualizovat konfiguraci protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-189">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="c6eea-190">Informace o implementaci zprostředkovatelů <xref:fundamentals/configuration/index>konfigurace naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="c6eea-190">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="c6eea-191">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="c6eea-191">Sample logging output</span></span>

<span data-ttu-id="c6eea-192">S ukázkovým kódem zobrazeným v předchozí části se protokoly zobrazí v konzole při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-192">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="c6eea-193">Zde je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="c6eea-193">Here's an example of console output:</span></span>

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

<span data-ttu-id="c6eea-194">Předchozí protokoly byly generovány provedením požadavku HTTP Get `http://localhost:5000/api/todo/0`do ukázkové aplikace na adrese .</span><span class="sxs-lookup"><span data-stu-id="c6eea-194">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="c6eea-195">Tady je příklad stejných protokolů, které se zobrazují v okně Ladění při spuštění ukázkové aplikace v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c6eea-195">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="c6eea-196">Protokoly, které jsou `ILogger` vytvořeny volání zobrazené v předchozí části začínají "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="c6eea-196">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="c6eea-197">Protokoly, které začínají kategorie "Microsoft" jsou z ASP.NET kódu core framework.</span><span class="sxs-lookup"><span data-stu-id="c6eea-197">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="c6eea-198">ASP.NET Core a kód aplikace používají stejné protokolování ROZHRANÍ API a zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="c6eea-198">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="c6eea-199">Zbývající část tohoto článku vysvětluje některé podrobnosti a možnosti pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-199">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="c6eea-200">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="c6eea-200">NuGet packages</span></span>

<span data-ttu-id="c6eea-201">Rozhraní `ILogger` `ILoggerFactory` a jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="c6eea-201">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="c6eea-202">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-202">Log category</span></span>

<span data-ttu-id="c6eea-203">Při `ILogger` vytvoření objektu je pro něj *zadána kategorie.*</span><span class="sxs-lookup"><span data-stu-id="c6eea-203">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="c6eea-204">Tato kategorie je součástí každé zprávy protokolu `ILogger`vytvořené danou instancí .</span><span class="sxs-lookup"><span data-stu-id="c6eea-204">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="c6eea-205">Kategorie může být libovolný řetězec, ale konvence je použít název třídy, například "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="c6eea-205">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="c6eea-206">Slouží `ILogger<T>` k `ILogger` získání instance, která používá `T` plně kvalifikovaný název typu jako kategorie:</span><span class="sxs-lookup"><span data-stu-id="c6eea-206">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="c6eea-207">Chcete-li kategorii explicitně `ILoggerFactory.CreateLogger`určit, volejte :</span><span class="sxs-lookup"><span data-stu-id="c6eea-207">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="c6eea-208">`ILogger<T>`je ekvivalentní `CreateLogger` volání s plně kvalifikovaným typem názvu . `T`</span><span class="sxs-lookup"><span data-stu-id="c6eea-208">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="c6eea-209">Úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-209">Log level</span></span>

<span data-ttu-id="c6eea-210">Každý protokol určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-210">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="c6eea-211">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="c6eea-211">The log level indicates the severity or importance.</span></span> <span data-ttu-id="c6eea-212">Můžete například zapsat `Information` protokol, když metoda `Warning` končí normálně a protokol, když metoda vrátí stavový kód *404 Not Found.*</span><span class="sxs-lookup"><span data-stu-id="c6eea-212">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="c6eea-213">Následující kód `Information` vytvoří `Warning` a protokoly:</span><span class="sxs-lookup"><span data-stu-id="c6eea-213">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="c6eea-214">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="c6eea-214">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="c6eea-215">Druhým parametrem je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytnuté zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="c6eea-215">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="c6eea-216">Parametry metody jsou vysvětleny v [části šablony zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-216">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="c6eea-217">Metody protokolu, které zahrnují úroveň v názvu `LogInformation` `LogWarning`metody (například a ) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="c6eea-217">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="c6eea-218">Tyto metody `Log` volání metody, `LogLevel` která přebírá parametr.</span><span class="sxs-lookup"><span data-stu-id="c6eea-218">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="c6eea-219">Můžete volat `Log` metodu přímo, nikoli jednu z těchto metod rozšíření, ale syntaxe je poměrně složité.</span><span class="sxs-lookup"><span data-stu-id="c6eea-219">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="c6eea-220">Další informace naleznete <xref:Microsoft.Extensions.Logging.ILogger> v tématu a [rozšíření protokolů zdrojový kód](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="c6eea-220">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="c6eea-221">ASP.NET Core definuje následující úrovně protokolu, seřazené zde od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="c6eea-221">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="c6eea-222">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="c6eea-222">Trace = 0</span></span>

  <span data-ttu-id="c6eea-223">Informace, které je obvykle cenné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="c6eea-223">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="c6eea-224">Tyto zprávy mohou obsahovat citlivá data aplikace, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-224">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="c6eea-225">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="c6eea-225">*Disabled by default.*</span></span>

* <span data-ttu-id="c6eea-226">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="c6eea-226">Debug = 1</span></span>

  <span data-ttu-id="c6eea-227">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="c6eea-227">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="c6eea-228">Příklad: `Entering method Configure with flag set to true.` `Debug` Povolit protokoly úrovně v produkčním prostředí pouze při řešení potíží, z důvodu velkého objemu protokolů.</span><span class="sxs-lookup"><span data-stu-id="c6eea-228">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="c6eea-229">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="c6eea-229">Information = 2</span></span>

  <span data-ttu-id="c6eea-230">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-230">For tracking the general flow of the app.</span></span> <span data-ttu-id="c6eea-231">Tyto protokoly mají obvykle některé dlouhodobé hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c6eea-231">These logs typically have some long-term value.</span></span> <span data-ttu-id="c6eea-232">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="c6eea-232">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="c6eea-233">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="c6eea-233">Warning = 3</span></span>

  <span data-ttu-id="c6eea-234">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-234">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="c6eea-235">Mohou mezi ně patřit chyby nebo jiné podmínky, které nezpůsobují zastavení aplikace, ale může být nutné ji prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="c6eea-235">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="c6eea-236">Zpracované výjimky jsou běžné místo `Warning` pro použití na úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-236">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="c6eea-237">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="c6eea-237">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="c6eea-238">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="c6eea-238">Error = 4</span></span>

  <span data-ttu-id="c6eea-239">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="c6eea-239">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="c6eea-240">Tyto zprávy označují selhání v aktuální aktivity nebo operace (například aktuální požadavek HTTP), není selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-240">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="c6eea-241">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="c6eea-241">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="c6eea-242">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="c6eea-242">Critical = 5</span></span>

  <span data-ttu-id="c6eea-243">Pro poruchy, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="c6eea-243">For failures that require immediate attention.</span></span> <span data-ttu-id="c6eea-244">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-244">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="c6eea-245">Pomocí úrovně protokolu můžete určit, kolik výstupu protokolu je zapsáno do určitého paměťového média nebo okna zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c6eea-245">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="c6eea-246">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c6eea-246">For example:</span></span>

* <span data-ttu-id="c6eea-247">Ve výrobě:</span><span class="sxs-lookup"><span data-stu-id="c6eea-247">In production:</span></span>
  * <span data-ttu-id="c6eea-248">Protokolování `Trace` na `Information` úrovních pro vytváří velký objem podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-248">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="c6eea-249">Chcete-li řídit náklady a nepřekročit limity úložiště dat, zařazujte `Trace` zprávy `Information` na úrovni do úložiště dat s vysokým objemem a nízkými náklady.</span><span class="sxs-lookup"><span data-stu-id="c6eea-249">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="c6eea-250">Protokolování `Warning` `Critical` na úrovních obvykle vytváří méně, menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-250">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="c6eea-251">Proto náklady a limity úložiště obvykle nejsou problém, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="c6eea-251">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="c6eea-252">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="c6eea-252">During development:</span></span>
  * <span data-ttu-id="c6eea-253">`Warning` Protokolovat `Critical` prostřednictvím zpráv do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-253">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="c6eea-254">Při `Trace` `Information` potížích přidávejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="c6eea-254">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="c6eea-255">Oddíl [Filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovatel zpracovává.</span><span class="sxs-lookup"><span data-stu-id="c6eea-255">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="c6eea-256">ASP.NET Core zapisuje protokoly pro rámcové události.</span><span class="sxs-lookup"><span data-stu-id="c6eea-256">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="c6eea-257">Příklady protokolu dříve v tomto článku `Information` vyloučeny protokoly pod úrovní, takže byly vytvořeny žádné `Debug` protokoly nebo `Trace` úroveň.</span><span class="sxs-lookup"><span data-stu-id="c6eea-257">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="c6eea-258">Tady je příklad protokolů konzoly vytvořených spuštěním `Debug` ukázkové aplikace nakonfigurované tak, aby zobrazovala protokoly:</span><span class="sxs-lookup"><span data-stu-id="c6eea-258">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="c6eea-259">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-259">Log event ID</span></span>

<span data-ttu-id="c6eea-260">Každý protokol může určit *ID události*.</span><span class="sxs-lookup"><span data-stu-id="c6eea-260">Each log can specify an *event ID*.</span></span> <span data-ttu-id="c6eea-261">Ukázková aplikace to provádí pomocí `LoggingEvents` místně definované třídy:</span><span class="sxs-lookup"><span data-stu-id="c6eea-261">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="c6eea-262">ID události přidruží sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-262">An event ID associates a set of events.</span></span> <span data-ttu-id="c6eea-263">Všechny protokoly související se zobrazením seznamu položek na stránce mohou být například 1001.</span><span class="sxs-lookup"><span data-stu-id="c6eea-263">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="c6eea-264">Zprostředkovatel protokolování může uložit ID události v poli ID, ve zprávě protokolování nebo vůbec.</span><span class="sxs-lookup"><span data-stu-id="c6eea-264">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="c6eea-265">Poskytovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-265">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="c6eea-266">Zprostředkovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="c6eea-266">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="c6eea-267">Šablona protokolu zpráv</span><span class="sxs-lookup"><span data-stu-id="c6eea-267">Log message template</span></span>

<span data-ttu-id="c6eea-268">Každý protokol určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="c6eea-268">Each log specifies a message template.</span></span> <span data-ttu-id="c6eea-269">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="c6eea-269">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="c6eea-270">Používejte názvy pro zástupné symboly, ne čísla.</span><span class="sxs-lookup"><span data-stu-id="c6eea-270">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="c6eea-271">Pořadí zástupných symbolů, nikoli jejich názvy, určuje, které parametry se používají k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="c6eea-271">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="c6eea-272">V následujícím kódu si všimněte, že názvy parametrů jsou mimo pořadí v šabloně zprávy:</span><span class="sxs-lookup"><span data-stu-id="c6eea-272">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="c6eea-273">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:</span><span class="sxs-lookup"><span data-stu-id="c6eea-273">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="c6eea-274">Architektura protokolování funguje tak, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="c6eea-274">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="c6eea-275">Samotné argumenty jsou předány systému protokolování, nikoli pouze formátované šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="c6eea-275">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="c6eea-276">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="c6eea-276">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="c6eea-277">Předpokládejme například, že volání metody logger vypadají takto:</span><span class="sxs-lookup"><span data-stu-id="c6eea-277">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="c6eea-278">Pokud odesíláte protokoly do Azure Table Storage, každá `ID` `RequestTime` entita tabulky Azure může mít a vlastnosti, což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-278">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="c6eea-279">Dotaz může najít všechny protokoly `RequestTime` v rámci určité oblasti bez analýzy doby mimo textovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-279">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="c6eea-280">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="c6eea-280">Logging exceptions</span></span>

<span data-ttu-id="c6eea-281">Metody protokolování mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-281">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="c6eea-282">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="c6eea-282">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="c6eea-283">Tady je příklad výstupu zprostředkovatele ladění z výše uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-283">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="c6eea-284">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="c6eea-284">Log filtering</span></span>

<span data-ttu-id="c6eea-285">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny zprostředkovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-285">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="c6eea-286">Všechny protokoly pod minimální úroveň nejsou předány tomuto zprostředkovateli, takže se nezobrazí ani neuloží.</span><span class="sxs-lookup"><span data-stu-id="c6eea-286">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="c6eea-287">Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-287">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="c6eea-288">Celá hodnota `LogLevel.None` je 6, která je `LogLevel.Critical` vyšší než (5).</span><span class="sxs-lookup"><span data-stu-id="c6eea-288">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="c6eea-289">Vytvoření pravidel filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="c6eea-289">Create filter rules in configuration</span></span>

<span data-ttu-id="c6eea-290">Kód šablony projektu `CreateDefaultBuilder` volá k nastavení protokolování pro zprostředkovatele konzoly, ladění a zdroje událostí (ASP.NET Core 2.2 nebo novějším).</span><span class="sxs-lookup"><span data-stu-id="c6eea-290">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="c6eea-291">Metoda `CreateDefaultBuilder` nastaví protokolování hledat konfiguraci v `Logging` části, jak je vysvětleno dříve v tomto [článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="c6eea-291">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="c6eea-292">Konfigurační data určují minimální úrovně protokolu podle zprostředkovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-292">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="c6eea-293">Tento JSON vytvoří šest pravidel filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a jeden pro všechny zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-293">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="c6eea-294">Jedno pravidlo je vybráno pro `ILogger` každého zprostředkovatele při vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-294">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="c6eea-295">Filtrování pravidel v kódu</span><span class="sxs-lookup"><span data-stu-id="c6eea-295">Filter rules in code</span></span>

<span data-ttu-id="c6eea-296">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-296">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="c6eea-297">Druhý `AddFilter` určuje zprostředkovatele ladění pomocí jeho název typu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-297">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="c6eea-298">První `AddFilter` platí pro všechny zprostředkovatele, protože neurčuje typ zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-298">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="c6eea-299">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="c6eea-299">How filtering rules are applied</span></span>

<span data-ttu-id="c6eea-300">Konfigurační `AddFilter` data a kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c6eea-300">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="c6eea-301">Prvních šest pocházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-301">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="c6eea-302">Číslo</span><span class="sxs-lookup"><span data-stu-id="c6eea-302">Number</span></span> | <span data-ttu-id="c6eea-303">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="c6eea-303">Provider</span></span>      | <span data-ttu-id="c6eea-304">Kategorie, které začínají ...</span><span class="sxs-lookup"><span data-stu-id="c6eea-304">Categories that begin with ...</span></span>          | <span data-ttu-id="c6eea-305">Minimální úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-305">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="c6eea-306">1</span><span class="sxs-lookup"><span data-stu-id="c6eea-306">1</span></span>      | <span data-ttu-id="c6eea-307">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-307">Debug</span></span>         | <span data-ttu-id="c6eea-308">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="c6eea-308">All categories</span></span>                          | <span data-ttu-id="c6eea-309">Informace</span><span class="sxs-lookup"><span data-stu-id="c6eea-309">Information</span></span>       |
| <span data-ttu-id="c6eea-310">2</span><span class="sxs-lookup"><span data-stu-id="c6eea-310">2</span></span>      | <span data-ttu-id="c6eea-311">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-311">Console</span></span>       | <span data-ttu-id="c6eea-312">Microsoft.AspNetCore.Mvc.Razor.Interní</span><span class="sxs-lookup"><span data-stu-id="c6eea-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="c6eea-313">Upozornění</span><span class="sxs-lookup"><span data-stu-id="c6eea-313">Warning</span></span>           |
| <span data-ttu-id="c6eea-314">3</span><span class="sxs-lookup"><span data-stu-id="c6eea-314">3</span></span>      | <span data-ttu-id="c6eea-315">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-315">Console</span></span>       | <span data-ttu-id="c6eea-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="c6eea-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="c6eea-317">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-317">Debug</span></span>             |
| <span data-ttu-id="c6eea-318">4</span><span class="sxs-lookup"><span data-stu-id="c6eea-318">4</span></span>      | <span data-ttu-id="c6eea-319">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-319">Console</span></span>       | <span data-ttu-id="c6eea-320">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="c6eea-320">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="c6eea-321">Chyba</span><span class="sxs-lookup"><span data-stu-id="c6eea-321">Error</span></span>             |
| <span data-ttu-id="c6eea-322">5</span><span class="sxs-lookup"><span data-stu-id="c6eea-322">5</span></span>      | <span data-ttu-id="c6eea-323">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-323">Console</span></span>       | <span data-ttu-id="c6eea-324">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="c6eea-324">All categories</span></span>                          | <span data-ttu-id="c6eea-325">Informace</span><span class="sxs-lookup"><span data-stu-id="c6eea-325">Information</span></span>       |
| <span data-ttu-id="c6eea-326">6</span><span class="sxs-lookup"><span data-stu-id="c6eea-326">6</span></span>      | <span data-ttu-id="c6eea-327">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="c6eea-327">All providers</span></span> | <span data-ttu-id="c6eea-328">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="c6eea-328">All categories</span></span>                          | <span data-ttu-id="c6eea-329">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-329">Debug</span></span>             |
| <span data-ttu-id="c6eea-330">7</span><span class="sxs-lookup"><span data-stu-id="c6eea-330">7</span></span>      | <span data-ttu-id="c6eea-331">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="c6eea-331">All providers</span></span> | <span data-ttu-id="c6eea-332">Systém</span><span class="sxs-lookup"><span data-stu-id="c6eea-332">System</span></span>                                  | <span data-ttu-id="c6eea-333">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-333">Debug</span></span>             |
| <span data-ttu-id="c6eea-334">8</span><span class="sxs-lookup"><span data-stu-id="c6eea-334">8</span></span>      | <span data-ttu-id="c6eea-335">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-335">Debug</span></span>         | <span data-ttu-id="c6eea-336">Microsoft</span><span class="sxs-lookup"><span data-stu-id="c6eea-336">Microsoft</span></span>                               | <span data-ttu-id="c6eea-337">Trasování</span><span class="sxs-lookup"><span data-stu-id="c6eea-337">Trace</span></span>             |

<span data-ttu-id="c6eea-338">Při `ILogger` vytvoření objektu, `ILoggerFactory` objekt vybere jedno pravidlo pro zprostředkovatele použít na tento protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-338">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="c6eea-339">Všechny zprávy napsané instancí `ILogger` jsou filtrovány na základě vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="c6eea-339">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="c6eea-340">Nejkonkrétnější pravidlo možné pro každého poskytovatele a dvojice kategorií je vybránz dostupných pravidel.</span><span class="sxs-lookup"><span data-stu-id="c6eea-340">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="c6eea-341">Následující algoritmus se používá pro `ILogger` každého zprostředkovatele při vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="c6eea-341">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="c6eea-342">Vyberte všechna pravidla, která odpovídají poskytovateli nebo jeho aliasu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-342">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="c6eea-343">Pokud není nalezena žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="c6eea-343">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="c6eea-344">Z výsledku předchozího kroku vyberte pravidla s nejdelší předponou kategorie shody.</span><span class="sxs-lookup"><span data-stu-id="c6eea-344">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="c6eea-345">Pokud není nalezena žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="c6eea-345">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="c6eea-346">Pokud je vybráno více pravidel, vezměte **poslední.**</span><span class="sxs-lookup"><span data-stu-id="c6eea-346">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="c6eea-347">Pokud nejsou vybrána `MinimumLevel`žádná pravidla, použijte .</span><span class="sxs-lookup"><span data-stu-id="c6eea-347">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="c6eea-348">S předchozím seznamem pravidel předpokládejme, `ILogger` že vytvoříte objekt pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="c6eea-348">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="c6eea-349">Pro zprostředkovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="c6eea-349">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="c6eea-350">Pravidlo 8 je nejkonkrétnější, takže to je ten, který byl vybrán.</span><span class="sxs-lookup"><span data-stu-id="c6eea-350">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="c6eea-351">Pro zprostředkovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="c6eea-351">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="c6eea-352">Pravidlo 3 je nejkonkrétnější.</span><span class="sxs-lookup"><span data-stu-id="c6eea-352">Rule 3 is most specific.</span></span>

<span data-ttu-id="c6eea-353">Výsledná `ILogger` instance odešle `Trace` protokoly úrovně a výše zprostředkovateli ladění.</span><span class="sxs-lookup"><span data-stu-id="c6eea-353">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="c6eea-354">Protokoly `Debug` úrovně a výše jsou odesílány zprostředkovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-354">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="c6eea-355">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="c6eea-355">Provider aliases</span></span>

<span data-ttu-id="c6eea-356">Každý zprostředkovatel definuje *alias,* který lze použít v konfiguraci namísto plně kvalifikovaný název typu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-356">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="c6eea-357">Pro předdefinované zprostředkovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="c6eea-357">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="c6eea-358">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-358">Console</span></span>
* <span data-ttu-id="c6eea-359">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-359">Debug</span></span>
* <span data-ttu-id="c6eea-360">EventSource</span><span class="sxs-lookup"><span data-stu-id="c6eea-360">EventSource</span></span>
* <span data-ttu-id="c6eea-361">Eventlog</span><span class="sxs-lookup"><span data-stu-id="c6eea-361">EventLog</span></span>
* <span data-ttu-id="c6eea-362">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c6eea-362">TraceSource</span></span>
* <span data-ttu-id="c6eea-363">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="c6eea-363">AzureAppServicesFile</span></span>
* <span data-ttu-id="c6eea-364">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="c6eea-364">AzureAppServicesBlob</span></span>
* <span data-ttu-id="c6eea-365">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="c6eea-365">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="c6eea-366">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="c6eea-366">Default minimum level</span></span>

<span data-ttu-id="c6eea-367">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že pro daného zprostředkovatele a kategorii neplatí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-367">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="c6eea-368">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="c6eea-368">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="c6eea-369">Pokud explicitně nenastavíte minimální úroveň, `Information`výchozí hodnota `Trace` je `Debug` , což znamená, že a protokoly jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="c6eea-369">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="c6eea-370">Funkce filtrování</span><span class="sxs-lookup"><span data-stu-id="c6eea-370">Filter functions</span></span>

<span data-ttu-id="c6eea-371">Funkce filtru je vyvolána pro všechny zprostředkovatele a kategorie, které nemají pravidla přiřazená podle konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-371">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="c6eea-372">Kód ve funkci má přístup k typu zprostředkovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-372">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="c6eea-373">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c6eea-373">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="c6eea-374">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="c6eea-374">System categories and levels</span></span>

<span data-ttu-id="c6eea-375">Zde jsou některé kategorie používané ASP.NET jádrem a jádrem entity s poznámkami o tom, jaké protokoly od nich lze očekávat:</span><span class="sxs-lookup"><span data-stu-id="c6eea-375">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="c6eea-376">Kategorie</span><span class="sxs-lookup"><span data-stu-id="c6eea-376">Category</span></span>                            | <span data-ttu-id="c6eea-377">Poznámky</span><span class="sxs-lookup"><span data-stu-id="c6eea-377">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="c6eea-378">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="c6eea-378">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="c6eea-379">Obecná diagnostika ASP.NET jádra.</span><span class="sxs-lookup"><span data-stu-id="c6eea-379">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="c6eea-380">Ochrana Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="c6eea-380">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="c6eea-381">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="c6eea-381">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="c6eea-382">Filtrování Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="c6eea-382">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="c6eea-383">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="c6eea-383">Hosts allowed.</span></span> |
| <span data-ttu-id="c6eea-384">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="c6eea-384">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="c6eea-385">Jak dlouho trvalo dokončení požadavků HTTP a v jakém čase byly spuštěny.</span><span class="sxs-lookup"><span data-stu-id="c6eea-385">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="c6eea-386">Která hostitelská spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="c6eea-386">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="c6eea-387">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="c6eea-387">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="c6eea-388">Diagnostika MVC a Razor.</span><span class="sxs-lookup"><span data-stu-id="c6eea-388">MVC and Razor diagnostics.</span></span> <span data-ttu-id="c6eea-389">Vazba modelu, spuštění filtru, kompilace zobrazení, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="c6eea-389">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="c6eea-390">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="c6eea-390">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="c6eea-391">Informace o porovnávání tras.</span><span class="sxs-lookup"><span data-stu-id="c6eea-391">Route matching information.</span></span> |
| <span data-ttu-id="c6eea-392">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="c6eea-392">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="c6eea-393">Připojení spustit, zastavit a udržet naživu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c6eea-393">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="c6eea-394">informace o certifikátu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c6eea-394">HTTPS certificate information.</span></span> |
| <span data-ttu-id="c6eea-395">Soubory Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="c6eea-395">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="c6eea-396">Doručené složky.</span><span class="sxs-lookup"><span data-stu-id="c6eea-396">Files served.</span></span> |
| <span data-ttu-id="c6eea-397">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="c6eea-397">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="c6eea-398">Diagnostika jádra obecnéentity frameworku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-398">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="c6eea-399">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-399">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="c6eea-400">Obory protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-400">Log scopes</span></span>

 <span data-ttu-id="c6eea-401">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="c6eea-401">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="c6eea-402">Toto seskupení lze použít k připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="c6eea-402">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="c6eea-403">Například každý protokol vytvořený jako součást zpracování transakce může obsahovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="c6eea-403">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="c6eea-404">Obor je `IDisposable` typ, který je <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> vrácen metodou a trvá, dokud je uvolněn.</span><span class="sxs-lookup"><span data-stu-id="c6eea-404">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="c6eea-405">Použití oboru zabalením volání `using` protokolování v bloku:</span><span class="sxs-lookup"><span data-stu-id="c6eea-405">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="c6eea-406">Následující kód umožňuje obory pro zprostředkovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="c6eea-406">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="c6eea-407">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c6eea-407">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="c6eea-408">Konfigurace možnosti `IncludeScopes` protokolování konzoly je nutná k povolení protokolování založeného na oboru.</span><span class="sxs-lookup"><span data-stu-id="c6eea-408">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="c6eea-409">Informace o konfiguraci naleznete v části [Konfigurace.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="c6eea-409">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="c6eea-410">Každá zpráva protokolu obsahuje informace s vymezenou oborem:</span><span class="sxs-lookup"><span data-stu-id="c6eea-410">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="c6eea-411">Vestavění poskytovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="c6eea-411">Built-in logging providers</span></span>

<span data-ttu-id="c6eea-412">ASP.NET Core dodává tyto poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="c6eea-412">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="c6eea-413">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-413">Console</span></span>](#console-provider)
* [<span data-ttu-id="c6eea-414">Ladění</span><span class="sxs-lookup"><span data-stu-id="c6eea-414">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="c6eea-415">Zdroj událostí</span><span class="sxs-lookup"><span data-stu-id="c6eea-415">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="c6eea-416">Eventlog</span><span class="sxs-lookup"><span data-stu-id="c6eea-416">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="c6eea-417">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c6eea-417">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="c6eea-418">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="c6eea-418">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="c6eea-419">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="c6eea-419">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="c6eea-420">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="c6eea-420">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="c6eea-421">Informace o protokolování stdout a ladění pomocí <xref:test/troubleshoot-azure-iis> ASP.NET <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>core modulem naleznete v tématu a .</span><span class="sxs-lookup"><span data-stu-id="c6eea-421">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="c6eea-422">Zprostředkovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="c6eea-422">Console provider</span></span>

<span data-ttu-id="c6eea-423">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-423">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="c6eea-424">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c6eea-424">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="c6eea-425">Zprostředkovatel ladění</span><span class="sxs-lookup"><span data-stu-id="c6eea-425">Debug provider</span></span>

<span data-ttu-id="c6eea-426">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí`Debug.WriteLine` [třídy System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (volání metody).</span><span class="sxs-lookup"><span data-stu-id="c6eea-426">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="c6eea-427">V systému Linux tento zprostředkovatel zapisuje protokoly do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="c6eea-427">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="c6eea-428">Zprostředkovatel zdroje událostí</span><span class="sxs-lookup"><span data-stu-id="c6eea-428">Event Source provider</span></span>

<span data-ttu-id="c6eea-429">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na `Microsoft-Extensions-Logging`křížovou platformu Zdroje událostí s názvem .</span><span class="sxs-lookup"><span data-stu-id="c6eea-429">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="c6eea-430">V systému Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="c6eea-430">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="c6eea-431">Zprostředkovatel zdroje událostí je `CreateDefaultBuilder` přidán automaticky, když je volána k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-431">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="c6eea-432">nástroje pro trasování dotnet</span><span class="sxs-lookup"><span data-stu-id="c6eea-432">dotnet trace tooling</span></span>

<span data-ttu-id="c6eea-433">[Dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) nástroj je globální nástroj rozhraní se sídlem v rychléplatformě, který umožňuje shromažďování trasování .NET Core spuštěného procesu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-433">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="c6eea-434">Nástroj shromažďuje <xref:Microsoft.Extensions.Logging.EventSource> data zprostředkovatele <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>pomocí .</span><span class="sxs-lookup"><span data-stu-id="c6eea-434">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="c6eea-435">Nainstalujte nástroje trasování dotnet pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-435">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="c6eea-436">Pomocí nástrojů sledování dotnet shromažďujte trasování z aplikace:</span><span class="sxs-lookup"><span data-stu-id="c6eea-436">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="c6eea-437">Pokud aplikace nevytváří hostitele s `CreateDefaultBuilder`, přidejte [poskytovatele zdroje událostí](#event-source-provider) do konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-437">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="c6eea-438">Spusťte aplikaci pomocí příkazu. `dotnet run`</span><span class="sxs-lookup"><span data-stu-id="c6eea-438">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="c6eea-439">Určete identifikátor procesu (PID) aplikace .NET Core:</span><span class="sxs-lookup"><span data-stu-id="c6eea-439">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="c6eea-440">V systému Windows použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c6eea-440">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="c6eea-441">Správce úloh (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="c6eea-441">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="c6eea-442">seznam úloh, příkaz</span><span class="sxs-lookup"><span data-stu-id="c6eea-442">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="c6eea-443">Příkaz Získat proces powershellu</span><span class="sxs-lookup"><span data-stu-id="c6eea-443">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="c6eea-444">Na Linuxu použijte [příkaz pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="c6eea-444">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="c6eea-445">Najděte PID pro proces, který má stejný název jako sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-445">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="c6eea-446">Spusťte `dotnet trace` příkaz.</span><span class="sxs-lookup"><span data-stu-id="c6eea-446">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="c6eea-447">Obecná syntaxe příkazu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-447">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="c6eea-448">Při použití příkazového prostředí Prostředí `--providers` PowerShell uzavřete hodnotu do jednoduchých uvozovek (`'`):</span><span class="sxs-lookup"><span data-stu-id="c6eea-448">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="c6eea-449">Na platformách jiných než `-f speedscope` Windows přidejte možnost změnit formát `speedscope`výstupního trasovacího souboru na .</span><span class="sxs-lookup"><span data-stu-id="c6eea-449">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="c6eea-450">Klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="c6eea-450">Keyword</span></span> | <span data-ttu-id="c6eea-451">Popis</span><span class="sxs-lookup"><span data-stu-id="c6eea-451">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="c6eea-452">1</span><span class="sxs-lookup"><span data-stu-id="c6eea-452">1</span></span>       | <span data-ttu-id="c6eea-453">Protokolovat meta `LoggingEventSource`události o .</span><span class="sxs-lookup"><span data-stu-id="c6eea-453">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="c6eea-454">Nezaznamenává události z `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="c6eea-454">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="c6eea-455">2</span><span class="sxs-lookup"><span data-stu-id="c6eea-455">2</span></span>       | <span data-ttu-id="c6eea-456">Zapne událost, `Message` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="c6eea-456">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="c6eea-457">Poskytuje informace programovým (neformátovaným) způsobem.</span><span class="sxs-lookup"><span data-stu-id="c6eea-457">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="c6eea-458">4</span><span class="sxs-lookup"><span data-stu-id="c6eea-458">4</span></span>       | <span data-ttu-id="c6eea-459">Zapne událost, `FormatMessage` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="c6eea-459">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="c6eea-460">Poskytuje formátovanou verzi řetězce informací.</span><span class="sxs-lookup"><span data-stu-id="c6eea-460">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="c6eea-461">8</span><span class="sxs-lookup"><span data-stu-id="c6eea-461">8</span></span>       | <span data-ttu-id="c6eea-462">Zapne událost, `MessageJson` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="c6eea-462">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="c6eea-463">Poskytuje json reprezentaci argumentů.</span><span class="sxs-lookup"><span data-stu-id="c6eea-463">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="c6eea-464">Úroveň události</span><span class="sxs-lookup"><span data-stu-id="c6eea-464">Event Level</span></span> | <span data-ttu-id="c6eea-465">Popis</span><span class="sxs-lookup"><span data-stu-id="c6eea-465">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="c6eea-466">0</span><span class="sxs-lookup"><span data-stu-id="c6eea-466">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="c6eea-467">1</span><span class="sxs-lookup"><span data-stu-id="c6eea-467">1</span></span>           | `Critical`      |
   | <span data-ttu-id="c6eea-468">2</span><span class="sxs-lookup"><span data-stu-id="c6eea-468">2</span></span>           | `Error`         |
   | <span data-ttu-id="c6eea-469">3</span><span class="sxs-lookup"><span data-stu-id="c6eea-469">3</span></span>           | `Warning`       |
   | <span data-ttu-id="c6eea-470">4</span><span class="sxs-lookup"><span data-stu-id="c6eea-470">4</span></span>           | `Informational` |
   | <span data-ttu-id="c6eea-471">5</span><span class="sxs-lookup"><span data-stu-id="c6eea-471">5</span></span>           | `Verbose`       |

   <span data-ttu-id="c6eea-472">`FilterSpecs`položky `{Logger Category}` `{Event Level}` pro a představují další podmínky filtrování protokolů.</span><span class="sxs-lookup"><span data-stu-id="c6eea-472">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="c6eea-473">Samostatné `FilterSpecs` položky s`;`středníkem ( ).</span><span class="sxs-lookup"><span data-stu-id="c6eea-473">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="c6eea-474">Příklad použití příkazového prostředí systému `--providers` Windows **(žádné** jednoduché uvozovky kolem hodnoty):</span><span class="sxs-lookup"><span data-stu-id="c6eea-474">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="c6eea-475">Předchozí příkaz aktivuje:</span><span class="sxs-lookup"><span data-stu-id="c6eea-475">The preceding command activates:</span></span>

   * <span data-ttu-id="c6eea-476">Protokolovací nástroj Zdroje událostí pro`4`vytvoření formátovaných`2`řetězců ( ) pro chyby ( ).</span><span class="sxs-lookup"><span data-stu-id="c6eea-476">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="c6eea-477">`Microsoft.AspNetCore.Hosting`protokolování `Informational` na úrovni`4`protokolování ( ).</span><span class="sxs-lookup"><span data-stu-id="c6eea-477">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="c6eea-478">Zastavení nástroje trasování dotnet stisknutím klávesy Enter nebo Ctrl+C.</span><span class="sxs-lookup"><span data-stu-id="c6eea-478">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="c6eea-479">Trasování je uložens s názvem *trace.nettrace* ve složce, `dotnet trace` kde je příkaz proveden.</span><span class="sxs-lookup"><span data-stu-id="c6eea-479">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="c6eea-480">Otevřete trasování pomocí [perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="c6eea-480">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="c6eea-481">Otevřete soubor *trace.nettrace* a prozkoumejte události trasování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-481">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="c6eea-482">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-482">For more information, see:</span></span>

* <span data-ttu-id="c6eea-483">[Trasování pro nástroj pro analýzu výkonu (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentace jádra.NET)</span><span class="sxs-lookup"><span data-stu-id="c6eea-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="c6eea-484">[Trasování pro nástroj pro analýzu výkonu (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentace úložiště dotnet/diagnostics GitHub)</span><span class="sxs-lookup"><span data-stu-id="c6eea-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="c6eea-485">[Třída LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (prohlížeč rozhraní API.NET)</span><span class="sxs-lookup"><span data-stu-id="c6eea-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="c6eea-486">[Název reference LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Chcete-li získat referenční zdroj `release/{Version}`pro `{Version}` jinou verzi, změňte větev na , kde je požadovaná verze ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6eea-486">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="c6eea-487">[Perfview](#perfview) &ndash; Užitečné pro zobrazení trasování zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-487">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="c6eea-488">Zobrazení Perfview</span><span class="sxs-lookup"><span data-stu-id="c6eea-488">Perfview</span></span>

<span data-ttu-id="c6eea-489">Pomocí [nástroje PerfView](https://github.com/Microsoft/perfview) sbírat a zobrazovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-489">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="c6eea-490">Existují další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi ETW vyzařované ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6eea-490">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="c6eea-491">Chcete-li nakonfigurovat PerfView pro shromažďování událostí `*Microsoft-Extensions-Logging` protokolovaných tímto zprostředkovatelem, přidejte řetězec do seznamu **Další zprostředkovatelé.**</span><span class="sxs-lookup"><span data-stu-id="c6eea-491">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="c6eea-492">(Nenechte si ujít hvězdičku na začátku řetězce.)</span><span class="sxs-lookup"><span data-stu-id="c6eea-492">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="c6eea-494">Zprostředkovatel služby Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="c6eea-494">Windows EventLog provider</span></span>

<span data-ttu-id="c6eea-495">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="c6eea-495">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="c6eea-496">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="c6eea-496">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="c6eea-497">Pokud `null` není zadáno, použije se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="c6eea-497">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="c6eea-498">`LogName`&ndash; "Aplikace"</span><span class="sxs-lookup"><span data-stu-id="c6eea-498">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="c6eea-499">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="c6eea-499">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="c6eea-500">`MachineName`&ndash; místní počítač</span><span class="sxs-lookup"><span data-stu-id="c6eea-500">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="c6eea-501">Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level).</span><span class="sxs-lookup"><span data-stu-id="c6eea-501">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="c6eea-502">Chcete-li protokolovat události nižší než `Warning`, explicitně nastavte úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-502">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="c6eea-503">Do souboru *appsettings.json* například přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="c6eea-503">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="c6eea-504">Zprostředkovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="c6eea-504">TraceSource provider</span></span>

<span data-ttu-id="c6eea-505">Balíček [zprostředkovatele Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-505">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="c6eea-506">[AddTraceSource přetížení](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="c6eea-507">Chcete-li použít tohoto zprostředkovatele, aplikace musí běžet na rozhraní .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="c6eea-507">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="c6eea-508">Zprostředkovatel může směrovat zprávy do různých [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použité v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c6eea-508">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="c6eea-509">Poskytovatel služeb Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c6eea-509">Azure App Service provider</span></span>

<span data-ttu-id="c6eea-510">Balíček zprostředkovatele [Microsoft.Extensions.Log.Log.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Služby Azure App Service a do [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="c6eea-510">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="c6eea-511">Balíček zprostředkovatele není součástí sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c6eea-511">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="c6eea-512">Chcete-li použít zprostředkovatele, přidejte balíček zprostředkovatele do projektu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-512">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="c6eea-513">Chcete-li konfigurovat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> nastavení <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>zprostředkovatele, použijte a , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-513">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="c6eea-514">Když se nasadíte do aplikace App Service, aplikace respektuje nastavení v části [protokoly služby App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stránce **Služby aplikací** na portálu Azure.</span><span class="sxs-lookup"><span data-stu-id="c6eea-514">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="c6eea-515">Při aktualizaci následujících nastavení se změny projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-515">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="c6eea-516">**Protokolování aplikací (souborový systém)**</span><span class="sxs-lookup"><span data-stu-id="c6eea-516">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="c6eea-517">**Protokolování aplikací (objekt blob)**</span><span class="sxs-lookup"><span data-stu-id="c6eea-517">**Application Logging (Blob)**</span></span>

<span data-ttu-id="c6eea-518">Výchozí umístění souborů protokolu je ve složce *D:\\\\home LogFiles\\Application* a výchozí název souboru je *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="c6eea-518">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="c6eea-519">Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="c6eea-519">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="c6eea-520">Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="c6eea-520">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="c6eea-521">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="c6eea-521">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="c6eea-522">Nemá žádný vliv, když je&mdash;projekt spuštěn místně, nezapisuje do místních souborů nebo místní vývojové úložiště pro objekty BLOB.</span><span class="sxs-lookup"><span data-stu-id="c6eea-522">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="c6eea-523">Streamování protokolu Azure</span><span class="sxs-lookup"><span data-stu-id="c6eea-523">Azure log streaming</span></span>

<span data-ttu-id="c6eea-524">Streamování protokolu Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="c6eea-524">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="c6eea-525">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="c6eea-525">The app server</span></span>
* <span data-ttu-id="c6eea-526">Webový server</span><span class="sxs-lookup"><span data-stu-id="c6eea-526">The web server</span></span>
* <span data-ttu-id="c6eea-527">Trasování neúspěšných požadavků</span><span class="sxs-lookup"><span data-stu-id="c6eea-527">Failed request tracing</span></span>

<span data-ttu-id="c6eea-528">Konfigurace streamování protokolu Azure:</span><span class="sxs-lookup"><span data-stu-id="c6eea-528">To configure Azure log streaming:</span></span>

* <span data-ttu-id="c6eea-529">Přejděte na stránku **protokolů služby App Service** na stránce portálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-529">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="c6eea-530">Nastavení **protokolování aplikací (souborový systém)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="c6eea-530">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="c6eea-531">Zvolte **úroveň**protokolu .</span><span class="sxs-lookup"><span data-stu-id="c6eea-531">Choose the log **Level**.</span></span> <span data-ttu-id="c6eea-532">Toto nastavení platí jenom pro streamování protokolu Azure, ne pro jiné poskytovatele protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c6eea-532">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="c6eea-533">Přejděte na stránku **Log Stream** a zobrazte zprávy aplikací.</span><span class="sxs-lookup"><span data-stu-id="c6eea-533">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="c6eea-534">Jsou zaznamenány aplikací prostřednictvím `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c6eea-534">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="c6eea-535">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="c6eea-535">Azure Application Insights trace logging</span></span>

<span data-ttu-id="c6eea-536">Balíček zprostředkovatele [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c6eea-536">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="c6eea-537">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu telemetrických dat.</span><span class="sxs-lookup"><span data-stu-id="c6eea-537">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="c6eea-538">Pokud používáte tohoto zprostředkovatele, můžete dotazovat a analyzovat protokoly pomocí nástrojů Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c6eea-538">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="c6eea-539">Zprostředkovatel protokolování je součástí jako závislost [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje všechny dostupné telemetrie pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6eea-539">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="c6eea-540">Pokud používáte tento balíček, není nutné instalovat balíček zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-540">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="c6eea-541">Nepoužívejte balíček&mdash; [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) pro ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="c6eea-541">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="c6eea-542">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="c6eea-542">For more information, see the following resources:</span></span>

* [<span data-ttu-id="c6eea-543">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="c6eea-543">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="c6eea-544">[Application Insights pro ASP.NET základní aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou škálu telemetrie Application Insights spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="c6eea-544">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="c6eea-545">[ApplicationInsightsLoggerProvider pro protokoly ILogger jádra .NET](/azure/azure-monitor/app/ilogger) – začněte zde, pokud chcete implementovat poskytovatele protokolování bez zbytku telemetrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c6eea-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="c6eea-546">[Adaptéry pro protokolování Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="c6eea-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="c6eea-547">[Nainstalujte, nakonfigurujte a inicializujte kurz Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="c6eea-547">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="c6eea-548">Poskytovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="c6eea-548">Third-party logging providers</span></span>

<span data-ttu-id="c6eea-549">Architektury protokolování třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c6eea-549">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="c6eea-550">[elmah.io](https://elmah.io/) ([Úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6eea-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="c6eea-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="c6eea-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="c6eea-552">[JSNLog](https://jsnlog.com/) ([Úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="c6eea-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="c6eea-553">[KissLog.net](https://kisslog.net/) ([Úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="c6eea-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="c6eea-554">[Log4Net](https://logging.apache.org/log4net/) ([Úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="c6eea-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="c6eea-555">[Loggr](https://loggr.net/) ([Úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6eea-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="c6eea-556">[NLog](https://nlog-project.org/) ([Úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6eea-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="c6eea-557">[Sentry](https://sentry.io/welcome/) ([Úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="c6eea-557">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="c6eea-558">[Serilog](https://serilog.net/) ([Úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="c6eea-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="c6eea-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Úložiště Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="c6eea-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="c6eea-560">Některé architektury jiných výrobců můžete provádět [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="c6eea-560">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="c6eea-561">Použití rozhraní třetí strany je podobné použití jednoho z předdefinovaných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="c6eea-561">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="c6eea-562">Přidejte balíček NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-562">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="c6eea-563">Volání `ILoggerFactory` metody rozšíření poskytované rozhraní protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-563">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="c6eea-564">Další informace naleznete v dokumentaci každého zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-564">For more information, see each provider's documentation.</span></span> <span data-ttu-id="c6eea-565">Společnost Microsoft nepodporuje poskytovatele protokolování jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="c6eea-565">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c6eea-566">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c6eea-566">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6eea-567">Tom [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c6eea-567">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c6eea-568">Rozhraní .NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="c6eea-568">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="c6eea-569">Tento článek ukazuje, jak používat rozhraní API protokolování s integrovanými zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="c6eea-569">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="c6eea-570">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c6eea-570">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="c6eea-571">Přidání zprostředkovatelů</span><span class="sxs-lookup"><span data-stu-id="c6eea-571">Add providers</span></span>

<span data-ttu-id="c6eea-572">Zprostředkovatel protokolování zobrazí nebo uloží protokoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-572">A logging provider displays or stores logs.</span></span> <span data-ttu-id="c6eea-573">Například zprostředkovatel konzoly zobrazí protokoly na konzoli a poskytovatel Azure Application Insights je uloží do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c6eea-573">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="c6eea-574">Protokoly lze odeslat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="c6eea-574">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="c6eea-575">Chcete-li přidat zprostředkovatele, `Add{provider name}` volejte metodu rozšíření zprostředkovatele v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c6eea-575">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="c6eea-576">Předchozí kód vyžaduje odkazy `Microsoft.Extensions.Logging` na `Microsoft.Extensions.Configuration`a .</span><span class="sxs-lookup"><span data-stu-id="c6eea-576">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="c6eea-577">Výchozí šablona <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>projektu volá , která přidá následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="c6eea-577">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="c6eea-578">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-578">Console</span></span>
* <span data-ttu-id="c6eea-579">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-579">Debug</span></span>
* <span data-ttu-id="c6eea-580">EventSource (počínaje ASP.NET jádrem 2.2)</span><span class="sxs-lookup"><span data-stu-id="c6eea-580">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="c6eea-581">Pokud používáte `CreateDefaultBuilder`, můžete nahradit výchozí zprostředkovatele vlastními volbami.</span><span class="sxs-lookup"><span data-stu-id="c6eea-581">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="c6eea-582">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte požadované zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-582">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="c6eea-583">Další informace o [předdefinovaných zprostředkovatelích protokolování](#built-in-logging-providers) a [poskytovatelích protokolování třetích stran](#third-party-logging-providers) dále v článku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-583">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="c6eea-584">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="c6eea-584">Create logs</span></span>

<span data-ttu-id="c6eea-585">Chcete-li vytvořit protokoly, použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="c6eea-585">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="c6eea-586">Ve webové aplikaci nebo hostované `ILogger` službě získáte z vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="c6eea-586">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="c6eea-587">V aplikacích konzoly, `LoggerFactory` které nejsou `ILogger`hostitelskou konzolou, použijte k vytvoření aplikace .</span><span class="sxs-lookup"><span data-stu-id="c6eea-587">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="c6eea-588">Následující příklad ASP.NET Core vytvoří `TodoApiSample.Pages.AboutModel` protokolovací nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-588">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="c6eea-589">*Kategorie* protokolu je řetězec, který je přidružen ke každému protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-589">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="c6eea-590">Instance `ILogger<T>` poskytované DI vytvoří protokoly, které mají plně `T` kvalifikovaný název typu jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-590">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="c6eea-591">V následujících příkladech ASP.NET core a konzolové aplikace, `Information` protokolování se používá k vytvoření protokolů s jako úroveň.</span><span class="sxs-lookup"><span data-stu-id="c6eea-591">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="c6eea-592">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="c6eea-592">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="c6eea-593">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-593">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="c6eea-594">Vytvoření protokolů při spuštění</span><span class="sxs-lookup"><span data-stu-id="c6eea-594">Create logs in Startup</span></span>

<span data-ttu-id="c6eea-595">Chcete-li zapsat `Startup` protokoly `ILogger` do třídy, zahrňte parametr do podpisu konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="c6eea-595">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="c6eea-596">Vytvoření protokolů ve třídě Program</span><span class="sxs-lookup"><span data-stu-id="c6eea-596">Create logs in the Program class</span></span>

<span data-ttu-id="c6eea-597">Chcete-li zapsat `Program` protokoly `ILogger` ve třídě, získejte instanci z DI:</span><span class="sxs-lookup"><span data-stu-id="c6eea-597">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="c6eea-598">Protokolování během výstavby hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="c6eea-598">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="c6eea-599">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="c6eea-599">However, a separate logger can be used.</span></span> <span data-ttu-id="c6eea-600">V následujícím příkladu se protokolovací nástroj [Serilog](https://serilog.net/) používá k přihlášení `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c6eea-600">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="c6eea-601">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="c6eea-601">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="c6eea-602">Žádné asynchronní metody protokolování</span><span class="sxs-lookup"><span data-stu-id="c6eea-602">No asynchronous logger methods</span></span>

<span data-ttu-id="c6eea-603">Protokolování by mělo být tak rychlé, že nestojí za náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-603">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="c6eea-604">Pokud je úložiště dat protokolování pomalé, nezapisujte do něj přímo.</span><span class="sxs-lookup"><span data-stu-id="c6eea-604">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="c6eea-605">Zvažte zápis zpráv protokolu do rychlého úložiště zpočátku a potom je přesunout do pomalé úložiště později.</span><span class="sxs-lookup"><span data-stu-id="c6eea-605">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="c6eea-606">Například pokud se přihlašujete k serveru SQL Server, `Log` nechcete `Log` to provést přímo v metodě, protože metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="c6eea-606">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="c6eea-607">Místo toho synchronně přidat zprávy protokolu do fronty v paměti a mají pracovník na pozadí vytáhnout zprávy z fronty provést asynchronní práci odesílání dat na SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c6eea-607">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="c6eea-608">Další informace naleznete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHub.</span><span class="sxs-lookup"><span data-stu-id="c6eea-608">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="c6eea-609">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="c6eea-609">Configuration</span></span>

<span data-ttu-id="c6eea-610">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="c6eea-610">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="c6eea-611">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="c6eea-611">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="c6eea-612">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-612">Command-line arguments.</span></span>
* <span data-ttu-id="c6eea-613">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-613">Environment variables.</span></span>
* <span data-ttu-id="c6eea-614">Objekty .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="c6eea-614">In-memory .NET objects.</span></span>
* <span data-ttu-id="c6eea-615">Nešifrované úložiště [Správce tajných](xref:security/app-secrets) služeb.</span><span class="sxs-lookup"><span data-stu-id="c6eea-615">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="c6eea-616">Šifrované úložiště uživatelů, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="c6eea-616">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="c6eea-617">Vlastní zprostředkovatelé (nainstalované nebo vytvořené).</span><span class="sxs-lookup"><span data-stu-id="c6eea-617">Custom providers (installed or created).</span></span>

<span data-ttu-id="c6eea-618">Například konfigurace protokolování je běžně `Logging` poskytována v části souborů nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-618">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="c6eea-619">Následující příklad ukazuje obsah typické *nastavení aplikace. Development.json* soubor:</span><span class="sxs-lookup"><span data-stu-id="c6eea-619">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="c6eea-620">Vlastnost `Logging` může `LogLevel` mít a protokolovat vlastnosti zprostředkovatele (je zobrazena konzola).</span><span class="sxs-lookup"><span data-stu-id="c6eea-620">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="c6eea-621">Vlastnost `LogLevel` v `Logging` části určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-621">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="c6eea-622">V příkladu `System` `Microsoft` a kategorie `Information` protokolu na úrovni `Debug` a všechny ostatní protokolu na úrovni.</span><span class="sxs-lookup"><span data-stu-id="c6eea-622">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="c6eea-623">Další vlastnosti v části `Logging` zadejte zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-623">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="c6eea-624">Příklad je pro zprostředkovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-624">The example is for the Console provider.</span></span> <span data-ttu-id="c6eea-625">Pokud zprostředkovatel podporuje [obory protokolu](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="c6eea-625">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="c6eea-626">Vlastnost zprostředkovatele (například `Console` v příkladu) `LogLevel` může také určit vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c6eea-626">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="c6eea-627">`LogLevel`v rámci zprostředkovatele určuje úrovně pro protokolování pro tohoto zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-627">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="c6eea-628">Pokud jsou úrovně `Logging.{providername}.LogLevel`zadány v , `Logging.LogLevel`přepíší vše, co je nastaveno v .</span><span class="sxs-lookup"><span data-stu-id="c6eea-628">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="c6eea-629">Rozhraní API protokolování neobsahuje scénář pro změnu úrovní protokolu, když je spuštěná aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-629">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="c6eea-630">Někteří poskytovatelé konfigurace jsou však schopni znovu načíst konfiguraci, která má okamžitý vliv na konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-630">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="c6eea-631">Například [zprostředkovatel konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider), který `CreateDefaultBuilder` je přidán do čtení souborů nastavení, znovu načte konfiguraci protokolování ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="c6eea-631">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="c6eea-632">Pokud se konfigurace změní v kódu, když je aplikace spuštěná, aplikace může volat [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) a aktualizovat konfiguraci protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-632">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="c6eea-633">Informace o implementaci zprostředkovatelů <xref:fundamentals/configuration/index>konfigurace naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="c6eea-633">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="c6eea-634">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="c6eea-634">Sample logging output</span></span>

<span data-ttu-id="c6eea-635">S ukázkovým kódem zobrazeným v předchozí části se protokoly zobrazí v konzole při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-635">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="c6eea-636">Zde je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="c6eea-636">Here's an example of console output:</span></span>

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

<span data-ttu-id="c6eea-637">Předchozí protokoly byly generovány provedením požadavku HTTP Get `http://localhost:5000/api/todo/0`do ukázkové aplikace na adrese .</span><span class="sxs-lookup"><span data-stu-id="c6eea-637">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="c6eea-638">Tady je příklad stejných protokolů, které se zobrazují v okně Ladění při spuštění ukázkové aplikace v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c6eea-638">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="c6eea-639">Protokoly, které jsou `ILogger` vytvořeny volání zobrazené v předchozí části začínají "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="c6eea-639">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="c6eea-640">Protokoly, které začínají kategorie "Microsoft" jsou z ASP.NET kódu core framework.</span><span class="sxs-lookup"><span data-stu-id="c6eea-640">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="c6eea-641">ASP.NET Core a kód aplikace používají stejné protokolování ROZHRANÍ API a zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="c6eea-641">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="c6eea-642">Zbývající část tohoto článku vysvětluje některé podrobnosti a možnosti pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-642">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="c6eea-643">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="c6eea-643">NuGet packages</span></span>

<span data-ttu-id="c6eea-644">Rozhraní `ILogger` `ILoggerFactory` a jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="c6eea-644">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="c6eea-645">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-645">Log category</span></span>

<span data-ttu-id="c6eea-646">Při `ILogger` vytvoření objektu je pro něj *zadána kategorie.*</span><span class="sxs-lookup"><span data-stu-id="c6eea-646">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="c6eea-647">Tato kategorie je součástí každé zprávy protokolu `ILogger`vytvořené danou instancí .</span><span class="sxs-lookup"><span data-stu-id="c6eea-647">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="c6eea-648">Kategorie může být libovolný řetězec, ale konvence je použít název třídy, například "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="c6eea-648">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="c6eea-649">Slouží `ILogger<T>` k `ILogger` získání instance, která používá `T` plně kvalifikovaný název typu jako kategorie:</span><span class="sxs-lookup"><span data-stu-id="c6eea-649">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="c6eea-650">Chcete-li kategorii explicitně `ILoggerFactory.CreateLogger`určit, volejte :</span><span class="sxs-lookup"><span data-stu-id="c6eea-650">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="c6eea-651">`ILogger<T>`je ekvivalentní `CreateLogger` volání s plně kvalifikovaným typem názvu . `T`</span><span class="sxs-lookup"><span data-stu-id="c6eea-651">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="c6eea-652">Úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-652">Log level</span></span>

<span data-ttu-id="c6eea-653">Každý protokol určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-653">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="c6eea-654">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="c6eea-654">The log level indicates the severity or importance.</span></span> <span data-ttu-id="c6eea-655">Můžete například zapsat `Information` protokol, když metoda `Warning` končí normálně a protokol, když metoda vrátí stavový kód *404 Not Found.*</span><span class="sxs-lookup"><span data-stu-id="c6eea-655">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="c6eea-656">Následující kód `Information` vytvoří `Warning` a protokoly:</span><span class="sxs-lookup"><span data-stu-id="c6eea-656">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="c6eea-657">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="c6eea-657">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="c6eea-658">Druhým parametrem je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytnuté zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="c6eea-658">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="c6eea-659">Parametry metody jsou vysvětleny v [části šablony zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-659">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="c6eea-660">Metody protokolu, které zahrnují úroveň v názvu `LogInformation` `LogWarning`metody (například a ) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="c6eea-660">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="c6eea-661">Tyto metody `Log` volání metody, `LogLevel` která přebírá parametr.</span><span class="sxs-lookup"><span data-stu-id="c6eea-661">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="c6eea-662">Můžete volat `Log` metodu přímo, nikoli jednu z těchto metod rozšíření, ale syntaxe je poměrně složité.</span><span class="sxs-lookup"><span data-stu-id="c6eea-662">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="c6eea-663">Další informace naleznete <xref:Microsoft.Extensions.Logging.ILogger> v tématu a [rozšíření protokolů zdrojový kód](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="c6eea-663">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="c6eea-664">ASP.NET Core definuje následující úrovně protokolu, seřazené zde od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="c6eea-664">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="c6eea-665">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="c6eea-665">Trace = 0</span></span>

  <span data-ttu-id="c6eea-666">Informace, které je obvykle cenné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="c6eea-666">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="c6eea-667">Tyto zprávy mohou obsahovat citlivá data aplikace, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-667">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="c6eea-668">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="c6eea-668">*Disabled by default.*</span></span>

* <span data-ttu-id="c6eea-669">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="c6eea-669">Debug = 1</span></span>

  <span data-ttu-id="c6eea-670">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="c6eea-670">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="c6eea-671">Příklad: `Entering method Configure with flag set to true.` `Debug` Povolit protokoly úrovně v produkčním prostředí pouze při řešení potíží, z důvodu velkého objemu protokolů.</span><span class="sxs-lookup"><span data-stu-id="c6eea-671">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="c6eea-672">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="c6eea-672">Information = 2</span></span>

  <span data-ttu-id="c6eea-673">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-673">For tracking the general flow of the app.</span></span> <span data-ttu-id="c6eea-674">Tyto protokoly mají obvykle některé dlouhodobé hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c6eea-674">These logs typically have some long-term value.</span></span> <span data-ttu-id="c6eea-675">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="c6eea-675">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="c6eea-676">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="c6eea-676">Warning = 3</span></span>

  <span data-ttu-id="c6eea-677">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-677">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="c6eea-678">Mohou mezi ně patřit chyby nebo jiné podmínky, které nezpůsobují zastavení aplikace, ale může být nutné ji prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="c6eea-678">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="c6eea-679">Zpracované výjimky jsou běžné místo `Warning` pro použití na úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-679">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="c6eea-680">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="c6eea-680">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="c6eea-681">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="c6eea-681">Error = 4</span></span>

  <span data-ttu-id="c6eea-682">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="c6eea-682">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="c6eea-683">Tyto zprávy označují selhání v aktuální aktivity nebo operace (například aktuální požadavek HTTP), není selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-683">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="c6eea-684">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="c6eea-684">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="c6eea-685">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="c6eea-685">Critical = 5</span></span>

  <span data-ttu-id="c6eea-686">Pro poruchy, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="c6eea-686">For failures that require immediate attention.</span></span> <span data-ttu-id="c6eea-687">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-687">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="c6eea-688">Pomocí úrovně protokolu můžete určit, kolik výstupu protokolu je zapsáno do určitého paměťového média nebo okna zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c6eea-688">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="c6eea-689">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c6eea-689">For example:</span></span>

* <span data-ttu-id="c6eea-690">Ve výrobě:</span><span class="sxs-lookup"><span data-stu-id="c6eea-690">In production:</span></span>
  * <span data-ttu-id="c6eea-691">Protokolování `Trace` na `Information` úrovních pro vytváří velký objem podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-691">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="c6eea-692">Chcete-li řídit náklady a nepřekročit limity úložiště dat, zařazujte `Trace` zprávy `Information` na úrovni do úložiště dat s vysokým objemem a nízkými náklady.</span><span class="sxs-lookup"><span data-stu-id="c6eea-692">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="c6eea-693">Protokolování `Warning` `Critical` na úrovních obvykle vytváří méně, menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-693">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="c6eea-694">Proto náklady a limity úložiště obvykle nejsou problém, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="c6eea-694">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="c6eea-695">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="c6eea-695">During development:</span></span>
  * <span data-ttu-id="c6eea-696">`Warning` Protokolovat `Critical` prostřednictvím zpráv do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-696">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="c6eea-697">Při `Trace` `Information` potížích přidávejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="c6eea-697">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="c6eea-698">Oddíl [Filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovatel zpracovává.</span><span class="sxs-lookup"><span data-stu-id="c6eea-698">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="c6eea-699">ASP.NET Core zapisuje protokoly pro rámcové události.</span><span class="sxs-lookup"><span data-stu-id="c6eea-699">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="c6eea-700">Příklady protokolu dříve v tomto článku `Information` vyloučeny protokoly pod úrovní, takže byly vytvořeny žádné `Debug` protokoly nebo `Trace` úroveň.</span><span class="sxs-lookup"><span data-stu-id="c6eea-700">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="c6eea-701">Tady je příklad protokolů konzoly vytvořených spuštěním `Debug` ukázkové aplikace nakonfigurované tak, aby zobrazovala protokoly:</span><span class="sxs-lookup"><span data-stu-id="c6eea-701">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="c6eea-702">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-702">Log event ID</span></span>

<span data-ttu-id="c6eea-703">Každý protokol může určit *ID události*.</span><span class="sxs-lookup"><span data-stu-id="c6eea-703">Each log can specify an *event ID*.</span></span> <span data-ttu-id="c6eea-704">Ukázková aplikace to provádí pomocí `LoggingEvents` místně definované třídy:</span><span class="sxs-lookup"><span data-stu-id="c6eea-704">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="c6eea-705">ID události přidruží sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-705">An event ID associates a set of events.</span></span> <span data-ttu-id="c6eea-706">Všechny protokoly související se zobrazením seznamu položek na stránce mohou být například 1001.</span><span class="sxs-lookup"><span data-stu-id="c6eea-706">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="c6eea-707">Zprostředkovatel protokolování může uložit ID události v poli ID, ve zprávě protokolování nebo vůbec.</span><span class="sxs-lookup"><span data-stu-id="c6eea-707">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="c6eea-708">Poskytovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="c6eea-708">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="c6eea-709">Zprostředkovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="c6eea-709">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="c6eea-710">Šablona protokolu zpráv</span><span class="sxs-lookup"><span data-stu-id="c6eea-710">Log message template</span></span>

<span data-ttu-id="c6eea-711">Každý protokol určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="c6eea-711">Each log specifies a message template.</span></span> <span data-ttu-id="c6eea-712">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="c6eea-712">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="c6eea-713">Používejte názvy pro zástupné symboly, ne čísla.</span><span class="sxs-lookup"><span data-stu-id="c6eea-713">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="c6eea-714">Pořadí zástupných symbolů, nikoli jejich názvy, určuje, které parametry se používají k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="c6eea-714">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="c6eea-715">V následujícím kódu si všimněte, že názvy parametrů jsou mimo pořadí v šabloně zprávy:</span><span class="sxs-lookup"><span data-stu-id="c6eea-715">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="c6eea-716">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:</span><span class="sxs-lookup"><span data-stu-id="c6eea-716">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="c6eea-717">Architektura protokolování funguje tak, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="c6eea-717">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="c6eea-718">Samotné argumenty jsou předány systému protokolování, nikoli pouze formátované šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="c6eea-718">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="c6eea-719">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="c6eea-719">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="c6eea-720">Předpokládejme například, že volání metody logger vypadají takto:</span><span class="sxs-lookup"><span data-stu-id="c6eea-720">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="c6eea-721">Pokud odesíláte protokoly do Azure Table Storage, každá `ID` `RequestTime` entita tabulky Azure může mít a vlastnosti, což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-721">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="c6eea-722">Dotaz může najít všechny protokoly `RequestTime` v rámci určité oblasti bez analýzy doby mimo textovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-722">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="c6eea-723">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="c6eea-723">Logging exceptions</span></span>

<span data-ttu-id="c6eea-724">Metody protokolování mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-724">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="c6eea-725">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="c6eea-725">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="c6eea-726">Tady je příklad výstupu zprostředkovatele ladění z výše uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-726">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="c6eea-727">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="c6eea-727">Log filtering</span></span>

<span data-ttu-id="c6eea-728">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny zprostředkovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="c6eea-728">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="c6eea-729">Všechny protokoly pod minimální úroveň nejsou předány tomuto zprostředkovateli, takže se nezobrazí ani neuloží.</span><span class="sxs-lookup"><span data-stu-id="c6eea-729">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="c6eea-730">Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-730">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="c6eea-731">Celá hodnota `LogLevel.None` je 6, která je `LogLevel.Critical` vyšší než (5).</span><span class="sxs-lookup"><span data-stu-id="c6eea-731">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="c6eea-732">Vytvoření pravidel filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="c6eea-732">Create filter rules in configuration</span></span>

<span data-ttu-id="c6eea-733">Kód šablony projektu `CreateDefaultBuilder` volá k nastavení protokolování pro zprostředkovatele konzoly, ladění a zdroje událostí (ASP.NET Core 2.2 nebo novějším).</span><span class="sxs-lookup"><span data-stu-id="c6eea-733">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="c6eea-734">Metoda `CreateDefaultBuilder` nastaví protokolování hledat konfiguraci v `Logging` části, jak je vysvětleno dříve v tomto [článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="c6eea-734">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="c6eea-735">Konfigurační data určují minimální úrovně protokolu podle zprostředkovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-735">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="c6eea-736">Tento JSON vytvoří šest pravidel filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a jeden pro všechny zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-736">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="c6eea-737">Jedno pravidlo je vybráno pro `ILogger` každého zprostředkovatele při vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-737">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="c6eea-738">Filtrování pravidel v kódu</span><span class="sxs-lookup"><span data-stu-id="c6eea-738">Filter rules in code</span></span>

<span data-ttu-id="c6eea-739">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="c6eea-739">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="c6eea-740">Druhý `AddFilter` určuje zprostředkovatele ladění pomocí jeho název typu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-740">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="c6eea-741">První `AddFilter` platí pro všechny zprostředkovatele, protože neurčuje typ zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-741">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="c6eea-742">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="c6eea-742">How filtering rules are applied</span></span>

<span data-ttu-id="c6eea-743">Konfigurační `AddFilter` data a kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c6eea-743">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="c6eea-744">Prvních šest pocházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-744">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="c6eea-745">Číslo</span><span class="sxs-lookup"><span data-stu-id="c6eea-745">Number</span></span> | <span data-ttu-id="c6eea-746">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="c6eea-746">Provider</span></span>      | <span data-ttu-id="c6eea-747">Kategorie, které začínají ...</span><span class="sxs-lookup"><span data-stu-id="c6eea-747">Categories that begin with ...</span></span>          | <span data-ttu-id="c6eea-748">Minimální úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-748">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="c6eea-749">1</span><span class="sxs-lookup"><span data-stu-id="c6eea-749">1</span></span>      | <span data-ttu-id="c6eea-750">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-750">Debug</span></span>         | <span data-ttu-id="c6eea-751">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="c6eea-751">All categories</span></span>                          | <span data-ttu-id="c6eea-752">Informace</span><span class="sxs-lookup"><span data-stu-id="c6eea-752">Information</span></span>       |
| <span data-ttu-id="c6eea-753">2</span><span class="sxs-lookup"><span data-stu-id="c6eea-753">2</span></span>      | <span data-ttu-id="c6eea-754">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-754">Console</span></span>       | <span data-ttu-id="c6eea-755">Microsoft.AspNetCore.Mvc.Razor.Interní</span><span class="sxs-lookup"><span data-stu-id="c6eea-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="c6eea-756">Upozornění</span><span class="sxs-lookup"><span data-stu-id="c6eea-756">Warning</span></span>           |
| <span data-ttu-id="c6eea-757">3</span><span class="sxs-lookup"><span data-stu-id="c6eea-757">3</span></span>      | <span data-ttu-id="c6eea-758">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-758">Console</span></span>       | <span data-ttu-id="c6eea-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="c6eea-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="c6eea-760">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-760">Debug</span></span>             |
| <span data-ttu-id="c6eea-761">4</span><span class="sxs-lookup"><span data-stu-id="c6eea-761">4</span></span>      | <span data-ttu-id="c6eea-762">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-762">Console</span></span>       | <span data-ttu-id="c6eea-763">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="c6eea-763">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="c6eea-764">Chyba</span><span class="sxs-lookup"><span data-stu-id="c6eea-764">Error</span></span>             |
| <span data-ttu-id="c6eea-765">5</span><span class="sxs-lookup"><span data-stu-id="c6eea-765">5</span></span>      | <span data-ttu-id="c6eea-766">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-766">Console</span></span>       | <span data-ttu-id="c6eea-767">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="c6eea-767">All categories</span></span>                          | <span data-ttu-id="c6eea-768">Informace</span><span class="sxs-lookup"><span data-stu-id="c6eea-768">Information</span></span>       |
| <span data-ttu-id="c6eea-769">6</span><span class="sxs-lookup"><span data-stu-id="c6eea-769">6</span></span>      | <span data-ttu-id="c6eea-770">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="c6eea-770">All providers</span></span> | <span data-ttu-id="c6eea-771">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="c6eea-771">All categories</span></span>                          | <span data-ttu-id="c6eea-772">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-772">Debug</span></span>             |
| <span data-ttu-id="c6eea-773">7</span><span class="sxs-lookup"><span data-stu-id="c6eea-773">7</span></span>      | <span data-ttu-id="c6eea-774">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="c6eea-774">All providers</span></span> | <span data-ttu-id="c6eea-775">Systém</span><span class="sxs-lookup"><span data-stu-id="c6eea-775">System</span></span>                                  | <span data-ttu-id="c6eea-776">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-776">Debug</span></span>             |
| <span data-ttu-id="c6eea-777">8</span><span class="sxs-lookup"><span data-stu-id="c6eea-777">8</span></span>      | <span data-ttu-id="c6eea-778">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-778">Debug</span></span>         | <span data-ttu-id="c6eea-779">Microsoft</span><span class="sxs-lookup"><span data-stu-id="c6eea-779">Microsoft</span></span>                               | <span data-ttu-id="c6eea-780">Trasování</span><span class="sxs-lookup"><span data-stu-id="c6eea-780">Trace</span></span>             |

<span data-ttu-id="c6eea-781">Při `ILogger` vytvoření objektu, `ILoggerFactory` objekt vybere jedno pravidlo pro zprostředkovatele použít na tento protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-781">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="c6eea-782">Všechny zprávy napsané instancí `ILogger` jsou filtrovány na základě vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="c6eea-782">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="c6eea-783">Nejkonkrétnější pravidlo možné pro každého poskytovatele a dvojice kategorií je vybránz dostupných pravidel.</span><span class="sxs-lookup"><span data-stu-id="c6eea-783">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="c6eea-784">Následující algoritmus se používá pro `ILogger` každého zprostředkovatele při vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="c6eea-784">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="c6eea-785">Vyberte všechna pravidla, která odpovídají poskytovateli nebo jeho aliasu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-785">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="c6eea-786">Pokud není nalezena žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="c6eea-786">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="c6eea-787">Z výsledku předchozího kroku vyberte pravidla s nejdelší předponou kategorie shody.</span><span class="sxs-lookup"><span data-stu-id="c6eea-787">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="c6eea-788">Pokud není nalezena žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="c6eea-788">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="c6eea-789">Pokud je vybráno více pravidel, vezměte **poslední.**</span><span class="sxs-lookup"><span data-stu-id="c6eea-789">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="c6eea-790">Pokud nejsou vybrána `MinimumLevel`žádná pravidla, použijte .</span><span class="sxs-lookup"><span data-stu-id="c6eea-790">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="c6eea-791">S předchozím seznamem pravidel předpokládejme, `ILogger` že vytvoříte objekt pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="c6eea-791">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="c6eea-792">Pro zprostředkovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="c6eea-792">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="c6eea-793">Pravidlo 8 je nejkonkrétnější, takže to je ten, který byl vybrán.</span><span class="sxs-lookup"><span data-stu-id="c6eea-793">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="c6eea-794">Pro zprostředkovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="c6eea-794">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="c6eea-795">Pravidlo 3 je nejkonkrétnější.</span><span class="sxs-lookup"><span data-stu-id="c6eea-795">Rule 3 is most specific.</span></span>

<span data-ttu-id="c6eea-796">Výsledná `ILogger` instance odešle `Trace` protokoly úrovně a výše zprostředkovateli ladění.</span><span class="sxs-lookup"><span data-stu-id="c6eea-796">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="c6eea-797">Protokoly `Debug` úrovně a výše jsou odesílány zprostředkovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-797">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="c6eea-798">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="c6eea-798">Provider aliases</span></span>

<span data-ttu-id="c6eea-799">Každý zprostředkovatel definuje *alias,* který lze použít v konfiguraci namísto plně kvalifikovaný název typu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-799">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="c6eea-800">Pro předdefinované zprostředkovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="c6eea-800">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="c6eea-801">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-801">Console</span></span>
* <span data-ttu-id="c6eea-802">Ladit</span><span class="sxs-lookup"><span data-stu-id="c6eea-802">Debug</span></span>
* <span data-ttu-id="c6eea-803">EventSource</span><span class="sxs-lookup"><span data-stu-id="c6eea-803">EventSource</span></span>
* <span data-ttu-id="c6eea-804">Eventlog</span><span class="sxs-lookup"><span data-stu-id="c6eea-804">EventLog</span></span>
* <span data-ttu-id="c6eea-805">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c6eea-805">TraceSource</span></span>
* <span data-ttu-id="c6eea-806">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="c6eea-806">AzureAppServicesFile</span></span>
* <span data-ttu-id="c6eea-807">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="c6eea-807">AzureAppServicesBlob</span></span>
* <span data-ttu-id="c6eea-808">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="c6eea-808">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="c6eea-809">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="c6eea-809">Default minimum level</span></span>

<span data-ttu-id="c6eea-810">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že pro daného zprostředkovatele a kategorii neplatí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-810">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="c6eea-811">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="c6eea-811">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="c6eea-812">Pokud explicitně nenastavíte minimální úroveň, `Information`výchozí hodnota `Trace` je `Debug` , což znamená, že a protokoly jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="c6eea-812">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="c6eea-813">Funkce filtrování</span><span class="sxs-lookup"><span data-stu-id="c6eea-813">Filter functions</span></span>

<span data-ttu-id="c6eea-814">Funkce filtru je vyvolána pro všechny zprostředkovatele a kategorie, které nemají pravidla přiřazená podle konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-814">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="c6eea-815">Kód ve funkci má přístup k typu zprostředkovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-815">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="c6eea-816">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c6eea-816">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="c6eea-817">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="c6eea-817">System categories and levels</span></span>

<span data-ttu-id="c6eea-818">Zde jsou některé kategorie používané ASP.NET jádrem a jádrem entity s poznámkami o tom, jaké protokoly od nich lze očekávat:</span><span class="sxs-lookup"><span data-stu-id="c6eea-818">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="c6eea-819">Kategorie</span><span class="sxs-lookup"><span data-stu-id="c6eea-819">Category</span></span>                            | <span data-ttu-id="c6eea-820">Poznámky</span><span class="sxs-lookup"><span data-stu-id="c6eea-820">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="c6eea-821">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="c6eea-821">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="c6eea-822">Obecná diagnostika ASP.NET jádra.</span><span class="sxs-lookup"><span data-stu-id="c6eea-822">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="c6eea-823">Ochrana Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="c6eea-823">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="c6eea-824">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="c6eea-824">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="c6eea-825">Filtrování Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="c6eea-825">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="c6eea-826">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="c6eea-826">Hosts allowed.</span></span> |
| <span data-ttu-id="c6eea-827">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="c6eea-827">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="c6eea-828">Jak dlouho trvalo dokončení požadavků HTTP a v jakém čase byly spuštěny.</span><span class="sxs-lookup"><span data-stu-id="c6eea-828">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="c6eea-829">Která hostitelská spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="c6eea-829">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="c6eea-830">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="c6eea-830">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="c6eea-831">Diagnostika MVC a Razor.</span><span class="sxs-lookup"><span data-stu-id="c6eea-831">MVC and Razor diagnostics.</span></span> <span data-ttu-id="c6eea-832">Vazba modelu, spuštění filtru, kompilace zobrazení, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="c6eea-832">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="c6eea-833">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="c6eea-833">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="c6eea-834">Informace o porovnávání tras.</span><span class="sxs-lookup"><span data-stu-id="c6eea-834">Route matching information.</span></span> |
| <span data-ttu-id="c6eea-835">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="c6eea-835">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="c6eea-836">Připojení spustit, zastavit a udržet naživu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c6eea-836">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="c6eea-837">informace o certifikátu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c6eea-837">HTTPS certificate information.</span></span> |
| <span data-ttu-id="c6eea-838">Soubory Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="c6eea-838">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="c6eea-839">Doručené složky.</span><span class="sxs-lookup"><span data-stu-id="c6eea-839">Files served.</span></span> |
| <span data-ttu-id="c6eea-840">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="c6eea-840">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="c6eea-841">Diagnostika jádra obecnéentity frameworku.</span><span class="sxs-lookup"><span data-stu-id="c6eea-841">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="c6eea-842">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-842">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="c6eea-843">Obory protokolu</span><span class="sxs-lookup"><span data-stu-id="c6eea-843">Log scopes</span></span>

 <span data-ttu-id="c6eea-844">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="c6eea-844">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="c6eea-845">Toto seskupení lze použít k připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="c6eea-845">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="c6eea-846">Například každý protokol vytvořený jako součást zpracování transakce může obsahovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="c6eea-846">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="c6eea-847">Obor je `IDisposable` typ, který je <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> vrácen metodou a trvá, dokud je uvolněn.</span><span class="sxs-lookup"><span data-stu-id="c6eea-847">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="c6eea-848">Použití oboru zabalením volání `using` protokolování v bloku:</span><span class="sxs-lookup"><span data-stu-id="c6eea-848">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="c6eea-849">Následující kód umožňuje obory pro zprostředkovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="c6eea-849">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="c6eea-850">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c6eea-850">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="c6eea-851">Konfigurace možnosti `IncludeScopes` protokolování konzoly je nutná k povolení protokolování založeného na oboru.</span><span class="sxs-lookup"><span data-stu-id="c6eea-851">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="c6eea-852">Informace o konfiguraci naleznete v části [Konfigurace.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="c6eea-852">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="c6eea-853">Každá zpráva protokolu obsahuje informace s vymezenou oborem:</span><span class="sxs-lookup"><span data-stu-id="c6eea-853">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="c6eea-854">Vestavění poskytovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="c6eea-854">Built-in logging providers</span></span>

<span data-ttu-id="c6eea-855">ASP.NET Core dodává tyto poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="c6eea-855">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="c6eea-856">Konzola</span><span class="sxs-lookup"><span data-stu-id="c6eea-856">Console</span></span>](#console-provider)
* [<span data-ttu-id="c6eea-857">Ladění</span><span class="sxs-lookup"><span data-stu-id="c6eea-857">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="c6eea-858">Zdroj událostí</span><span class="sxs-lookup"><span data-stu-id="c6eea-858">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="c6eea-859">Eventlog</span><span class="sxs-lookup"><span data-stu-id="c6eea-859">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="c6eea-860">TraceSource</span><span class="sxs-lookup"><span data-stu-id="c6eea-860">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="c6eea-861">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="c6eea-861">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="c6eea-862">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="c6eea-862">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="c6eea-863">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="c6eea-863">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="c6eea-864">Informace o protokolování stdout a ladění pomocí <xref:test/troubleshoot-azure-iis> ASP.NET <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>core modulem naleznete v tématu a .</span><span class="sxs-lookup"><span data-stu-id="c6eea-864">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="c6eea-865">Zprostředkovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="c6eea-865">Console provider</span></span>

<span data-ttu-id="c6eea-866">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-866">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="c6eea-867">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c6eea-867">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="c6eea-868">Zprostředkovatel ladění</span><span class="sxs-lookup"><span data-stu-id="c6eea-868">Debug provider</span></span>

<span data-ttu-id="c6eea-869">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí`Debug.WriteLine` [třídy System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (volání metody).</span><span class="sxs-lookup"><span data-stu-id="c6eea-869">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="c6eea-870">V systému Linux tento zprostředkovatel zapisuje protokoly do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="c6eea-870">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="c6eea-871">Zprostředkovatel zdroje událostí</span><span class="sxs-lookup"><span data-stu-id="c6eea-871">Event Source provider</span></span>

<span data-ttu-id="c6eea-872">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na `Microsoft-Extensions-Logging`křížovou platformu Zdroje událostí s názvem .</span><span class="sxs-lookup"><span data-stu-id="c6eea-872">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="c6eea-873">V systému Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="c6eea-873">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="c6eea-874">Zprostředkovatel zdroje událostí je `CreateDefaultBuilder` přidán automaticky, když je volána k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-874">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="c6eea-875">Pomocí [nástroje PerfView](https://github.com/Microsoft/perfview) sbírat a zobrazovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="c6eea-875">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="c6eea-876">Existují další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi ETW vyzařované ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6eea-876">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="c6eea-877">Chcete-li nakonfigurovat PerfView pro shromažďování událostí `*Microsoft-Extensions-Logging` protokolovaných tímto zprostředkovatelem, přidejte řetězec do seznamu **Další zprostředkovatelé.**</span><span class="sxs-lookup"><span data-stu-id="c6eea-877">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="c6eea-878">(Nenechte si ujít hvězdičku na začátku řetězce.)</span><span class="sxs-lookup"><span data-stu-id="c6eea-878">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="c6eea-880">Zprostředkovatel služby Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="c6eea-880">Windows EventLog provider</span></span>

<span data-ttu-id="c6eea-881">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="c6eea-881">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="c6eea-882">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="c6eea-882">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="c6eea-883">Pokud `null` není zadáno, použije se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="c6eea-883">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="c6eea-884">`LogName`&ndash; "Aplikace"</span><span class="sxs-lookup"><span data-stu-id="c6eea-884">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="c6eea-885">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="c6eea-885">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="c6eea-886">`MachineName`&ndash; místní počítač</span><span class="sxs-lookup"><span data-stu-id="c6eea-886">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="c6eea-887">Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level).</span><span class="sxs-lookup"><span data-stu-id="c6eea-887">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="c6eea-888">Chcete-li protokolovat události nižší než `Warning`, explicitně nastavte úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-888">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="c6eea-889">Do souboru *appsettings.json* například přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="c6eea-889">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="c6eea-890">Zprostředkovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="c6eea-890">TraceSource provider</span></span>

<span data-ttu-id="c6eea-891">Balíček [zprostředkovatele Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-891">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="c6eea-892">[AddTraceSource přetížení](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="c6eea-893">Chcete-li použít tohoto zprostředkovatele, aplikace musí běžet na rozhraní .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="c6eea-893">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="c6eea-894">Zprostředkovatel může směrovat zprávy do různých [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použité v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c6eea-894">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="c6eea-895">Poskytovatel služeb Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c6eea-895">Azure App Service provider</span></span>

<span data-ttu-id="c6eea-896">Balíček zprostředkovatele [Microsoft.Extensions.Log.Log.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Služby Azure App Service a do [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="c6eea-896">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="c6eea-897">Balíček zprostředkovatele není součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c6eea-897">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="c6eea-898">Při cílení na rozhraní .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` metabalíček přidejte balíček zprostředkovatele do projektu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-898">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="c6eea-899">Přetížení <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> umožňuje projít v <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="c6eea-899">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="c6eea-900">Objekt nastavení může přepsat výchozí nastavení, například šablonu výstupu protokolování, název objektu blob a omezení velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="c6eea-900">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="c6eea-901">(Výstupní*šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici s voláním `ILogger` metody.)</span><span class="sxs-lookup"><span data-stu-id="c6eea-901">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="c6eea-902">Když se nasadíte do aplikace App Service, aplikace respektuje nastavení v části [protokoly služby App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stránce **Služby aplikací** na portálu Azure.</span><span class="sxs-lookup"><span data-stu-id="c6eea-902">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="c6eea-903">Při aktualizaci následujících nastavení se změny projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-903">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="c6eea-904">**Protokolování aplikací (souborový systém)**</span><span class="sxs-lookup"><span data-stu-id="c6eea-904">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="c6eea-905">**Protokolování aplikací (objekt blob)**</span><span class="sxs-lookup"><span data-stu-id="c6eea-905">**Application Logging (Blob)**</span></span>

<span data-ttu-id="c6eea-906">Výchozí umístění souborů protokolu je ve složce *D:\\\\home LogFiles\\Application* a výchozí název souboru je *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="c6eea-906">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="c6eea-907">Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="c6eea-907">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="c6eea-908">Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="c6eea-908">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="c6eea-909">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="c6eea-909">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="c6eea-910">Nemá žádný vliv, když je&mdash;projekt spuštěn místně, nezapisuje do místních souborů nebo místní vývojové úložiště pro objekty BLOB.</span><span class="sxs-lookup"><span data-stu-id="c6eea-910">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="c6eea-911">Streamování protokolu Azure</span><span class="sxs-lookup"><span data-stu-id="c6eea-911">Azure log streaming</span></span>

<span data-ttu-id="c6eea-912">Streamování protokolu Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="c6eea-912">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="c6eea-913">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="c6eea-913">The app server</span></span>
* <span data-ttu-id="c6eea-914">Webový server</span><span class="sxs-lookup"><span data-stu-id="c6eea-914">The web server</span></span>
* <span data-ttu-id="c6eea-915">Trasování neúspěšných požadavků</span><span class="sxs-lookup"><span data-stu-id="c6eea-915">Failed request tracing</span></span>

<span data-ttu-id="c6eea-916">Konfigurace streamování protokolu Azure:</span><span class="sxs-lookup"><span data-stu-id="c6eea-916">To configure Azure log streaming:</span></span>

* <span data-ttu-id="c6eea-917">Přejděte na stránku **protokolů služby App Service** na stránce portálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c6eea-917">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="c6eea-918">Nastavení **protokolování aplikací (souborový systém)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="c6eea-918">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="c6eea-919">Zvolte **úroveň**protokolu .</span><span class="sxs-lookup"><span data-stu-id="c6eea-919">Choose the log **Level**.</span></span> <span data-ttu-id="c6eea-920">Toto nastavení platí jenom pro streamování protokolu Azure, ne pro jiné poskytovatele protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c6eea-920">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="c6eea-921">Přejděte na stránku **Log Stream** a zobrazte zprávy aplikací.</span><span class="sxs-lookup"><span data-stu-id="c6eea-921">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="c6eea-922">Jsou zaznamenány aplikací prostřednictvím `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c6eea-922">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="c6eea-923">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="c6eea-923">Azure Application Insights trace logging</span></span>

<span data-ttu-id="c6eea-924">Balíček zprostředkovatele [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c6eea-924">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="c6eea-925">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu telemetrických dat.</span><span class="sxs-lookup"><span data-stu-id="c6eea-925">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="c6eea-926">Pokud používáte tohoto zprostředkovatele, můžete dotazovat a analyzovat protokoly pomocí nástrojů Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c6eea-926">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="c6eea-927">Zprostředkovatel protokolování je součástí jako závislost [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje všechny dostupné telemetrie pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6eea-927">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="c6eea-928">Pokud používáte tento balíček, není nutné instalovat balíček zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-928">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="c6eea-929">Nepoužívejte balíček&mdash; [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) pro ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="c6eea-929">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="c6eea-930">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="c6eea-930">For more information, see the following resources:</span></span>

* [<span data-ttu-id="c6eea-931">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="c6eea-931">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="c6eea-932">[Application Insights pro ASP.NET základní aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou škálu telemetrie Application Insights spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="c6eea-932">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="c6eea-933">[ApplicationInsightsLoggerProvider pro protokoly ILogger jádra .NET](/azure/azure-monitor/app/ilogger) – začněte zde, pokud chcete implementovat poskytovatele protokolování bez zbytku telemetrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="c6eea-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="c6eea-934">[Adaptéry pro protokolování Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="c6eea-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="c6eea-935">[Nainstalujte, nakonfigurujte a inicializujte kurz Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="c6eea-935">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="c6eea-936">Poskytovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="c6eea-936">Third-party logging providers</span></span>

<span data-ttu-id="c6eea-937">Architektury protokolování třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c6eea-937">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="c6eea-938">[elmah.io](https://elmah.io/) ([Úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6eea-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="c6eea-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="c6eea-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="c6eea-940">[JSNLog](https://jsnlog.com/) ([Úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="c6eea-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="c6eea-941">[KissLog.net](https://kisslog.net/) ([Úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="c6eea-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="c6eea-942">[Log4Net](https://logging.apache.org/log4net/) ([Úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="c6eea-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="c6eea-943">[Loggr](https://loggr.net/) ([Úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6eea-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="c6eea-944">[NLog](https://nlog-project.org/) ([Úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="c6eea-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="c6eea-945">[Sentry](https://sentry.io/welcome/) ([Úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="c6eea-945">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="c6eea-946">[Serilog](https://serilog.net/) ([Úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="c6eea-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="c6eea-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Úložiště Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="c6eea-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="c6eea-948">Některé architektury jiných výrobců můžete provádět [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="c6eea-948">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="c6eea-949">Použití rozhraní třetí strany je podobné použití jednoho z předdefinovaných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="c6eea-949">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="c6eea-950">Přidejte balíček NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="c6eea-950">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="c6eea-951">Volání `ILoggerFactory` metody rozšíření poskytované rozhraní protokolování.</span><span class="sxs-lookup"><span data-stu-id="c6eea-951">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="c6eea-952">Další informace naleznete v dokumentaci každého zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c6eea-952">For more information, see each provider's documentation.</span></span> <span data-ttu-id="c6eea-953">Společnost Microsoft nepodporuje poskytovatele protokolování jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="c6eea-953">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c6eea-954">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c6eea-954">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end

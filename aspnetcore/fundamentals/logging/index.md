---
title: Přihlášení do jádra rozhraní .NET core a ASP.NET core
author: rick-anderson
description: Zjistěte, jak používat architekturu protokolování poskytovanou balíčkem Microsoft.Extensions.Logging NuGet.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 58e236ad7f0863b87907d5585e1cb6bf61d46e99
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663301"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="41952-103">Přihlášení do jádra rozhraní .NET core a ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="41952-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="41952-104">Tom [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="41952-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="41952-105">Rozhraní .NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="41952-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="41952-106">Tento článek ukazuje, jak používat rozhraní API protokolování s integrovanými zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="41952-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41952-107">Většina příkladů kódu uvedených v tomto článku pochází z ASP.NET základních aplikací.</span><span class="sxs-lookup"><span data-stu-id="41952-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="41952-108">Části těchto fragmentů kódu specifické pro protokolování platí pro všechny aplikace .NET Core, která používá [obecný hostitel](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="41952-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="41952-109">Příklad použití obecného hostitele v aplikaci, která není webovou konzolou, najdete<xref:fundamentals/host/hosted-services>v *Program.cs* soubor [ukázkové aplikace Úlohy na pozadí](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( ).</span><span class="sxs-lookup"><span data-stu-id="41952-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="41952-110">Protokolování kódu pro aplikace bez obecného hostitele se liší ve způsobu, jakým [jsou přidány zprostředkovatele](#add-providers) a [jsou vytvořeny úhozy kláves](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="41952-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="41952-111">Příklady kódu bez hostitele jsou uvedeny v těchto částech článku.</span><span class="sxs-lookup"><span data-stu-id="41952-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="41952-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="41952-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="41952-113">Přidání zprostředkovatelů</span><span class="sxs-lookup"><span data-stu-id="41952-113">Add providers</span></span>

<span data-ttu-id="41952-114">Zprostředkovatel protokolování zobrazí nebo uloží protokoly.</span><span class="sxs-lookup"><span data-stu-id="41952-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="41952-115">Například zprostředkovatel konzoly zobrazí protokoly na konzoli a poskytovatel Azure Application Insights je uloží do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="41952-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="41952-116">Protokoly lze odeslat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="41952-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41952-117">Pokud chcete přidat zprostředkovatele do aplikace, která používá `Add{provider name}` obecný hostitel, zavolejte metodu rozšíření poskytovatele v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="41952-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="41952-118">V aplikaci konzoly bez hostitele volejte `Add{provider name}` metodu rozšíření `LoggerFactory`zprostředkovatele při vytváření :</span><span class="sxs-lookup"><span data-stu-id="41952-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="41952-119">`LoggerFactory`a `AddConsole` vyžadují `using` prohlášení `Microsoft.Extensions.Logging`pro .</span><span class="sxs-lookup"><span data-stu-id="41952-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="41952-120">Výchozí ASP.NET volání výchozích <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>šablon projektu jádra , které přidá následující poskytovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="41952-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="41952-121">Konzola</span><span class="sxs-lookup"><span data-stu-id="41952-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="41952-122">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="41952-123">Zdroj událostí</span><span class="sxs-lookup"><span data-stu-id="41952-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="41952-124">[EventLog](#windows-eventlog-provider) (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="41952-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="41952-125">Výchozí zprostředkovatele můžete nahradit vlastními volbami.</span><span class="sxs-lookup"><span data-stu-id="41952-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="41952-126">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte požadované zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="41952-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="41952-127">Chcete-li přidat zprostředkovatele, `Add{provider name}` volejte metodu rozšíření zprostředkovatele v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="41952-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="41952-128">Předchozí kód vyžaduje odkazy `Microsoft.Extensions.Logging` na `Microsoft.Extensions.Configuration`a .</span><span class="sxs-lookup"><span data-stu-id="41952-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="41952-129">Výchozí šablona <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>projektu volá , která přidá následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="41952-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="41952-130">Konzola</span><span class="sxs-lookup"><span data-stu-id="41952-130">Console</span></span>
* <span data-ttu-id="41952-131">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-131">Debug</span></span>
* <span data-ttu-id="41952-132">EventSource (počínaje ASP.NET jádrem 2.2)</span><span class="sxs-lookup"><span data-stu-id="41952-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="41952-133">Pokud používáte `CreateDefaultBuilder`, můžete nahradit výchozí zprostředkovatele vlastními volbami.</span><span class="sxs-lookup"><span data-stu-id="41952-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="41952-134">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte požadované zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="41952-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="41952-135">Další informace o [předdefinovaných zprostředkovatelích protokolování](#built-in-logging-providers) a [poskytovatelích protokolování třetích stran](#third-party-logging-providers) dále v článku.</span><span class="sxs-lookup"><span data-stu-id="41952-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="41952-136">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="41952-136">Create logs</span></span>

<span data-ttu-id="41952-137">Chcete-li vytvořit protokoly, použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="41952-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="41952-138">Ve webové aplikaci nebo hostované `ILogger` službě získáte z vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="41952-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="41952-139">V aplikacích konzoly, `LoggerFactory` které nejsou `ILogger`hostitelskou konzolou, použijte k vytvoření aplikace .</span><span class="sxs-lookup"><span data-stu-id="41952-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="41952-140">Následující příklad ASP.NET Core vytvoří `TodoApiSample.Pages.AboutModel` protokolovací nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="41952-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="41952-141">*Kategorie* protokolu je řetězec, který je přidružen ke každému protokolu.</span><span class="sxs-lookup"><span data-stu-id="41952-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="41952-142">Instance `ILogger<T>` poskytované DI vytvoří protokoly, které mají plně `T` kvalifikovaný název typu jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="41952-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="41952-143">Následující příklad aplikace konzoly bez hostitele `LoggingConsoleApp.Program` vytvoří protokolovací nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="41952-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="41952-144">V následujících příkladech ASP.NET core a konzolové aplikace, `Information` protokolování se používá k vytvoření protokolů s jako úroveň.</span><span class="sxs-lookup"><span data-stu-id="41952-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="41952-145">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="41952-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="41952-146">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="41952-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="41952-147">Vytvoření protokolů ve třídě Program</span><span class="sxs-lookup"><span data-stu-id="41952-147">Create logs in the Program class</span></span>

<span data-ttu-id="41952-148">Chcete-li zapsat `Program` protokoly ve třídě aplikace `ILogger` ASP.NET Core, získejte instanci z DI po vytvoření hostitele:</span><span class="sxs-lookup"><span data-stu-id="41952-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="41952-149">Protokolování během výstavby hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="41952-149">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="41952-150">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="41952-150">However, a separate logger can be used.</span></span> <span data-ttu-id="41952-151">V následujícím příkladu se protokolovací nástroj [Serilog](https://serilog.net/) používá k přihlášení `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="41952-151">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="41952-152">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="41952-152">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="41952-153">Vytvoření protokolů ve třídě Startup</span><span class="sxs-lookup"><span data-stu-id="41952-153">Create logs in the Startup class</span></span>

<span data-ttu-id="41952-154">Chcete-li zapsat `Startup.Configure` protokoly v metodě `ILogger` aplikace ASP.NET Core, zahrňte parametr do podpisu metody:</span><span class="sxs-lookup"><span data-stu-id="41952-154">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="41952-155">Zápis protokolů před dokončením nastavení kontejneru DI v metodě `Startup.ConfigureServices` není podporován:</span><span class="sxs-lookup"><span data-stu-id="41952-155">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="41952-156">Vkládání protokolů `Startup` do konstruktoru není podporováno.</span><span class="sxs-lookup"><span data-stu-id="41952-156">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="41952-157">Vkládání protokolů `Startup.ConfigureServices` do podpisu metody není podporováno.</span><span class="sxs-lookup"><span data-stu-id="41952-157">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="41952-158">Důvodem pro toto omezení je, že protokolování závisí na DI a na konfiguraci, která zase závisí na DI.</span><span class="sxs-lookup"><span data-stu-id="41952-158">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="41952-159">Kontejner DI není nastaven, `ConfigureServices` dokud nebude dokončena.</span><span class="sxs-lookup"><span data-stu-id="41952-159">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="41952-160">Konstruktor vkládání úhozů do `Startup` funguje v dřívějších verzích ASP.NET Core, protože samostatný kontejner DI je vytvořen pro web hostitele.</span><span class="sxs-lookup"><span data-stu-id="41952-160">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="41952-161">Informace o tom, proč je pro obecný hostitel vytvořen pouze jeden kontejner, naleznete v [oznámení o změně rozdělení](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="41952-161">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="41952-162">Pokud potřebujete nakonfigurovat službu, `ILogger<T>`která závisí na , můžete to stále provést pomocí vkládání konstruktoru nebo poskytnutím metody výroby.</span><span class="sxs-lookup"><span data-stu-id="41952-162">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="41952-163">Přístup metody výroby se doporučuje pouze v případě, že neexistuje žádná jiná možnost.</span><span class="sxs-lookup"><span data-stu-id="41952-163">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="41952-164">Předpokládejme například, že potřebujete vyplnit vlastnost službou z DI:</span><span class="sxs-lookup"><span data-stu-id="41952-164">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="41952-165">Předchozí zvýrazněný kód `Func` je, který běží poprvé kontejner UD potřebuje `MyService`k vytvoření instance .</span><span class="sxs-lookup"><span data-stu-id="41952-165">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="41952-166">Tímto způsobem můžete přistupovat k libovolné registrované službě.</span><span class="sxs-lookup"><span data-stu-id="41952-166">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="41952-167">Vytvoření protokolů při spuštění</span><span class="sxs-lookup"><span data-stu-id="41952-167">Create logs in Startup</span></span>

<span data-ttu-id="41952-168">Chcete-li zapsat `Startup` protokoly `ILogger` do třídy, zahrňte parametr do podpisu konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="41952-168">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="41952-169">Vytvoření protokolů ve třídě Program</span><span class="sxs-lookup"><span data-stu-id="41952-169">Create logs in the Program class</span></span>

<span data-ttu-id="41952-170">Chcete-li zapsat `Program` protokoly `ILogger` ve třídě, získejte instanci z DI:</span><span class="sxs-lookup"><span data-stu-id="41952-170">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="41952-171">Protokolování během výstavby hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="41952-171">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="41952-172">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="41952-172">However, a separate logger can be used.</span></span> <span data-ttu-id="41952-173">V následujícím příkladu se protokolovací nástroj [Serilog](https://serilog.net/) používá k přihlášení `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="41952-173">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="41952-174">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="41952-174">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="41952-175">Žádné asynchronní metody protokolování</span><span class="sxs-lookup"><span data-stu-id="41952-175">No asynchronous logger methods</span></span>

<span data-ttu-id="41952-176">Protokolování by mělo být tak rychlé, že nestojí za náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="41952-176">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="41952-177">Pokud je úložiště dat protokolování pomalé, nezapisujte do něj přímo.</span><span class="sxs-lookup"><span data-stu-id="41952-177">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="41952-178">Zvažte zápis zpráv protokolu do rychlého úložiště zpočátku a potom je přesunout do pomalé úložiště později.</span><span class="sxs-lookup"><span data-stu-id="41952-178">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="41952-179">Například pokud se přihlašujete k serveru SQL Server, `Log` nechcete `Log` to provést přímo v metodě, protože metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="41952-179">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="41952-180">Místo toho synchronně přidat zprávy protokolu do fronty v paměti a mají pracovník na pozadí vytáhnout zprávy z fronty provést asynchronní práci odesílání dat na SQL Server.</span><span class="sxs-lookup"><span data-stu-id="41952-180">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="41952-181">Další informace naleznete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHub.</span><span class="sxs-lookup"><span data-stu-id="41952-181">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="41952-182">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="41952-182">Configuration</span></span>

<span data-ttu-id="41952-183">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="41952-183">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="41952-184">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="41952-184">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="41952-185">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="41952-185">Command-line arguments.</span></span>
* <span data-ttu-id="41952-186">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="41952-186">Environment variables.</span></span>
* <span data-ttu-id="41952-187">Objekty .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="41952-187">In-memory .NET objects.</span></span>
* <span data-ttu-id="41952-188">Nešifrované úložiště [Správce tajných](xref:security/app-secrets) služeb.</span><span class="sxs-lookup"><span data-stu-id="41952-188">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="41952-189">Šifrované úložiště uživatelů, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="41952-189">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="41952-190">Vlastní zprostředkovatelé (nainstalované nebo vytvořené).</span><span class="sxs-lookup"><span data-stu-id="41952-190">Custom providers (installed or created).</span></span>

<span data-ttu-id="41952-191">Například konfigurace protokolování je běžně `Logging` poskytována v části souborů nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-191">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="41952-192">Následující příklad ukazuje obsah typické *nastavení aplikace. Development.json* soubor:</span><span class="sxs-lookup"><span data-stu-id="41952-192">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="41952-193">Vlastnost `Logging` může `LogLevel` mít a protokolovat vlastnosti zprostředkovatele (je zobrazena konzola).</span><span class="sxs-lookup"><span data-stu-id="41952-193">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="41952-194">Vlastnost `LogLevel` v `Logging` části určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="41952-194">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="41952-195">V příkladu `System` `Microsoft` a kategorie `Information` protokolu na úrovni `Debug` a všechny ostatní protokolu na úrovni.</span><span class="sxs-lookup"><span data-stu-id="41952-195">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="41952-196">Další vlastnosti v části `Logging` zadejte zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="41952-196">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="41952-197">Příklad je pro zprostředkovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="41952-197">The example is for the Console provider.</span></span> <span data-ttu-id="41952-198">Pokud zprostředkovatel podporuje [obory protokolu](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="41952-198">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="41952-199">Vlastnost zprostředkovatele (například `Console` v příkladu) `LogLevel` může také určit vlastnost.</span><span class="sxs-lookup"><span data-stu-id="41952-199">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="41952-200">`LogLevel`v rámci zprostředkovatele určuje úrovně pro protokolování pro tohoto zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="41952-200">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="41952-201">Pokud jsou úrovně `Logging.{providername}.LogLevel`zadány v , `Logging.LogLevel`přepíší vše, co je nastaveno v .</span><span class="sxs-lookup"><span data-stu-id="41952-201">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="41952-202">Rozhraní API protokolování neobsahuje scénář pro změnu úrovní protokolu, když je spuštěná aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-202">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="41952-203">Někteří poskytovatelé konfigurace jsou však schopni znovu načíst konfiguraci, která má okamžitý vliv na konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="41952-203">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="41952-204">Například [zprostředkovatel konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider), který `CreateDefaultBuilder` je přidán do čtení souborů nastavení, znovu načte konfiguraci protokolování ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="41952-204">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="41952-205">Pokud se konfigurace změní v kódu, když je aplikace spuštěná, aplikace může volat [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) a aktualizovat konfiguraci protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-205">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="41952-206">Informace o implementaci zprostředkovatelů <xref:fundamentals/configuration/index>konfigurace naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="41952-206">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="41952-207">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="41952-207">Sample logging output</span></span>

<span data-ttu-id="41952-208">S ukázkovým kódem zobrazeným v předchozí části se protokoly zobrazí v konzole při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="41952-208">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="41952-209">Zde je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="41952-209">Here's an example of console output:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

<span data-ttu-id="41952-210">Předchozí protokoly byly generovány provedením požadavku HTTP Get `http://localhost:5000/api/todo/0`do ukázkové aplikace na adrese .</span><span class="sxs-lookup"><span data-stu-id="41952-210">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="41952-211">Tady je příklad stejných protokolů, které se zobrazují v okně Ladění při spuštění ukázkové aplikace v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="41952-211">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

<span data-ttu-id="41952-212">Protokoly, které jsou `ILogger` vytvořeny volání zobrazené v předchozí části začínají "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="41952-212">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="41952-213">Protokoly, které začínají kategorie "Microsoft" jsou z ASP.NET kódu core framework.</span><span class="sxs-lookup"><span data-stu-id="41952-213">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="41952-214">ASP.NET Core a kód aplikace používají stejné protokolování ROZHRANÍ API a zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="41952-214">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="41952-215">Protokoly, které jsou `ILogger` vytvořeny volání zobrazené v předchozí části začínají "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="41952-215">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="41952-216">Protokoly, které začínají kategorie "Microsoft" jsou z ASP.NET kódu core framework.</span><span class="sxs-lookup"><span data-stu-id="41952-216">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="41952-217">ASP.NET Core a kód aplikace používají stejné protokolování ROZHRANÍ API a zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="41952-217">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="41952-218">Zbývající část tohoto článku vysvětluje některé podrobnosti a možnosti pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="41952-218">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="41952-219">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="41952-219">NuGet packages</span></span>

<span data-ttu-id="41952-220">Rozhraní `ILogger` `ILoggerFactory` a jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="41952-220">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="41952-221">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="41952-221">Log category</span></span>

<span data-ttu-id="41952-222">Při `ILogger` vytvoření objektu je pro něj *zadána kategorie.*</span><span class="sxs-lookup"><span data-stu-id="41952-222">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="41952-223">Tato kategorie je součástí každé zprávy protokolu `ILogger`vytvořené danou instancí .</span><span class="sxs-lookup"><span data-stu-id="41952-223">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="41952-224">Kategorie může být libovolný řetězec, ale konvence je použít název třídy, například "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="41952-224">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="41952-225">Slouží `ILogger<T>` k `ILogger` získání instance, která používá `T` plně kvalifikovaný název typu jako kategorie:</span><span class="sxs-lookup"><span data-stu-id="41952-225">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="41952-226">Chcete-li kategorii explicitně `ILoggerFactory.CreateLogger`určit, volejte :</span><span class="sxs-lookup"><span data-stu-id="41952-226">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="41952-227">`ILogger<T>`je ekvivalentní `CreateLogger` volání s plně kvalifikovaným typem názvu . `T`</span><span class="sxs-lookup"><span data-stu-id="41952-227">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="41952-228">Úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="41952-228">Log level</span></span>

<span data-ttu-id="41952-229">Každý protokol určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="41952-229">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="41952-230">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="41952-230">The log level indicates the severity or importance.</span></span> <span data-ttu-id="41952-231">Můžete například zapsat `Information` protokol, když metoda `Warning` končí normálně a protokol, když metoda vrátí stavový kód *404 Not Found.*</span><span class="sxs-lookup"><span data-stu-id="41952-231">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="41952-232">Následující kód `Information` vytvoří `Warning` a protokoly:</span><span class="sxs-lookup"><span data-stu-id="41952-232">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="41952-233">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="41952-233">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="41952-234">Druhým parametrem je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytnuté zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="41952-234">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="41952-235">Parametry metody jsou vysvětleny v [části šablony zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="41952-235">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="41952-236">Metody protokolu, které zahrnují úroveň v názvu `LogInformation` `LogWarning`metody (například a ) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="41952-236">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="41952-237">Tyto metody `Log` volání metody, `LogLevel` která přebírá parametr.</span><span class="sxs-lookup"><span data-stu-id="41952-237">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="41952-238">Můžete volat `Log` metodu přímo, nikoli jednu z těchto metod rozšíření, ale syntaxe je poměrně složité.</span><span class="sxs-lookup"><span data-stu-id="41952-238">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="41952-239">Další informace naleznete <xref:Microsoft.Extensions.Logging.ILogger> v tématu a [rozšíření protokolů zdrojový kód](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="41952-239">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="41952-240">ASP.NET Core definuje následující úrovně protokolu, seřazené zde od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="41952-240">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="41952-241">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="41952-241">Trace = 0</span></span>

  <span data-ttu-id="41952-242">Informace, které je obvykle cenné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="41952-242">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="41952-243">Tyto zprávy mohou obsahovat citlivá data aplikace, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="41952-243">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="41952-244">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="41952-244">*Disabled by default.*</span></span>

* <span data-ttu-id="41952-245">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="41952-245">Debug = 1</span></span>

  <span data-ttu-id="41952-246">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="41952-246">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="41952-247">Příklad: `Entering method Configure with flag set to true.` `Debug` Povolit protokoly úrovně v produkčním prostředí pouze při řešení potíží, z důvodu velkého objemu protokolů.</span><span class="sxs-lookup"><span data-stu-id="41952-247">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="41952-248">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="41952-248">Information = 2</span></span>

  <span data-ttu-id="41952-249">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-249">For tracking the general flow of the app.</span></span> <span data-ttu-id="41952-250">Tyto protokoly mají obvykle některé dlouhodobé hodnoty.</span><span class="sxs-lookup"><span data-stu-id="41952-250">These logs typically have some long-term value.</span></span> <span data-ttu-id="41952-251">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="41952-251">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="41952-252">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="41952-252">Warning = 3</span></span>

  <span data-ttu-id="41952-253">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-253">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="41952-254">Mohou mezi ně patřit chyby nebo jiné podmínky, které nezpůsobují zastavení aplikace, ale může být nutné ji prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="41952-254">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="41952-255">Zpracované výjimky jsou běžné místo `Warning` pro použití na úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="41952-255">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="41952-256">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="41952-256">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="41952-257">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="41952-257">Error = 4</span></span>

  <span data-ttu-id="41952-258">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="41952-258">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="41952-259">Tyto zprávy označují selhání v aktuální aktivity nebo operace (například aktuální požadavek HTTP), není selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-259">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="41952-260">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="41952-260">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="41952-261">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="41952-261">Critical = 5</span></span>

  <span data-ttu-id="41952-262">Pro poruchy, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="41952-262">For failures that require immediate attention.</span></span> <span data-ttu-id="41952-263">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="41952-263">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="41952-264">Pomocí úrovně protokolu můžete určit, kolik výstupu protokolu je zapsáno do určitého paměťového média nebo okna zobrazení.</span><span class="sxs-lookup"><span data-stu-id="41952-264">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="41952-265">Příklad:</span><span class="sxs-lookup"><span data-stu-id="41952-265">For example:</span></span>

* <span data-ttu-id="41952-266">Ve výrobě:</span><span class="sxs-lookup"><span data-stu-id="41952-266">In production:</span></span>
  * <span data-ttu-id="41952-267">Protokolování `Trace` na `Information` úrovních pro vytváří velký objem podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="41952-267">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="41952-268">Chcete-li řídit náklady a nepřekročit limity úložiště dat, zařazujte `Trace` zprávy `Information` na úrovni do úložiště dat s vysokým objemem a nízkými náklady.</span><span class="sxs-lookup"><span data-stu-id="41952-268">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="41952-269">Protokolování `Warning` `Critical` na úrovních obvykle vytváří méně, menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="41952-269">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="41952-270">Proto náklady a limity úložiště obvykle nejsou problém, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="41952-270">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="41952-271">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="41952-271">During development:</span></span>
  * <span data-ttu-id="41952-272">`Warning` Protokolovat `Critical` prostřednictvím zpráv do konzoly.</span><span class="sxs-lookup"><span data-stu-id="41952-272">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="41952-273">Při `Trace` `Information` potížích přidávejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="41952-273">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="41952-274">Oddíl [Filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovatel zpracovává.</span><span class="sxs-lookup"><span data-stu-id="41952-274">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="41952-275">ASP.NET Core zapisuje protokoly pro rámcové události.</span><span class="sxs-lookup"><span data-stu-id="41952-275">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="41952-276">Příklady protokolu dříve v tomto článku `Information` vyloučeny protokoly pod úrovní, takže byly vytvořeny žádné `Debug` protokoly nebo `Trace` úroveň.</span><span class="sxs-lookup"><span data-stu-id="41952-276">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="41952-277">Tady je příklad protokolů konzoly vytvořených spuštěním `Debug` ukázkové aplikace nakonfigurované tak, aby zobrazovala protokoly:</span><span class="sxs-lookup"><span data-stu-id="41952-277">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

## <a name="log-event-id"></a><span data-ttu-id="41952-278">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="41952-278">Log event ID</span></span>

<span data-ttu-id="41952-279">Každý protokol může určit *ID události*.</span><span class="sxs-lookup"><span data-stu-id="41952-279">Each log can specify an *event ID*.</span></span> <span data-ttu-id="41952-280">Ukázková aplikace to provádí pomocí `LoggingEvents` místně definované třídy:</span><span class="sxs-lookup"><span data-stu-id="41952-280">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="41952-281">ID události přidruží sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="41952-281">An event ID associates a set of events.</span></span> <span data-ttu-id="41952-282">Všechny protokoly související se zobrazením seznamu položek na stránce mohou být například 1001.</span><span class="sxs-lookup"><span data-stu-id="41952-282">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="41952-283">Zprostředkovatel protokolování může uložit ID události v poli ID, ve zprávě protokolování nebo vůbec.</span><span class="sxs-lookup"><span data-stu-id="41952-283">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="41952-284">Poskytovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="41952-284">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="41952-285">Zprostředkovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="41952-285">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="41952-286">Šablona protokolu zpráv</span><span class="sxs-lookup"><span data-stu-id="41952-286">Log message template</span></span>

<span data-ttu-id="41952-287">Každý protokol určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="41952-287">Each log specifies a message template.</span></span> <span data-ttu-id="41952-288">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="41952-288">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="41952-289">Používejte názvy pro zástupné symboly, ne čísla.</span><span class="sxs-lookup"><span data-stu-id="41952-289">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="41952-290">Pořadí zástupných symbolů, nikoli jejich názvy, určuje, které parametry se používají k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="41952-290">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="41952-291">V následujícím kódu si všimněte, že názvy parametrů jsou mimo pořadí v šabloně zprávy:</span><span class="sxs-lookup"><span data-stu-id="41952-291">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="41952-292">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:</span><span class="sxs-lookup"><span data-stu-id="41952-292">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="41952-293">Architektura protokolování funguje tak, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="41952-293">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="41952-294">Samotné argumenty jsou předány systému protokolování, nikoli pouze formátované šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="41952-294">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="41952-295">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="41952-295">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="41952-296">Předpokládejme například, že volání metody logger vypadají takto:</span><span class="sxs-lookup"><span data-stu-id="41952-296">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="41952-297">Pokud odesíláte protokoly do Azure Table Storage, každá `ID` `RequestTime` entita tabulky Azure může mít a vlastnosti, což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="41952-297">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="41952-298">Dotaz může najít všechny protokoly `RequestTime` v rámci určité oblasti bez analýzy doby mimo textovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="41952-298">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="41952-299">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="41952-299">Logging exceptions</span></span>

<span data-ttu-id="41952-300">Metody protokolování mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="41952-300">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="41952-301">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="41952-301">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="41952-302">Tady je příklad výstupu zprostředkovatele ladění z výše uvedeného kódu.</span><span class="sxs-lookup"><span data-stu-id="41952-302">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="41952-303">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="41952-303">Log filtering</span></span>

<span data-ttu-id="41952-304">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny zprostředkovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="41952-304">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="41952-305">Všechny protokoly pod minimální úroveň nejsou předány tomuto zprostředkovateli, takže se nezobrazí ani neuloží.</span><span class="sxs-lookup"><span data-stu-id="41952-305">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="41952-306">Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="41952-306">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="41952-307">Celá hodnota `LogLevel.None` je 6, která je `LogLevel.Critical` vyšší než (5).</span><span class="sxs-lookup"><span data-stu-id="41952-307">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="41952-308">Vytvoření pravidel filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="41952-308">Create filter rules in configuration</span></span>

<span data-ttu-id="41952-309">Kód šablony projektu `CreateDefaultBuilder` volá k nastavení protokolování pro zprostředkovatele konzoly, ladění a zdroje událostí (ASP.NET Core 2.2 nebo novějším).</span><span class="sxs-lookup"><span data-stu-id="41952-309">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="41952-310">Metoda `CreateDefaultBuilder` nastaví protokolování hledat konfiguraci v `Logging` části, jak je vysvětleno dříve v tomto [článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="41952-310">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="41952-311">Konfigurační data určují minimální úrovně protokolu podle zprostředkovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="41952-311">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="41952-312">Tento JSON vytvoří šest pravidel filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a jeden pro všechny zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="41952-312">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="41952-313">Jedno pravidlo je vybráno pro `ILogger` každého zprostředkovatele při vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="41952-313">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="41952-314">Filtrování pravidel v kódu</span><span class="sxs-lookup"><span data-stu-id="41952-314">Filter rules in code</span></span>

<span data-ttu-id="41952-315">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="41952-315">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="41952-316">Druhý `AddFilter` určuje zprostředkovatele ladění pomocí jeho název typu.</span><span class="sxs-lookup"><span data-stu-id="41952-316">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="41952-317">První `AddFilter` platí pro všechny zprostředkovatele, protože neurčuje typ zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="41952-317">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="41952-318">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="41952-318">How filtering rules are applied</span></span>

<span data-ttu-id="41952-319">Konfigurační `AddFilter` data a kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="41952-319">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="41952-320">Prvních šest pocházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="41952-320">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="41952-321">Číslo</span><span class="sxs-lookup"><span data-stu-id="41952-321">Number</span></span> | <span data-ttu-id="41952-322">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="41952-322">Provider</span></span>      | <span data-ttu-id="41952-323">Kategorie, které začínají ...</span><span class="sxs-lookup"><span data-stu-id="41952-323">Categories that begin with ...</span></span>          | <span data-ttu-id="41952-324">Minimální úroveň protokolu</span><span class="sxs-lookup"><span data-stu-id="41952-324">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="41952-325">1</span><span class="sxs-lookup"><span data-stu-id="41952-325">1</span></span>      | <span data-ttu-id="41952-326">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-326">Debug</span></span>         | <span data-ttu-id="41952-327">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="41952-327">All categories</span></span>                          | <span data-ttu-id="41952-328">Informace</span><span class="sxs-lookup"><span data-stu-id="41952-328">Information</span></span>       |
| <span data-ttu-id="41952-329">2</span><span class="sxs-lookup"><span data-stu-id="41952-329">2</span></span>      | <span data-ttu-id="41952-330">Konzola</span><span class="sxs-lookup"><span data-stu-id="41952-330">Console</span></span>       | <span data-ttu-id="41952-331">Microsoft.AspNetCore.Mvc.Razor.Interní</span><span class="sxs-lookup"><span data-stu-id="41952-331">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="41952-332">Upozornění</span><span class="sxs-lookup"><span data-stu-id="41952-332">Warning</span></span>           |
| <span data-ttu-id="41952-333">3</span><span class="sxs-lookup"><span data-stu-id="41952-333">3</span></span>      | <span data-ttu-id="41952-334">Konzola</span><span class="sxs-lookup"><span data-stu-id="41952-334">Console</span></span>       | <span data-ttu-id="41952-335">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="41952-335">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="41952-336">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-336">Debug</span></span>             |
| <span data-ttu-id="41952-337">4</span><span class="sxs-lookup"><span data-stu-id="41952-337">4</span></span>      | <span data-ttu-id="41952-338">Konzola</span><span class="sxs-lookup"><span data-stu-id="41952-338">Console</span></span>       | <span data-ttu-id="41952-339">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="41952-339">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="41952-340">Chyba</span><span class="sxs-lookup"><span data-stu-id="41952-340">Error</span></span>             |
| <span data-ttu-id="41952-341">5</span><span class="sxs-lookup"><span data-stu-id="41952-341">5</span></span>      | <span data-ttu-id="41952-342">Konzola</span><span class="sxs-lookup"><span data-stu-id="41952-342">Console</span></span>       | <span data-ttu-id="41952-343">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="41952-343">All categories</span></span>                          | <span data-ttu-id="41952-344">Informace</span><span class="sxs-lookup"><span data-stu-id="41952-344">Information</span></span>       |
| <span data-ttu-id="41952-345">6</span><span class="sxs-lookup"><span data-stu-id="41952-345">6</span></span>      | <span data-ttu-id="41952-346">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="41952-346">All providers</span></span> | <span data-ttu-id="41952-347">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="41952-347">All categories</span></span>                          | <span data-ttu-id="41952-348">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-348">Debug</span></span>             |
| <span data-ttu-id="41952-349">7</span><span class="sxs-lookup"><span data-stu-id="41952-349">7</span></span>      | <span data-ttu-id="41952-350">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="41952-350">All providers</span></span> | <span data-ttu-id="41952-351">Systém</span><span class="sxs-lookup"><span data-stu-id="41952-351">System</span></span>                                  | <span data-ttu-id="41952-352">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-352">Debug</span></span>             |
| <span data-ttu-id="41952-353">8</span><span class="sxs-lookup"><span data-stu-id="41952-353">8</span></span>      | <span data-ttu-id="41952-354">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-354">Debug</span></span>         | <span data-ttu-id="41952-355">Microsoft</span><span class="sxs-lookup"><span data-stu-id="41952-355">Microsoft</span></span>                               | <span data-ttu-id="41952-356">Trasování</span><span class="sxs-lookup"><span data-stu-id="41952-356">Trace</span></span>             |

<span data-ttu-id="41952-357">Při `ILogger` vytvoření objektu, `ILoggerFactory` objekt vybere jedno pravidlo pro zprostředkovatele použít na tento protokolování.</span><span class="sxs-lookup"><span data-stu-id="41952-357">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="41952-358">Všechny zprávy napsané instancí `ILogger` jsou filtrovány na základě vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="41952-358">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="41952-359">Nejkonkrétnější pravidlo možné pro každého poskytovatele a dvojice kategorií je vybránz dostupných pravidel.</span><span class="sxs-lookup"><span data-stu-id="41952-359">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="41952-360">Následující algoritmus se používá pro `ILogger` každého zprostředkovatele při vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="41952-360">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="41952-361">Vyberte všechna pravidla, která odpovídají poskytovateli nebo jeho aliasu.</span><span class="sxs-lookup"><span data-stu-id="41952-361">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="41952-362">Pokud není nalezena žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="41952-362">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="41952-363">Z výsledku předchozího kroku vyberte pravidla s nejdelší předponou kategorie shody.</span><span class="sxs-lookup"><span data-stu-id="41952-363">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="41952-364">Pokud není nalezena žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="41952-364">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="41952-365">Pokud je vybráno více pravidel, vezměte **poslední.**</span><span class="sxs-lookup"><span data-stu-id="41952-365">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="41952-366">Pokud nejsou vybrána `MinimumLevel`žádná pravidla, použijte .</span><span class="sxs-lookup"><span data-stu-id="41952-366">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="41952-367">S předchozím seznamem pravidel předpokládejme, `ILogger` že vytvoříte objekt pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="41952-367">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="41952-368">Pro zprostředkovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="41952-368">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="41952-369">Pravidlo 8 je nejkonkrétnější, takže to je ten, který byl vybrán.</span><span class="sxs-lookup"><span data-stu-id="41952-369">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="41952-370">Pro zprostředkovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="41952-370">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="41952-371">Pravidlo 3 je nejkonkrétnější.</span><span class="sxs-lookup"><span data-stu-id="41952-371">Rule 3 is most specific.</span></span>

<span data-ttu-id="41952-372">Výsledná `ILogger` instance odešle `Trace` protokoly úrovně a výše zprostředkovateli ladění.</span><span class="sxs-lookup"><span data-stu-id="41952-372">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="41952-373">Protokoly `Debug` úrovně a výše jsou odesílány zprostředkovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="41952-373">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="41952-374">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="41952-374">Provider aliases</span></span>

<span data-ttu-id="41952-375">Každý zprostředkovatel definuje *alias,* který lze použít v konfiguraci namísto plně kvalifikovaný název typu.</span><span class="sxs-lookup"><span data-stu-id="41952-375">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="41952-376">Pro předdefinované zprostředkovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="41952-376">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="41952-377">Konzola</span><span class="sxs-lookup"><span data-stu-id="41952-377">Console</span></span>
* <span data-ttu-id="41952-378">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-378">Debug</span></span>
* <span data-ttu-id="41952-379">EventSource</span><span class="sxs-lookup"><span data-stu-id="41952-379">EventSource</span></span>
* <span data-ttu-id="41952-380">Eventlog</span><span class="sxs-lookup"><span data-stu-id="41952-380">EventLog</span></span>
* <span data-ttu-id="41952-381">TraceSource</span><span class="sxs-lookup"><span data-stu-id="41952-381">TraceSource</span></span>
* <span data-ttu-id="41952-382">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="41952-382">AzureAppServicesFile</span></span>
* <span data-ttu-id="41952-383">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="41952-383">AzureAppServicesBlob</span></span>
* <span data-ttu-id="41952-384">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="41952-384">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="41952-385">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="41952-385">Default minimum level</span></span>

<span data-ttu-id="41952-386">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že pro daného zprostředkovatele a kategorii neplatí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="41952-386">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="41952-387">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="41952-387">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="41952-388">Pokud explicitně nenastavíte minimální úroveň, `Information`výchozí hodnota `Trace` je `Debug` , což znamená, že a protokoly jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="41952-388">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="41952-389">Funkce filtrování</span><span class="sxs-lookup"><span data-stu-id="41952-389">Filter functions</span></span>

<span data-ttu-id="41952-390">Funkce filtru je vyvolána pro všechny zprostředkovatele a kategorie, které nemají pravidla přiřazená podle konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="41952-390">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="41952-391">Kód ve funkci má přístup k typu zprostředkovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="41952-391">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="41952-392">Příklad:</span><span class="sxs-lookup"><span data-stu-id="41952-392">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="41952-393">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="41952-393">System categories and levels</span></span>

<span data-ttu-id="41952-394">Zde jsou některé kategorie používané ASP.NET jádrem a jádrem entity s poznámkami o tom, jaké protokoly od nich lze očekávat:</span><span class="sxs-lookup"><span data-stu-id="41952-394">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="41952-395">Kategorie</span><span class="sxs-lookup"><span data-stu-id="41952-395">Category</span></span>                            | <span data-ttu-id="41952-396">Poznámky</span><span class="sxs-lookup"><span data-stu-id="41952-396">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="41952-397">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="41952-397">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="41952-398">Obecná diagnostika ASP.NET jádra.</span><span class="sxs-lookup"><span data-stu-id="41952-398">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="41952-399">Ochrana Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="41952-399">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="41952-400">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="41952-400">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="41952-401">Filtrování Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="41952-401">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="41952-402">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="41952-402">Hosts allowed.</span></span> |
| <span data-ttu-id="41952-403">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="41952-403">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="41952-404">Jak dlouho trvalo dokončení požadavků HTTP a v jakém čase byly spuštěny.</span><span class="sxs-lookup"><span data-stu-id="41952-404">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="41952-405">Která hostitelská spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="41952-405">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="41952-406">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="41952-406">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="41952-407">Diagnostika MVC a Razor.</span><span class="sxs-lookup"><span data-stu-id="41952-407">MVC and Razor diagnostics.</span></span> <span data-ttu-id="41952-408">Vazba modelu, spuštění filtru, kompilace zobrazení, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="41952-408">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="41952-409">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="41952-409">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="41952-410">Informace o porovnávání tras.</span><span class="sxs-lookup"><span data-stu-id="41952-410">Route matching information.</span></span> |
| <span data-ttu-id="41952-411">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="41952-411">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="41952-412">Připojení spustit, zastavit a udržet naživu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="41952-412">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="41952-413">informace o certifikátu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="41952-413">HTTPS certificate information.</span></span> |
| <span data-ttu-id="41952-414">Soubory Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="41952-414">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="41952-415">Doručené složky.</span><span class="sxs-lookup"><span data-stu-id="41952-415">Files served.</span></span> |
| <span data-ttu-id="41952-416">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="41952-416">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="41952-417">Diagnostika jádra obecnéentity frameworku.</span><span class="sxs-lookup"><span data-stu-id="41952-417">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="41952-418">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="41952-418">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="41952-419">Obory protokolu</span><span class="sxs-lookup"><span data-stu-id="41952-419">Log scopes</span></span>

 <span data-ttu-id="41952-420">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="41952-420">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="41952-421">Toto seskupení lze použít k připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="41952-421">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="41952-422">Například každý protokol vytvořený jako součást zpracování transakce může obsahovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="41952-422">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="41952-423">Obor je `IDisposable` typ, který je <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> vrácen metodou a trvá, dokud je uvolněn.</span><span class="sxs-lookup"><span data-stu-id="41952-423">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="41952-424">Použití oboru zabalením volání `using` protokolování v bloku:</span><span class="sxs-lookup"><span data-stu-id="41952-424">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="41952-425">Následující kód umožňuje obory pro zprostředkovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="41952-425">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="41952-426">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="41952-426">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="41952-427">Konfigurace možnosti `IncludeScopes` protokolování konzoly je nutná k povolení protokolování založeného na oboru.</span><span class="sxs-lookup"><span data-stu-id="41952-427">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="41952-428">Informace o konfiguraci naleznete v části [Konfigurace.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="41952-428">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="41952-429">Každá zpráva protokolu obsahuje informace s vymezenou oborem:</span><span class="sxs-lookup"><span data-stu-id="41952-429">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="41952-430">Vestavění poskytovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="41952-430">Built-in logging providers</span></span>

<span data-ttu-id="41952-431">ASP.NET Core dodává tyto poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="41952-431">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="41952-432">Konzola</span><span class="sxs-lookup"><span data-stu-id="41952-432">Console</span></span>](#console-provider)
* [<span data-ttu-id="41952-433">Ladit</span><span class="sxs-lookup"><span data-stu-id="41952-433">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="41952-434">Zdroj událostí</span><span class="sxs-lookup"><span data-stu-id="41952-434">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="41952-435">Eventlog</span><span class="sxs-lookup"><span data-stu-id="41952-435">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="41952-436">TraceSource</span><span class="sxs-lookup"><span data-stu-id="41952-436">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="41952-437">Soubor AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="41952-437">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="41952-438">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="41952-438">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="41952-439">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="41952-439">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="41952-440">Informace o protokolování stdout a ladění pomocí <xref:test/troubleshoot-azure-iis> ASP.NET <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>core modulem naleznete v tématu a .</span><span class="sxs-lookup"><span data-stu-id="41952-440">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="41952-441">Zprostředkovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="41952-441">Console provider</span></span>

<span data-ttu-id="41952-442">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="41952-442">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="41952-443">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="41952-443">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="41952-444">Zprostředkovatel ladění</span><span class="sxs-lookup"><span data-stu-id="41952-444">Debug provider</span></span>

<span data-ttu-id="41952-445">Balíček zprostředkovatele [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí`Debug.WriteLine` [třídy System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (volání metody).</span><span class="sxs-lookup"><span data-stu-id="41952-445">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="41952-446">V systému Linux tento zprostředkovatel zapisuje protokoly do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="41952-446">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="41952-447">Zprostředkovatel zdroje událostí</span><span class="sxs-lookup"><span data-stu-id="41952-447">Event Source provider</span></span>

<span data-ttu-id="41952-448">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na `Microsoft-Extensions-Logging`křížovou platformu Zdroje událostí s názvem .</span><span class="sxs-lookup"><span data-stu-id="41952-448">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="41952-449">V systému Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="41952-449">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="41952-450">Zprostředkovatel zdroje událostí je `CreateDefaultBuilder` přidán automaticky, když je volána k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="41952-450">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="41952-451">nástroje pro trasování dotnet</span><span class="sxs-lookup"><span data-stu-id="41952-451">dotnet trace tooling</span></span>

<span data-ttu-id="41952-452">[Dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) nástroj je globální nástroj rozhraní se sídlem v rychléplatformě, který umožňuje shromažďování trasování .NET Core spuštěného procesu.</span><span class="sxs-lookup"><span data-stu-id="41952-452">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="41952-453">Nástroj shromažďuje <xref:Microsoft.Extensions.Logging.EventSource> data zprostředkovatele <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>pomocí .</span><span class="sxs-lookup"><span data-stu-id="41952-453">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="41952-454">Nainstalujte nástroje trasování dotnet pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="41952-454">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="41952-455">Pomocí nástrojů sledování dotnet shromažďujte trasování z aplikace:</span><span class="sxs-lookup"><span data-stu-id="41952-455">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="41952-456">Pokud aplikace nevytváří hostitele s `CreateDefaultBuilder`, přidejte [poskytovatele zdroje událostí](#event-source-provider) do konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-456">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="41952-457">Spusťte aplikaci pomocí příkazu. `dotnet run`</span><span class="sxs-lookup"><span data-stu-id="41952-457">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="41952-458">Určete identifikátor procesu (PID) aplikace .NET Core:</span><span class="sxs-lookup"><span data-stu-id="41952-458">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="41952-459">V systému Windows použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="41952-459">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="41952-460">Správce úloh (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="41952-460">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="41952-461">seznam úloh, příkaz</span><span class="sxs-lookup"><span data-stu-id="41952-461">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="41952-462">Příkaz Získat proces powershellu</span><span class="sxs-lookup"><span data-stu-id="41952-462">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="41952-463">Na Linuxu použijte [příkaz pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="41952-463">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="41952-464">Najděte PID pro proces, který má stejný název jako sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-464">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="41952-465">Spusťte `dotnet trace` příkaz.</span><span class="sxs-lookup"><span data-stu-id="41952-465">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="41952-466">Obecná syntaxe příkazu:</span><span class="sxs-lookup"><span data-stu-id="41952-466">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="41952-467">Při použití příkazového prostředí Prostředí `--providers` PowerShell uzavřete hodnotu do jednoduchých uvozovek (`'`):</span><span class="sxs-lookup"><span data-stu-id="41952-467">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="41952-468">Na platformách jiných než `-f speedscope` Windows přidejte možnost změnit formát `speedscope`výstupního trasovacího souboru na .</span><span class="sxs-lookup"><span data-stu-id="41952-468">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="41952-469">Klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="41952-469">Keyword</span></span> | <span data-ttu-id="41952-470">Popis</span><span class="sxs-lookup"><span data-stu-id="41952-470">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="41952-471">1</span><span class="sxs-lookup"><span data-stu-id="41952-471">1</span></span>       | <span data-ttu-id="41952-472">Protokolovat meta `LoggingEventSource`události o .</span><span class="sxs-lookup"><span data-stu-id="41952-472">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="41952-473">Nezaznamenává události z `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="41952-473">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="41952-474">2</span><span class="sxs-lookup"><span data-stu-id="41952-474">2</span></span>       | <span data-ttu-id="41952-475">Zapne událost, `Message` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="41952-475">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="41952-476">Poskytuje informace programovým (neformátovaným) způsobem.</span><span class="sxs-lookup"><span data-stu-id="41952-476">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="41952-477">4</span><span class="sxs-lookup"><span data-stu-id="41952-477">4</span></span>       | <span data-ttu-id="41952-478">Zapne událost, `FormatMessage` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="41952-478">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="41952-479">Poskytuje formátovanou verzi řetězce informací.</span><span class="sxs-lookup"><span data-stu-id="41952-479">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="41952-480">8</span><span class="sxs-lookup"><span data-stu-id="41952-480">8</span></span>       | <span data-ttu-id="41952-481">Zapne událost, `MessageJson` `ILogger.Log()` když je volána.</span><span class="sxs-lookup"><span data-stu-id="41952-481">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="41952-482">Poskytuje json reprezentaci argumentů.</span><span class="sxs-lookup"><span data-stu-id="41952-482">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="41952-483">Úroveň události</span><span class="sxs-lookup"><span data-stu-id="41952-483">Event Level</span></span> | <span data-ttu-id="41952-484">Popis</span><span class="sxs-lookup"><span data-stu-id="41952-484">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="41952-485">0</span><span class="sxs-lookup"><span data-stu-id="41952-485">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="41952-486">1</span><span class="sxs-lookup"><span data-stu-id="41952-486">1</span></span>           | `Critical`      |
   | <span data-ttu-id="41952-487">2</span><span class="sxs-lookup"><span data-stu-id="41952-487">2</span></span>           | `Error`         |
   | <span data-ttu-id="41952-488">3</span><span class="sxs-lookup"><span data-stu-id="41952-488">3</span></span>           | `Warning`       |
   | <span data-ttu-id="41952-489">4</span><span class="sxs-lookup"><span data-stu-id="41952-489">4</span></span>           | `Informational` |
   | <span data-ttu-id="41952-490">5</span><span class="sxs-lookup"><span data-stu-id="41952-490">5</span></span>           | `Verbose`       |

   <span data-ttu-id="41952-491">`FilterSpecs`položky `{Logger Category}` `{Event Level}` pro a představují další podmínky filtrování protokolů.</span><span class="sxs-lookup"><span data-stu-id="41952-491">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="41952-492">Samostatné `FilterSpecs` položky s`;`středníkem ( ).</span><span class="sxs-lookup"><span data-stu-id="41952-492">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="41952-493">Příklad použití příkazového prostředí systému `--providers` Windows **(žádné** jednoduché uvozovky kolem hodnoty):</span><span class="sxs-lookup"><span data-stu-id="41952-493">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="41952-494">Předchozí příkaz aktivuje:</span><span class="sxs-lookup"><span data-stu-id="41952-494">The preceding command activates:</span></span>

   * <span data-ttu-id="41952-495">Protokolovací nástroj Zdroje událostí pro`4`vytvoření formátovaných`2`řetězců ( ) pro chyby ( ).</span><span class="sxs-lookup"><span data-stu-id="41952-495">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="41952-496">`Microsoft.AspNetCore.Hosting`protokolování `Informational` na úrovni`4`protokolování ( ).</span><span class="sxs-lookup"><span data-stu-id="41952-496">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="41952-497">Zastavení nástroje trasování dotnet stisknutím klávesy Enter nebo Ctrl+C.</span><span class="sxs-lookup"><span data-stu-id="41952-497">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="41952-498">Trasování je uložens s názvem *trace.nettrace* ve složce, `dotnet trace` kde je příkaz proveden.</span><span class="sxs-lookup"><span data-stu-id="41952-498">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="41952-499">Otevřete trasování pomocí [perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="41952-499">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="41952-500">Otevřete soubor *trace.nettrace* a prozkoumejte události trasování.</span><span class="sxs-lookup"><span data-stu-id="41952-500">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="41952-501">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="41952-501">For more information, see:</span></span>

* <span data-ttu-id="41952-502">[Trasování pro nástroj pro analýzu výkonu (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentace jádra.NET)</span><span class="sxs-lookup"><span data-stu-id="41952-502">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="41952-503">[Trasování pro nástroj pro analýzu výkonu (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentace úložiště dotnet/diagnostics GitHub)</span><span class="sxs-lookup"><span data-stu-id="41952-503">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="41952-504">[Třída LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (prohlížeč rozhraní API.NET)</span><span class="sxs-lookup"><span data-stu-id="41952-504">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="41952-505">[Název reference LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Chcete-li získat referenční zdroj `release/{Version}`pro `{Version}` jinou verzi, změňte větev na , kde je požadovaná verze ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41952-505">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="41952-506">[Perfview](#perfview) &ndash; Užitečné pro zobrazení trasování zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="41952-506">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="41952-507">Zobrazení Perfview</span><span class="sxs-lookup"><span data-stu-id="41952-507">Perfview</span></span>

::: moniker-end

<span data-ttu-id="41952-508">Pomocí [nástroje PerfView](https://github.com/Microsoft/perfview) sbírat a zobrazovat protokoly.</span><span class="sxs-lookup"><span data-stu-id="41952-508">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="41952-509">Existují další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi ETW vyzařované ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41952-509">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="41952-510">Chcete-li nakonfigurovat PerfView pro shromažďování událostí `*Microsoft-Extensions-Logging` protokolovaných tímto zprostředkovatelem, přidejte řetězec do seznamu **Další zprostředkovatelé.**</span><span class="sxs-lookup"><span data-stu-id="41952-510">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="41952-511">(Nenechte si ujít hvězdičku na začátku řetězce.)</span><span class="sxs-lookup"><span data-stu-id="41952-511">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview Další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="41952-513">Zprostředkovatel služby Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="41952-513">Windows EventLog provider</span></span>

<span data-ttu-id="41952-514">Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="41952-514">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="41952-515">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="41952-515">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="41952-516">Pokud `null` není zadáno, použije se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="41952-516">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="41952-517">`LogName`&ndash; "Aplikace"</span><span class="sxs-lookup"><span data-stu-id="41952-517">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="41952-518">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="41952-518">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="41952-519">`MachineName`&ndash; místní počítač</span><span class="sxs-lookup"><span data-stu-id="41952-519">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="41952-520">Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level).</span><span class="sxs-lookup"><span data-stu-id="41952-520">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="41952-521">Chcete-li protokolovat události nižší než `Warning`, explicitně nastavte úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="41952-521">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="41952-522">Do souboru *appsettings.json* například přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="41952-522">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="41952-523">Zprostředkovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="41952-523">TraceSource provider</span></span>

<span data-ttu-id="41952-524">Balíček [zprostředkovatele Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="41952-524">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="41952-525">[AddTraceSource přetížení](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="41952-525">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="41952-526">Chcete-li použít tohoto zprostředkovatele, aplikace musí běžet na rozhraní .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="41952-526">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="41952-527">Zprostředkovatel může směrovat zprávy do různých [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použité v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41952-527">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="41952-528">Poskytovatel služeb Azure App Service</span><span class="sxs-lookup"><span data-stu-id="41952-528">Azure App Service provider</span></span>

<span data-ttu-id="41952-529">Balíček zprostředkovatele [Microsoft.Extensions.Log.Log.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Služby Azure App Service a do [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="41952-529">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41952-530">Balíček zprostředkovatele není součástí sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41952-530">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="41952-531">Chcete-li použít zprostředkovatele, přidejte balíček zprostředkovatele do projektu.</span><span class="sxs-lookup"><span data-stu-id="41952-531">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="41952-532">Balíček zprostředkovatele není součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="41952-532">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="41952-533">Při cílení na rozhraní .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` metabalíček přidejte balíček zprostředkovatele do projektu.</span><span class="sxs-lookup"><span data-stu-id="41952-533">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41952-534">Chcete-li konfigurovat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> nastavení <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>zprostředkovatele, použijte a , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="41952-534">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="41952-535">Chcete-li konfigurovat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> nastavení <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>zprostředkovatele, použijte a , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="41952-535">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="41952-536">Přetížení <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> umožňuje projít v <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="41952-536">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="41952-537">Objekt nastavení může přepsat výchozí nastavení, například šablonu výstupu protokolování, název objektu blob a omezení velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="41952-537">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="41952-538">(Výstupní*šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici s voláním `ILogger` metody.)</span><span class="sxs-lookup"><span data-stu-id="41952-538">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="41952-539">Když se nasadíte do aplikace App Service, aplikace respektuje nastavení v části [protokoly služby App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stránce **Služby aplikací** na portálu Azure.</span><span class="sxs-lookup"><span data-stu-id="41952-539">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="41952-540">Při aktualizaci následujících nastavení se změny projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-540">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="41952-541">**Protokolování aplikací (souborový systém)**</span><span class="sxs-lookup"><span data-stu-id="41952-541">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="41952-542">**Protokolování aplikací (objekt blob)**</span><span class="sxs-lookup"><span data-stu-id="41952-542">**Application Logging (Blob)**</span></span>

<span data-ttu-id="41952-543">Výchozí umístění souborů protokolu je ve složce *D:\\\\home LogFiles\\Application* a výchozí název souboru je *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="41952-543">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="41952-544">Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="41952-544">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="41952-545">Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="41952-545">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="41952-546">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="41952-546">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="41952-547">Nemá žádný vliv, když je&mdash;projekt spuštěn místně, nezapisuje do místních souborů nebo místní vývojové úložiště pro objekty BLOB.</span><span class="sxs-lookup"><span data-stu-id="41952-547">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="41952-548">Streamování protokolu Azure</span><span class="sxs-lookup"><span data-stu-id="41952-548">Azure log streaming</span></span>

<span data-ttu-id="41952-549">Streamování protokolu Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="41952-549">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="41952-550">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="41952-550">The app server</span></span>
* <span data-ttu-id="41952-551">Webový server</span><span class="sxs-lookup"><span data-stu-id="41952-551">The web server</span></span>
* <span data-ttu-id="41952-552">Trasování neúspěšných požadavků</span><span class="sxs-lookup"><span data-stu-id="41952-552">Failed request tracing</span></span>

<span data-ttu-id="41952-553">Konfigurace streamování protokolu Azure:</span><span class="sxs-lookup"><span data-stu-id="41952-553">To configure Azure log streaming:</span></span>

* <span data-ttu-id="41952-554">Přejděte na stránku **protokolů služby App Service** na stránce portálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="41952-554">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="41952-555">Nastavení **protokolování aplikací (souborový systém)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="41952-555">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="41952-556">Zvolte **úroveň**protokolu .</span><span class="sxs-lookup"><span data-stu-id="41952-556">Choose the log **Level**.</span></span> <span data-ttu-id="41952-557">Toto nastavení platí jenom pro streamování protokolu Azure, ne pro jiné poskytovatele protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41952-557">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="41952-558">Přejděte na stránku **Log Stream** a zobrazte zprávy aplikací.</span><span class="sxs-lookup"><span data-stu-id="41952-558">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="41952-559">Jsou zaznamenány aplikací prostřednictvím `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41952-559">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="41952-560">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="41952-560">Azure Application Insights trace logging</span></span>

<span data-ttu-id="41952-561">Balíček zprostředkovatele [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="41952-561">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="41952-562">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu telemetrických dat.</span><span class="sxs-lookup"><span data-stu-id="41952-562">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="41952-563">Pokud používáte tohoto zprostředkovatele, můžete dotazovat a analyzovat protokoly pomocí nástrojů Application Insights.</span><span class="sxs-lookup"><span data-stu-id="41952-563">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="41952-564">Zprostředkovatel protokolování je součástí jako závislost [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje všechny dostupné telemetrie pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41952-564">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="41952-565">Pokud používáte tento balíček, není nutné instalovat balíček zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="41952-565">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="41952-566">Nepoužívejte balíček&mdash; [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) pro ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="41952-566">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="41952-567">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="41952-567">For more information, see the following resources:</span></span>

* [<span data-ttu-id="41952-568">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="41952-568">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="41952-569">[Application Insights pro ASP.NET základní aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou škálu telemetrie Application Insights spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="41952-569">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="41952-570">[ApplicationInsightsLoggerProvider pro protokoly ILogger jádra .NET](/azure/azure-monitor/app/ilogger) – začněte zde, pokud chcete implementovat poskytovatele protokolování bez zbytku telemetrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="41952-570">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="41952-571">[Adaptéry pro protokolování Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="41952-571">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="41952-572">[Nainstalujte, nakonfigurujte a inicializujte kurz Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="41952-572">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="41952-573">Poskytovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="41952-573">Third-party logging providers</span></span>

<span data-ttu-id="41952-574">Architektury protokolování třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="41952-574">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="41952-575">[elmah.io](https://elmah.io/) ([Úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="41952-575">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="41952-576">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="41952-576">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="41952-577">[JSNLog](https://jsnlog.com/) ([Úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="41952-577">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="41952-578">[KissLog.net](https://kisslog.net/) ([Úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="41952-578">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="41952-579">[Log4Net](https://logging.apache.org/log4net/) ([Úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="41952-579">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="41952-580">[Loggr](https://loggr.net/) ([Úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="41952-580">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="41952-581">[NLog](https://nlog-project.org/) ([Úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="41952-581">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="41952-582">[Sentry](https://sentry.io/welcome/) ([Úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="41952-582">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="41952-583">[Serilog](https://serilog.net/) ([Úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="41952-583">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="41952-584">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Úložiště Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="41952-584">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="41952-585">Některé architektury jiných výrobců můžete provádět [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="41952-585">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="41952-586">Použití rozhraní třetí strany je podobné použití jednoho z předdefinovaných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="41952-586">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="41952-587">Přidejte balíček NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="41952-587">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="41952-588">Volání `ILoggerFactory` metody rozšíření poskytované rozhraní protokolování.</span><span class="sxs-lookup"><span data-stu-id="41952-588">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="41952-589">Další informace naleznete v dokumentaci každého zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="41952-589">For more information, see each provider's documentation.</span></span> <span data-ttu-id="41952-590">Společnost Microsoft nepodporuje poskytovatele protokolování jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="41952-590">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41952-591">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="41952-591">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

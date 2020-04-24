---
title: Přihlášení v .NET Core a ASP.NET Core
author: rick-anderson
description: Naučte se používat protokolovací rozhraní poskytovanou balíčkem NuGet Microsoft. Extensions. Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 7be8cef3377132ed43efde209db67401d7bdb6dc
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110912"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="63e12-103">Přihlášení v .NET Core a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63e12-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="63e12-104">Tím, že [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="63e12-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="63e12-105">.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="63e12-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="63e12-106">V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="63e12-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="63e12-107">Většina příkladů kódu, které jsou uvedené v tomto článku, se nachází v ASP.NET Corech aplikacích.</span><span class="sxs-lookup"><span data-stu-id="63e12-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="63e12-108">Části těchto fragmentů kódu specifické pro protokolování se vztahují na libovolnou aplikaci .NET Core, která používá [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="63e12-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="63e12-109">Příklad použití obecného hostitele v aplikaci jiné než webové konzoly najdete v souboru *program.cs* [ukázkové aplikace úlohy na pozadí](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="63e12-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="63e12-110">Kód protokolování pro aplikace bez obecného hostitele se liší v způsobu [Přidání zprostředkovatelů](#add-providers) a [vytváření protokolovacích](#create-logs)nástrojů.</span><span class="sxs-lookup"><span data-stu-id="63e12-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="63e12-111">Příklady kódu, který není hostitelský, jsou uvedeny v těchto částech článku.</span><span class="sxs-lookup"><span data-stu-id="63e12-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="63e12-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63e12-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="63e12-113">Přidat zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="63e12-113">Add providers</span></span>

<span data-ttu-id="63e12-114">Zprostředkovatel protokolování zobrazuje nebo ukládá protokoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="63e12-115">Například poskytovatel konzoly zobrazuje protokoly v konzole nástroje a poskytovatel Azure Application Insights je ukládá do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="63e12-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="63e12-116">Protokoly lze odesílat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="63e12-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="63e12-117">Chcete-li přidat poskytovatele v aplikaci, která používá obecného hostitele, zavolejte metodu `Add{provider name}` rozšíření poskytovatele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="63e12-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="63e12-118">V konzolové aplikaci, která není hostitelem, zavolejte metodu `Add{provider name}` rozšíření poskytovatele při vytváření: `LoggerFactory`</span><span class="sxs-lookup"><span data-stu-id="63e12-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="63e12-119">`LoggerFactory`a `AddConsole` vyžadují `using` příkaz pro `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="63e12-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="63e12-120">Výchozí ASP.NET Core volání <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>šablon projektu, které přidává následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="63e12-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="63e12-121">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="63e12-122">Ladí</span><span class="sxs-lookup"><span data-stu-id="63e12-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="63e12-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="63e12-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="63e12-124">Protokol [událostí](#windows-eventlog-provider) (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="63e12-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="63e12-125">Výchozí poskytovatele můžete nahradit vašimi vlastními možnostmi.</span><span class="sxs-lookup"><span data-stu-id="63e12-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="63e12-126">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte poskytovatele, které chcete.</span><span class="sxs-lookup"><span data-stu-id="63e12-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="63e12-127">Přečtěte si další informace o [integrovaných poskytovatelích protokolování](#built-in-logging-providers) a [zprostředkovatelích protokolování třetích stran](#third-party-logging-providers) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="63e12-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="63e12-128">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="63e12-128">Create logs</span></span>

<span data-ttu-id="63e12-129">K vytváření protokolů použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="63e12-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="63e12-130">V rámci webové aplikace nebo hostované služby Získejte `ILogger` od vložení závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="63e12-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="63e12-131">V konzolových aplikacích bez hostitele použijte `LoggerFactory` k vytvoření. `ILogger`</span><span class="sxs-lookup"><span data-stu-id="63e12-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="63e12-132">Následující ASP.NET Core příklad vytvoří protokolovací nástroj s `TodoApiSample.Pages.AboutModel` jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="63e12-133">*Kategorie* protokolu je řetězec, který je spojený s každým protokolem.</span><span class="sxs-lookup"><span data-stu-id="63e12-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="63e12-134">`ILogger<T>` Instance poskytovaná di vytvoří protokoly, které mají plně kvalifikovaný název typu `T` jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="63e12-135">Následující příklad nehostitelských konzolových aplikací vytvoří protokolovací nástroj s `LoggingConsoleApp.Program` jako kategorií.</span><span class="sxs-lookup"><span data-stu-id="63e12-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="63e12-136">V následujících příkladech ASP.NET Core a konzolových aplikací se k vytváření protokolů s `Information` jako úroveň používá protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="63e12-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="63e12-137">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="63e12-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="63e12-138">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="63e12-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="63e12-139">Vytvořit protokoly ve třídě program</span><span class="sxs-lookup"><span data-stu-id="63e12-139">Create logs in the Program class</span></span>

<span data-ttu-id="63e12-140">Pokud chcete zapisovat protokoly ve `Program` třídě ASP.NET Core aplikace, Získejte po sestavení hostitele `ILogger` instanci od typu di:</span><span class="sxs-lookup"><span data-stu-id="63e12-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="63e12-141">Protokolování během konstrukce hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="63e12-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="63e12-142">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="63e12-142">However, a separate logger can be used.</span></span> <span data-ttu-id="63e12-143">V následujícím příkladu se k přihlášení používá protokolovací nástroj [Serilog](https://serilog.net/) `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="63e12-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="63e12-144">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="63e12-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="63e12-145">Vytvořit protokoly ve spouštěcí třídě</span><span class="sxs-lookup"><span data-stu-id="63e12-145">Create logs in the Startup class</span></span>

<span data-ttu-id="63e12-146">Pokud chcete zapisovat protokoly v `Startup.Configure` metodě ASP.NET Core aplikace, zahrňte do signatury metody `ILogger` parametr:</span><span class="sxs-lookup"><span data-stu-id="63e12-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="63e12-147">Zápis protokolů před dokončením nastavení DI Container v `Startup.ConfigureServices` metodě není podporován:</span><span class="sxs-lookup"><span data-stu-id="63e12-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="63e12-148">Vkládání protokolovacího nástroje `Startup` do konstruktoru není podporováno.</span><span class="sxs-lookup"><span data-stu-id="63e12-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="63e12-149">Vkládání protokolovacího nástroje `Startup.ConfigureServices` do signatury metody není podporováno.</span><span class="sxs-lookup"><span data-stu-id="63e12-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="63e12-150">Důvodem tohoto omezení je, že protokolování závisí na DI a na konfiguraci, která v systému zapíná na DI.</span><span class="sxs-lookup"><span data-stu-id="63e12-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="63e12-151">Kontejner DI není nastavený, dokud `ConfigureServices` se nedokončí.</span><span class="sxs-lookup"><span data-stu-id="63e12-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="63e12-152">Vložení konstruktoru protokolovacího nástroje do `Startup` sady Works v dřívějších verzích ASP.NET Core, protože pro webového hostitele je vytvořen samostatný kontejner di.</span><span class="sxs-lookup"><span data-stu-id="63e12-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="63e12-153">Informace o tom, proč je pro obecného hostitele vytvořeno jenom jeden kontejner, najdete v části [oznámení o změně](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="63e12-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="63e12-154">Pokud potřebujete nakonfigurovat službu, která závisí na `ILogger<T>`, můžete to provést pomocí injektáže konstruktoru nebo poskytnutím metody pro vytváření.</span><span class="sxs-lookup"><span data-stu-id="63e12-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="63e12-155">Přístup k metodě pro vytváření je doporučen pouze v případě, že není k dispozici žádná jiná možnost.</span><span class="sxs-lookup"><span data-stu-id="63e12-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="63e12-156">Předpokládejme například, že potřebujete vyplnit vlastnost pomocí služby z DI:</span><span class="sxs-lookup"><span data-stu-id="63e12-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="63e12-157">Předchozí zvýrazněný kód je `Func` , který se spustí, když di Container potřebuje vytvořit instanci. `MyService`</span><span class="sxs-lookup"><span data-stu-id="63e12-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="63e12-158">Tímto způsobem můžete získat přístup k libovolné registrované službě.</span><span class="sxs-lookup"><span data-stu-id="63e12-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="63e12-159">Vytváření protokolů v Blazor</span><span class="sxs-lookup"><span data-stu-id="63e12-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="63e12-160">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="63e12-160">Blazor WebAssembly</span></span>

<span data-ttu-id="63e12-161">Nakonfigurujte protokolování aplikací WebAssembly v Blazor pomocí `WebAssemblyHostBuilder.Logging` vlastnosti v: `Program.Main`</span><span class="sxs-lookup"><span data-stu-id="63e12-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="63e12-162">`Logging` Vlastnost je typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, takže všechny metody rozšíření, které jsou k dispozici <xref:Microsoft.Extensions.Logging.ILoggingBuilder> na, jsou také k dispozici na `Logging`.</span><span class="sxs-lookup"><span data-stu-id="63e12-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="63e12-163">Přihlášení součástí Razor</span><span class="sxs-lookup"><span data-stu-id="63e12-163">Log in Razor components</span></span>

<span data-ttu-id="63e12-164">Protokolovací nástroje respektují konfiguraci spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="63e12-165">`using` Direktiva pro <xref:Microsoft.Extensions.Logging> je nutná k podpoře dokončování technologie IntelliSense pro rozhraní API, <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> například <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>a.</span><span class="sxs-lookup"><span data-stu-id="63e12-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="63e12-166">Následující příklad ukazuje protokolování s komponentou <xref:Microsoft.Extensions.Logging.ILogger> v součástech Razor:</span><span class="sxs-lookup"><span data-stu-id="63e12-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

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

<span data-ttu-id="63e12-167">Následující příklad ukazuje protokolování s komponentou <xref:Microsoft.Extensions.Logging.ILoggerFactory> v součástech Razor:</span><span class="sxs-lookup"><span data-stu-id="63e12-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="63e12-168">Žádné metody asynchronního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="63e12-168">No asynchronous logger methods</span></span>

<span data-ttu-id="63e12-169">Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="63e12-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="63e12-170">Pokud je úložiště dat protokolování pomalé, nezapište ho přímo.</span><span class="sxs-lookup"><span data-stu-id="63e12-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="63e12-171">Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je později přesunout do pomalého úložiště.</span><span class="sxs-lookup"><span data-stu-id="63e12-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="63e12-172">Například pokud se přihlašujete k SQL Server, nechcete provádět přímo v `Log` metodě, protože `Log` metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="63e12-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="63e12-173">Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="63e12-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="63e12-174">Další informace najdete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHubu.</span><span class="sxs-lookup"><span data-stu-id="63e12-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="63e12-175">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="63e12-175">Configuration</span></span>

<span data-ttu-id="63e12-176">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="63e12-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="63e12-177">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="63e12-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="63e12-178">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="63e12-178">Command-line arguments.</span></span>
* <span data-ttu-id="63e12-179">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="63e12-179">Environment variables.</span></span>
* <span data-ttu-id="63e12-180">Objekty rozhraní .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="63e12-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="63e12-181">Úložiště nešifrovaného [tajného správce](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="63e12-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="63e12-182">Šifrované uživatelské úložiště, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="63e12-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="63e12-183">Vlastní poskytovatelé (nainstalováno nebo vytvořeno).</span><span class="sxs-lookup"><span data-stu-id="63e12-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="63e12-184">Například konfigurace protokolování je běžně poskytována v `Logging` části soubory nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="63e12-185">Následující příklad ukazuje obsah typického *appSettings. Soubor Development. JSON* :</span><span class="sxs-lookup"><span data-stu-id="63e12-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="63e12-186">`Logging` Vlastnost může mít `LogLevel` vlastnosti zprostředkovatele a log (zobrazí se konzola).</span><span class="sxs-lookup"><span data-stu-id="63e12-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="63e12-187">`LogLevel` Vlastnost v rámci `Logging` určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="63e12-188">V tomto příkladu se `System` `Microsoft` kategorie přihlásí na `Information` úrovni a všechny ostatní protokoly na `Debug` úrovni.</span><span class="sxs-lookup"><span data-stu-id="63e12-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="63e12-189">Další vlastnosti v `Logging` části zadat zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="63e12-190">Příklad je pro poskytovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-190">The example is for the Console provider.</span></span> <span data-ttu-id="63e12-191">Pokud zprostředkovatel podporuje [obory protokolů](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="63e12-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="63e12-192">Vlastnost poskytovatele (například `Console` v příkladu) může také určovat `LogLevel` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="63e12-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="63e12-193">`LogLevel`v rámci zprostředkovatele Určuje úrovně, které se mají protokolovat pro daného poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="63e12-194">Pokud jsou úrovně specifikovány `Logging.{providername}.LogLevel`v, přepíše cokoli nastavené `Logging.LogLevel`v.</span><span class="sxs-lookup"><span data-stu-id="63e12-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="63e12-195">Rozhraní API pro protokolování neobsahuje scénář pro změnu úrovní protokolu, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="63e12-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="63e12-196">Někteří poskytovatelé konfigurace ale mohou znovu načíst konfiguraci, která se projeví okamžitě při konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="63e12-197">Například [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider), který je přidán nástrojem `CreateDefaultBuilder` ke čtení souborů nastavení, ve výchozím nastavení znovu načte konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="63e12-198">Pokud se konfigurace v kódu změní, když je aplikace spuštěná, může aplikace volat [IConfigurationRoot. Load](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby se aktualizovala konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="63e12-199">Informace o implementaci zprostředkovatelů konfigurace najdete v <xref:fundamentals/configuration/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="63e12-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="63e12-200">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="63e12-200">Sample logging output</span></span>

<span data-ttu-id="63e12-201">Pomocí ukázkového kódu zobrazeného v předchozí části se protokoly zobrazí v konzole nástroje při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="63e12-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="63e12-202">Tady je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="63e12-202">Here's an example of console output:</span></span>

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

<span data-ttu-id="63e12-203">Předchozí protokoly byly vygenerovány vytvořením žádosti HTTP GET do ukázkové aplikace na adrese `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="63e12-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="63e12-204">Tady je příklad stejného protokolu, který se zobrazí v okně ladění při spuštění ukázkové aplikace v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="63e12-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="63e12-205">Protokoly, které jsou vytvořené `ILogger` voláními, které jsou uvedené v předchozí části, začínají na "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="63e12-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="63e12-206">Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63e12-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="63e12-207">ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="63e12-208">Zbývající část tohoto článku vysvětluje několik podrobností a možností protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="63e12-209">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="63e12-209">NuGet packages</span></span>

<span data-ttu-id="63e12-210">Rozhraní `ILogger` a `ILoggerFactory` jsou v [Microsoft. Extensions. Loggings. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="63e12-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="63e12-211">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="63e12-211">Log category</span></span>

<span data-ttu-id="63e12-212">Při vytvoření `ILogger` objektu je pro něj zadána *kategorie* .</span><span class="sxs-lookup"><span data-stu-id="63e12-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="63e12-213">Tato kategorie je součástí každé zprávy protokolu vytvořené pomocí této instance `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="63e12-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="63e12-214">Kategorie může být libovolný řetězec, ale konvence používá název třídy, jako je například "TodoApi. Controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="63e12-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="63e12-215">Použijte `ILogger<T>` k získání `ILogger` instance, která používá plně kvalifikovaný název typu `T` jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="63e12-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="63e12-216">Chcete-li explicitně zadat kategorii, `ILoggerFactory.CreateLogger`zavolejte:</span><span class="sxs-lookup"><span data-stu-id="63e12-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="63e12-217">`ILogger<T>`je ekvivalentní volání `CreateLogger` s plně kvalifikovaným názvem typu `T`.</span><span class="sxs-lookup"><span data-stu-id="63e12-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="63e12-218">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="63e12-218">Log level</span></span>

<span data-ttu-id="63e12-219">Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="63e12-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="63e12-220">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="63e12-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="63e12-221">Můžete například zapsat `Information` protokol, pokud metoda končí normálně a `Warning` protokol, když metoda vrátí stavový kód *404 Nenalezeno* .</span><span class="sxs-lookup"><span data-stu-id="63e12-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="63e12-222">Následující kód vytváří `Information` a `Warning` protokoluje:</span><span class="sxs-lookup"><span data-stu-id="63e12-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="63e12-223">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="63e12-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="63e12-224">Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="63e12-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="63e12-225">Parametry metody jsou vysvětleny v [části Šablona zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="63e12-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="63e12-226">Metody protokolu, které zahrnují úroveň v názvu metody (například `LogInformation` a `LogWarning`), jsou [metody rozšíření pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="63e12-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="63e12-227">Tyto metody volají `Log` metodu, která přebírá `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="63e12-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="63e12-228">`Log` Metodu můžete zavolat přímo místo jedné z těchto rozšiřujících metod, ale syntaxe je poměrně složitá.</span><span class="sxs-lookup"><span data-stu-id="63e12-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="63e12-229">Další informace naleznete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a ve [zdrojovém kódu rozšíření protokolovacího](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)nástroje.</span><span class="sxs-lookup"><span data-stu-id="63e12-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="63e12-230">ASP.NET Core definuje následující úrovně protokolu seřazené od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="63e12-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="63e12-231">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="63e12-231">Trace = 0</span></span>

  <span data-ttu-id="63e12-232">Informace, které jsou obvykle užitečné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="63e12-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="63e12-233">Tyto zprávy mohou obsahovat citlivá data aplikací, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="63e12-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="63e12-234">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="63e12-234">*Disabled by default.*</span></span>

* <span data-ttu-id="63e12-235">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="63e12-235">Debug = 1</span></span>

  <span data-ttu-id="63e12-236">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="63e12-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="63e12-237">Příklad: `Entering method Configure with flag set to true.` povolte `Debug` protokoly úrovně v produkčním prostředí jenom při řešení potíží, protože se jedná o velký objem protokolů.</span><span class="sxs-lookup"><span data-stu-id="63e12-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="63e12-238">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="63e12-238">Information = 2</span></span>

  <span data-ttu-id="63e12-239">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="63e12-240">Tyto protokoly obvykle mají nějakou dlouhodobou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="63e12-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="63e12-241">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="63e12-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="63e12-242">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="63e12-242">Warning = 3</span></span>

  <span data-ttu-id="63e12-243">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="63e12-244">Ty můžou zahrnovat chyby nebo jiné podmínky, které nezpůsobí zastavení aplikace, ale možná bude nutné je prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="63e12-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="63e12-245">Zpracování výjimek je běžné místo pro použití úrovně `Warning` protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="63e12-246">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="63e12-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="63e12-247">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="63e12-247">Error = 4</span></span>

  <span data-ttu-id="63e12-248">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="63e12-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="63e12-249">Tyto zprávy indikují selhání aktuální aktivity nebo operace (jako je aktuální požadavek HTTP), ne při selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="63e12-250">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="63e12-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="63e12-251">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="63e12-251">Critical = 5</span></span>

  <span data-ttu-id="63e12-252">Chyby, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="63e12-252">For failures that require immediate attention.</span></span> <span data-ttu-id="63e12-253">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="63e12-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="63e12-254">Úroveň protokolu můžete použít k určení, kolik výstupu protokolu se zapisuje do konkrétního úložného média nebo okna pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="63e12-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="63e12-255">Příklad:</span><span class="sxs-lookup"><span data-stu-id="63e12-255">For example:</span></span>

* <span data-ttu-id="63e12-256">V produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="63e12-256">In production:</span></span>
  * <span data-ttu-id="63e12-257">Protokolování na úrovni `Trace` přes `Information` vytváří velké množství podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="63e12-258">Pokud chcete řídit náklady a nepřekročit limity úložiště dat, `Trace` protokolujte zprávy na `Information` úrovni do úložiště dat s nízkými náklady na velké objemy dat.</span><span class="sxs-lookup"><span data-stu-id="63e12-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="63e12-259">Přihlášení `Warning` přes `Critical` úrovně obvykle vytváří méně a menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="63e12-260">Proto náklady a limity úložiště většinou nejsou obavy, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="63e12-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="63e12-261">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="63e12-261">During development:</span></span>
  * <span data-ttu-id="63e12-262">Umožňuje `Warning` protokolovat `Critical` zprávy do konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="63e12-263">Při `Trace` odstraňování `Information` potíží přidejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="63e12-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="63e12-264">Část [filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovateli zpracovává.</span><span class="sxs-lookup"><span data-stu-id="63e12-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="63e12-265">ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="63e12-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="63e12-266">Příklady protokolů výše v tomto článku vyloučily nižší `Information` úrovně protokolů, takže se `Debug` nevytvořily žádné protokoly ani `Trace` protokoly na úrovni.</span><span class="sxs-lookup"><span data-stu-id="63e12-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="63e12-267">Tady je příklad protokolů konzoly vytvořených spuštěním ukázkové aplikace nakonfigurované pro zobrazení `Debug` protokolů:</span><span class="sxs-lookup"><span data-stu-id="63e12-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="63e12-268">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="63e12-268">Log event ID</span></span>

<span data-ttu-id="63e12-269">Každý protokol může určovat *ID události*.</span><span class="sxs-lookup"><span data-stu-id="63e12-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="63e12-270">Tato ukázková aplikace používá lokálně definovanou `LoggingEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="63e12-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="63e12-271">ID události přidružuje sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="63e12-271">An event ID associates a set of events.</span></span> <span data-ttu-id="63e12-272">Například všechny protokoly související se zobrazením seznamu položek na stránce může být 1001.</span><span class="sxs-lookup"><span data-stu-id="63e12-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="63e12-273">Zprostředkovatel protokolování může ukládat ID události v poli ID, ve zprávě protokolování nebo vůbec ne.</span><span class="sxs-lookup"><span data-stu-id="63e12-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="63e12-274">Zprostředkovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="63e12-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="63e12-275">Poskytovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="63e12-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="63e12-276">Šablona zprávy protokolu</span><span class="sxs-lookup"><span data-stu-id="63e12-276">Log message template</span></span>

<span data-ttu-id="63e12-277">Každý protokol Určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="63e12-277">Each log specifies a message template.</span></span> <span data-ttu-id="63e12-278">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="63e12-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="63e12-279">Použijte názvy zástupných symbolů, nikoli čísla.</span><span class="sxs-lookup"><span data-stu-id="63e12-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="63e12-280">Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry slouží k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="63e12-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="63e12-281">V následujícím kódu si všimněte, že názvy parametrů jsou v šabloně zprávy mimo pořadí:</span><span class="sxs-lookup"><span data-stu-id="63e12-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="63e12-282">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v posloupnosti:</span><span class="sxs-lookup"><span data-stu-id="63e12-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="63e12-283">Protokolovací rozhraní funguje tímto způsobem, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="63e12-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="63e12-284">Samotné argumenty jsou předány do protokolovacího systému, nikoli pouze ze šablony formátované zprávy.</span><span class="sxs-lookup"><span data-stu-id="63e12-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="63e12-285">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="63e12-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="63e12-286">Předpokládejme například, že volání metody protokolovacího nástroje vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="63e12-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="63e12-287">Pokud odesíláte protokoly do služby Azure Table Storage, Každá entita tabulky Azure může mít `ID` vlastnosti a `RequestTime` , což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="63e12-288">Dotaz může najít všechny protokoly v rámci určitého `RequestTime` rozsahu, aniž by došlo k analýze časového limitu textové zprávy.</span><span class="sxs-lookup"><span data-stu-id="63e12-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="63e12-289">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="63e12-289">Logging exceptions</span></span>

<span data-ttu-id="63e12-290">Metody protokolovacího nástroje mají přetížení, které umožňují předat výjimku, jak je uvedeno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="63e12-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="63e12-291">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="63e12-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="63e12-292">Tady je příklad výstupu poskytovatele ladění z kódu uvedeného výše.</span><span class="sxs-lookup"><span data-stu-id="63e12-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="63e12-293">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="63e12-293">Log filtering</span></span>

<span data-ttu-id="63e12-294">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny poskytovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="63e12-295">Všechny protokoly nižší než minimální úroveň nebudou tomuto poskytovateli předány, takže se nebudou zobrazovat ani ukládat.</span><span class="sxs-lookup"><span data-stu-id="63e12-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="63e12-296">Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="63e12-297">Celočíselná hodnota `LogLevel.None` je 6, což je více než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="63e12-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="63e12-298">Vytvořit pravidla filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="63e12-298">Create filter rules in configuration</span></span>

<span data-ttu-id="63e12-299">Kód šablony projektu volá `CreateDefaultBuilder` k nastavení protokolování pro poskytovatele, konzolu, ladění a EventSource (ASP.NET Core 2,2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="63e12-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="63e12-300">`CreateDefaultBuilder` Metoda nastaví protokolování tak, aby hledalo konfiguraci v `Logging` části, jak je vysvětleno [výše v tomto článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="63e12-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="63e12-301">Konfigurační data určují minimální úrovně protokolu podle poskytovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="63e12-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="63e12-302">Tento kód JSON vytvoří šest pravidel filtru: jeden pro poskytovatele ladění, čtyři pro poskytovatele konzoly a jeden pro všechny poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="63e12-303">Při vytvoření `ILogger` objektu se vybere jedno pravidlo pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="63e12-304">Filtrovat pravidla v kódu</span><span class="sxs-lookup"><span data-stu-id="63e12-304">Filter rules in code</span></span>

<span data-ttu-id="63e12-305">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="63e12-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="63e12-306">Druhý `AddFilter` Určuje poskytovatele ladění pomocí jeho názvu typu.</span><span class="sxs-lookup"><span data-stu-id="63e12-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="63e12-307">První `AddFilter` se vztahuje na všechny poskytovatele, protože neurčuje typ poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="63e12-308">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="63e12-308">How filtering rules are applied</span></span>

<span data-ttu-id="63e12-309">Konfigurační data a `AddFilter` kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="63e12-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="63e12-310">Prvních šest přicházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="63e12-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="63e12-311">Číslo</span><span class="sxs-lookup"><span data-stu-id="63e12-311">Number</span></span> | <span data-ttu-id="63e12-312">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="63e12-312">Provider</span></span>      | <span data-ttu-id="63e12-313">Kategorie, které začínají na...</span><span class="sxs-lookup"><span data-stu-id="63e12-313">Categories that begin with ...</span></span>          | <span data-ttu-id="63e12-314">Minimální úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="63e12-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="63e12-315">1</span><span class="sxs-lookup"><span data-stu-id="63e12-315">1</span></span>      | <span data-ttu-id="63e12-316">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-316">Debug</span></span>         | <span data-ttu-id="63e12-317">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="63e12-317">All categories</span></span>                          | <span data-ttu-id="63e12-318">Informace</span><span class="sxs-lookup"><span data-stu-id="63e12-318">Information</span></span>       |
| <span data-ttu-id="63e12-319">2</span><span class="sxs-lookup"><span data-stu-id="63e12-319">2</span></span>      | <span data-ttu-id="63e12-320">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-320">Console</span></span>       | <span data-ttu-id="63e12-321">Microsoft. AspNetCore. Mvc. Razor. Internal</span><span class="sxs-lookup"><span data-stu-id="63e12-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="63e12-322">Upozornění</span><span class="sxs-lookup"><span data-stu-id="63e12-322">Warning</span></span>           |
| <span data-ttu-id="63e12-323">3</span><span class="sxs-lookup"><span data-stu-id="63e12-323">3</span></span>      | <span data-ttu-id="63e12-324">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-324">Console</span></span>       | <span data-ttu-id="63e12-325">Microsoft. AspNetCore. Mvc. Razor. Razor</span><span class="sxs-lookup"><span data-stu-id="63e12-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="63e12-326">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-326">Debug</span></span>             |
| <span data-ttu-id="63e12-327">4</span><span class="sxs-lookup"><span data-stu-id="63e12-327">4</span></span>      | <span data-ttu-id="63e12-328">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-328">Console</span></span>       | <span data-ttu-id="63e12-329">Microsoft. AspNetCore. Mvc. Razor</span><span class="sxs-lookup"><span data-stu-id="63e12-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="63e12-330">Chyba</span><span class="sxs-lookup"><span data-stu-id="63e12-330">Error</span></span>             |
| <span data-ttu-id="63e12-331">5</span><span class="sxs-lookup"><span data-stu-id="63e12-331">5</span></span>      | <span data-ttu-id="63e12-332">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-332">Console</span></span>       | <span data-ttu-id="63e12-333">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="63e12-333">All categories</span></span>                          | <span data-ttu-id="63e12-334">Informace</span><span class="sxs-lookup"><span data-stu-id="63e12-334">Information</span></span>       |
| <span data-ttu-id="63e12-335">6</span><span class="sxs-lookup"><span data-stu-id="63e12-335">6</span></span>      | <span data-ttu-id="63e12-336">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="63e12-336">All providers</span></span> | <span data-ttu-id="63e12-337">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="63e12-337">All categories</span></span>                          | <span data-ttu-id="63e12-338">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-338">Debug</span></span>             |
| <span data-ttu-id="63e12-339">7</span><span class="sxs-lookup"><span data-stu-id="63e12-339">7</span></span>      | <span data-ttu-id="63e12-340">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="63e12-340">All providers</span></span> | <span data-ttu-id="63e12-341">Systém</span><span class="sxs-lookup"><span data-stu-id="63e12-341">System</span></span>                                  | <span data-ttu-id="63e12-342">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-342">Debug</span></span>             |
| <span data-ttu-id="63e12-343">8</span><span class="sxs-lookup"><span data-stu-id="63e12-343">8</span></span>      | <span data-ttu-id="63e12-344">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-344">Debug</span></span>         | <span data-ttu-id="63e12-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="63e12-345">Microsoft</span></span>                               | <span data-ttu-id="63e12-346">Trasování</span><span class="sxs-lookup"><span data-stu-id="63e12-346">Trace</span></span>             |

<span data-ttu-id="63e12-347">Při vytvoření `ILogger` objektu vybere `ILoggerFactory` objekt jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="63e12-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="63e12-348">Všechny zprávy napsané `ILogger` instancí jsou filtrovány podle vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="63e12-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="63e12-349">V dostupných pravidlech se vybere nejpřesnější pravidlo pro jednotlivé dvojice zprostředkovatel a kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="63e12-350">Následující algoritmus se používá pro každého poskytovatele při `ILogger` vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="63e12-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="63e12-351">Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem.</span><span class="sxs-lookup"><span data-stu-id="63e12-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="63e12-352">Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="63e12-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="63e12-353">Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="63e12-354">Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="63e12-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="63e12-355">Pokud je vybráno více pravidel, vezměte **Poslední** z nich.</span><span class="sxs-lookup"><span data-stu-id="63e12-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="63e12-356">Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="63e12-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="63e12-357">V předchozím seznamu pravidel Předpokládejme, že vytvoříte `ILogger` objekt pro kategorii "Microsoft. AspNetCore. Mvc. Razor. RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="63e12-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="63e12-358">Pro poskytovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="63e12-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="63e12-359">Pravidlo 8 je nejvíce specifické, takže je to ten vybraný.</span><span class="sxs-lookup"><span data-stu-id="63e12-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="63e12-360">Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="63e12-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="63e12-361">Pravidlo 3 je nejvíce specifické.</span><span class="sxs-lookup"><span data-stu-id="63e12-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="63e12-362">Výsledná `ILogger` instance odesílá protokoly `Trace` úrovně a výše do poskytovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="63e12-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="63e12-363">Protokoly `Debug` úrovně a výše jsou odesílány poskytovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="63e12-364">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="63e12-364">Provider aliases</span></span>

<span data-ttu-id="63e12-365">Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu.</span><span class="sxs-lookup"><span data-stu-id="63e12-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="63e12-366">Pro předdefinované poskytovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="63e12-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="63e12-367">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-367">Console</span></span>
* <span data-ttu-id="63e12-368">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-368">Debug</span></span>
* <span data-ttu-id="63e12-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="63e12-369">EventSource</span></span>
* <span data-ttu-id="63e12-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="63e12-370">EventLog</span></span>
* <span data-ttu-id="63e12-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="63e12-371">TraceSource</span></span>
* <span data-ttu-id="63e12-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="63e12-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="63e12-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="63e12-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="63e12-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="63e12-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="63e12-375">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="63e12-375">Default minimum level</span></span>

<span data-ttu-id="63e12-376">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že se pro daného zprostředkovatele a kategorii nepoužijí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="63e12-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="63e12-377">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="63e12-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="63e12-378">Pokud nenastavíte explicitně minimální úroveň, výchozí hodnota je `Information`, což znamená, že `Trace` protokoly a `Debug` budou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="63e12-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="63e12-379">Funkce filtrování</span><span class="sxs-lookup"><span data-stu-id="63e12-379">Filter functions</span></span>

<span data-ttu-id="63e12-380">Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="63e12-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="63e12-381">Kód ve funkci má přístup k typu poskytovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="63e12-382">Příklad:</span><span class="sxs-lookup"><span data-stu-id="63e12-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="63e12-383">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="63e12-383">System categories and levels</span></span>

<span data-ttu-id="63e12-384">Tady je několik kategorií používaných ASP.NET Core a Entity Framework Core s poznámkami o tom, jaké protokoly se mají od nich očekávat:</span><span class="sxs-lookup"><span data-stu-id="63e12-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="63e12-385">Kategorie</span><span class="sxs-lookup"><span data-stu-id="63e12-385">Category</span></span>                            | <span data-ttu-id="63e12-386">Poznámky</span><span class="sxs-lookup"><span data-stu-id="63e12-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="63e12-387">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="63e12-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="63e12-388">Obecná diagnostika ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="63e12-389">Microsoft. AspNetCore. DataProtection</span><span class="sxs-lookup"><span data-stu-id="63e12-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="63e12-390">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="63e12-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="63e12-391">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="63e12-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="63e12-392">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="63e12-392">Hosts allowed.</span></span> |
| <span data-ttu-id="63e12-393">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="63e12-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="63e12-394">Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění.</span><span class="sxs-lookup"><span data-stu-id="63e12-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="63e12-395">Která hostující spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="63e12-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="63e12-396">Microsoft. AspNetCore. Mvc</span><span class="sxs-lookup"><span data-stu-id="63e12-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="63e12-397">Diagnostika MVC a Razor</span><span class="sxs-lookup"><span data-stu-id="63e12-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="63e12-398">Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="63e12-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="63e12-399">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="63e12-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="63e12-400">Informace o shodě trasy.</span><span class="sxs-lookup"><span data-stu-id="63e12-400">Route matching information.</span></span> |
| <span data-ttu-id="63e12-401">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="63e12-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="63e12-402">Připojení – spouštění, zastavování a udržování reakcí na Alive.</span><span class="sxs-lookup"><span data-stu-id="63e12-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="63e12-403">Informace o certifikátu HTTPS</span><span class="sxs-lookup"><span data-stu-id="63e12-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="63e12-404">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="63e12-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="63e12-405">Soubory byly obsluhovány.</span><span class="sxs-lookup"><span data-stu-id="63e12-405">Files served.</span></span> |
| <span data-ttu-id="63e12-406">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="63e12-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="63e12-407">Obecná diagnostika Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="63e12-408">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="63e12-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="63e12-409">Rozsahy protokolů</span><span class="sxs-lookup"><span data-stu-id="63e12-409">Log scopes</span></span>

 <span data-ttu-id="63e12-410">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="63e12-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="63e12-411">Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="63e12-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="63e12-412">Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="63e12-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="63e12-413">Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodou a trvá, dokud nebude uvolněn.</span><span class="sxs-lookup"><span data-stu-id="63e12-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="63e12-414">Použití oboru zabalením volání protokolovacího nástroje `using` v bloku:</span><span class="sxs-lookup"><span data-stu-id="63e12-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="63e12-415">Následující kód umožňuje obory pro poskytovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="63e12-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="63e12-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="63e12-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="63e12-417">Aby bylo `IncludeScopes` možné povolit protokolování na základě oboru, je nutné nakonfigurovat možnost protokolovacího nástroje konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="63e12-418">Informace o konfiguraci najdete v části věnované [konfiguraci](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="63e12-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="63e12-419">Každá zpráva protokolu obsahuje informace s vymezeným oborem:</span><span class="sxs-lookup"><span data-stu-id="63e12-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="63e12-420">Vestavění zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="63e12-420">Built-in logging providers</span></span>

<span data-ttu-id="63e12-421">ASP.NET Core dodává následující poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="63e12-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="63e12-422">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="63e12-423">Ladí</span><span class="sxs-lookup"><span data-stu-id="63e12-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="63e12-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="63e12-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="63e12-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="63e12-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="63e12-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="63e12-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="63e12-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="63e12-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="63e12-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="63e12-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="63e12-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="63e12-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="63e12-430">Informace o stdout a protokolování ladění pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection></span><span class="sxs-lookup"><span data-stu-id="63e12-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="63e12-431">Poskytovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="63e12-431">Console provider</span></span>

<span data-ttu-id="63e12-432">Balíček poskytovatele [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="63e12-433">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="63e12-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="63e12-434">Poskytovatel ladění</span><span class="sxs-lookup"><span data-stu-id="63e12-434">Debug provider</span></span>

<span data-ttu-id="63e12-435">Balíček poskytovatele [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` volání metody).</span><span class="sxs-lookup"><span data-stu-id="63e12-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="63e12-436">V systému Linux tento poskytovatel zapisuje protokoly do */var/log/Message*.</span><span class="sxs-lookup"><span data-stu-id="63e12-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="63e12-437">Zprostředkovatel zdroje událostí</span><span class="sxs-lookup"><span data-stu-id="63e12-437">Event Source provider</span></span>

<span data-ttu-id="63e12-438">Balíček zprostředkovatele [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje do zdroje událostí pro různé platformy s názvem `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="63e12-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="63e12-439">Ve Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="63e12-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="63e12-440">Poskytovatel zdroje událostí se přidá automaticky, když `CreateDefaultBuilder` se volá k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="63e12-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="63e12-441">nástroje pro trasování dotnet</span><span class="sxs-lookup"><span data-stu-id="63e12-441">dotnet trace tooling</span></span>

<span data-ttu-id="63e12-442">Nástroj [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) je globální nástroj CLI pro různé platformy, který umožňuje shromažďování trasování .NET Core pro běžící proces.</span><span class="sxs-lookup"><span data-stu-id="63e12-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="63e12-443">Nástroj shromažďuje <xref:Microsoft.Extensions.Logging.EventSource> data zprostředkovatele pomocí <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="63e12-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="63e12-444">Nainstalujte nástroje pro trasování dotnet pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="63e12-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="63e12-445">Shromažďování trasování z aplikace pomocí trasovacích nástrojů dotnet:</span><span class="sxs-lookup"><span data-stu-id="63e12-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="63e12-446">Pokud aplikace nevytváří hostitele pomocí `CreateDefaultBuilder`, přidejte [poskytovatele zdroje událostí](#event-source-provider) do konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="63e12-447">Spusťte aplikaci pomocí `dotnet run` příkazu.</span><span class="sxs-lookup"><span data-stu-id="63e12-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="63e12-448">Určení identifikátoru procesu (PID) aplikace .NET Core:</span><span class="sxs-lookup"><span data-stu-id="63e12-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="63e12-449">Ve Windows použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="63e12-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="63e12-450">Správce úloh (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="63e12-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="63e12-451">tasklist – příkaz</span><span class="sxs-lookup"><span data-stu-id="63e12-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="63e12-452">Příkaz PowerShellu Get-Process</span><span class="sxs-lookup"><span data-stu-id="63e12-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="63e12-453">V systému Linux použijte [příkaz pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="63e12-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="63e12-454">Vyhledejte kód PID pro proces, který má stejný název jako sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="63e12-455">Spusťte `dotnet trace` příkaz.</span><span class="sxs-lookup"><span data-stu-id="63e12-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="63e12-456">Obecná syntaxe příkazu:</span><span class="sxs-lookup"><span data-stu-id="63e12-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="63e12-457">Při použití příkazového prostředí PowerShellu uzavřete `--providers` hodnotu do jednoduchých uvozovek (`'`):</span><span class="sxs-lookup"><span data-stu-id="63e12-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="63e12-458">Na platformách jiných než Windows přidejte `-f speedscope` možnost změny formátu výstupní trasovacího souboru na. `speedscope`</span><span class="sxs-lookup"><span data-stu-id="63e12-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="63e12-459">Klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="63e12-459">Keyword</span></span> | <span data-ttu-id="63e12-460">Popis</span><span class="sxs-lookup"><span data-stu-id="63e12-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="63e12-461">1</span><span class="sxs-lookup"><span data-stu-id="63e12-461">1</span></span>       | <span data-ttu-id="63e12-462">Protokoluje meta události týkající `LoggingEventSource`se.</span><span class="sxs-lookup"><span data-stu-id="63e12-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="63e12-463">Neprotokoluje události `ILogger`z).</span><span class="sxs-lookup"><span data-stu-id="63e12-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="63e12-464">2</span><span class="sxs-lookup"><span data-stu-id="63e12-464">2</span></span>       | <span data-ttu-id="63e12-465">Zapne `Message` událost při `ILogger.Log()` volání metody.</span><span class="sxs-lookup"><span data-stu-id="63e12-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="63e12-466">Poskytuje informace v programovém (neformátovaném) způsobu.</span><span class="sxs-lookup"><span data-stu-id="63e12-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="63e12-467">4</span><span class="sxs-lookup"><span data-stu-id="63e12-467">4</span></span>       | <span data-ttu-id="63e12-468">Zapne `FormatMessage` událost při `ILogger.Log()` volání metody.</span><span class="sxs-lookup"><span data-stu-id="63e12-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="63e12-469">Poskytuje formátovanou verzi řetězce informací.</span><span class="sxs-lookup"><span data-stu-id="63e12-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="63e12-470">8</span><span class="sxs-lookup"><span data-stu-id="63e12-470">8</span></span>       | <span data-ttu-id="63e12-471">Zapne `MessageJson` událost při `ILogger.Log()` volání metody.</span><span class="sxs-lookup"><span data-stu-id="63e12-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="63e12-472">Poskytuje reprezentace argumentů ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="63e12-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="63e12-473">Úroveň události</span><span class="sxs-lookup"><span data-stu-id="63e12-473">Event Level</span></span> | <span data-ttu-id="63e12-474">Popis</span><span class="sxs-lookup"><span data-stu-id="63e12-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="63e12-475">0</span><span class="sxs-lookup"><span data-stu-id="63e12-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="63e12-476">1</span><span class="sxs-lookup"><span data-stu-id="63e12-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="63e12-477">2</span><span class="sxs-lookup"><span data-stu-id="63e12-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="63e12-478">3</span><span class="sxs-lookup"><span data-stu-id="63e12-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="63e12-479">4</span><span class="sxs-lookup"><span data-stu-id="63e12-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="63e12-480">5</span><span class="sxs-lookup"><span data-stu-id="63e12-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="63e12-481">`FilterSpecs`položky pro `{Logger Category}` a `{Event Level}` reprezentují další podmínky filtrování protokolů.</span><span class="sxs-lookup"><span data-stu-id="63e12-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="63e12-482">Jednotlivé `FilterSpecs` položky oddělte středníkem`;`().</span><span class="sxs-lookup"><span data-stu-id="63e12-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="63e12-483">Příklad použití příkazového prostředí systému Windows (**bez** jednoduchých uvozovek kolem `--providers` hodnoty):</span><span class="sxs-lookup"><span data-stu-id="63e12-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="63e12-484">Předchozí příkaz se aktivuje:</span><span class="sxs-lookup"><span data-stu-id="63e12-484">The preceding command activates:</span></span>

   * <span data-ttu-id="63e12-485">Protokolovací nástroj zdroje událostí pro vytváření formátovaných řetězců`4`() pro chyby`2`().</span><span class="sxs-lookup"><span data-stu-id="63e12-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="63e12-486">`Microsoft.AspNetCore.Hosting`protokolování na úrovni `Informational` protokolování (`4`).</span><span class="sxs-lookup"><span data-stu-id="63e12-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="63e12-487">Kliknutím na klávesu ENTER nebo stisknutím kombinace kláves CTRL + C zastavte nástroje pro trasování dotnet.</span><span class="sxs-lookup"><span data-stu-id="63e12-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="63e12-488">Trasování je uloženo s názvem *Trace. nettrace* ve složce, ve které je `dotnet trace` příkaz spuštěn.</span><span class="sxs-lookup"><span data-stu-id="63e12-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="63e12-489">Otevřete trasování pomocí [PerfView](#perfview).</span><span class="sxs-lookup"><span data-stu-id="63e12-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="63e12-490">Otevřete soubor *Trace. nettrace* a prozkoumejte události trasování.</span><span class="sxs-lookup"><span data-stu-id="63e12-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="63e12-491">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="63e12-491">For more information, see:</span></span>

* <span data-ttu-id="63e12-492">[Trasování pro nástroj Analýza výkonu (dotnet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentace k .NET Core)</span><span class="sxs-lookup"><span data-stu-id="63e12-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="63e12-493">[Trasování pro nástroj Analýza výkonu (dotnet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentace k úložišti GitHub/Diagnostika)</span><span class="sxs-lookup"><span data-stu-id="63e12-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="63e12-494">[LoggingEventSource – třída](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (prohlížeč rozhraní .NET API)</span><span class="sxs-lookup"><span data-stu-id="63e12-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="63e12-495">[LoggingEventSource reference Source (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Chcete-li získat zdroj odkazů pro jinou verzi, změňte větev na `release/{Version}`, kde `{Version}` je požadovaná verze ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="63e12-496">[PerfView](#perfview) &ndash; užitečné pro zobrazení trasování zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="63e12-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="63e12-497">PerfView</span><span class="sxs-lookup"><span data-stu-id="63e12-497">Perfview</span></span>

<span data-ttu-id="63e12-498">K shromažďování a zobrazování protokolů použijte [Nástroj PerfView](https://github.com/Microsoft/perfview) .</span><span class="sxs-lookup"><span data-stu-id="63e12-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="63e12-499">Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="63e12-500">Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte `*Microsoft-Extensions-Logging` řetězec do seznamu **další poskytovatelé** .</span><span class="sxs-lookup"><span data-stu-id="63e12-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="63e12-501">(Na začátku řetězce nechybíš hvězdičku.)</span><span class="sxs-lookup"><span data-stu-id="63e12-501">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="63e12-503">Zprostředkovatel událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="63e12-503">Windows EventLog provider</span></span>

<span data-ttu-id="63e12-504">Balíček poskytovatele [Microsoft. Extensions. log. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="63e12-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="63e12-505">[AddEventLog přetížení](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="63e12-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="63e12-506">Pokud `null` není zadaný, použijí se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="63e12-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="63e12-507">`LogName`&ndash; "Aplikace"</span><span class="sxs-lookup"><span data-stu-id="63e12-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="63e12-508">`SourceName`&ndash; ".NET runtime"</span><span class="sxs-lookup"><span data-stu-id="63e12-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="63e12-509">`MachineName`&ndash; místní počítač</span><span class="sxs-lookup"><span data-stu-id="63e12-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="63e12-510">Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level).</span><span class="sxs-lookup"><span data-stu-id="63e12-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="63e12-511">Chcete-li protokolovat `Warning`události nižší než, explicitně nastavte úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="63e12-512">Do souboru *appSettings. JSON* přidejte například následující:</span><span class="sxs-lookup"><span data-stu-id="63e12-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="63e12-513">Poskytovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="63e12-513">TraceSource provider</span></span>

<span data-ttu-id="63e12-514">Balíček poskytovatele [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="63e12-515">[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="63e12-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="63e12-516">Aby bylo možné používat tohoto poskytovatele, aplikace musí běžet na .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="63e12-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="63e12-517">Poskytovatel může směrovat zprávy na celou řadu [posluchačů](/dotnet/framework/debug-trace-profile/trace-listeners), jako je <xref:System.Diagnostics.TextWriterTraceListener> například použití v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63e12-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="63e12-518">Poskytovatel Azure App Service</span><span class="sxs-lookup"><span data-stu-id="63e12-518">Azure App Service provider</span></span>

<span data-ttu-id="63e12-519">Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.</span><span class="sxs-lookup"><span data-stu-id="63e12-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="63e12-520">Balíček poskytovatele není zahrnutý ve sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63e12-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="63e12-521">Chcete-li použít poskytovatele, přidejte do projektu balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="63e12-522">Chcete-li konfigurovat nastavení zprostředkovatele <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> , <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>použijte a, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="63e12-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="63e12-523">Když nasadíte aplikaci do App Service, aplikace respektuje nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) stránky **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="63e12-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="63e12-524">Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="63e12-525">**Protokolování aplikace (systém souborů)**</span><span class="sxs-lookup"><span data-stu-id="63e12-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="63e12-526">**Protokolování aplikace (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="63e12-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="63e12-527">Výchozím umístěním pro soubory protokolu je složka *\\aplikace D:\\Home\\LogFiles* a výchozí název souboru je *Diagnostics-YYYYMMDD. txt*.</span><span class="sxs-lookup"><span data-stu-id="63e12-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="63e12-528">Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="63e12-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="63e12-529">Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/MM/DD/hh/{GUID}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="63e12-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="63e12-530">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="63e12-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="63e12-531">Nemá žádný vliv, pokud se projekt spouští místně&mdash;, nepíše do místních souborů nebo místního vývojového úložiště pro objekty blob.</span><span class="sxs-lookup"><span data-stu-id="63e12-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="63e12-532">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="63e12-532">Azure log streaming</span></span>

<span data-ttu-id="63e12-533">Streamování protokolů Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="63e12-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="63e12-534">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="63e12-534">The app server</span></span>
* <span data-ttu-id="63e12-535">Webový server</span><span class="sxs-lookup"><span data-stu-id="63e12-535">The web server</span></span>
* <span data-ttu-id="63e12-536">Trasování chybných požadavků</span><span class="sxs-lookup"><span data-stu-id="63e12-536">Failed request tracing</span></span>

<span data-ttu-id="63e12-537">Konfigurace streamování protokolů Azure:</span><span class="sxs-lookup"><span data-stu-id="63e12-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="63e12-538">Na stránce portálu vaší aplikace přejděte na stránku **protokoly App Service** .</span><span class="sxs-lookup"><span data-stu-id="63e12-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="63e12-539">Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="63e12-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="63e12-540">Vyberte **úroveň**protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-540">Choose the log **Level**.</span></span> <span data-ttu-id="63e12-541">Toto nastavení platí jenom pro streamování protokolů Azure, ne pro jiné poskytovatele protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63e12-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="63e12-542">Pokud chcete zobrazit zprávy aplikace, přejděte na stránku **streamu protokolu** .</span><span class="sxs-lookup"><span data-stu-id="63e12-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="63e12-543">Protokolují ho aplikace přes `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63e12-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="63e12-544">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="63e12-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="63e12-545">Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="63e12-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="63e12-546">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie.</span><span class="sxs-lookup"><span data-stu-id="63e12-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="63e12-547">Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.</span><span class="sxs-lookup"><span data-stu-id="63e12-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="63e12-548">Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="63e12-549">Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="63e12-550">Nepoužívejte&mdash;balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , který je pro ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="63e12-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="63e12-551">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="63e12-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="63e12-552">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="63e12-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="63e12-553">[Application Insights pro ASP.NET Core aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou řadu Application Insights telemetrie spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="63e12-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="63e12-554">[ApplicationInsightsLoggerProvider pro protokoly .NET Core ILogger](/azure/azure-monitor/app/ilogger) – začněte sem, pokud chcete implementovat poskytovatele protokolování bez zbytku Application Insights telemetrie.</span><span class="sxs-lookup"><span data-stu-id="63e12-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="63e12-555">[Application Insights adaptéry protokolování](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="63e12-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="63e12-556">[Instalace, konfigurace a inicializace Application Insights sady SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="63e12-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="63e12-557">Zprostředkovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="63e12-557">Third-party logging providers</span></span>

<span data-ttu-id="63e12-558">Protokolovací architektury třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="63e12-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="63e12-559">[elmah.IO](https://elmah.io/) ([úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="63e12-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="63e12-560">[GELF](https://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="63e12-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="63e12-561">[JSNLog](https://jsnlog.com/) ([úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="63e12-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="63e12-562">[KissLog.NET](https://kisslog.net/) ([úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="63e12-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="63e12-563">[Log4Net](https://logging.apache.org/log4net/) ([úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="63e12-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="63e12-564">[Loggr](https://loggr.net/) ([úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="63e12-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="63e12-565">[NLOG](https://nlog-project.org/) ([úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="63e12-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="63e12-566">[Sentry](https://sentry.io/welcome/) ([úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="63e12-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="63e12-567">[Serilog](https://serilog.net/) ([úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="63e12-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="63e12-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="63e12-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="63e12-569">Některé architektury třetích stran můžou provádět [sémantické protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="63e12-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="63e12-570">Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="63e12-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="63e12-571">Přidejte do projektu balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="63e12-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="63e12-572">Zavolejte metodu `ILoggerFactory` rozšíření poskytovanou protokolovacím rozhraním.</span><span class="sxs-lookup"><span data-stu-id="63e12-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="63e12-573">Další informace najdete v dokumentaci pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="63e12-574">Microsoft nepodporuje zprostředkovatele protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="63e12-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63e12-575">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="63e12-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="63e12-576">Tím, že [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="63e12-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="63e12-577">.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="63e12-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="63e12-578">V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="63e12-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="63e12-579">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63e12-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="63e12-580">Přidat zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="63e12-580">Add providers</span></span>

<span data-ttu-id="63e12-581">Zprostředkovatel protokolování zobrazuje nebo ukládá protokoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="63e12-582">Například poskytovatel konzoly zobrazuje protokoly v konzole nástroje a poskytovatel Azure Application Insights je ukládá do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="63e12-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="63e12-583">Protokoly lze odesílat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="63e12-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="63e12-584">Chcete-li přidat poskytovatele, zavolejte metodu `Add{provider name}` rozšíření poskytovatele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="63e12-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="63e12-585">Předchozí kód vyžaduje odkazy na `Microsoft.Extensions.Logging` a. `Microsoft.Extensions.Configuration`</span><span class="sxs-lookup"><span data-stu-id="63e12-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="63e12-586">Výchozí volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>šablony projektu, které přidává následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="63e12-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="63e12-587">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-587">Console</span></span>
* <span data-ttu-id="63e12-588">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-588">Debug</span></span>
* <span data-ttu-id="63e12-589">EventSource (počínaje ASP.NET Core 2,2)</span><span class="sxs-lookup"><span data-stu-id="63e12-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="63e12-590">Pokud používáte `CreateDefaultBuilder`, můžete výchozí poskytovatele nahradit vašimi vlastními možnostmi.</span><span class="sxs-lookup"><span data-stu-id="63e12-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="63e12-591">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte poskytovatele, které chcete.</span><span class="sxs-lookup"><span data-stu-id="63e12-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="63e12-592">Přečtěte si další informace o [integrovaných poskytovatelích protokolování](#built-in-logging-providers) a [zprostředkovatelích protokolování třetích stran](#third-party-logging-providers) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="63e12-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="63e12-593">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="63e12-593">Create logs</span></span>

<span data-ttu-id="63e12-594">K vytváření protokolů použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="63e12-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="63e12-595">V rámci webové aplikace nebo hostované služby Získejte `ILogger` od vložení závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="63e12-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="63e12-596">V konzolových aplikacích bez hostitele použijte `LoggerFactory` k vytvoření. `ILogger`</span><span class="sxs-lookup"><span data-stu-id="63e12-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="63e12-597">Následující ASP.NET Core příklad vytvoří protokolovací nástroj s `TodoApiSample.Pages.AboutModel` jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="63e12-598">*Kategorie* protokolu je řetězec, který je spojený s každým protokolem.</span><span class="sxs-lookup"><span data-stu-id="63e12-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="63e12-599">`ILogger<T>` Instance poskytovaná di vytvoří protokoly, které mají plně kvalifikovaný název typu `T` jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="63e12-600">V následujících příkladech ASP.NET Core a konzolových aplikací se k vytváření protokolů s `Information` jako úroveň používá protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="63e12-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="63e12-601">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="63e12-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="63e12-602">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="63e12-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="63e12-603">Vytvořit protokoly při spuštění</span><span class="sxs-lookup"><span data-stu-id="63e12-603">Create logs in Startup</span></span>

<span data-ttu-id="63e12-604">Chcete-li zapsat protokoly `Startup` ve třídě, zahrňte `ILogger` parametr do signatury konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="63e12-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="63e12-605">Vytvořit protokoly ve třídě program</span><span class="sxs-lookup"><span data-stu-id="63e12-605">Create logs in the Program class</span></span>

<span data-ttu-id="63e12-606">Chcete-li zapsat protokoly `Program` ve třídě, Získejte `ILogger` instanci od typu di:</span><span class="sxs-lookup"><span data-stu-id="63e12-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="63e12-607">Protokolování během konstrukce hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="63e12-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="63e12-608">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="63e12-608">However, a separate logger can be used.</span></span> <span data-ttu-id="63e12-609">V následujícím příkladu se k přihlášení používá protokolovací nástroj [Serilog](https://serilog.net/) `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="63e12-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="63e12-610">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="63e12-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="63e12-611">Žádné metody asynchronního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="63e12-611">No asynchronous logger methods</span></span>

<span data-ttu-id="63e12-612">Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="63e12-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="63e12-613">Pokud je úložiště dat protokolování pomalé, nezapište ho přímo.</span><span class="sxs-lookup"><span data-stu-id="63e12-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="63e12-614">Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je později přesunout do pomalého úložiště.</span><span class="sxs-lookup"><span data-stu-id="63e12-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="63e12-615">Například pokud se přihlašujete k SQL Server, nechcete provádět přímo v `Log` metodě, protože `Log` metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="63e12-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="63e12-616">Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="63e12-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="63e12-617">Další informace najdete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHubu.</span><span class="sxs-lookup"><span data-stu-id="63e12-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="63e12-618">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="63e12-618">Configuration</span></span>

<span data-ttu-id="63e12-619">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="63e12-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="63e12-620">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="63e12-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="63e12-621">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="63e12-621">Command-line arguments.</span></span>
* <span data-ttu-id="63e12-622">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="63e12-622">Environment variables.</span></span>
* <span data-ttu-id="63e12-623">Objekty rozhraní .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="63e12-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="63e12-624">Úložiště nešifrovaného [tajného správce](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="63e12-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="63e12-625">Šifrované uživatelské úložiště, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="63e12-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="63e12-626">Vlastní poskytovatelé (nainstalováno nebo vytvořeno).</span><span class="sxs-lookup"><span data-stu-id="63e12-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="63e12-627">Například konfigurace protokolování je běžně poskytována v `Logging` části soubory nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="63e12-628">Následující příklad ukazuje obsah typického *appSettings. Soubor Development. JSON* :</span><span class="sxs-lookup"><span data-stu-id="63e12-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="63e12-629">`Logging` Vlastnost může mít `LogLevel` vlastnosti zprostředkovatele a log (zobrazí se konzola).</span><span class="sxs-lookup"><span data-stu-id="63e12-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="63e12-630">`LogLevel` Vlastnost v rámci `Logging` určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="63e12-631">V tomto příkladu se `System` `Microsoft` kategorie přihlásí na `Information` úrovni a všechny ostatní protokoly na `Debug` úrovni.</span><span class="sxs-lookup"><span data-stu-id="63e12-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="63e12-632">Další vlastnosti v `Logging` části zadat zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="63e12-633">Příklad je pro poskytovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-633">The example is for the Console provider.</span></span> <span data-ttu-id="63e12-634">Pokud zprostředkovatel podporuje [obory protokolů](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="63e12-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="63e12-635">Vlastnost poskytovatele (například `Console` v příkladu) může také určovat `LogLevel` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="63e12-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="63e12-636">`LogLevel`v rámci zprostředkovatele Určuje úrovně, které se mají protokolovat pro daného poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="63e12-637">Pokud jsou úrovně specifikovány `Logging.{providername}.LogLevel`v, přepíše cokoli nastavené `Logging.LogLevel`v.</span><span class="sxs-lookup"><span data-stu-id="63e12-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="63e12-638">Rozhraní API pro protokolování neobsahuje scénář pro změnu úrovní protokolu, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="63e12-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="63e12-639">Někteří poskytovatelé konfigurace ale mohou znovu načíst konfiguraci, která se projeví okamžitě při konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="63e12-640">Například [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider), který je přidán nástrojem `CreateDefaultBuilder` ke čtení souborů nastavení, ve výchozím nastavení znovu načte konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="63e12-641">Pokud se konfigurace v kódu změní, když je aplikace spuštěná, může aplikace volat [IConfigurationRoot. Load](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby se aktualizovala konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="63e12-642">Informace o implementaci zprostředkovatelů konfigurace najdete v <xref:fundamentals/configuration/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="63e12-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="63e12-643">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="63e12-643">Sample logging output</span></span>

<span data-ttu-id="63e12-644">Pomocí ukázkového kódu zobrazeného v předchozí části se protokoly zobrazí v konzole nástroje při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="63e12-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="63e12-645">Tady je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="63e12-645">Here's an example of console output:</span></span>

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

<span data-ttu-id="63e12-646">Předchozí protokoly byly vygenerovány vytvořením žádosti HTTP GET do ukázkové aplikace na adrese `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="63e12-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="63e12-647">Tady je příklad stejného protokolu, který se zobrazí v okně ladění při spuštění ukázkové aplikace v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="63e12-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="63e12-648">Protokoly, které jsou vytvořené `ILogger` voláními, které jsou uvedené v předchozí části, začínají na "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="63e12-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="63e12-649">Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63e12-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="63e12-650">ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="63e12-651">Zbývající část tohoto článku vysvětluje několik podrobností a možností protokolování.</span><span class="sxs-lookup"><span data-stu-id="63e12-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="63e12-652">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="63e12-652">NuGet packages</span></span>

<span data-ttu-id="63e12-653">Rozhraní `ILogger` a `ILoggerFactory` jsou v [Microsoft. Extensions. Loggings. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="63e12-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="63e12-654">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="63e12-654">Log category</span></span>

<span data-ttu-id="63e12-655">Při vytvoření `ILogger` objektu je pro něj zadána *kategorie* .</span><span class="sxs-lookup"><span data-stu-id="63e12-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="63e12-656">Tato kategorie je součástí každé zprávy protokolu vytvořené pomocí této instance `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="63e12-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="63e12-657">Kategorie může být libovolný řetězec, ale konvence používá název třídy, jako je například "TodoApi. Controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="63e12-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="63e12-658">Použijte `ILogger<T>` k získání `ILogger` instance, která používá plně kvalifikovaný název typu `T` jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="63e12-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="63e12-659">Chcete-li explicitně zadat kategorii, `ILoggerFactory.CreateLogger`zavolejte:</span><span class="sxs-lookup"><span data-stu-id="63e12-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="63e12-660">`ILogger<T>`je ekvivalentní volání `CreateLogger` s plně kvalifikovaným názvem typu `T`.</span><span class="sxs-lookup"><span data-stu-id="63e12-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="63e12-661">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="63e12-661">Log level</span></span>

<span data-ttu-id="63e12-662">Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="63e12-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="63e12-663">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="63e12-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="63e12-664">Můžete například zapsat `Information` protokol, pokud metoda končí normálně a `Warning` protokol, když metoda vrátí stavový kód *404 Nenalezeno* .</span><span class="sxs-lookup"><span data-stu-id="63e12-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="63e12-665">Následující kód vytváří `Information` a `Warning` protokoluje:</span><span class="sxs-lookup"><span data-stu-id="63e12-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="63e12-666">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="63e12-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="63e12-667">Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="63e12-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="63e12-668">Parametry metody jsou vysvětleny v [části Šablona zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="63e12-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="63e12-669">Metody protokolu, které zahrnují úroveň v názvu metody (například `LogInformation` a `LogWarning`), jsou [metody rozšíření pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="63e12-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="63e12-670">Tyto metody volají `Log` metodu, která přebírá `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="63e12-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="63e12-671">`Log` Metodu můžete zavolat přímo místo jedné z těchto rozšiřujících metod, ale syntaxe je poměrně složitá.</span><span class="sxs-lookup"><span data-stu-id="63e12-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="63e12-672">Další informace naleznete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a ve [zdrojovém kódu rozšíření protokolovacího](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)nástroje.</span><span class="sxs-lookup"><span data-stu-id="63e12-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="63e12-673">ASP.NET Core definuje následující úrovně protokolu seřazené od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="63e12-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="63e12-674">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="63e12-674">Trace = 0</span></span>

  <span data-ttu-id="63e12-675">Informace, které jsou obvykle užitečné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="63e12-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="63e12-676">Tyto zprávy mohou obsahovat citlivá data aplikací, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="63e12-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="63e12-677">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="63e12-677">*Disabled by default.*</span></span>

* <span data-ttu-id="63e12-678">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="63e12-678">Debug = 1</span></span>

  <span data-ttu-id="63e12-679">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="63e12-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="63e12-680">Příklad: `Entering method Configure with flag set to true.` povolte `Debug` protokoly úrovně v produkčním prostředí jenom při řešení potíží, protože se jedná o velký objem protokolů.</span><span class="sxs-lookup"><span data-stu-id="63e12-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="63e12-681">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="63e12-681">Information = 2</span></span>

  <span data-ttu-id="63e12-682">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="63e12-683">Tyto protokoly obvykle mají nějakou dlouhodobou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="63e12-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="63e12-684">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="63e12-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="63e12-685">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="63e12-685">Warning = 3</span></span>

  <span data-ttu-id="63e12-686">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="63e12-687">Ty můžou zahrnovat chyby nebo jiné podmínky, které nezpůsobí zastavení aplikace, ale možná bude nutné je prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="63e12-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="63e12-688">Zpracování výjimek je běžné místo pro použití úrovně `Warning` protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="63e12-689">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="63e12-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="63e12-690">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="63e12-690">Error = 4</span></span>

  <span data-ttu-id="63e12-691">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="63e12-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="63e12-692">Tyto zprávy indikují selhání aktuální aktivity nebo operace (jako je aktuální požadavek HTTP), ne při selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="63e12-693">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="63e12-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="63e12-694">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="63e12-694">Critical = 5</span></span>

  <span data-ttu-id="63e12-695">Chyby, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="63e12-695">For failures that require immediate attention.</span></span> <span data-ttu-id="63e12-696">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="63e12-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="63e12-697">Úroveň protokolu můžete použít k určení, kolik výstupu protokolu se zapisuje do konkrétního úložného média nebo okna pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="63e12-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="63e12-698">Příklad:</span><span class="sxs-lookup"><span data-stu-id="63e12-698">For example:</span></span>

* <span data-ttu-id="63e12-699">V produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="63e12-699">In production:</span></span>
  * <span data-ttu-id="63e12-700">Protokolování na úrovni `Trace` přes `Information` vytváří velké množství podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="63e12-701">Pokud chcete řídit náklady a nepřekročit limity úložiště dat, `Trace` protokolujte zprávy na `Information` úrovni do úložiště dat s nízkými náklady na velké objemy dat.</span><span class="sxs-lookup"><span data-stu-id="63e12-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="63e12-702">Přihlášení `Warning` přes `Critical` úrovně obvykle vytváří méně a menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="63e12-703">Proto náklady a limity úložiště většinou nejsou obavy, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="63e12-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="63e12-704">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="63e12-704">During development:</span></span>
  * <span data-ttu-id="63e12-705">Umožňuje `Warning` protokolovat `Critical` zprávy do konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="63e12-706">Při `Trace` odstraňování `Information` potíží přidejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="63e12-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="63e12-707">Část [filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovateli zpracovává.</span><span class="sxs-lookup"><span data-stu-id="63e12-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="63e12-708">ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="63e12-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="63e12-709">Příklady protokolů výše v tomto článku vyloučily nižší `Information` úrovně protokolů, takže se `Debug` nevytvořily žádné protokoly ani `Trace` protokoly na úrovni.</span><span class="sxs-lookup"><span data-stu-id="63e12-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="63e12-710">Tady je příklad protokolů konzoly vytvořených spuštěním ukázkové aplikace nakonfigurované pro zobrazení `Debug` protokolů:</span><span class="sxs-lookup"><span data-stu-id="63e12-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="63e12-711">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="63e12-711">Log event ID</span></span>

<span data-ttu-id="63e12-712">Každý protokol může určovat *ID události*.</span><span class="sxs-lookup"><span data-stu-id="63e12-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="63e12-713">Tato ukázková aplikace používá lokálně definovanou `LoggingEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="63e12-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="63e12-714">ID události přidružuje sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="63e12-714">An event ID associates a set of events.</span></span> <span data-ttu-id="63e12-715">Například všechny protokoly související se zobrazením seznamu položek na stránce může být 1001.</span><span class="sxs-lookup"><span data-stu-id="63e12-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="63e12-716">Zprostředkovatel protokolování může ukládat ID události v poli ID, ve zprávě protokolování nebo vůbec ne.</span><span class="sxs-lookup"><span data-stu-id="63e12-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="63e12-717">Zprostředkovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="63e12-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="63e12-718">Poskytovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="63e12-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="63e12-719">Šablona zprávy protokolu</span><span class="sxs-lookup"><span data-stu-id="63e12-719">Log message template</span></span>

<span data-ttu-id="63e12-720">Každý protokol Určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="63e12-720">Each log specifies a message template.</span></span> <span data-ttu-id="63e12-721">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="63e12-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="63e12-722">Použijte názvy zástupných symbolů, nikoli čísla.</span><span class="sxs-lookup"><span data-stu-id="63e12-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="63e12-723">Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry slouží k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="63e12-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="63e12-724">V následujícím kódu si všimněte, že názvy parametrů jsou v šabloně zprávy mimo pořadí:</span><span class="sxs-lookup"><span data-stu-id="63e12-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="63e12-725">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v posloupnosti:</span><span class="sxs-lookup"><span data-stu-id="63e12-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="63e12-726">Protokolovací rozhraní funguje tímto způsobem, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="63e12-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="63e12-727">Samotné argumenty jsou předány do protokolovacího systému, nikoli pouze ze šablony formátované zprávy.</span><span class="sxs-lookup"><span data-stu-id="63e12-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="63e12-728">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="63e12-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="63e12-729">Předpokládejme například, že volání metody protokolovacího nástroje vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="63e12-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="63e12-730">Pokud odesíláte protokoly do služby Azure Table Storage, Každá entita tabulky Azure může mít `ID` vlastnosti a `RequestTime` , což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="63e12-731">Dotaz může najít všechny protokoly v rámci určitého `RequestTime` rozsahu, aniž by došlo k analýze časového limitu textové zprávy.</span><span class="sxs-lookup"><span data-stu-id="63e12-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="63e12-732">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="63e12-732">Logging exceptions</span></span>

<span data-ttu-id="63e12-733">Metody protokolovacího nástroje mají přetížení, které umožňují předat výjimku, jak je uvedeno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="63e12-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="63e12-734">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="63e12-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="63e12-735">Tady je příklad výstupu poskytovatele ladění z kódu uvedeného výše.</span><span class="sxs-lookup"><span data-stu-id="63e12-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="63e12-736">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="63e12-736">Log filtering</span></span>

<span data-ttu-id="63e12-737">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny poskytovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="63e12-738">Všechny protokoly nižší než minimální úroveň nebudou tomuto poskytovateli předány, takže se nebudou zobrazovat ani ukládat.</span><span class="sxs-lookup"><span data-stu-id="63e12-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="63e12-739">Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="63e12-740">Celočíselná hodnota `LogLevel.None` je 6, což je více než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="63e12-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="63e12-741">Vytvořit pravidla filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="63e12-741">Create filter rules in configuration</span></span>

<span data-ttu-id="63e12-742">Kód šablony projektu volá `CreateDefaultBuilder` k nastavení protokolování pro poskytovatele, konzolu, ladění a EventSource (ASP.NET Core 2,2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="63e12-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="63e12-743">`CreateDefaultBuilder` Metoda nastaví protokolování tak, aby hledalo konfiguraci v `Logging` části, jak je vysvětleno [výše v tomto článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="63e12-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="63e12-744">Konfigurační data určují minimální úrovně protokolu podle poskytovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="63e12-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="63e12-745">Tento kód JSON vytvoří šest pravidel filtru: jeden pro poskytovatele ladění, čtyři pro poskytovatele konzoly a jeden pro všechny poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="63e12-746">Při vytvoření `ILogger` objektu se vybere jedno pravidlo pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="63e12-747">Filtrovat pravidla v kódu</span><span class="sxs-lookup"><span data-stu-id="63e12-747">Filter rules in code</span></span>

<span data-ttu-id="63e12-748">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="63e12-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="63e12-749">Druhý `AddFilter` Určuje poskytovatele ladění pomocí jeho názvu typu.</span><span class="sxs-lookup"><span data-stu-id="63e12-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="63e12-750">První `AddFilter` se vztahuje na všechny poskytovatele, protože neurčuje typ poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="63e12-751">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="63e12-751">How filtering rules are applied</span></span>

<span data-ttu-id="63e12-752">Konfigurační data a `AddFilter` kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="63e12-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="63e12-753">Prvních šest přicházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="63e12-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="63e12-754">Číslo</span><span class="sxs-lookup"><span data-stu-id="63e12-754">Number</span></span> | <span data-ttu-id="63e12-755">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="63e12-755">Provider</span></span>      | <span data-ttu-id="63e12-756">Kategorie, které začínají na...</span><span class="sxs-lookup"><span data-stu-id="63e12-756">Categories that begin with ...</span></span>          | <span data-ttu-id="63e12-757">Minimální úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="63e12-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="63e12-758">1</span><span class="sxs-lookup"><span data-stu-id="63e12-758">1</span></span>      | <span data-ttu-id="63e12-759">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-759">Debug</span></span>         | <span data-ttu-id="63e12-760">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="63e12-760">All categories</span></span>                          | <span data-ttu-id="63e12-761">Informace</span><span class="sxs-lookup"><span data-stu-id="63e12-761">Information</span></span>       |
| <span data-ttu-id="63e12-762">2</span><span class="sxs-lookup"><span data-stu-id="63e12-762">2</span></span>      | <span data-ttu-id="63e12-763">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-763">Console</span></span>       | <span data-ttu-id="63e12-764">Microsoft. AspNetCore. Mvc. Razor. Internal</span><span class="sxs-lookup"><span data-stu-id="63e12-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="63e12-765">Upozornění</span><span class="sxs-lookup"><span data-stu-id="63e12-765">Warning</span></span>           |
| <span data-ttu-id="63e12-766">3</span><span class="sxs-lookup"><span data-stu-id="63e12-766">3</span></span>      | <span data-ttu-id="63e12-767">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-767">Console</span></span>       | <span data-ttu-id="63e12-768">Microsoft. AspNetCore. Mvc. Razor. Razor</span><span class="sxs-lookup"><span data-stu-id="63e12-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="63e12-769">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-769">Debug</span></span>             |
| <span data-ttu-id="63e12-770">4</span><span class="sxs-lookup"><span data-stu-id="63e12-770">4</span></span>      | <span data-ttu-id="63e12-771">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-771">Console</span></span>       | <span data-ttu-id="63e12-772">Microsoft. AspNetCore. Mvc. Razor</span><span class="sxs-lookup"><span data-stu-id="63e12-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="63e12-773">Chyba</span><span class="sxs-lookup"><span data-stu-id="63e12-773">Error</span></span>             |
| <span data-ttu-id="63e12-774">5</span><span class="sxs-lookup"><span data-stu-id="63e12-774">5</span></span>      | <span data-ttu-id="63e12-775">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-775">Console</span></span>       | <span data-ttu-id="63e12-776">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="63e12-776">All categories</span></span>                          | <span data-ttu-id="63e12-777">Informace</span><span class="sxs-lookup"><span data-stu-id="63e12-777">Information</span></span>       |
| <span data-ttu-id="63e12-778">6</span><span class="sxs-lookup"><span data-stu-id="63e12-778">6</span></span>      | <span data-ttu-id="63e12-779">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="63e12-779">All providers</span></span> | <span data-ttu-id="63e12-780">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="63e12-780">All categories</span></span>                          | <span data-ttu-id="63e12-781">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-781">Debug</span></span>             |
| <span data-ttu-id="63e12-782">7</span><span class="sxs-lookup"><span data-stu-id="63e12-782">7</span></span>      | <span data-ttu-id="63e12-783">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="63e12-783">All providers</span></span> | <span data-ttu-id="63e12-784">Systém</span><span class="sxs-lookup"><span data-stu-id="63e12-784">System</span></span>                                  | <span data-ttu-id="63e12-785">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-785">Debug</span></span>             |
| <span data-ttu-id="63e12-786">8</span><span class="sxs-lookup"><span data-stu-id="63e12-786">8</span></span>      | <span data-ttu-id="63e12-787">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-787">Debug</span></span>         | <span data-ttu-id="63e12-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="63e12-788">Microsoft</span></span>                               | <span data-ttu-id="63e12-789">Trasování</span><span class="sxs-lookup"><span data-stu-id="63e12-789">Trace</span></span>             |

<span data-ttu-id="63e12-790">Při vytvoření `ILogger` objektu vybere `ILoggerFactory` objekt jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="63e12-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="63e12-791">Všechny zprávy napsané `ILogger` instancí jsou filtrovány podle vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="63e12-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="63e12-792">V dostupných pravidlech se vybere nejpřesnější pravidlo pro jednotlivé dvojice zprostředkovatel a kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="63e12-793">Následující algoritmus se používá pro každého poskytovatele při `ILogger` vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="63e12-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="63e12-794">Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem.</span><span class="sxs-lookup"><span data-stu-id="63e12-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="63e12-795">Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="63e12-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="63e12-796">Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie.</span><span class="sxs-lookup"><span data-stu-id="63e12-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="63e12-797">Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="63e12-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="63e12-798">Pokud je vybráno více pravidel, vezměte **Poslední** z nich.</span><span class="sxs-lookup"><span data-stu-id="63e12-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="63e12-799">Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="63e12-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="63e12-800">V předchozím seznamu pravidel Předpokládejme, že vytvoříte `ILogger` objekt pro kategorii "Microsoft. AspNetCore. Mvc. Razor. RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="63e12-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="63e12-801">Pro poskytovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="63e12-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="63e12-802">Pravidlo 8 je nejvíce specifické, takže je to ten vybraný.</span><span class="sxs-lookup"><span data-stu-id="63e12-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="63e12-803">Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="63e12-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="63e12-804">Pravidlo 3 je nejvíce specifické.</span><span class="sxs-lookup"><span data-stu-id="63e12-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="63e12-805">Výsledná `ILogger` instance odesílá protokoly `Trace` úrovně a výše do poskytovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="63e12-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="63e12-806">Protokoly `Debug` úrovně a výše jsou odesílány poskytovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="63e12-807">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="63e12-807">Provider aliases</span></span>

<span data-ttu-id="63e12-808">Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu.</span><span class="sxs-lookup"><span data-stu-id="63e12-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="63e12-809">Pro předdefinované poskytovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="63e12-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="63e12-810">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-810">Console</span></span>
* <span data-ttu-id="63e12-811">Ladit</span><span class="sxs-lookup"><span data-stu-id="63e12-811">Debug</span></span>
* <span data-ttu-id="63e12-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="63e12-812">EventSource</span></span>
* <span data-ttu-id="63e12-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="63e12-813">EventLog</span></span>
* <span data-ttu-id="63e12-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="63e12-814">TraceSource</span></span>
* <span data-ttu-id="63e12-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="63e12-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="63e12-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="63e12-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="63e12-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="63e12-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="63e12-818">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="63e12-818">Default minimum level</span></span>

<span data-ttu-id="63e12-819">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že se pro daného zprostředkovatele a kategorii nepoužijí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="63e12-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="63e12-820">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="63e12-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="63e12-821">Pokud nenastavíte explicitně minimální úroveň, výchozí hodnota je `Information`, což znamená, že `Trace` protokoly a `Debug` budou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="63e12-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="63e12-822">Funkce filtrování</span><span class="sxs-lookup"><span data-stu-id="63e12-822">Filter functions</span></span>

<span data-ttu-id="63e12-823">Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="63e12-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="63e12-824">Kód ve funkci má přístup k typu poskytovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="63e12-825">Příklad:</span><span class="sxs-lookup"><span data-stu-id="63e12-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="63e12-826">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="63e12-826">System categories and levels</span></span>

<span data-ttu-id="63e12-827">Tady je několik kategorií používaných ASP.NET Core a Entity Framework Core s poznámkami o tom, jaké protokoly se mají od nich očekávat:</span><span class="sxs-lookup"><span data-stu-id="63e12-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="63e12-828">Kategorie</span><span class="sxs-lookup"><span data-stu-id="63e12-828">Category</span></span>                            | <span data-ttu-id="63e12-829">Poznámky</span><span class="sxs-lookup"><span data-stu-id="63e12-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="63e12-830">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="63e12-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="63e12-831">Obecná diagnostika ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="63e12-832">Microsoft. AspNetCore. DataProtection</span><span class="sxs-lookup"><span data-stu-id="63e12-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="63e12-833">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="63e12-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="63e12-834">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="63e12-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="63e12-835">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="63e12-835">Hosts allowed.</span></span> |
| <span data-ttu-id="63e12-836">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="63e12-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="63e12-837">Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění.</span><span class="sxs-lookup"><span data-stu-id="63e12-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="63e12-838">Která hostující spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="63e12-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="63e12-839">Microsoft. AspNetCore. Mvc</span><span class="sxs-lookup"><span data-stu-id="63e12-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="63e12-840">Diagnostika MVC a Razor</span><span class="sxs-lookup"><span data-stu-id="63e12-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="63e12-841">Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="63e12-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="63e12-842">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="63e12-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="63e12-843">Informace o shodě trasy.</span><span class="sxs-lookup"><span data-stu-id="63e12-843">Route matching information.</span></span> |
| <span data-ttu-id="63e12-844">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="63e12-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="63e12-845">Připojení – spouštění, zastavování a udržování reakcí na Alive.</span><span class="sxs-lookup"><span data-stu-id="63e12-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="63e12-846">Informace o certifikátu HTTPS</span><span class="sxs-lookup"><span data-stu-id="63e12-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="63e12-847">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="63e12-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="63e12-848">Soubory byly obsluhovány.</span><span class="sxs-lookup"><span data-stu-id="63e12-848">Files served.</span></span> |
| <span data-ttu-id="63e12-849">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="63e12-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="63e12-850">Obecná diagnostika Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="63e12-851">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="63e12-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="63e12-852">Rozsahy protokolů</span><span class="sxs-lookup"><span data-stu-id="63e12-852">Log scopes</span></span>

 <span data-ttu-id="63e12-853">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="63e12-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="63e12-854">Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="63e12-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="63e12-855">Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="63e12-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="63e12-856">Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodou a trvá, dokud nebude uvolněn.</span><span class="sxs-lookup"><span data-stu-id="63e12-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="63e12-857">Použití oboru zabalením volání protokolovacího nástroje `using` v bloku:</span><span class="sxs-lookup"><span data-stu-id="63e12-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="63e12-858">Následující kód umožňuje obory pro poskytovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="63e12-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="63e12-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="63e12-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="63e12-860">Aby bylo `IncludeScopes` možné povolit protokolování na základě oboru, je nutné nakonfigurovat možnost protokolovacího nástroje konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="63e12-861">Informace o konfiguraci najdete v části věnované [konfiguraci](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="63e12-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="63e12-862">Každá zpráva protokolu obsahuje informace s vymezeným oborem:</span><span class="sxs-lookup"><span data-stu-id="63e12-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="63e12-863">Vestavění zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="63e12-863">Built-in logging providers</span></span>

<span data-ttu-id="63e12-864">ASP.NET Core dodává následující poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="63e12-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="63e12-865">Konzola</span><span class="sxs-lookup"><span data-stu-id="63e12-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="63e12-866">Ladí</span><span class="sxs-lookup"><span data-stu-id="63e12-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="63e12-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="63e12-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="63e12-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="63e12-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="63e12-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="63e12-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="63e12-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="63e12-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="63e12-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="63e12-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="63e12-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="63e12-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="63e12-873">Informace o stdout a protokolování ladění pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection></span><span class="sxs-lookup"><span data-stu-id="63e12-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="63e12-874">Poskytovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="63e12-874">Console provider</span></span>

<span data-ttu-id="63e12-875">Balíček poskytovatele [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="63e12-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="63e12-876">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="63e12-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="63e12-877">Poskytovatel ladění</span><span class="sxs-lookup"><span data-stu-id="63e12-877">Debug provider</span></span>

<span data-ttu-id="63e12-878">Balíček poskytovatele [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` volání metody).</span><span class="sxs-lookup"><span data-stu-id="63e12-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="63e12-879">V systému Linux tento poskytovatel zapisuje protokoly do */var/log/Message*.</span><span class="sxs-lookup"><span data-stu-id="63e12-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="63e12-880">Zprostředkovatel zdroje událostí</span><span class="sxs-lookup"><span data-stu-id="63e12-880">Event Source provider</span></span>

<span data-ttu-id="63e12-881">Balíček zprostředkovatele [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje do zdroje událostí pro různé platformy s názvem `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="63e12-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="63e12-882">Ve Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="63e12-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="63e12-883">Poskytovatel zdroje událostí se přidá automaticky, když `CreateDefaultBuilder` se volá k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="63e12-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="63e12-884">K shromažďování a zobrazování protokolů použijte [Nástroj PerfView](https://github.com/Microsoft/perfview) .</span><span class="sxs-lookup"><span data-stu-id="63e12-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="63e12-885">Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="63e12-886">Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte `*Microsoft-Extensions-Logging` řetězec do seznamu **další poskytovatelé** .</span><span class="sxs-lookup"><span data-stu-id="63e12-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="63e12-887">(Na začátku řetězce nechybíš hvězdičku.)</span><span class="sxs-lookup"><span data-stu-id="63e12-887">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="63e12-889">Zprostředkovatel událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="63e12-889">Windows EventLog provider</span></span>

<span data-ttu-id="63e12-890">Balíček poskytovatele [Microsoft. Extensions. log. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="63e12-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="63e12-891">[AddEventLog přetížení](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="63e12-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="63e12-892">Pokud `null` není zadaný, použijí se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="63e12-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="63e12-893">`LogName`&ndash; "Aplikace"</span><span class="sxs-lookup"><span data-stu-id="63e12-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="63e12-894">`SourceName`&ndash; ".NET runtime"</span><span class="sxs-lookup"><span data-stu-id="63e12-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="63e12-895">`MachineName`&ndash; místní počítač</span><span class="sxs-lookup"><span data-stu-id="63e12-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="63e12-896">Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level).</span><span class="sxs-lookup"><span data-stu-id="63e12-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="63e12-897">Chcete-li protokolovat `Warning`události nižší než, explicitně nastavte úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="63e12-898">Do souboru *appSettings. JSON* přidejte například následující:</span><span class="sxs-lookup"><span data-stu-id="63e12-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="63e12-899">Poskytovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="63e12-899">TraceSource provider</span></span>

<span data-ttu-id="63e12-900">Balíček poskytovatele [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="63e12-901">[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="63e12-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="63e12-902">Aby bylo možné používat tohoto poskytovatele, aplikace musí běžet na .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="63e12-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="63e12-903">Poskytovatel může směrovat zprávy na celou řadu [posluchačů](/dotnet/framework/debug-trace-profile/trace-listeners), jako je <xref:System.Diagnostics.TextWriterTraceListener> například použití v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63e12-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="63e12-904">Poskytovatel Azure App Service</span><span class="sxs-lookup"><span data-stu-id="63e12-904">Azure App Service provider</span></span>

<span data-ttu-id="63e12-905">Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.</span><span class="sxs-lookup"><span data-stu-id="63e12-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="63e12-906">Balíček poskytovatele není zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="63e12-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="63e12-907">Při cílení na .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` Metapackage přidejte do projektu balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="63e12-908"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Přetížení umožňuje předat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="63e12-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="63e12-909">Objekt nastavení může přepsat výchozí nastavení, jako je například šablona výstupu protokolování, název objektu BLOB a omezení velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="63e12-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="63e12-910">(*Výstupní šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici `ILogger` při volání metody.)</span><span class="sxs-lookup"><span data-stu-id="63e12-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="63e12-911">Když nasadíte aplikaci do App Service, aplikace respektuje nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) stránky **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="63e12-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="63e12-912">Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="63e12-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="63e12-913">**Protokolování aplikace (systém souborů)**</span><span class="sxs-lookup"><span data-stu-id="63e12-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="63e12-914">**Protokolování aplikace (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="63e12-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="63e12-915">Výchozím umístěním pro soubory protokolu je složka *\\aplikace D:\\Home\\LogFiles* a výchozí název souboru je *Diagnostics-YYYYMMDD. txt*.</span><span class="sxs-lookup"><span data-stu-id="63e12-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="63e12-916">Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="63e12-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="63e12-917">Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/MM/DD/hh/{GUID}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="63e12-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="63e12-918">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="63e12-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="63e12-919">Nemá žádný vliv, pokud se projekt spouští místně&mdash;, nepíše do místních souborů nebo místního vývojového úložiště pro objekty blob.</span><span class="sxs-lookup"><span data-stu-id="63e12-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="63e12-920">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="63e12-920">Azure log streaming</span></span>

<span data-ttu-id="63e12-921">Streamování protokolů Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="63e12-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="63e12-922">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="63e12-922">The app server</span></span>
* <span data-ttu-id="63e12-923">Webový server</span><span class="sxs-lookup"><span data-stu-id="63e12-923">The web server</span></span>
* <span data-ttu-id="63e12-924">Trasování chybných požadavků</span><span class="sxs-lookup"><span data-stu-id="63e12-924">Failed request tracing</span></span>

<span data-ttu-id="63e12-925">Konfigurace streamování protokolů Azure:</span><span class="sxs-lookup"><span data-stu-id="63e12-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="63e12-926">Na stránce portálu vaší aplikace přejděte na stránku **protokoly App Service** .</span><span class="sxs-lookup"><span data-stu-id="63e12-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="63e12-927">Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="63e12-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="63e12-928">Vyberte **úroveň**protokolu.</span><span class="sxs-lookup"><span data-stu-id="63e12-928">Choose the log **Level**.</span></span> <span data-ttu-id="63e12-929">Toto nastavení platí jenom pro streamování protokolů Azure, ne pro jiné poskytovatele protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="63e12-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="63e12-930">Pokud chcete zobrazit zprávy aplikace, přejděte na stránku **streamu protokolu** .</span><span class="sxs-lookup"><span data-stu-id="63e12-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="63e12-931">Protokolují ho aplikace přes `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="63e12-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="63e12-932">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="63e12-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="63e12-933">Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="63e12-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="63e12-934">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie.</span><span class="sxs-lookup"><span data-stu-id="63e12-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="63e12-935">Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.</span><span class="sxs-lookup"><span data-stu-id="63e12-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="63e12-936">Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63e12-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="63e12-937">Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="63e12-938">Nepoužívejte&mdash;balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , který je pro ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="63e12-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="63e12-939">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="63e12-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="63e12-940">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="63e12-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="63e12-941">[Application Insights pro ASP.NET Core aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou řadu Application Insights telemetrie spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="63e12-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="63e12-942">[ApplicationInsightsLoggerProvider pro protokoly .NET Core ILogger](/azure/azure-monitor/app/ilogger) – začněte sem, pokud chcete implementovat poskytovatele protokolování bez zbytku Application Insights telemetrie.</span><span class="sxs-lookup"><span data-stu-id="63e12-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="63e12-943">[Application Insights adaptéry protokolování](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="63e12-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="63e12-944">[Instalace, konfigurace a inicializace Application Insights sady SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="63e12-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="63e12-945">Zprostředkovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="63e12-945">Third-party logging providers</span></span>

<span data-ttu-id="63e12-946">Protokolovací architektury třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="63e12-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="63e12-947">[elmah.IO](https://elmah.io/) ([úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="63e12-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="63e12-948">[GELF](https://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="63e12-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="63e12-949">[JSNLog](https://jsnlog.com/) ([úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="63e12-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="63e12-950">[KissLog.NET](https://kisslog.net/) ([úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="63e12-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="63e12-951">[Log4Net](https://logging.apache.org/log4net/) ([úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="63e12-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="63e12-952">[Loggr](https://loggr.net/) ([úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="63e12-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="63e12-953">[NLOG](https://nlog-project.org/) ([úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="63e12-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="63e12-954">[Sentry](https://sentry.io/welcome/) ([úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="63e12-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="63e12-955">[Serilog](https://serilog.net/) ([úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="63e12-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="63e12-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="63e12-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="63e12-957">Některé architektury třetích stran můžou provádět [sémantické protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="63e12-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="63e12-958">Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="63e12-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="63e12-959">Přidejte do projektu balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="63e12-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="63e12-960">Zavolejte metodu `ILoggerFactory` rozšíření poskytovanou protokolovacím rozhraním.</span><span class="sxs-lookup"><span data-stu-id="63e12-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="63e12-961">Další informace najdete v dokumentaci pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="63e12-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="63e12-962">Microsoft nepodporuje zprostředkovatele protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="63e12-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63e12-963">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="63e12-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end

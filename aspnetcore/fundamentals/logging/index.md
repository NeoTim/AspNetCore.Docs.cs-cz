---
title: Přihlášení v .NET Core a ASP.NET Core
author: rick-anderson
description: Naučte se používat protokolovací rozhraní poskytovanou balíčkem NuGet Microsoft. Extensions. Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 697e6cf0cd1b51ad6c2942e21bc084d1fe6bfa4e
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259733"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="b560a-103">Přihlášení v .NET Core a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b560a-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="b560a-104">Tím, že [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b560a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b560a-105">.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="b560a-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="b560a-106">V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="b560a-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b560a-107">Většina příkladů kódu, které jsou uvedené v tomto článku, se nachází v ASP.NET Corech aplikacích.</span><span class="sxs-lookup"><span data-stu-id="b560a-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="b560a-108">Části těchto fragmentů kódu specifické pro protokolování se vztahují na libovolnou aplikaci .NET Core, která používá [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b560a-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="b560a-109">Informace o tom, jak používat obecného hostitele v aplikacích nevyužívajících webovou konzolu, najdete v tématu [hostované služby](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="b560a-109">For information about how to use the Generic Host in non-web console apps, see [Hosted services](xref:fundamentals/host/hosted-services).</span></span>

<span data-ttu-id="b560a-110">Kód protokolování pro aplikace bez obecného hostitele se liší v způsobu [Přidání zprostředkovatelů](#add-providers) a [vytváření protokolovacích](#create-logs)nástrojů.</span><span class="sxs-lookup"><span data-stu-id="b560a-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="b560a-111">Příklady kódu, který není hostitelský, jsou uvedeny v těchto částech článku.</span><span class="sxs-lookup"><span data-stu-id="b560a-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="b560a-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b560a-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="b560a-113">Přidat zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="b560a-113">Add providers</span></span>

<span data-ttu-id="b560a-114">Zprostředkovatel protokolování zobrazuje nebo ukládá protokoly.</span><span class="sxs-lookup"><span data-stu-id="b560a-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="b560a-115">Například poskytovatel konzoly zobrazuje protokoly v konzole nástroje a poskytovatel Azure Application Insights je ukládá do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b560a-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="b560a-116">Protokoly lze odesílat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="b560a-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b560a-117">Chcete-li přidat poskytovatele v aplikaci, která používá obecného hostitele, zavolejte metodu rozšíření `Add{provider name}` poskytovatele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b560a-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="b560a-118">V konzolové aplikaci, která není hostitelem, zavolejte při vytváření `LoggerFactory` metodu rozšíření `Add{provider name}` poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="b560a-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="b560a-119">`LoggerFactory` a `AddConsole` vyžadují příkaz `using` pro `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="b560a-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="b560a-120">Výchozí ASP.NET Core šablony projektů volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, které přidávají následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="b560a-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="b560a-121">Stromu</span><span class="sxs-lookup"><span data-stu-id="b560a-121">Console</span></span>
* <span data-ttu-id="b560a-122">Ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-122">Debug</span></span>
* <span data-ttu-id="b560a-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="b560a-123">EventSource</span></span>
* <span data-ttu-id="b560a-124">Protokol událostí (pouze při spuštění v systému Windows)</span><span class="sxs-lookup"><span data-stu-id="b560a-124">EventLog (only when running on Windows)</span></span>

<span data-ttu-id="b560a-125">Výchozí poskytovatele můžete nahradit vašimi vlastními možnostmi.</span><span class="sxs-lookup"><span data-stu-id="b560a-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="b560a-126">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> a přidejte poskytovatele, které chcete.</span><span class="sxs-lookup"><span data-stu-id="b560a-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="b560a-127">Chcete-li přidat poskytovatele, zavolejte metodu rozšíření `Add{provider name}` poskytovatele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b560a-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="b560a-128">Předchozí kód vyžaduje odkazy na `Microsoft.Extensions.Logging` a `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="b560a-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="b560a-129">Výchozí šablona projektu volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, která přidává následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="b560a-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="b560a-130">Stromu</span><span class="sxs-lookup"><span data-stu-id="b560a-130">Console</span></span>
* <span data-ttu-id="b560a-131">Ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-131">Debug</span></span>
* <span data-ttu-id="b560a-132">EventSource (počínaje ASP.NET Core 2,2)</span><span class="sxs-lookup"><span data-stu-id="b560a-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="b560a-133">Pokud používáte `CreateDefaultBuilder`, můžete výchozí poskytovatele nahradit vašimi vlastními možnostmi.</span><span class="sxs-lookup"><span data-stu-id="b560a-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="b560a-134">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> a přidejte poskytovatele, které chcete.</span><span class="sxs-lookup"><span data-stu-id="b560a-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="b560a-135">Přečtěte si další informace o [integrovaných poskytovatelích protokolování](#built-in-logging-providers) a [zprostředkovatelích protokolování třetích stran](#third-party-logging-providers) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="b560a-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="b560a-136">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="b560a-136">Create logs</span></span>

<span data-ttu-id="b560a-137">Chcete-li vytvořit protokoly, použijte objekt <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="b560a-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="b560a-138">Ve webové aplikaci nebo v hostované službě získáte @no__t 0 od injektáže závislosti (DI).</span><span class="sxs-lookup"><span data-stu-id="b560a-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="b560a-139">V nehostitelských konzolových aplikacích použijte `LoggerFactory` a vytvořte `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="b560a-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="b560a-140">Následující ASP.NET Core příklad vytvoří protokolovací nástroj s `TodoApiSample.Pages.AboutModel` jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="b560a-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="b560a-141">*Kategorie* protokolu je řetězec, který je spojený s každým protokolem.</span><span class="sxs-lookup"><span data-stu-id="b560a-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="b560a-142">Instance `ILogger<T>` poskytnutá nástrojem DI vytvoří protokoly, které mají plně kvalifikovaný název typu `T` jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="b560a-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="b560a-143">Následující příklad nehostitelských konzolových aplikací vytvoří protokolovací nástroj s `LoggingConsoleApp.Program` jako kategorií.</span><span class="sxs-lookup"><span data-stu-id="b560a-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="b560a-144">V následujících příkladech ASP.NET Core a konzolových aplikací se k vytváření protokolů s `Information` jako úroveň používá protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="b560a-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="b560a-145">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="b560a-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="b560a-146">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="b560a-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="b560a-147">Vytvořit protokoly ve třídě program</span><span class="sxs-lookup"><span data-stu-id="b560a-147">Create logs in the Program class</span></span>

<span data-ttu-id="b560a-148">Pokud chcete zapisovat protokoly ve třídě `Program` ASP.NET Core aplikace, Získejte po sestavení hostitele instanci `ILogger` od typu DI:</span><span class="sxs-lookup"><span data-stu-id="b560a-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="b560a-149">Vytvořit protokoly ve spouštěcí třídě</span><span class="sxs-lookup"><span data-stu-id="b560a-149">Create logs in the Startup class</span></span>

<span data-ttu-id="b560a-150">Pokud chcete zapisovat protokoly v metodě `Startup.Configure` ASP.NET Core aplikace, zahrňte do signatury metody parametr `ILogger`:</span><span class="sxs-lookup"><span data-stu-id="b560a-150">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="b560a-151">Zápis protokolů před dokončením nastavení typu DI Container v metodě `Startup.ConfigureServices` není podporován:</span><span class="sxs-lookup"><span data-stu-id="b560a-151">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="b560a-152">Vkládání protokolovacího nástroje do konstruktoru `Startup` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="b560a-152">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="b560a-153">Vkládání protokolovacího nástroje do signatury metody `Startup.ConfigureServices` se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="b560a-153">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="b560a-154">Důvodem tohoto omezení je, že protokolování závisí na DI a na konfiguraci, která v systému zapíná na DI.</span><span class="sxs-lookup"><span data-stu-id="b560a-154">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="b560a-155">Kontejner DI není nastavený, dokud se nedokončí `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b560a-155">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="b560a-156">Vložení konstruktoru protokolovacího nástroje do `Startup` funguje v dřívějších verzích ASP.NET Core, protože pro webového hostitele je vytvořen samostatný kontejner DI.</span><span class="sxs-lookup"><span data-stu-id="b560a-156">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="b560a-157">Informace o tom, proč je pro obecného hostitele vytvořeno jenom jeden kontejner, najdete v části [oznámení o změně](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="b560a-157">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="b560a-158">Pokud potřebujete nakonfigurovat službu, která závisí na `ILogger<T>`, můžete to provést pomocí injektáže konstruktoru nebo poskytnutím výrobní metody.</span><span class="sxs-lookup"><span data-stu-id="b560a-158">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="b560a-159">Přístup k metodě pro vytváření je doporučen pouze v případě, že není k dispozici žádná jiná možnost.</span><span class="sxs-lookup"><span data-stu-id="b560a-159">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="b560a-160">Předpokládejme například, že potřebujete vyplnit vlastnost pomocí služby z DI:</span><span class="sxs-lookup"><span data-stu-id="b560a-160">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="b560a-161">Předchozí zvýrazněný kód je `Func`, který se spustí, když je objekt DI kontejneru potřeba vytvořit instanci `MyService`.</span><span class="sxs-lookup"><span data-stu-id="b560a-161">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="b560a-162">Tímto způsobem můžete získat přístup k libovolné registrované službě.</span><span class="sxs-lookup"><span data-stu-id="b560a-162">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="b560a-163">Vytvořit protokoly při spuštění</span><span class="sxs-lookup"><span data-stu-id="b560a-163">Create logs in Startup</span></span>

<span data-ttu-id="b560a-164">Chcete-li zapisovat protokoly ve třídě `Startup`, zahrňte do signatury konstruktoru parametr `ILogger`:</span><span class="sxs-lookup"><span data-stu-id="b560a-164">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="b560a-165">Vytvořit protokoly ve třídě program</span><span class="sxs-lookup"><span data-stu-id="b560a-165">Create logs in the Program class</span></span>

<span data-ttu-id="b560a-166">Chcete-li zapisovat protokoly ve třídě `Program`, Získejte instanci `ILogger` od DI:</span><span class="sxs-lookup"><span data-stu-id="b560a-166">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="b560a-167">Žádné metody asynchronního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="b560a-167">No asynchronous logger methods</span></span>

<span data-ttu-id="b560a-168">Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="b560a-168">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="b560a-169">Pokud je úložiště dat protokolování pomalé, nezapište ho přímo.</span><span class="sxs-lookup"><span data-stu-id="b560a-169">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="b560a-170">Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je později přesunout do pomalého úložiště.</span><span class="sxs-lookup"><span data-stu-id="b560a-170">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="b560a-171">Například pokud se přihlašujete k SQL Server, nechcete provádět přímo v metodě `Log`, protože metody `Log` jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="b560a-171">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="b560a-172">Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b560a-172">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="b560a-173">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="b560a-173">Configuration</span></span>

<span data-ttu-id="b560a-174">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b560a-174">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="b560a-175">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="b560a-175">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="b560a-176">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b560a-176">Command-line arguments.</span></span>
* <span data-ttu-id="b560a-177">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b560a-177">Environment variables.</span></span>
* <span data-ttu-id="b560a-178">Objekty rozhraní .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="b560a-178">In-memory .NET objects.</span></span>
* <span data-ttu-id="b560a-179">Úložiště nešifrovaného [tajného správce](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="b560a-179">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="b560a-180">Šifrované uživatelské úložiště, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="b560a-180">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="b560a-181">Vlastní poskytovatelé (nainstalováno nebo vytvořeno).</span><span class="sxs-lookup"><span data-stu-id="b560a-181">Custom providers (installed or created).</span></span>

<span data-ttu-id="b560a-182">Například konfigurace protokolování je běžně poskytována v části `Logging` souborů nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b560a-182">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="b560a-183">Následující příklad ukazuje obsah typického *appSettings. Soubor Development. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b560a-183">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

::: moniker range=">= aspnetcore-2.1"

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

<span data-ttu-id="b560a-184">Vlastnost `Logging` může mít `LogLevel` a vlastnosti zprostředkovatele protokolu (zobrazí se konzola).</span><span class="sxs-lookup"><span data-stu-id="b560a-184">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="b560a-185">Vlastnost `LogLevel` v části `Logging` určuje minimální [úroveň](#log-level) protokolování pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="b560a-185">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="b560a-186">V příkladu jsou `System` a `Microsoft` kategorie v protokolu `Information` a všechny ostatní přihlášeny na úrovni `Debug`.</span><span class="sxs-lookup"><span data-stu-id="b560a-186">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="b560a-187">Další vlastnosti v části `Logging` zadejte poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="b560a-187">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="b560a-188">Příklad je pro poskytovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="b560a-188">The example is for the Console provider.</span></span> <span data-ttu-id="b560a-189">Pokud zprostředkovatel podporuje [obory protokolů](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="b560a-189">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="b560a-190">Vlastnost zprostředkovatele (například `Console` v příkladu) může také určovat vlastnost `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="b560a-190">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="b560a-191">@no__t – 0 v rámci zprostředkovatele Určuje úrovně, které se mají protokolovat pro daného poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b560a-191">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="b560a-192">Pokud jsou úrovně zadány v `Logging.{providername}.LogLevel`, přepíše cokoli nastavené v `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="b560a-192">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

::: moniker-end

<span data-ttu-id="b560a-193">Informace o implementaci zprostředkovatelů konfigurace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b560a-193">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="b560a-194">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="b560a-194">Sample logging output</span></span>

<span data-ttu-id="b560a-195">Pomocí ukázkového kódu zobrazeného v předchozí části se protokoly zobrazí v konzole nástroje při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b560a-195">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="b560a-196">Tady je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="b560a-196">Here's an example of console output:</span></span>

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

<span data-ttu-id="b560a-197">Předchozí protokoly byly vygenerovány vytvořením žádosti HTTP GET do ukázkové aplikace na pozici `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="b560a-197">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="b560a-198">Tady je příklad stejného protokolu, který se zobrazí v okně ladění při spuštění ukázkové aplikace v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b560a-198">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="b560a-199">Protokoly, které jsou vytvářeny voláními `ILogger` zobrazenými v předchozí části, začínají na "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="b560a-199">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="b560a-200">Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b560a-200">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="b560a-201">ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="b560a-201">ASP.NET Core and application code are using the same logging API and providers.</span></span>

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

<span data-ttu-id="b560a-202">Protokoly, které jsou vytvářeny voláními `ILogger` zobrazenými v předchozí části, začínají na "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="b560a-202">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="b560a-203">Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b560a-203">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="b560a-204">ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="b560a-204">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="b560a-205">Zbývající část tohoto článku vysvětluje několik podrobností a možností protokolování.</span><span class="sxs-lookup"><span data-stu-id="b560a-205">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="b560a-206">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="b560a-206">NuGet packages</span></span>

<span data-ttu-id="b560a-207">Rozhraní `ILogger` a `ILoggerFactory` jsou v [Microsoft. Extensions. Loggings. abstrakce](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="b560a-207">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="b560a-208">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="b560a-208">Log category</span></span>

<span data-ttu-id="b560a-209">Je-li vytvořen objekt `ILogger`, je pro něj zadána *kategorie* .</span><span class="sxs-lookup"><span data-stu-id="b560a-209">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="b560a-210">Tato kategorie je součástí každé zprávy protokolu vytvořené touto instancí `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="b560a-210">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="b560a-211">Kategorie může být libovolný řetězec, ale konvence používá název třídy, jako je například "TodoApi. Controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="b560a-211">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="b560a-212">@No__t-0 použijte k získání instance `ILogger`, která jako kategorii používá plně kvalifikovaný název typu `T`:</span><span class="sxs-lookup"><span data-stu-id="b560a-212">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="b560a-213">Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="b560a-213">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="b560a-214">`ILogger<T>` je ekvivalentem volání `CreateLogger` s plně kvalifikovaným názvem typu `T`.</span><span class="sxs-lookup"><span data-stu-id="b560a-214">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="b560a-215">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="b560a-215">Log level</span></span>

<span data-ttu-id="b560a-216">Každý protokol Určuje hodnotu @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="b560a-216">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="b560a-217">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="b560a-217">The log level indicates the severity or importance.</span></span> <span data-ttu-id="b560a-218">Můžete například zapsat protokol `Information`, pokud metoda končí normálně, a protokol `Warning`, když metoda vrátí stavový kód 404, který *nebyl nalezen* .</span><span class="sxs-lookup"><span data-stu-id="b560a-218">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="b560a-219">Následující kód vytvoří protokoly `Information` a `Warning`:</span><span class="sxs-lookup"><span data-stu-id="b560a-219">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="b560a-220">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="b560a-220">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="b560a-221">Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="b560a-221">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="b560a-222">Parametry metody jsou vysvětleny v [části Šablona zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="b560a-222">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="b560a-223">Metody protokolu, které zahrnují úroveň v názvu metody (například `LogInformation` a `LogWarning`), jsou [metody rozšíření pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="b560a-223">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="b560a-224">Tyto metody volají metodu `Log`, která přebírá parametr `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="b560a-224">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="b560a-225">Metodu `Log` můžete volat přímo místo jedné z těchto rozšiřujících metod, ale syntaxe je poměrně složitá.</span><span class="sxs-lookup"><span data-stu-id="b560a-225">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="b560a-226">Další informace naleznete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a ve [zdrojovém kódu rozšíření protokolovacího](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)nástroje.</span><span class="sxs-lookup"><span data-stu-id="b560a-226">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="b560a-227">ASP.NET Core definuje následující úrovně protokolu seřazené od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="b560a-227">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="b560a-228">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="b560a-228">Trace = 0</span></span>

  <span data-ttu-id="b560a-229">Informace, které jsou obvykle užitečné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="b560a-229">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="b560a-230">Tyto zprávy mohou obsahovat citlivá data aplikací, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="b560a-230">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="b560a-231">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="b560a-231">*Disabled by default.*</span></span>

* <span data-ttu-id="b560a-232">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="b560a-232">Debug = 1</span></span>

  <span data-ttu-id="b560a-233">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="b560a-233">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="b560a-234">Příklad: `Entering method Configure with flag set to true.` povolit protokoly na úrovni `Debug` v produkčním prostředí, a to kvůli velkému objemu protokolů.</span><span class="sxs-lookup"><span data-stu-id="b560a-234">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="b560a-235">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="b560a-235">Information = 2</span></span>

  <span data-ttu-id="b560a-236">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="b560a-236">For tracking the general flow of the app.</span></span> <span data-ttu-id="b560a-237">Tyto protokoly obvykle mají nějakou dlouhodobou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b560a-237">These logs typically have some long-term value.</span></span> <span data-ttu-id="b560a-238">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="b560a-238">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="b560a-239">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="b560a-239">Warning = 3</span></span>

  <span data-ttu-id="b560a-240">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="b560a-240">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="b560a-241">Ty můžou zahrnovat chyby nebo jiné podmínky, které nezpůsobí zastavení aplikace, ale možná bude nutné je prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="b560a-241">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="b560a-242">Zpracování výjimek je běžné místo pro použití úrovně protokolu `Warning`.</span><span class="sxs-lookup"><span data-stu-id="b560a-242">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="b560a-243">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="b560a-243">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="b560a-244">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="b560a-244">Error = 4</span></span>

  <span data-ttu-id="b560a-245">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="b560a-245">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="b560a-246">Tyto zprávy indikují selhání aktuální aktivity nebo operace (jako je aktuální požadavek HTTP), ne při selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="b560a-246">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="b560a-247">Příklad zprávy protokolu: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="b560a-247">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="b560a-248">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="b560a-248">Critical = 5</span></span>

  <span data-ttu-id="b560a-249">Chyby, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="b560a-249">For failures that require immediate attention.</span></span> <span data-ttu-id="b560a-250">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="b560a-250">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="b560a-251">Úroveň protokolu můžete použít k určení, kolik výstupu protokolu se zapisuje do konkrétního úložného média nebo okna pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b560a-251">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="b560a-252">Například:</span><span class="sxs-lookup"><span data-stu-id="b560a-252">For example:</span></span>

* <span data-ttu-id="b560a-253">V produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="b560a-253">In production:</span></span>
  * <span data-ttu-id="b560a-254">Přihlášení na úrovni `Trace` až `Information` vytvoří velké množství podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="b560a-254">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="b560a-255">Pokud chcete řídit náklady a nepřekračuje limity úložiště dat, protokolujte `Trace` prostřednictvím zpráv na úrovni `Information` až po úložiště dat s nízkým objemem dat s nízkou úrovní nákladů.</span><span class="sxs-lookup"><span data-stu-id="b560a-255">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="b560a-256">Přihlášení na `Warning` až `Critical` úrovně obvykle vytváří méně a menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="b560a-256">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="b560a-257">Proto náklady a limity úložiště většinou nejsou obavy, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="b560a-257">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="b560a-258">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="b560a-258">During development:</span></span>
  * <span data-ttu-id="b560a-259">Protokoluje `Warning` až `Critical` zpráv do konzoly.</span><span class="sxs-lookup"><span data-stu-id="b560a-259">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="b560a-260">Při řešení potíží přidejte `Trace` až `Information` zprávy.</span><span class="sxs-lookup"><span data-stu-id="b560a-260">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="b560a-261">Část [filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovateli zpracovává.</span><span class="sxs-lookup"><span data-stu-id="b560a-261">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="b560a-262">ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b560a-262">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="b560a-263">Příklady protokolů výše v tomto článku vyloučily protokoly @no__t úroveň 0, takže se nevytvořily žádné protokoly úrovní `Debug` ani `Trace`.</span><span class="sxs-lookup"><span data-stu-id="b560a-263">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="b560a-264">Tady je příklad protokolů konzoly vytvořených spuštěním ukázkové aplikace nakonfigurované tak, aby zobrazovaly protokoly `Debug`:</span><span class="sxs-lookup"><span data-stu-id="b560a-264">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="b560a-265">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="b560a-265">Log event ID</span></span>

<span data-ttu-id="b560a-266">Každý protokol může určovat *ID události*.</span><span class="sxs-lookup"><span data-stu-id="b560a-266">Each log can specify an *event ID*.</span></span> <span data-ttu-id="b560a-267">Tato ukázková aplikace používá místně definovanou třídu `LoggingEvents`:</span><span class="sxs-lookup"><span data-stu-id="b560a-267">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="b560a-268">ID události přidružuje sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="b560a-268">An event ID associates a set of events.</span></span> <span data-ttu-id="b560a-269">Například všechny protokoly související se zobrazením seznamu položek na stránce může být 1001.</span><span class="sxs-lookup"><span data-stu-id="b560a-269">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="b560a-270">Zprostředkovatel protokolování může ukládat ID události v poli ID, ve zprávě protokolování nebo vůbec ne.</span><span class="sxs-lookup"><span data-stu-id="b560a-270">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="b560a-271">Zprostředkovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="b560a-271">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="b560a-272">Poskytovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="b560a-272">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="b560a-273">Šablona zprávy protokolu</span><span class="sxs-lookup"><span data-stu-id="b560a-273">Log message template</span></span>

<span data-ttu-id="b560a-274">Každý protokol Určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="b560a-274">Each log specifies a message template.</span></span> <span data-ttu-id="b560a-275">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="b560a-275">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="b560a-276">Použijte názvy zástupných symbolů, nikoli čísla.</span><span class="sxs-lookup"><span data-stu-id="b560a-276">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="b560a-277">Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry slouží k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="b560a-277">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="b560a-278">V následujícím kódu si všimněte, že názvy parametrů jsou v šabloně zprávy mimo pořadí:</span><span class="sxs-lookup"><span data-stu-id="b560a-278">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="b560a-279">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v posloupnosti:</span><span class="sxs-lookup"><span data-stu-id="b560a-279">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="b560a-280">Protokolovací rozhraní funguje tímto způsobem, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="b560a-280">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="b560a-281">Samotné argumenty jsou předány do protokolovacího systému, nikoli pouze ze šablony formátované zprávy.</span><span class="sxs-lookup"><span data-stu-id="b560a-281">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="b560a-282">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="b560a-282">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="b560a-283">Předpokládejme například, že volání metody protokolovacího nástroje vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="b560a-283">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="b560a-284">Pokud odesíláte protokoly do Azure Table Storage, Každá entita tabulky Azure může mít vlastnosti `ID` a `RequestTime`, což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="b560a-284">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="b560a-285">Dotaz může najít všechny protokoly v rámci určitého rozsahu @no__t 0, aniž by došlo k analýze času v textové zprávě.</span><span class="sxs-lookup"><span data-stu-id="b560a-285">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="b560a-286">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="b560a-286">Logging exceptions</span></span>

<span data-ttu-id="b560a-287">Metody protokolovacího nástroje mají přetížení, které umožňují předat výjimku, jak je uvedeno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b560a-287">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="b560a-288">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="b560a-288">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="b560a-289">Tady je příklad výstupu poskytovatele ladění z kódu uvedeného výše.</span><span class="sxs-lookup"><span data-stu-id="b560a-289">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="b560a-290">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="b560a-290">Log filtering</span></span>

<span data-ttu-id="b560a-291">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny poskytovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="b560a-291">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="b560a-292">Všechny protokoly nižší než minimální úroveň nebudou tomuto poskytovateli předány, takže se nebudou zobrazovat ani ukládat.</span><span class="sxs-lookup"><span data-stu-id="b560a-292">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="b560a-293">Chcete-li potlačit všechny protokoly, zadejte jako minimální úroveň protokolu `LogLevel.None`.</span><span class="sxs-lookup"><span data-stu-id="b560a-293">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="b560a-294">Celočíselná hodnota `LogLevel.None` je 6, což je více než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="b560a-294">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="b560a-295">Vytvořit pravidla filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="b560a-295">Create filter rules in configuration</span></span>

<span data-ttu-id="b560a-296">Kód šablony projektu volá `CreateDefaultBuilder` k nastavení protokolování pro konzolu a zprostředkovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="b560a-296">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="b560a-297">Metoda `CreateDefaultBuilder` nastaví protokolování tak, aby hledalo konfiguraci v části `Logging`, jak je vysvětleno [výše v tomto článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="b560a-297">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="b560a-298">Konfigurační data určují minimální úrovně protokolu podle poskytovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b560a-298">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="b560a-299">Tento kód JSON vytvoří šest pravidel filtru: jeden pro poskytovatele ladění, čtyři pro poskytovatele konzoly a jeden pro všechny poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b560a-299">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="b560a-300">Jedno pravidlo je vybráno pro každého poskytovatele při vytvoření objektu `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="b560a-300">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="b560a-301">Filtrovat pravidla v kódu</span><span class="sxs-lookup"><span data-stu-id="b560a-301">Filter rules in code</span></span>

<span data-ttu-id="b560a-302">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="b560a-302">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="b560a-303">Druhý `AddFilter` určuje poskytovatele ladění pomocí jeho názvu typu.</span><span class="sxs-lookup"><span data-stu-id="b560a-303">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="b560a-304">První `AddFilter` se vztahuje na všechny poskytovatele, protože neurčuje typ poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b560a-304">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="b560a-305">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="b560a-305">How filtering rules are applied</span></span>

<span data-ttu-id="b560a-306">Konfigurační data a kód `AddFilter` uvedené v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b560a-306">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="b560a-307">Prvních šest přicházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="b560a-307">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="b560a-308">Číslo</span><span class="sxs-lookup"><span data-stu-id="b560a-308">Number</span></span> | <span data-ttu-id="b560a-309">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b560a-309">Provider</span></span>      | <span data-ttu-id="b560a-310">Kategorie, které začínají na...</span><span class="sxs-lookup"><span data-stu-id="b560a-310">Categories that begin with ...</span></span>          | <span data-ttu-id="b560a-311">Minimální úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="b560a-311">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="b560a-312">1</span><span class="sxs-lookup"><span data-stu-id="b560a-312">1</span></span>      | <span data-ttu-id="b560a-313">Ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-313">Debug</span></span>         | <span data-ttu-id="b560a-314">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="b560a-314">All categories</span></span>                          | <span data-ttu-id="b560a-315">Informace</span><span class="sxs-lookup"><span data-stu-id="b560a-315">Information</span></span>       |
| <span data-ttu-id="b560a-316">2</span><span class="sxs-lookup"><span data-stu-id="b560a-316">2</span></span>      | <span data-ttu-id="b560a-317">Stromu</span><span class="sxs-lookup"><span data-stu-id="b560a-317">Console</span></span>       | <span data-ttu-id="b560a-318">Microsoft. AspNetCore. Mvc. Razor. Internal</span><span class="sxs-lookup"><span data-stu-id="b560a-318">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="b560a-319">Upozornění</span><span class="sxs-lookup"><span data-stu-id="b560a-319">Warning</span></span>           |
| <span data-ttu-id="b560a-320">3</span><span class="sxs-lookup"><span data-stu-id="b560a-320">3</span></span>      | <span data-ttu-id="b560a-321">Stromu</span><span class="sxs-lookup"><span data-stu-id="b560a-321">Console</span></span>       | <span data-ttu-id="b560a-322">Microsoft. AspNetCore. Mvc. Razor. Razor</span><span class="sxs-lookup"><span data-stu-id="b560a-322">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="b560a-323">Ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-323">Debug</span></span>             |
| <span data-ttu-id="b560a-324">4</span><span class="sxs-lookup"><span data-stu-id="b560a-324">4</span></span>      | <span data-ttu-id="b560a-325">Stromu</span><span class="sxs-lookup"><span data-stu-id="b560a-325">Console</span></span>       | <span data-ttu-id="b560a-326">Microsoft. AspNetCore. Mvc. Razor</span><span class="sxs-lookup"><span data-stu-id="b560a-326">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="b560a-327">Chyba</span><span class="sxs-lookup"><span data-stu-id="b560a-327">Error</span></span>             |
| <span data-ttu-id="b560a-328">5</span><span class="sxs-lookup"><span data-stu-id="b560a-328">5</span></span>      | <span data-ttu-id="b560a-329">Stromu</span><span class="sxs-lookup"><span data-stu-id="b560a-329">Console</span></span>       | <span data-ttu-id="b560a-330">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="b560a-330">All categories</span></span>                          | <span data-ttu-id="b560a-331">Informace</span><span class="sxs-lookup"><span data-stu-id="b560a-331">Information</span></span>       |
| <span data-ttu-id="b560a-332">6</span><span class="sxs-lookup"><span data-stu-id="b560a-332">6</span></span>      | <span data-ttu-id="b560a-333">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="b560a-333">All providers</span></span> | <span data-ttu-id="b560a-334">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="b560a-334">All categories</span></span>                          | <span data-ttu-id="b560a-335">Ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-335">Debug</span></span>             |
| <span data-ttu-id="b560a-336">7</span><span class="sxs-lookup"><span data-stu-id="b560a-336">7</span></span>      | <span data-ttu-id="b560a-337">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="b560a-337">All providers</span></span> | <span data-ttu-id="b560a-338">Systému</span><span class="sxs-lookup"><span data-stu-id="b560a-338">System</span></span>                                  | <span data-ttu-id="b560a-339">Ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-339">Debug</span></span>             |
| <span data-ttu-id="b560a-340">8</span><span class="sxs-lookup"><span data-stu-id="b560a-340">8</span></span>      | <span data-ttu-id="b560a-341">Ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-341">Debug</span></span>         | <span data-ttu-id="b560a-342">Microsoft</span><span class="sxs-lookup"><span data-stu-id="b560a-342">Microsoft</span></span>                               | <span data-ttu-id="b560a-343">Trasování</span><span class="sxs-lookup"><span data-stu-id="b560a-343">Trace</span></span>             |

<span data-ttu-id="b560a-344">Když se vytvoří objekt `ILogger`, objekt `ILoggerFactory` vybere jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="b560a-344">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="b560a-345">Všechny zprávy napsané instancí `ILogger` jsou filtrovány na základě vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="b560a-345">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="b560a-346">V dostupných pravidlech se vybere nejpřesnější pravidlo pro jednotlivé dvojice zprostředkovatel a kategorie.</span><span class="sxs-lookup"><span data-stu-id="b560a-346">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="b560a-347">Následující algoritmus se používá pro každého poskytovatele, když se pro danou kategorii vytvoří `ILogger`:</span><span class="sxs-lookup"><span data-stu-id="b560a-347">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="b560a-348">Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem.</span><span class="sxs-lookup"><span data-stu-id="b560a-348">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="b560a-349">Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="b560a-349">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="b560a-350">Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie.</span><span class="sxs-lookup"><span data-stu-id="b560a-350">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="b560a-351">Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="b560a-351">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="b560a-352">Pokud je vybráno více pravidel, vezměte **Poslední** z nich.</span><span class="sxs-lookup"><span data-stu-id="b560a-352">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="b560a-353">Pokud nejsou vybraná žádná pravidla, použijte `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="b560a-353">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="b560a-354">Pomocí předchozího seznamu pravidel Předpokládejme, že vytvoříte objekt `ILogger` pro kategorii "Microsoft. AspNetCore. Mvc. Razor. RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="b560a-354">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="b560a-355">Pro poskytovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="b560a-355">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="b560a-356">Pravidlo 8 je nejvíce specifické, takže je to ten vybraný.</span><span class="sxs-lookup"><span data-stu-id="b560a-356">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="b560a-357">Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="b560a-357">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="b560a-358">Pravidlo 3 je nejvíce specifické.</span><span class="sxs-lookup"><span data-stu-id="b560a-358">Rule 3 is most specific.</span></span>

<span data-ttu-id="b560a-359">Výsledná instance `ILogger` odesílá do poskytovatele ladění protokoly `Trace` a vyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="b560a-359">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="b560a-360">Do poskytovatele konzoly se odesílají protokoly @no__t úrovně 0 a vyšší.</span><span class="sxs-lookup"><span data-stu-id="b560a-360">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="b560a-361">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="b560a-361">Provider aliases</span></span>

<span data-ttu-id="b560a-362">Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu.</span><span class="sxs-lookup"><span data-stu-id="b560a-362">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="b560a-363">Pro předdefinované poskytovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="b560a-363">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="b560a-364">Stromu</span><span class="sxs-lookup"><span data-stu-id="b560a-364">Console</span></span>
* <span data-ttu-id="b560a-365">Ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-365">Debug</span></span>
* <span data-ttu-id="b560a-366">EventSource</span><span class="sxs-lookup"><span data-stu-id="b560a-366">EventSource</span></span>
* <span data-ttu-id="b560a-367">EventLog</span><span class="sxs-lookup"><span data-stu-id="b560a-367">EventLog</span></span>
* <span data-ttu-id="b560a-368">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b560a-368">TraceSource</span></span>
* <span data-ttu-id="b560a-369">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b560a-369">AzureAppServicesFile</span></span>
* <span data-ttu-id="b560a-370">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b560a-370">AzureAppServicesBlob</span></span>
* <span data-ttu-id="b560a-371">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b560a-371">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="b560a-372">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="b560a-372">Default minimum level</span></span>

<span data-ttu-id="b560a-373">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že se pro daného zprostředkovatele a kategorii nepoužijí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="b560a-373">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="b560a-374">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="b560a-374">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="b560a-375">Pokud nenastavíte explicitně minimální úroveň, výchozí hodnota je `Information`, což znamená, že protokoly `Trace` a `Debug` budou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="b560a-375">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="b560a-376">Funkce filtru</span><span class="sxs-lookup"><span data-stu-id="b560a-376">Filter functions</span></span>

<span data-ttu-id="b560a-377">Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="b560a-377">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="b560a-378">Kód ve funkci má přístup k typu poskytovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="b560a-378">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="b560a-379">Například:</span><span class="sxs-lookup"><span data-stu-id="b560a-379">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="b560a-380">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="b560a-380">System categories and levels</span></span>

<span data-ttu-id="b560a-381">Tady je několik kategorií používaných ASP.NET Core a Entity Framework Core s poznámkami o tom, jaké protokoly se mají od nich očekávat:</span><span class="sxs-lookup"><span data-stu-id="b560a-381">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="b560a-382">Category</span><span class="sxs-lookup"><span data-stu-id="b560a-382">Category</span></span>                            | <span data-ttu-id="b560a-383">Poznámky</span><span class="sxs-lookup"><span data-stu-id="b560a-383">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="b560a-384">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="b560a-384">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="b560a-385">Obecná diagnostika ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b560a-385">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="b560a-386">Microsoft. AspNetCore. DataProtection</span><span class="sxs-lookup"><span data-stu-id="b560a-386">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="b560a-387">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="b560a-387">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="b560a-388">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="b560a-388">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="b560a-389">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="b560a-389">Hosts allowed.</span></span> |
| <span data-ttu-id="b560a-390">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="b560a-390">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="b560a-391">Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění.</span><span class="sxs-lookup"><span data-stu-id="b560a-391">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="b560a-392">Která hostující spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="b560a-392">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="b560a-393">Microsoft. AspNetCore. Mvc</span><span class="sxs-lookup"><span data-stu-id="b560a-393">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="b560a-394">Diagnostika MVC a Razor</span><span class="sxs-lookup"><span data-stu-id="b560a-394">MVC and Razor diagnostics.</span></span> <span data-ttu-id="b560a-395">Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="b560a-395">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="b560a-396">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="b560a-396">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="b560a-397">Informace o shodě trasy.</span><span class="sxs-lookup"><span data-stu-id="b560a-397">Route matching information.</span></span> |
| <span data-ttu-id="b560a-398">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="b560a-398">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="b560a-399">Připojení – spouštění, zastavování a udržování reakcí na Alive.</span><span class="sxs-lookup"><span data-stu-id="b560a-399">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="b560a-400">Informace o certifikátu HTTPS</span><span class="sxs-lookup"><span data-stu-id="b560a-400">HTTPS certificate information.</span></span> |
| <span data-ttu-id="b560a-401">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="b560a-401">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="b560a-402">Soubory byly obsluhovány.</span><span class="sxs-lookup"><span data-stu-id="b560a-402">Files served.</span></span> |
| <span data-ttu-id="b560a-403">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b560a-403">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="b560a-404">Obecná diagnostika Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b560a-404">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="b560a-405">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="b560a-405">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="b560a-406">Rozsahy protokolů</span><span class="sxs-lookup"><span data-stu-id="b560a-406">Log scopes</span></span>

 <span data-ttu-id="b560a-407">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="b560a-407">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="b560a-408">Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="b560a-408">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="b560a-409">Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="b560a-409">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="b560a-410">Obor je typ @no__t 0, který je vrácený metodou <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> a trvá, dokud nebude uvolněn.</span><span class="sxs-lookup"><span data-stu-id="b560a-410">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="b560a-411">Použijte rozsah vybalením volání protokolovacího nástroje v bloku `using`:</span><span class="sxs-lookup"><span data-stu-id="b560a-411">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="b560a-412">Následující kód umožňuje obory pro poskytovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="b560a-412">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="b560a-413">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b560a-413">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b560a-414">Pro povolení protokolování založeného na rozsahu je nutná konfigurace možnosti protokolovacího nástroje konzoly `IncludeScopes`.</span><span class="sxs-lookup"><span data-stu-id="b560a-414">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="b560a-415">Informace o konfiguraci najdete v části věnované [konfiguraci](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="b560a-415">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="b560a-416">Každá zpráva protokolu obsahuje informace s vymezeným oborem:</span><span class="sxs-lookup"><span data-stu-id="b560a-416">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="b560a-417">Vestavění zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="b560a-417">Built-in logging providers</span></span>

<span data-ttu-id="b560a-418">ASP.NET Core dodává následující poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="b560a-418">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="b560a-419">Stromu</span><span class="sxs-lookup"><span data-stu-id="b560a-419">Console</span></span>](#console-provider)
* [<span data-ttu-id="b560a-420">Ladí</span><span class="sxs-lookup"><span data-stu-id="b560a-420">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="b560a-421">EventSource</span><span class="sxs-lookup"><span data-stu-id="b560a-421">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="b560a-422">EventLog</span><span class="sxs-lookup"><span data-stu-id="b560a-422">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="b560a-423">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b560a-423">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="b560a-424">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b560a-424">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b560a-425">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b560a-425">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b560a-426">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b560a-426">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="b560a-427">Informace o stdout a protokolování ladění pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b560a-427">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="b560a-428">Poskytovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="b560a-428">Console provider</span></span>

<span data-ttu-id="b560a-429">Balíček poskytovatele [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="b560a-429">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="b560a-430">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b560a-430">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="b560a-431">Poskytovatel ladění</span><span class="sxs-lookup"><span data-stu-id="b560a-431">Debug provider</span></span>

<span data-ttu-id="b560a-432">Balíček poskytovatele [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (volání metody `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="b560a-432">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="b560a-433">V systému Linux tento poskytovatel zapisuje protokoly do */var/log/Message*.</span><span class="sxs-lookup"><span data-stu-id="b560a-433">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="eventsource-provider"></a><span data-ttu-id="b560a-434">Zprostředkovatel EventSource</span><span class="sxs-lookup"><span data-stu-id="b560a-434">EventSource provider</span></span>

<span data-ttu-id="b560a-435">Pro aplikace cílené na ASP.NET Core 1.1.0 nebo novější může balíček zprostředkovatele [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) implementovat trasování událostí.</span><span class="sxs-lookup"><span data-stu-id="b560a-435">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="b560a-436">Ve Windows používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="b560a-436">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="b560a-437">Poskytovatel je pro různé platformy, ale pro Linux nebo macOS ještě nejsou k dispozici žádné nástroje pro shromažďování událostí a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b560a-437">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="b560a-438">Dobrým způsobem, jak shromažďovat a zobrazovat protokoly, je použít [Nástroj PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="b560a-438">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="b560a-439">Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b560a-439">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="b560a-440">Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte řetězec `*Microsoft-Extensions-Logging` do seznamu **dalších zprostředkovatelů** .</span><span class="sxs-lookup"><span data-stu-id="b560a-440">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="b560a-441">(Na začátku řetězce nechybíš hvězdičku.)</span><span class="sxs-lookup"><span data-stu-id="b560a-441">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="b560a-443">Zprostředkovatel událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="b560a-443">Windows EventLog provider</span></span>

<span data-ttu-id="b560a-444">Balíček poskytovatele [Microsoft. Extensions. log. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="b560a-444">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="b560a-445">[AddEventLog přetížení](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="b560a-445">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span>

### <a name="tracesource-provider"></a><span data-ttu-id="b560a-446">Poskytovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="b560a-446">TraceSource provider</span></span>

<span data-ttu-id="b560a-447">Balíček poskytovatele [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá knihovny a poskytovatele <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="b560a-447">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="b560a-448">[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="b560a-448">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="b560a-449">Aby bylo možné používat tohoto poskytovatele, aplikace musí běžet na .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="b560a-449">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="b560a-450">Poskytovatel může směrovat zprávy na celou řadu [posluchačů](/dotnet/framework/debug-trace-profile/trace-listeners), jako je například <xref:System.Diagnostics.TextWriterTraceListener> použitý v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b560a-450">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="b560a-451">Poskytovatel Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b560a-451">Azure App Service provider</span></span>

<span data-ttu-id="b560a-452">Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.</span><span class="sxs-lookup"><span data-stu-id="b560a-452">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b560a-453">Balíček poskytovatele není zahrnutý ve sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b560a-453">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="b560a-454">Chcete-li použít poskytovatele, přidejte do projektu balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b560a-454">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="b560a-455">Balíček poskytovatele není zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b560a-455">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b560a-456">Při cílení na .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` Metapackage přidejte do projektu balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b560a-456">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b560a-457">Chcete-li nakonfigurovat nastavení zprostředkovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b560a-457">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b560a-458">Chcete-li nakonfigurovat nastavení zprostředkovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b560a-458">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b560a-459">Přetížení <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> umožňuje předat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="b560a-459">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="b560a-460">Objekt nastavení může přepsat výchozí nastavení, jako je například šablona výstupu protokolování, název objektu BLOB a omezení velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="b560a-460">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="b560a-461">(*Výstupní šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici s voláním metody `ILogger`.)</span><span class="sxs-lookup"><span data-stu-id="b560a-461">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="b560a-462">Když nasadíte aplikaci do App Service, aplikace respektuje nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) stránky **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b560a-462">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="b560a-463">Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b560a-463">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="b560a-464">**Protokolování aplikace (systém souborů)**</span><span class="sxs-lookup"><span data-stu-id="b560a-464">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="b560a-465">**Protokolování aplikace (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="b560a-465">**Application Logging (Blob)**</span></span>

<span data-ttu-id="b560a-466">Výchozí umístění souborů protokolu je ve složce *D: \\home @ no__t-2LogFiles @ no__t-3Application* a výchozí název souboru je *Diagnostics-YYYYMMDD. txt*.</span><span class="sxs-lookup"><span data-stu-id="b560a-466">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="b560a-467">Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="b560a-467">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="b560a-468">Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/MM/DD/hh/{GUID}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="b560a-468">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="b560a-469">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="b560a-469">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="b560a-470">Nemá žádný vliv, pokud se projekt spouští místně @ no__t-0it nepíše do místních souborů nebo místního vývojového úložiště pro objekty blob.</span><span class="sxs-lookup"><span data-stu-id="b560a-470">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="b560a-471">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="b560a-471">Azure log streaming</span></span>

<span data-ttu-id="b560a-472">Streamování protokolů Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="b560a-472">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="b560a-473">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="b560a-473">The app server</span></span>
* <span data-ttu-id="b560a-474">Webový server</span><span class="sxs-lookup"><span data-stu-id="b560a-474">The web server</span></span>
* <span data-ttu-id="b560a-475">Trasování chybných požadavků</span><span class="sxs-lookup"><span data-stu-id="b560a-475">Failed request tracing</span></span>

<span data-ttu-id="b560a-476">Konfigurace streamování protokolů Azure:</span><span class="sxs-lookup"><span data-stu-id="b560a-476">To configure Azure log streaming:</span></span>

* <span data-ttu-id="b560a-477">Na stránce portálu vaší aplikace přejděte na stránku **protokoly App Service** .</span><span class="sxs-lookup"><span data-stu-id="b560a-477">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="b560a-478">Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="b560a-478">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="b560a-479">Vyberte **úroveň**protokolu.</span><span class="sxs-lookup"><span data-stu-id="b560a-479">Choose the log **Level**.</span></span>

<span data-ttu-id="b560a-480">Pokud chcete zobrazit zprávy aplikace, přejděte na stránku **streamu protokolu** .</span><span class="sxs-lookup"><span data-stu-id="b560a-480">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="b560a-481">Protokolují ho aplikace přes rozhraní @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="b560a-481">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="b560a-482">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="b560a-482">Azure Application Insights trace logging</span></span>

<span data-ttu-id="b560a-483">Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b560a-483">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="b560a-484">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie.</span><span class="sxs-lookup"><span data-stu-id="b560a-484">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="b560a-485">Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.</span><span class="sxs-lookup"><span data-stu-id="b560a-485">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="b560a-486">Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b560a-486">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="b560a-487">Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b560a-487">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="b560a-488">Nepoužívejte balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) @ no__t-1that's pro ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="b560a-488">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="b560a-489">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="b560a-489">For more information, see the following resources:</span></span>

* [<span data-ttu-id="b560a-490">Přehled Application Insights</span><span class="sxs-lookup"><span data-stu-id="b560a-490">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="b560a-491">[Application Insights pro ASP.NET Core aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou řadu Application Insights telemetrie spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="b560a-491">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="b560a-492">[ApplicationInsightsLoggerProvider pro protokoly .NET Core ILogger](/azure/azure-monitor/app/ilogger) – začněte sem, pokud chcete implementovat poskytovatele protokolování bez zbytku Application Insights telemetrie.</span><span class="sxs-lookup"><span data-stu-id="b560a-492">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="b560a-493">[Application Insights adaptéry protokolování](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="b560a-493">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="b560a-494">[Instalace, konfigurace a inicializace Application Insights sady SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="b560a-494">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="b560a-495">Zprostředkovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="b560a-495">Third-party logging providers</span></span>

<span data-ttu-id="b560a-496">Protokolovací architektury třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b560a-496">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="b560a-497">[elmah.IO](https://elmah.io/) ([úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b560a-497">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="b560a-498">[GELF](https://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="b560a-498">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="b560a-499">[JSNLog](https://jsnlog.com/) ([úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="b560a-499">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="b560a-500">[KissLog.NET](https://kisslog.net/) ([úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="b560a-500">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="b560a-501">[Log4Net](https://logging.apache.org/log4net/) ([úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="b560a-501">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="b560a-502">[Loggr](https://loggr.net/) ([úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b560a-502">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="b560a-503">[NLOG](https://nlog-project.org/) ([úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b560a-503">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="b560a-504">[Sentry](https://sentry.io/welcome/) ([úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="b560a-504">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="b560a-505">[Serilog](https://serilog.net/) ([úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="b560a-505">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="b560a-506">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="b560a-506">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="b560a-507">Některé architektury třetích stran můžou provádět [sémantické protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="b560a-507">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="b560a-508">Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="b560a-508">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="b560a-509">Přidejte do projektu balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b560a-509">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="b560a-510">Zavolejte metodu rozšíření `ILoggerFactory` poskytovanou protokolovacím rozhraním.</span><span class="sxs-lookup"><span data-stu-id="b560a-510">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="b560a-511">Další informace najdete v dokumentaci pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b560a-511">For more information, see each provider's documentation.</span></span> <span data-ttu-id="b560a-512">Microsoft nepodporuje zprostředkovatele protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="b560a-512">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b560a-513">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b560a-513">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

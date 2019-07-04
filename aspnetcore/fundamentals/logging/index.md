---
title: Protokolování v ASP.NET Core
author: tdykstra
description: Další informace o protokolovacího rozhraní v ASP.NET Core. Objevte poskytovatelé vestavěné protokolování a další informace o Oblíbené zprostředkovatele třetí strany.
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/01/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 41135f04c9587f77dd417f63df2c2a70c2d545cd
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561666"
---
# <a name="logging-in-aspnet-core"></a><span data-ttu-id="64ab5-104">Protokolování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64ab5-104">Logging in ASP.NET Core</span></span>

<span data-ttu-id="64ab5-105">Podle [Steve Smith](https://ardalis.com/) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="64ab5-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="64ab5-106">ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran.</span><span class="sxs-lookup"><span data-stu-id="64ab5-106">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="64ab5-107">Tento článek ukazuje, jak používat rozhraní API protokolování s předdefinované zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-107">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="64ab5-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="64ab5-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="64ab5-109">Přidat zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="64ab5-109">Add providers</span></span>

<span data-ttu-id="64ab5-110">Zprostředkovatel protokolování zobrazí nebo ukládají protokoly.</span><span class="sxs-lookup"><span data-stu-id="64ab5-110">A logging provider displays or stores logs.</span></span> <span data-ttu-id="64ab5-111">Například konzola poskytovatel zobrazí protokoly v konzole a uloží je zprostředkovatel služby Azure Application Insights ve službě Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="64ab5-111">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="64ab5-112">Protokoly můžete odeslány do více cílů přidáním více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="64ab5-112">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="64ab5-113">Chcete-li přidat poskytovatele, zavolejte poskytovatele `Add{provider name}` metody rozšíření v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ab5-113">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="64ab5-114">Předcházející kód vyžaduje odkazy `Microsoft.Extensions.Logging` a `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="64ab5-114">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="64ab5-115">Volání výchozí projekt šablony <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, který přidá následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="64ab5-115">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="64ab5-116">Konzola</span><span class="sxs-lookup"><span data-stu-id="64ab5-116">Console</span></span>
* <span data-ttu-id="64ab5-117">Ladění</span><span class="sxs-lookup"><span data-stu-id="64ab5-117">Debug</span></span>
* <span data-ttu-id="64ab5-118">EventSource (od verze 2.2 technologie ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="64ab5-118">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="64ab5-119">Pokud používáte `CreateDefaultBuilder`, výchozí poskytovatele můžete nahradit vlastními volbami.</span><span class="sxs-lookup"><span data-stu-id="64ab5-119">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="64ab5-120">Volání <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, a přidat zprostředkovatele, který chcete.</span><span class="sxs-lookup"><span data-stu-id="64ab5-120">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="64ab5-121">Používat poskytovatele, nainstalujte svůj balíček NuGet a volání metody rozšíření zprostředkovatele na instanci <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span><span class="sxs-lookup"><span data-stu-id="64ab5-121">To use a provider, install its NuGet package and call the provider's extension method on an instance of <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="64ab5-122">ASP.NET Core [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) poskytuje `ILoggerFactory` instance.</span><span class="sxs-lookup"><span data-stu-id="64ab5-122">ASP.NET Core [dependency injection (DI)](xref:fundamentals/dependency-injection) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="64ab5-123">`AddConsole` a `AddDebug` metody rozšíření jsou definovány v [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) a [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) balíčky.</span><span class="sxs-lookup"><span data-stu-id="64ab5-123">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="64ab5-124">Každá metoda rozšíření volá `ILoggerFactory.AddProvider` metodu instance zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-124">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span>

> [!NOTE]
> <span data-ttu-id="64ab5-125">[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) přidá poskytovatele protokolování v `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="64ab5-125">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adds logging providers in the `Startup.Configure` method.</span></span> <span data-ttu-id="64ab5-126">K získání výstupu protokolu z kódu, který se spustí dříve, přidejte poskytovatelů protokolování v `Startup` konstruktoru třídy.</span><span class="sxs-lookup"><span data-stu-id="64ab5-126">To obtain log output from code that executes earlier, add logging providers in the `Startup` class constructor.</span></span>

::: moniker-end

<span data-ttu-id="64ab5-127">Další informace o [vestavěné protokolování poskytovatelé](#built-in-logging-providers) a [zprostředkovatele přihlášení třetí strany](#third-party-logging-providers) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="64ab5-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="64ab5-128">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="64ab5-128">Create logs</span></span>

<span data-ttu-id="64ab5-129">Získat <xref:Microsoft.Extensions.Logging.ILogger%601> objekt z DI.</span><span class="sxs-lookup"><span data-stu-id="64ab5-129">Get an <xref:Microsoft.Extensions.Logging.ILogger%601> object from DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="64ab5-130">Následující příklad řadič vytvoří `Information` a `Warning` protokoly.</span><span class="sxs-lookup"><span data-stu-id="64ab5-130">The following controller example creates `Information` and `Warning` logs.</span></span> <span data-ttu-id="64ab5-131">*Kategorie* je `TodoApiSample.Controllers.TodoController` (plně kvalifikovaný název třídy `TodoController` v ukázkové aplikaci):</span><span class="sxs-lookup"><span data-stu-id="64ab5-131">The *category* is `TodoApiSample.Controllers.TodoController` (the fully qualified class name of `TodoController` in the sample app):</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="64ab5-132">Následující příklad stránky Razor vytvoří protokolů pomocí `Information` jako *úroveň* a `TodoApiSample.Pages.AboutModel` jako *kategorie*:</span><span class="sxs-lookup"><span data-stu-id="64ab5-132">The following Razor Pages example creates logs with `Information` as the *level* and `TodoApiSample.Pages.AboutModel` as the *category*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="64ab5-133">V předchozím příkladu se vytvoří protokolů pomocí `Information` a `Warning` jako *úroveň* a `TodoController` třídy jako *kategorie*.</span><span class="sxs-lookup"><span data-stu-id="64ab5-133">The preceding example creates logs with `Information` and `Warning` as the *level* and `TodoController` class as the *category*.</span></span> 

::: moniker-end

<span data-ttu-id="64ab5-134">Protokol *úroveň* označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="64ab5-134">The Log *level* indicates the severity of the logged event.</span></span> <span data-ttu-id="64ab5-135">Protokol *kategorie* je řetězec, který je spojen s každou protokolu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-135">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="64ab5-136">`ILogger<T>` Instance vytvářejí protokoly, které mají plně kvalifikovaný název typu `T` kategorii.</span><span class="sxs-lookup"><span data-stu-id="64ab5-136">The `ILogger<T>` instance creates logs that have the fully qualified name of type `T` as the category.</span></span> <span data-ttu-id="64ab5-137">[Úrovně](#log-level) a [kategorie](#log-category) jsou vysvětlené podrobněji dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="64ab5-137">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="64ab5-138">Vytvořit protokoly v po spuštění</span><span class="sxs-lookup"><span data-stu-id="64ab5-138">Create logs in Startup</span></span>

<span data-ttu-id="64ab5-139">Zápis protokolů `Startup` třídy, patří `ILogger` parametr v konstruktoru podpis:</span><span class="sxs-lookup"><span data-stu-id="64ab5-139">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a><span data-ttu-id="64ab5-140">Vytvořit protokoly v aplikaci</span><span class="sxs-lookup"><span data-stu-id="64ab5-140">Create logs in Program</span></span>

<span data-ttu-id="64ab5-141">Zápis protokolů `Program` třídy, získat `ILogger` instanci z DI:</span><span class="sxs-lookup"><span data-stu-id="64ab5-141">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="64ab5-142">Žádné metody na asynchronní protokolovací nástroj</span><span class="sxs-lookup"><span data-stu-id="64ab5-142">No asynchronous logger methods</span></span>

<span data-ttu-id="64ab5-143">Protokolování by měl být tak rychle, že se vyplatí výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-143">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="64ab5-144">Pokud vaše úložiště dat protokolování je pomalá, nemáte přímý zápis.</span><span class="sxs-lookup"><span data-stu-id="64ab5-144">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="64ab5-145">Vezměte v úvahu zpočátku zápis zpráv protokolu do rychlého úložiště a pak později přesunout k úložišti pomalé.</span><span class="sxs-lookup"><span data-stu-id="64ab5-145">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="64ab5-146">Například pokud jste protokolování na SQL Server, nechcete provést přímo v `Log` metody, protože `Log` metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="64ab5-146">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="64ab5-147">Místo toho synchronně přidání protokolu zpráv do fronty v paměti a mají pracovní proces na pozadí zpráv z fronty pro asynchronní práci z odesílání dat do SQL serveru.</span><span class="sxs-lookup"><span data-stu-id="64ab5-147">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="64ab5-148">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="64ab5-148">Configuration</span></span>

<span data-ttu-id="64ab5-149">Zprostředkovatel konfigurace protokolování poskytuje jeden nebo více poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="64ab5-149">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="64ab5-150">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="64ab5-150">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="64ab5-151">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="64ab5-151">Command-line arguments.</span></span>
* <span data-ttu-id="64ab5-152">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="64ab5-152">Environment variables.</span></span>
* <span data-ttu-id="64ab5-153">Objekty .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="64ab5-153">In-memory .NET objects.</span></span>
* <span data-ttu-id="64ab5-154">Nešifrované [manažera tajných](xref:security/app-secrets) úložiště.</span><span class="sxs-lookup"><span data-stu-id="64ab5-154">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="64ab5-155">Uložení šifrovaného uživatelského, jako například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="64ab5-155">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="64ab5-156">Vlastní zprostředkovatelé (nainstalované nebo vytváření).</span><span class="sxs-lookup"><span data-stu-id="64ab5-156">Custom providers (installed or created).</span></span>

<span data-ttu-id="64ab5-157">Například konfigurace protokolování běžně poskytované `Logging` části souborů s nastavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="64ab5-157">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="64ab5-158">Následující příklad ukazuje obsah typické *appsettings. Development.JSON* souboru:</span><span class="sxs-lookup"><span data-stu-id="64ab5-158">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="64ab5-159">`Logging` Vlastnost může mít `LogLevel` a protokolu vlastnosti zprostředkovatele (konzoly se zobrazí).</span><span class="sxs-lookup"><span data-stu-id="64ab5-159">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="64ab5-160">`LogLevel` Vlastnosti v části `Logging` Určuje minimální [úroveň](#log-level) k protokolování pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="64ab5-160">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="64ab5-161">V tomto příkladu `System` a `Microsoft` kategorie protokolu `Information` úroveň a všech ostatních protokolů na `Debug` úroveň.</span><span class="sxs-lookup"><span data-stu-id="64ab5-161">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="64ab5-162">Další vlastnosti v části `Logging` určit konkrétní zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-162">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="64ab5-163">V příkladu se pro zprostředkovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="64ab5-163">The example is for the Console provider.</span></span> <span data-ttu-id="64ab5-164">Pokud poskytovatel podporuje [protokolu obory](#log-scopes), `IncludeScopes` označuje, zda jsou povolena.</span><span class="sxs-lookup"><span data-stu-id="64ab5-164">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="64ab5-165">Vlastnost zprostředkovatele (například `Console` v příkladu) může také určit `LogLevel` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="64ab5-165">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="64ab5-166">`LogLevel` v části zprostředkovatele Určuje úrovně pro přihlášení pro daného poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-166">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="64ab5-167">Pokud úrovně jsou určené v `Logging.{providername}.LogLevel`, má přednost před nic nastavit `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="64ab5-167">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

<span data-ttu-id="64ab5-168">`LogLevel` klíče představují názvy protokolů.</span><span class="sxs-lookup"><span data-stu-id="64ab5-168">`LogLevel` keys represent log names.</span></span> <span data-ttu-id="64ab5-169">`Default` Klíč platí do protokolů, které nejsou výslovně uvedena.</span><span class="sxs-lookup"><span data-stu-id="64ab5-169">The `Default` key applies to logs not explicitly listed.</span></span> <span data-ttu-id="64ab5-170">Hodnota představuje [úrovně protokolování](#log-level) použitý pro daný protokol.</span><span class="sxs-lookup"><span data-stu-id="64ab5-170">The value represents the [log level](#log-level) applied to the given log.</span></span>

::: moniker-end

<span data-ttu-id="64ab5-171">Informace o implementaci zprostředkovatele konfigurace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="64ab5-171">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="64ab5-172">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="64ab5-172">Sample logging output</span></span>

<span data-ttu-id="64ab5-173">Ukázkový kód je znázorněno v předchozí části protokolů se objeví v konzole při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="64ab5-173">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="64ab5-174">Tady je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="64ab5-174">Here's an example of console output:</span></span>

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

<span data-ttu-id="64ab5-175">Protokoly předchozích nevygenerovaly se tím, že požadavek HTTP Get na ukázkovou aplikaci na `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="64ab5-175">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="64ab5-176">Tady je příklad stejného protokolů, jak se objeví v okně ladění, když spustíte ukázkovou aplikaci v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="64ab5-176">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="64ab5-177">Protokoly, které jsou vytvořené `ILogger` volání je znázorněno v předchozí části začínají řetězcem "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="64ab5-177">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="64ab5-178">Protokoly, které začínají řetězcem "Microsoft". kategorie jsou z kódu rozhraní framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="64ab5-178">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="64ab5-179">ASP.NET Core a kód aplikace používají stejné protokolování rozhraní API a poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="64ab5-179">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="64ab5-180">Zbývající část tohoto článku popisuje některé podrobnosti a možností pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-180">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="64ab5-181">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="64ab5-181">NuGet packages</span></span>

<span data-ttu-id="64ab5-182">`ILogger` a `ILoggerFactory` rozhraní jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), a mají výchozí implementace pro ně [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="64ab5-182">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="64ab5-183">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="64ab5-183">Log category</span></span>

<span data-ttu-id="64ab5-184">Když `ILogger` je vytvořen objekt, *kategorie* je pro ni zadán.</span><span class="sxs-lookup"><span data-stu-id="64ab5-184">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="64ab5-185">Kategorie je součástí každé zprávě protokolu vytvořené z této instance `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="64ab5-185">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="64ab5-186">Kategorie může být libovolný řetězec, ale tato konvence je použití názvu třídy, jako je například "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="64ab5-186">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="64ab5-187">Použití `ILogger<T>` zobrazíte `ILogger` instanci, která používá plně kvalifikovaný typ název `T` jako kategorie:</span><span class="sxs-lookup"><span data-stu-id="64ab5-187">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="64ab5-188">Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="64ab5-188">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="64ab5-189">`ILogger<T>` je ekvivalentní volání `CreateLogger` názvem plně kvalifikovaný typ `T`.</span><span class="sxs-lookup"><span data-stu-id="64ab5-189">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="64ab5-190">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="64ab5-190">Log level</span></span>

<span data-ttu-id="64ab5-191">Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-191">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="64ab5-192">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="64ab5-192">The log level indicates the severity or importance.</span></span> <span data-ttu-id="64ab5-193">Například můžete například napsat `Information` po skončení metody obvykle a protokolu `Warning` protokolu po návratu metody *404 Nenalezeno* stavový kód.</span><span class="sxs-lookup"><span data-stu-id="64ab5-193">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="64ab5-194">Následující kód vytvoří `Information` a `Warning` protokoly:</span><span class="sxs-lookup"><span data-stu-id="64ab5-194">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="64ab5-195">V předchozím kódu je první parametr [protokolu událost s ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="64ab5-195">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="64ab5-196">Druhý parametr je šablona zprávy s zástupné symboly pro argument hodnoty podle zbývající parametry metody.</span><span class="sxs-lookup"><span data-stu-id="64ab5-196">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="64ab5-197">Parametry metody jsou vysvětleny v [zpráv šablonu části](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="64ab5-197">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="64ab5-198">Metody, které obsahují v názvu metody na úrovni protokolu (například `LogInformation` a `LogWarning`) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="64ab5-198">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="64ab5-199">Tyto metody volat `Log` metodu, která přebírá `LogLevel` parametru.</span><span class="sxs-lookup"><span data-stu-id="64ab5-199">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="64ab5-200">Můžete volat `Log` metoda přímo spíše než jeden z těchto metod rozšíření, ale syntaxe je poměrně složitý.</span><span class="sxs-lookup"><span data-stu-id="64ab5-200">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="64ab5-201">Další informace najdete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a [rozšíření protokolovače zdrojový kód](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="64ab5-201">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="64ab5-202">ASP.NET Core definuje následující úrovně protokolu zde seřazené od nejnižší a nejvyšší závažnost.</span><span class="sxs-lookup"><span data-stu-id="64ab5-202">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="64ab5-203">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="64ab5-203">Trace = 0</span></span>

  <span data-ttu-id="64ab5-204">Pro informace, které je obvykle vhodné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="64ab5-204">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="64ab5-205">Tyto zprávy mohou obsahovat citlivé aplikaci data a by nemělo být povoleno v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="64ab5-205">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="64ab5-206">*Ve výchozím nastavení zakázané.*</span><span class="sxs-lookup"><span data-stu-id="64ab5-206">*Disabled by default.*</span></span>

* <span data-ttu-id="64ab5-207">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="64ab5-207">Debug = 1</span></span>

  <span data-ttu-id="64ab5-208">Informace, které mohou být užitečné pro vývoj a ladění.</span><span class="sxs-lookup"><span data-stu-id="64ab5-208">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="64ab5-209">Příklad: `Entering method Configure with flag set to true.` Povolit `Debug` úroveň zaznamená v produkčním prostředí pouze při řešení potíží, kvůli velkému počtu protokoly.</span><span class="sxs-lookup"><span data-stu-id="64ab5-209">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="64ab5-210">Informace o = 2</span><span class="sxs-lookup"><span data-stu-id="64ab5-210">Information = 2</span></span>

  <span data-ttu-id="64ab5-211">Pro sledování obecný tok z aplikace.</span><span class="sxs-lookup"><span data-stu-id="64ab5-211">For tracking the general flow of the app.</span></span> <span data-ttu-id="64ab5-212">Tyto protokoly mají obvykle dlouhodobé některá z hodnot.</span><span class="sxs-lookup"><span data-stu-id="64ab5-212">These logs typically have some long-term value.</span></span> <span data-ttu-id="64ab5-213">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="64ab5-213">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="64ab5-214">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="64ab5-214">Warning = 3</span></span>

  <span data-ttu-id="64ab5-215">Neobvyklé nebo neočekávaných událostí v aplikaci flow.</span><span class="sxs-lookup"><span data-stu-id="64ab5-215">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="64ab5-216">Ty mohou obsahovat chyby nebo jinými podmínkami, které není způsobit, že aplikace přestane, ale může být nutné prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="64ab5-216">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="64ab5-217">Zpracované výjimky jsou běžné místo, kde můžete použít `Warning` úrovně protokolování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-217">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="64ab5-218">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="64ab5-218">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="64ab5-219">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="64ab5-219">Error = 4</span></span>

  <span data-ttu-id="64ab5-220">Chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="64ab5-220">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="64ab5-221">Tyto zprávy označují selhání aktuální aktivitu nebo operace (jako je například aktuální požadavek HTTP), ne k selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="64ab5-221">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="64ab5-222">Příklad zprávy protokolu: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="64ab5-222">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="64ab5-223">Kritická = 5</span><span class="sxs-lookup"><span data-stu-id="64ab5-223">Critical = 5</span></span>

  <span data-ttu-id="64ab5-224">Chyby, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="64ab5-224">For failures that require immediate attention.</span></span> <span data-ttu-id="64ab5-225">Příklady: scénářům ztráty dat, volné místo na disku.</span><span class="sxs-lookup"><span data-stu-id="64ab5-225">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="64ab5-226">Pomocí úroveň protokolu můžete řídit, kolik výstup protokolu je zapsán do konkrétního úložiště média nebo zobrazení okna.</span><span class="sxs-lookup"><span data-stu-id="64ab5-226">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="64ab5-227">Příklad:</span><span class="sxs-lookup"><span data-stu-id="64ab5-227">For example:</span></span>

* <span data-ttu-id="64ab5-228">V produkčním prostředí, odeslat `Trace` prostřednictvím `Information` úrovně do úložiště dat svazku.</span><span class="sxs-lookup"><span data-stu-id="64ab5-228">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="64ab5-229">Odeslat `Warning` prostřednictvím `Critical` na hodnotu data ukládat.</span><span class="sxs-lookup"><span data-stu-id="64ab5-229">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="64ab5-230">Během vývoje, odeslat `Warning` prostřednictvím `Critical` do konzoly a přidejte `Trace` prostřednictvím `Information` při řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="64ab5-230">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="64ab5-231">[Filtrování protokolu](#log-filtering) části dále v tomto článku vysvětluje, jak řídit které úrovně protokolu zpracovává zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-231">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="64ab5-232">ASP.NET Core zapisuje protokoly pro události rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="64ab5-232">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="64ab5-233">Příklady protokolu dříve v tomto článku vyloučené protokoly níže `Information` úroveň, takže žádná `Debug` nebo `Trace` úrovně protokoly byly vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="64ab5-233">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="64ab5-234">Tady je příklad vytvořených spuštěním ukázkové aplikace nakonfigurovat tak, aby zobrazit protokoly konzoly `Debug` protokoly:</span><span class="sxs-lookup"><span data-stu-id="64ab5-234">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="64ab5-235">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="64ab5-235">Log event ID</span></span>

<span data-ttu-id="64ab5-236">Můžete zadat všechny protokoly *ID události*.</span><span class="sxs-lookup"><span data-stu-id="64ab5-236">Each log can specify an *event ID*.</span></span> <span data-ttu-id="64ab5-237">Ukázková aplikace dělá to pomocí místně definované `LoggingEvents` třídy:</span><span class="sxs-lookup"><span data-stu-id="64ab5-237">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="64ab5-238">ID události přidruží sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="64ab5-238">An event ID associates a set of events.</span></span> <span data-ttu-id="64ab5-239">Všechny protokoly týkající se zobrazení seznamu položek na stránce může být například 1001.</span><span class="sxs-lookup"><span data-stu-id="64ab5-239">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="64ab5-240">Poskytovatel protokolování může ukládat ID události v poli ID ve zprávě protokolování nebo dokonce vůbec.</span><span class="sxs-lookup"><span data-stu-id="64ab5-240">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="64ab5-241">Ladění zprostředkovatele nezobrazí ID událostí.</span><span class="sxs-lookup"><span data-stu-id="64ab5-241">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="64ab5-242">Konzola poskytovatel zobrazuje ID událostí do hranatých závorek za kategorií:</span><span class="sxs-lookup"><span data-stu-id="64ab5-242">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="64ab5-243">Šablona zprávy protokolu</span><span class="sxs-lookup"><span data-stu-id="64ab5-243">Log message template</span></span>

<span data-ttu-id="64ab5-244">Každý protokol Určuje zpráv šablonu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-244">Each log specifies a message template.</span></span> <span data-ttu-id="64ab5-245">Šablona zprávy může obsahovat zástupné znaky, pro které jsou zadány argumenty.</span><span class="sxs-lookup"><span data-stu-id="64ab5-245">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="64ab5-246">Použijte názvy nahraďte zástupné symboly, nikoli čísla.</span><span class="sxs-lookup"><span data-stu-id="64ab5-246">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="64ab5-247">Pořadí zástupné symboly, nikoli jejich názvy, určuje, jaké parametry se používají k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="64ab5-247">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="64ab5-248">V následujícím kódu Všimněte si, že názvy parametrů jsou mimo pořadí v šablonu zprávy:</span><span class="sxs-lookup"><span data-stu-id="64ab5-248">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="64ab5-249">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:</span><span class="sxs-lookup"><span data-stu-id="64ab5-249">This code creates a log message with the parameter values in sequence:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="64ab5-250">Protokolovacího rozhraní funguje tak, aby zprostředkovatelé protokolování můžete implementovat [sémantického protokolování, označovaného také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="64ab5-250">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="64ab5-251">Samotné argumenty jsou předány do systému protokolování, ne jenom šablony formátovaná zpráva.</span><span class="sxs-lookup"><span data-stu-id="64ab5-251">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="64ab5-252">Tyto informace umožňuje poskytovatelům protokolování pro uložení hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="64ab5-252">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="64ab5-253">Předpokládejme například, protokolovacího nástroje metoda volání najdete takto:</span><span class="sxs-lookup"><span data-stu-id="64ab5-253">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="64ab5-254">Pokud jste odesílali protokoly do služby Azure Table Storage, může mít každá entita Azure Table `ID` a `RequestTime` vlastnosti, které zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-254">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="64ab5-255">Dotaz můžete najít všechny protokoly v rámci konkrétní `RequestTime` rozsahu bez parsování časový limit textové zprávy.</span><span class="sxs-lookup"><span data-stu-id="64ab5-255">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="64ab5-256">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="64ab5-256">Logging exceptions</span></span>

<span data-ttu-id="64ab5-257">Protokolovací nástroj metody mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="64ab5-257">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="64ab5-258">Různí poskytovatelé zpracovat informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="64ab5-258">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="64ab5-259">Tady je příklad výstupu ladění zprostředkovatele z výše uvedeném kódu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-259">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="64ab5-260">Filtrování protokolu</span><span class="sxs-lookup"><span data-stu-id="64ab5-260">Log filtering</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="64ab5-261">Můžete zadat minimální úroveň protokolování pro konkrétního zprostředkovatele a kategorie nebo pro všechny poskytovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="64ab5-261">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="64ab5-262">Všechny protokoly nižší než minimální úroveň nejsou předán tohoto poskytovatele, tak nemusíte získat zobrazení nebo uložené.</span><span class="sxs-lookup"><span data-stu-id="64ab5-262">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="64ab5-263">Chcete-li potlačit všechny protokoly, zadejte `LogLevel.None` jako minimální úroveň protokolování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-263">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="64ab5-264">Celočíselnou hodnotu `LogLevel.None` je 6, která je vyšší než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="64ab5-264">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="64ab5-265">Vytvoření pravidla filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="64ab5-265">Create filter rules in configuration</span></span>

<span data-ttu-id="64ab5-266">Volání kódu šablony projektu `CreateDefaultBuilder` nastavit protokolování pro poskytovatele konzoly a ladění.</span><span class="sxs-lookup"><span data-stu-id="64ab5-266">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="64ab5-267">`CreateDefaultBuilder` Metoda také nastaví protokolování hledat konfiguraci `Logging` oddílu, psát kód podobný tomuto:</span><span class="sxs-lookup"><span data-stu-id="64ab5-267">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17)]

<span data-ttu-id="64ab5-268">Konfigurační data Určuje minimální úrovně podle poskytovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="64ab5-268">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="64ab5-269">Tento dokument JSON vytvoří šest pravidla filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a pro všechny poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-269">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="64ab5-270">Jedno pravidlo je vybrán pro každého zprostředkovatele při `ILogger` je vytvořen objekt.</span><span class="sxs-lookup"><span data-stu-id="64ab5-270">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="64ab5-271">Pravidla filtru v kódu</span><span class="sxs-lookup"><span data-stu-id="64ab5-271">Filter rules in code</span></span>

<span data-ttu-id="64ab5-272">Následující příklad ukazuje, jak zaregistrovat pravidla filtrování v kódu:</span><span class="sxs-lookup"><span data-stu-id="64ab5-272">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="64ab5-273">Druhá `AddFilter` Určuje zprostředkovatele, který ladění pomocí názvu typu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-273">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="64ab5-274">První `AddFilter` platí pro všechny poskytovatele, protože ji neurčuje, typ zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-274">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="64ab5-275">Jak pravidla filtrování se použijí</span><span class="sxs-lookup"><span data-stu-id="64ab5-275">How filtering rules are applied</span></span>

<span data-ttu-id="64ab5-276">Konfigurační data a `AddFilter` kód zobrazený v předchozích ukázkách vytvořit pravidla je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="64ab5-276">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="64ab5-277">Prvních šest pocházet z příklad konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-277">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="64ab5-278">Číslo</span><span class="sxs-lookup"><span data-stu-id="64ab5-278">Number</span></span> | <span data-ttu-id="64ab5-279">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="64ab5-279">Provider</span></span>      | <span data-ttu-id="64ab5-280">Kategorie, které začínají...</span><span class="sxs-lookup"><span data-stu-id="64ab5-280">Categories that begin with ...</span></span>          | <span data-ttu-id="64ab5-281">Minimální úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="64ab5-281">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="64ab5-282">1</span><span class="sxs-lookup"><span data-stu-id="64ab5-282">1</span></span>      | <span data-ttu-id="64ab5-283">Ladění</span><span class="sxs-lookup"><span data-stu-id="64ab5-283">Debug</span></span>         | <span data-ttu-id="64ab5-284">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="64ab5-284">All categories</span></span>                          | <span data-ttu-id="64ab5-285">Informace o</span><span class="sxs-lookup"><span data-stu-id="64ab5-285">Information</span></span>       |
| <span data-ttu-id="64ab5-286">2</span><span class="sxs-lookup"><span data-stu-id="64ab5-286">2</span></span>      | <span data-ttu-id="64ab5-287">Konzola</span><span class="sxs-lookup"><span data-stu-id="64ab5-287">Console</span></span>       | <span data-ttu-id="64ab5-288">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="64ab5-288">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="64ab5-289">Upozornění</span><span class="sxs-lookup"><span data-stu-id="64ab5-289">Warning</span></span>           |
| <span data-ttu-id="64ab5-290">3</span><span class="sxs-lookup"><span data-stu-id="64ab5-290">3</span></span>      | <span data-ttu-id="64ab5-291">Konzola</span><span class="sxs-lookup"><span data-stu-id="64ab5-291">Console</span></span>       | <span data-ttu-id="64ab5-292">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="64ab5-292">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="64ab5-293">Ladění</span><span class="sxs-lookup"><span data-stu-id="64ab5-293">Debug</span></span>             |
| <span data-ttu-id="64ab5-294">4</span><span class="sxs-lookup"><span data-stu-id="64ab5-294">4</span></span>      | <span data-ttu-id="64ab5-295">Konzola</span><span class="sxs-lookup"><span data-stu-id="64ab5-295">Console</span></span>       | <span data-ttu-id="64ab5-296">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="64ab5-296">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="64ab5-297">Chyba</span><span class="sxs-lookup"><span data-stu-id="64ab5-297">Error</span></span>             |
| <span data-ttu-id="64ab5-298">5</span><span class="sxs-lookup"><span data-stu-id="64ab5-298">5</span></span>      | <span data-ttu-id="64ab5-299">Konzola</span><span class="sxs-lookup"><span data-stu-id="64ab5-299">Console</span></span>       | <span data-ttu-id="64ab5-300">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="64ab5-300">All categories</span></span>                          | <span data-ttu-id="64ab5-301">Informace o</span><span class="sxs-lookup"><span data-stu-id="64ab5-301">Information</span></span>       |
| <span data-ttu-id="64ab5-302">6</span><span class="sxs-lookup"><span data-stu-id="64ab5-302">6</span></span>      | <span data-ttu-id="64ab5-303">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="64ab5-303">All providers</span></span> | <span data-ttu-id="64ab5-304">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="64ab5-304">All categories</span></span>                          | <span data-ttu-id="64ab5-305">Ladění</span><span class="sxs-lookup"><span data-stu-id="64ab5-305">Debug</span></span>             |
| <span data-ttu-id="64ab5-306">7</span><span class="sxs-lookup"><span data-stu-id="64ab5-306">7</span></span>      | <span data-ttu-id="64ab5-307">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="64ab5-307">All providers</span></span> | <span data-ttu-id="64ab5-308">Systém</span><span class="sxs-lookup"><span data-stu-id="64ab5-308">System</span></span>                                  | <span data-ttu-id="64ab5-309">Ladění</span><span class="sxs-lookup"><span data-stu-id="64ab5-309">Debug</span></span>             |
| <span data-ttu-id="64ab5-310">8</span><span class="sxs-lookup"><span data-stu-id="64ab5-310">8</span></span>      | <span data-ttu-id="64ab5-311">Ladění</span><span class="sxs-lookup"><span data-stu-id="64ab5-311">Debug</span></span>         | <span data-ttu-id="64ab5-312">Microsoft</span><span class="sxs-lookup"><span data-stu-id="64ab5-312">Microsoft</span></span>                               | <span data-ttu-id="64ab5-313">Trasování</span><span class="sxs-lookup"><span data-stu-id="64ab5-313">Trace</span></span>             |

<span data-ttu-id="64ab5-314">Když `ILogger` je vytvořen objekt, `ILoggerFactory` objekt vybere jedno pravidlo na poskytovatele, který chcete použít pro tento protokolovač.</span><span class="sxs-lookup"><span data-stu-id="64ab5-314">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="64ab5-315">Všechny zprávy, autorem `ILogger` instance jsou filtrovány podle vybrané pravidla.</span><span class="sxs-lookup"><span data-stu-id="64ab5-315">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="64ab5-316">Nejspecifičtější pravidla pro každého zprostředkovatele a dvojice kategorie je vybrat z dostupných pravidla.</span><span class="sxs-lookup"><span data-stu-id="64ab5-316">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="64ab5-317">Následující požadovaný algoritmus se používá pro každého zprostředkovatele při `ILogger` se vytvoří pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="64ab5-317">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="64ab5-318">Vyberte všechna pravidla, které odpovídají zprostředkovateli nebo její alias.</span><span class="sxs-lookup"><span data-stu-id="64ab5-318">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="64ab5-319">Pokud není nalezena žádná shoda, vyberte všechna pravidla s poskytovatelem služby prázdný.</span><span class="sxs-lookup"><span data-stu-id="64ab5-319">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="64ab5-320">Z výsledků v předchozím kroku vyberte pravidla s nejdelší odpovídající předpona kategorie.</span><span class="sxs-lookup"><span data-stu-id="64ab5-320">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="64ab5-321">Pokud není nalezena žádná shoda, vyberte všechna pravidla, které nechcete zadat kategorii.</span><span class="sxs-lookup"><span data-stu-id="64ab5-321">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="64ab5-322">Pokud je vybraných víc pravidel, trvat, než **poslední** jeden.</span><span class="sxs-lookup"><span data-stu-id="64ab5-322">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="64ab5-323">Pokud jsou vybraná žádná pravidla, použijte `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="64ab5-323">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="64ab5-324">S předchozím seznam pravidel, Předpokládejme, že vytvoříte `ILogger` objektu pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="64ab5-324">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="64ab5-325">Ladění zprostředkovatele platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="64ab5-325">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="64ab5-326">Pravidlo 8 není nejvíce specifické, tak, aby se byla vybrána.</span><span class="sxs-lookup"><span data-stu-id="64ab5-326">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="64ab5-327">Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="64ab5-327">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="64ab5-328">Pravidlo 3 není nejvíce specifické.</span><span class="sxs-lookup"><span data-stu-id="64ab5-328">Rule 3 is most specific.</span></span>

<span data-ttu-id="64ab5-329">Výsledná `ILogger` instance odešle protokoly `Trace` úroveň a vyšší k poskytovateli ladění.</span><span class="sxs-lookup"><span data-stu-id="64ab5-329">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="64ab5-330">Protokoly z `Debug` úrovně a novější se odesílají do konzoly zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-330">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="64ab5-331">Aliasy poskytovatele</span><span class="sxs-lookup"><span data-stu-id="64ab5-331">Provider aliases</span></span>

<span data-ttu-id="64ab5-332">Každý poskytovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-332">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="64ab5-333">Pro předdefinované zprostředkovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="64ab5-333">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="64ab5-334">Konzola</span><span class="sxs-lookup"><span data-stu-id="64ab5-334">Console</span></span>
* <span data-ttu-id="64ab5-335">Ladění</span><span class="sxs-lookup"><span data-stu-id="64ab5-335">Debug</span></span>
* <span data-ttu-id="64ab5-336">EventSource</span><span class="sxs-lookup"><span data-stu-id="64ab5-336">EventSource</span></span>
* <span data-ttu-id="64ab5-337">EventLog</span><span class="sxs-lookup"><span data-stu-id="64ab5-337">EventLog</span></span>
* <span data-ttu-id="64ab5-338">TraceSource</span><span class="sxs-lookup"><span data-stu-id="64ab5-338">TraceSource</span></span>
* <span data-ttu-id="64ab5-339">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="64ab5-339">AzureAppServicesFile</span></span>
* <span data-ttu-id="64ab5-340">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="64ab5-340">AzureAppServicesBlob</span></span>
* <span data-ttu-id="64ab5-341">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="64ab5-341">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="64ab5-342">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="64ab5-342">Default minimum level</span></span>

<span data-ttu-id="64ab5-343">Je k dispozici minimální úroveň nastavení, která se projeví pouze v případě, že žádná pravidla z konfigurace nebo kódu platí pro daného poskytovatele a kategorii.</span><span class="sxs-lookup"><span data-stu-id="64ab5-343">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="64ab5-344">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="64ab5-344">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="64ab5-345">Pokud není explicitně nastavena na minimální úroveň, výchozí hodnota je `Information`, což znamená, že `Trace` a `Debug` protokoly jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="64ab5-345">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="64ab5-346">Funkce filtru</span><span class="sxs-lookup"><span data-stu-id="64ab5-346">Filter functions</span></span>

<span data-ttu-id="64ab5-347">Pro všechny poskytovatele a kategorie, které nemají přiřazené konfigurace nebo kódu pravidla je vyvolána funkce filtru.</span><span class="sxs-lookup"><span data-stu-id="64ab5-347">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="64ab5-348">Kód ve funkci má přístup k typu poskytovatele, kategorii a úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-348">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="64ab5-349">Příklad:</span><span class="sxs-lookup"><span data-stu-id="64ab5-349">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="64ab5-350">Někteří poskytovatelé protokolování umožňují zadat při protokoly by měly být zapsána do úložiště média nebo ignorovat podle úroveň protokolu a kategorie.</span><span class="sxs-lookup"><span data-stu-id="64ab5-350">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="64ab5-351">`AddConsole` a `AddDebug` rozšiřující metody poskytují přetížení přijímající kritéria filtrování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-351">The `AddConsole` and `AddDebug` extension methods provide overloads that accept filtering criteria.</span></span> <span data-ttu-id="64ab5-352">Následující ukázkový kód způsobí, že konzola poskytovatel ignorovat protokolech níže `Warning` úroveň, při ladění zprostředkovatele ignoruje protokoly, které vytvoří rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="64ab5-352">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="64ab5-353">`AddEventLog` Metoda má přetížení přijímající `EventLogSettings` instanci, která může obsahovat funkce filtrování v jeho `Filter` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="64ab5-353">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="64ab5-354">Zprostředkovatel TraceSource neposkytuje, některý z těchto přetížení, protože jeho úroveň protokolování a další parametry jsou založené na `SourceSwitch` a `TraceListener` používá.</span><span class="sxs-lookup"><span data-stu-id="64ab5-354">The TraceSource provider doesn't provide any of those overloads, since its logging level and other parameters are based on the `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="64ab5-355">Chcete-li nastavit pravidla filtrování pro všech zprostředkovatelů, které jsou registrovány `ILoggerFactory` použijte `WithFilter` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="64ab5-355">To set filtering rules for all providers that are registered with an `ILoggerFactory` instance, use the `WithFilter` extension method.</span></span> <span data-ttu-id="64ab5-356">Následující příklad omezuje framework protokoly (kategorie začíná řetězcem "Microsoft" nebo "Systém") a upozornění při protokolování na úrovni ladění pro protokoly vytvořené metodami kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="64ab5-356">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while logging at debug level for logs created by application code.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="64ab5-357">Chcete-li zabránit zapisovaná všechny protokoly, zadejte `LogLevel.None` jako minimální úroveň protokolování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-357">To prevent any logs from being written, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="64ab5-358">Celočíselnou hodnotu `LogLevel.None` je 6, která je vyšší než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="64ab5-358">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="64ab5-359">`WithFilter` – Metoda rozšíření poskytuje [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="64ab5-359">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="64ab5-360">Metoda vrátí nový `ILoggerFactory` zaregistrovaný poskytovatel instanci, která bude filtrovat zprávy protokolu, který je předán všichni poskytovatelé protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="64ab5-360">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="64ab5-361">To nemá vliv na jiné `ILoggerFactory` instance, včetně původní `ILoggerFactory` instance.</span><span class="sxs-lookup"><span data-stu-id="64ab5-361">It doesn't affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="64ab5-362">Systém kategorií a úrovně</span><span class="sxs-lookup"><span data-stu-id="64ab5-362">System categories and levels</span></span>

<span data-ttu-id="64ab5-363">Tady jsou některé kategorie, která používá ASP.NET Core a Entity Framework Core s poznámky o co protokoly můžete očekávat od nich:</span><span class="sxs-lookup"><span data-stu-id="64ab5-363">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="64ab5-364">Kategorie</span><span class="sxs-lookup"><span data-stu-id="64ab5-364">Category</span></span>                            | <span data-ttu-id="64ab5-365">Poznámky</span><span class="sxs-lookup"><span data-stu-id="64ab5-365">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="64ab5-366">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="64ab5-366">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="64ab5-367">Diagnostika obecné ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="64ab5-367">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="64ab5-368">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="64ab5-368">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="64ab5-369">Jaké byly považovat za, nalezen a používání klíčů.</span><span class="sxs-lookup"><span data-stu-id="64ab5-369">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="64ab5-370">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="64ab5-370">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="64ab5-371">Hostitelé povolené.</span><span class="sxs-lookup"><span data-stu-id="64ab5-371">Hosts allowed.</span></span> |
| <span data-ttu-id="64ab5-372">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="64ab5-372">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="64ab5-373">Jak dlouho požadavky HTTP trvalo dokončení a kdy se spouští.</span><span class="sxs-lookup"><span data-stu-id="64ab5-373">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="64ab5-374">Po spuštění sestavení, hostování byly načteny.</span><span class="sxs-lookup"><span data-stu-id="64ab5-374">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="64ab5-375">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="64ab5-375">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="64ab5-376">Diagnostika MVC a syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="64ab5-376">MVC and Razor diagnostics.</span></span> <span data-ttu-id="64ab5-377">Model, navázání, spuštění filtru, kompilace zobrazení výběr akce.</span><span class="sxs-lookup"><span data-stu-id="64ab5-377">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="64ab5-378">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="64ab5-378">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="64ab5-379">Trasu odpovídající informace.</span><span class="sxs-lookup"><span data-stu-id="64ab5-379">Route matching information.</span></span> |
| <span data-ttu-id="64ab5-380">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="64ab5-380">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="64ab5-381">Připojení spuštění, zastavení a keep alive odpovědi.</span><span class="sxs-lookup"><span data-stu-id="64ab5-381">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="64ab5-382">Informace o certifikátu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="64ab5-382">HTTPS certificate information.</span></span> |
| <span data-ttu-id="64ab5-383">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="64ab5-383">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="64ab5-384">Zpracování souborů.</span><span class="sxs-lookup"><span data-stu-id="64ab5-384">Files served.</span></span> |
| <span data-ttu-id="64ab5-385">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="64ab5-385">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="64ab5-386">Obecné Entity Framework Core diagnostiky.</span><span class="sxs-lookup"><span data-stu-id="64ab5-386">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="64ab5-387">Aktivita a konfigurace, detekce změn migracemi databází.</span><span class="sxs-lookup"><span data-stu-id="64ab5-387">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="64ab5-388">Protokol obory</span><span class="sxs-lookup"><span data-stu-id="64ab5-388">Log scopes</span></span>

 <span data-ttu-id="64ab5-389">A *oboru* můžete seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="64ab5-389">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="64ab5-390">Toto seskupení je možné se připojit k každý protokol, který je vytvořen jako součást sady stejná data.</span><span class="sxs-lookup"><span data-stu-id="64ab5-390">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="64ab5-391">Každý protokol vytvořených jako součást zpracování transakcí může obsahovat třeba ID transakce.</span><span class="sxs-lookup"><span data-stu-id="64ab5-391">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="64ab5-392">Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metoda a bude trvat, dokud je uvolněn.</span><span class="sxs-lookup"><span data-stu-id="64ab5-392">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="64ab5-393">Volání protokolovací nástroj pro zabalení použít obor `using` blok:</span><span class="sxs-lookup"><span data-stu-id="64ab5-393">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="64ab5-394">Následující kód umožní obory pro zprostředkovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="64ab5-394">The following code enables scopes for the console provider:</span></span>

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="64ab5-395">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ab5-395">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="64ab5-396">Konfigurace `IncludeScopes` možnost protokolovací nástroj konzoly je nutné povolit protokolování na základě oboru.</span><span class="sxs-lookup"><span data-stu-id="64ab5-396">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="64ab5-397">Informace o konfiguraci, najdete v článku [konfigurace](#configuration) oddílu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-397">For information on configuration, see the [Configuration](#configuration) section.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="64ab5-398">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ab5-398">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="64ab5-399">Konfigurace `IncludeScopes` možnost protokolovací nástroj konzoly je nutné povolit protokolování na základě oboru.</span><span class="sxs-lookup"><span data-stu-id="64ab5-399">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="64ab5-400">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="64ab5-400">*Startup.cs*:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

<span data-ttu-id="64ab5-401">Každé zprávě protokolu obsahuje rozsahem informací:</span><span class="sxs-lookup"><span data-stu-id="64ab5-401">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="64ab5-402">Poskytovatelé vestavěné protokolování</span><span class="sxs-lookup"><span data-stu-id="64ab5-402">Built-in logging providers</span></span>

<span data-ttu-id="64ab5-403">ASP.NET Core se celá dodává následující zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="64ab5-403">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="64ab5-404">Console</span><span class="sxs-lookup"><span data-stu-id="64ab5-404">Console</span></span>](#console-provider)
* [<span data-ttu-id="64ab5-405">Ladění</span><span class="sxs-lookup"><span data-stu-id="64ab5-405">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="64ab5-406">EventSource</span><span class="sxs-lookup"><span data-stu-id="64ab5-406">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="64ab5-407">EventLog</span><span class="sxs-lookup"><span data-stu-id="64ab5-407">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="64ab5-408">TraceSource</span><span class="sxs-lookup"><span data-stu-id="64ab5-408">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="64ab5-409">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="64ab5-409">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="64ab5-410">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="64ab5-410">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="64ab5-411">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="64ab5-411">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="64ab5-412">Informace o protokolování stdout najdete v tématu <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> a <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span><span class="sxs-lookup"><span data-stu-id="64ab5-412">For information about stdout logging, see <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> and <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="64ab5-413">Konzola zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="64ab5-413">Console provider</span></span>

<span data-ttu-id="64ab5-414">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) balíček zprostředkovatele odešle výstup protokolu konzoly.</span><span class="sxs-lookup"><span data-stu-id="64ab5-414">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

<span data-ttu-id="64ab5-415">[Přetížení AddConsole](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) umožňují předáním minimální úroveň protokolování, funkce filtru a logickou hodnotu, která označuje, zda jsou obory.</span><span class="sxs-lookup"><span data-stu-id="64ab5-415">[AddConsole overloads](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) let you pass in a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span> <span data-ttu-id="64ab5-416">Další možností je a zajistěte tak předání `IConfiguration` objektu, který můžete určit podporu obory a úrovní protokolování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-416">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span>

<span data-ttu-id="64ab5-417">Zprostředkovatel konzoly má významný dopad na výkon a není obvykle vhodné pro použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="64ab5-417">The console provider has a significant impact on performance and is generally not appropriate for use in production.</span></span>

<span data-ttu-id="64ab5-418">Když vytvoříte nový projekt v sadě Visual Studio `AddConsole` metoda vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="64ab5-418">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="64ab5-419">Tento kód odkazuje `Logging` část *appSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="64ab5-419">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

<span data-ttu-id="64ab5-420">Nastavení zobrazené protokoly framework limit upozornění zároveň umožní aplikaci k přihlášení na úrovni ladění, jak je vysvětleno v [filtrování protokolu](#log-filtering) oddílu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-420">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="64ab5-421">Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="64ab5-421">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

::: moniker-end

<span data-ttu-id="64ab5-422">Protokolování výstupu konzoly najdete ve složce projektu otevřete příkazový řádek a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="64ab5-422">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```console
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="64ab5-423">Ladění zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="64ab5-423">Debug provider</span></span>

<span data-ttu-id="64ab5-424">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) balíček zprostředkovatele zapíše výstup protokolu pomocí [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) třídy (`Debug.WriteLine` volání metody).</span><span class="sxs-lookup"><span data-stu-id="64ab5-424">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="64ab5-425">V systému Linux, tento zprostředkovatel zapisuje protokoly do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="64ab5-425">On Linux, this provider writes logs to */var/log/message*.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

<span data-ttu-id="64ab5-426">[Přetížení AddDebug](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) umožňují předáním minimální úroveň protokolování nebo funkce filtru.</span><span class="sxs-lookup"><span data-stu-id="64ab5-426">[AddDebug overloads](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) let you pass in a minimum log level or a filter function.</span></span>

::: moniker-end

### <a name="eventsource-provider"></a><span data-ttu-id="64ab5-427">Zprostředkovatel EventSource</span><span class="sxs-lookup"><span data-stu-id="64ab5-427">EventSource provider</span></span>

<span data-ttu-id="64ab5-428">Pro aplikace, které cílí ASP.NET Core 1.1.0 nebo novější, [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) balíček zprostředkovatele můžete implementovat trasování událostí.</span><span class="sxs-lookup"><span data-stu-id="64ab5-428">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="64ab5-429">Na Windows, používá [trasování událostí pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="64ab5-429">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="64ab5-430">Zprostředkovatel je multiplatformní, ale nejsou žádná událost kolekce a zobrazení nástroje ještě pro Linux nebo macOS.</span><span class="sxs-lookup"><span data-stu-id="64ab5-430">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

<span data-ttu-id="64ab5-431">Je dobrým způsobem, jak shromažďovat a zobrazovat protokoly použít [nástroje PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="64ab5-431">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="64ab5-432">Existují jiné nástroje pro prohlížení protokolů trasování událostí pro Windows, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows, protože ho vygeneroval technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="64ab5-432">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span>

<span data-ttu-id="64ab5-433">Ke konfiguraci PerfView pro shromažďování události zapsané podle tohoto zprostředkovatele, přidejte řetězec `*Microsoft-Extensions-Logging` k **dalších poskytovatelů** seznamu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-433">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="64ab5-434">(Nenechte si ujít hvězdičku na začátku řetězce.)</span><span class="sxs-lookup"><span data-stu-id="64ab5-434">(Don't miss the asterisk at the start of the string.)</span></span>

![Další zprostředkovatelé Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="64ab5-436">Poskytovatel protokolu událostí Windows</span><span class="sxs-lookup"><span data-stu-id="64ab5-436">Windows EventLog provider</span></span>

<span data-ttu-id="64ab5-437">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) balíček zprostředkovatele odesílá výstup protokolu do protokolu událostí Windows.</span><span class="sxs-lookup"><span data-stu-id="64ab5-437">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

<span data-ttu-id="64ab5-438">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předáním `EventLogSettings` nebo minimální úroveň protokolování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-438">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

::: moniker-end

### <a name="tracesource-provider"></a><span data-ttu-id="64ab5-439">TraceSource poskytovatele</span><span class="sxs-lookup"><span data-stu-id="64ab5-439">TraceSource provider</span></span>

<span data-ttu-id="64ab5-440">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá balíček zprostředkovatele <xref:System.Diagnostics.TraceSource> knihovny a poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="64ab5-440">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

<span data-ttu-id="64ab5-441">[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předáním přepínač zdroje a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-441">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="64ab5-442">K používání tohoto poskytovatele, musí aplikace spouštět rozhraní .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="64ab5-442">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="64ab5-443">Zprostředkovatel může směrovat zprávy širokou škálu [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použít v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="64ab5-443">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="64ab5-444">Následující příklad nastaví `TraceSource` zprostředkovatele, který zaznamenává `Warning` a vyšší zprávu do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="64ab5-444">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

### <a name="azure-app-service-provider"></a><span data-ttu-id="64ab5-445">Zprostředkovatel služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="64ab5-445">Azure App Service provider</span></span>

<span data-ttu-id="64ab5-446">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) balíček zprostředkovatele zapisuje protokoly do textových souborů v systému souborů aplikace služby Azure App Service a na [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu služby Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="64ab5-446">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="64ab5-447">Balíček zprostředkovatele je k dispozici pro aplikace zaměřené na .NET Core 1.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="64ab5-447">The provider package is available for apps targeting .NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="64ab5-448">Pokud je zaměřen na .NET Core, mějte na paměti následující body:</span><span class="sxs-lookup"><span data-stu-id="64ab5-448">If targeting .NET Core, note the following points:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="64ab5-449">Balíček poskytovatel je součástí ASP.NET Core [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="64ab5-449">The provider package is included in the ASP.NET Core [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="64ab5-450">Balíček zprostředkovatele není součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="64ab5-450">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="64ab5-451">Pokud chcete použít poskytovatele, nainstalujte balíček.</span><span class="sxs-lookup"><span data-stu-id="64ab5-451">To use the provider, install the package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="64ab5-452">Pokud cílí na rozhraní .NET Framework nebo odkazující `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, přidejte do projektu balíček zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-452">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> <span data-ttu-id="64ab5-453">Vyvolání `AddAzureWebAppDiagnostics`:</span><span class="sxs-lookup"><span data-stu-id="64ab5-453">Invoke `AddAzureWebAppDiagnostics`:</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="64ab5-454"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Přetížení vám umožní předat v <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="64ab5-454">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="64ab5-455">Objekt nastavení můžete přepsat výchozí nastavení, jako je například protokolování výstupu šablony, názvu objektu blob a limit velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="64ab5-455">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="64ab5-456">(*Výstupu šablony* je zpráv šablonu, která se použije pro všechny protokoly kromě co je součástí `ILogger` volání metody.)</span><span class="sxs-lookup"><span data-stu-id="64ab5-456">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="64ab5-457">Chcete-li nakonfigurovat nastavení poskytovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="64ab5-457">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

<span data-ttu-id="64ab5-458">Když nasadíte aplikaci služby App Service, aplikace respektuje nastavení v [protokoly služby App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) část **služby App Service** stránky na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="64ab5-458">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="64ab5-459">Když tato nastavení jsou aktualizovány, změny se projeví okamžitě bez nutnosti restartování nebo opětovné nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="64ab5-459">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="64ab5-460">**Protokolování aplikace (systém souborů)**</span><span class="sxs-lookup"><span data-stu-id="64ab5-460">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="64ab5-461">**Protokolování aplikace (Blob)**</span><span class="sxs-lookup"><span data-stu-id="64ab5-461">**Application Logging (Blob)**</span></span>

<span data-ttu-id="64ab5-462">Výchozím umístěním pro soubory protokolů je *D:\\domácí\\LogFiles\\aplikace* složky a výchozí název souboru je *diagnostiky yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="64ab5-462">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="64ab5-463">Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet souborů, které uchovávají se 2.</span><span class="sxs-lookup"><span data-stu-id="64ab5-463">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="64ab5-464">Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="64ab5-464">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="64ab5-465">Zprostředkovatel funguje pouze v případě projektu běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="64ab5-465">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="64ab5-466">Nemá žádný vliv, pokud projekt je spuštěn místně&mdash;nelze zapsat do místních souborů nebo místním vývojovým úložištěm objektů BLOB.</span><span class="sxs-lookup"><span data-stu-id="64ab5-466">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="64ab5-467">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="64ab5-467">Azure log streaming</span></span>

<span data-ttu-id="64ab5-468">Streamování protokolů Azure umožňuje zobrazit protokol aktivit v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="64ab5-468">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="64ab5-469">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="64ab5-469">The app server</span></span>
* <span data-ttu-id="64ab5-470">Webový server</span><span class="sxs-lookup"><span data-stu-id="64ab5-470">The web server</span></span>
* <span data-ttu-id="64ab5-471">Trasování neúspěšných žádostí</span><span class="sxs-lookup"><span data-stu-id="64ab5-471">Failed request tracing</span></span>

<span data-ttu-id="64ab5-472">Postup konfigurace, streamování protokolů Azure:</span><span class="sxs-lookup"><span data-stu-id="64ab5-472">To configure Azure log streaming:</span></span>

* <span data-ttu-id="64ab5-473">Přejděte **protokoly služby App Service** stránky na stránce portálu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="64ab5-473">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="64ab5-474">Nastavte **protokolování aplikace (systém souborů)** k **na**.</span><span class="sxs-lookup"><span data-stu-id="64ab5-474">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="64ab5-475">Vyberte protokol **úroveň**.</span><span class="sxs-lookup"><span data-stu-id="64ab5-475">Choose the log **Level**.</span></span>

<span data-ttu-id="64ab5-476">Přejděte **Stream protokolů** stránku, abyste zobrazili zprávy aplikace.</span><span class="sxs-lookup"><span data-stu-id="64ab5-476">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="64ab5-477">Jsou aplikace prostřednictvím přihlášení `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="64ab5-477">They're logged by the app through the `ILogger` interface.</span></span>

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="64ab5-478">Protokolování trasování programu Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="64ab5-478">Azure Application Insights trace logging</span></span>

<span data-ttu-id="64ab5-479">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) balíček zprostředkovatele zapisuje protokoly do služby Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="64ab5-479">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="64ab5-480">Application Insights je služba, která monitoruje webové aplikace a poskytuje nástroje pro dotazování a analýze telemetrických dat.</span><span class="sxs-lookup"><span data-stu-id="64ab5-480">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="64ab5-481">Pokud používáte tohoto zprostředkovatele, můžete dotazy a analýzu protokolů pomocí nástrojů Application Insights.</span><span class="sxs-lookup"><span data-stu-id="64ab5-481">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="64ab5-482">Poskytovatel protokolování je zahrnutý jako závislost [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který obsahuje veškerá dostupná telemetrie pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="64ab5-482">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="64ab5-483">Pokud používáte tento balíček, není nutné instalovat balíček zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-483">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="64ab5-484">Nepoužívejte [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) balíčku&mdash;, který je pro technologii ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="64ab5-484">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="64ab5-485">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="64ab5-485">For more information, see the following resources:</span></span>

* [<span data-ttu-id="64ab5-486">Application Insights overview</span><span class="sxs-lookup"><span data-stu-id="64ab5-486">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="64ab5-487">[Application Insights pro aplikace ASP.NET Core](/azure/azure-monitor/app/asp-net-core-no-visualstudio) – Začněte zde, pokud chcete implementovat telemetrii úplný rozsah Application Insights spolu s protokolování.</span><span class="sxs-lookup"><span data-stu-id="64ab5-487">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core-no-visualstudio) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="64ab5-488">[Protokoly ApplicationInsightsLoggerProvider pro .NET Core ILogger](/azure/azure-monitor/app/ilogger) – Začněte zde, pokud chcete pro implementaci zprostředkovatele protokolování bez rest telemetrii Application Insights.</span><span class="sxs-lookup"><span data-stu-id="64ab5-488">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="64ab5-489">[Protokolování adaptéry Application Insights](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span><span class="sxs-lookup"><span data-stu-id="64ab5-489">[Application Insights logging adapters](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span></span>
* <span data-ttu-id="64ab5-490">[Instalace, konfigurace a inicializujte sadu SDK Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="64ab5-490">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>
::: moniker-end

> [!NOTE]
> <span data-ttu-id="64ab5-491">Od 5/1/2019 s názvem článku [Application Insights pro ASP.NET Core](/azure/azure-monitor/app/asp-net-core) je zastaralé a kurzu kroky nefungují.</span><span class="sxs-lookup"><span data-stu-id="64ab5-491">As of 5/1/2019, the article titled [Application Insights for ASP.NET Core](/azure/azure-monitor/app/asp-net-core) is out of date, and the tutorial steps don't work.</span></span> <span data-ttu-id="64ab5-492">Odkazovat na [Application Insights pro aplikace ASP.NET Core](/azure/azure-monitor/app/asp-net-core-no-visualstudio) místo.</span><span class="sxs-lookup"><span data-stu-id="64ab5-492">Refer to [Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core-no-visualstudio) instead.</span></span> <span data-ttu-id="64ab5-493">Problému víme a pracujeme na opravě.</span><span class="sxs-lookup"><span data-stu-id="64ab5-493">We are aware of the issue and are working to correct it.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="64ab5-494">Zprostředkovatele přihlášení třetí strany</span><span class="sxs-lookup"><span data-stu-id="64ab5-494">Third-party logging providers</span></span>

<span data-ttu-id="64ab5-495">Rozhraní protokolování třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="64ab5-495">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="64ab5-496">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="64ab5-496">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="64ab5-497">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub se vzorovými](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="64ab5-497">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="64ab5-498">[JSNLog](http://jsnlog.com/) ([úložiště GitHub se vzorovými](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="64ab5-498">[JSNLog](http://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="64ab5-499">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="64ab5-499">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="64ab5-500">[Loggr](http://loggr.net/) ([úložiště GitHub se vzorovými](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="64ab5-500">[Loggr](http://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="64ab5-501">[NLog](http://nlog-project.org/) ([úložiště GitHub se vzorovými](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="64ab5-501">[NLog](http://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="64ab5-502">[SENTRY](https://sentry.io/welcome/) ([úložiště GitHub se vzorovými](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="64ab5-502">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="64ab5-503">[Serilog](https://serilog.net/) ([úložiště GitHub se vzorovými](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="64ab5-503">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="64ab5-504">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště Github se vzorovými](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="64ab5-504">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="64ab5-505">Můžete provádět některé rozhraní třetích stran [sémantického protokolování, označovaného také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="64ab5-505">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="64ab5-506">Použití rozhraní třetích stran je podobný pomocí jedné z předdefinovaných poskytovatelů:</span><span class="sxs-lookup"><span data-stu-id="64ab5-506">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="64ab5-507">Přidání balíčku NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="64ab5-507">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="64ab5-508">Volání `ILoggerFactory`.</span><span class="sxs-lookup"><span data-stu-id="64ab5-508">Call an `ILoggerFactory`.</span></span>

<span data-ttu-id="64ab5-509">Další informace najdete v dokumentaci každého zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="64ab5-509">For more information, see each provider's documentation.</span></span> <span data-ttu-id="64ab5-510">Zprostředkovatelů přihlášení třetích stran nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="64ab5-510">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="64ab5-511">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="64ab5-511">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

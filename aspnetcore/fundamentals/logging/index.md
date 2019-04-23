---
title: Protokolování v ASP.NET Core
author: tdykstra
description: Další informace o protokolovacího rozhraní v ASP.NET Core. Objevte poskytovatelé vestavěné protokolování a další informace o Oblíbené zprostředkovatele třetí strany.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/02/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 8a2e310b47e32e9015b0c127ed79d8f6bdf2e44d
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982850"
---
# <a name="logging-in-aspnet-core"></a><span data-ttu-id="004e2-104">Protokolování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="004e2-104">Logging in ASP.NET Core</span></span>

<span data-ttu-id="004e2-105">Podle [Steve Smith](https://ardalis.com/) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="004e2-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="004e2-106">ASP.NET Core podporuje protokolování rozhraní API, která funguje s různých poskytovatelů třetích stran a vestavěné protokolování.</span><span class="sxs-lookup"><span data-stu-id="004e2-106">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="004e2-107">Tento článek ukazuje, jak používat rozhraní API protokolování s předdefinované zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-107">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="004e2-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="004e2-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="004e2-109">Přidat zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="004e2-109">Add providers</span></span>

<span data-ttu-id="004e2-110">Zprostředkovatel protokolování zobrazí nebo ukládají protokoly.</span><span class="sxs-lookup"><span data-stu-id="004e2-110">A logging provider displays or stores logs.</span></span> <span data-ttu-id="004e2-111">Například konzola poskytovatel zobrazí protokoly v konzole a uloží je zprostředkovatel služby Azure Application Insights ve službě Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="004e2-111">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="004e2-112">Protokoly můžete odeslány do více cílů přidáním více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="004e2-112">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="004e2-113">Chcete-li přidat poskytovatele, zavolejte poskytovatele `Add{provider name}` metody rozšíření v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="004e2-113">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17-19)]

<span data-ttu-id="004e2-114">Volání výchozí projekt šablony <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, který přidá následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="004e2-114">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="004e2-115">Konzola</span><span class="sxs-lookup"><span data-stu-id="004e2-115">Console</span></span>
* <span data-ttu-id="004e2-116">Ladit</span><span class="sxs-lookup"><span data-stu-id="004e2-116">Debug</span></span>
* <span data-ttu-id="004e2-117">EventSource (od verze 2.2 technologie ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="004e2-117">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="004e2-118">Pokud používáte `CreateDefaultBuilder`, výchozí poskytovatele můžete nahradit vlastními volbami.</span><span class="sxs-lookup"><span data-stu-id="004e2-118">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="004e2-119">Volání <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, a přidat zprostředkovatele, který chcete.</span><span class="sxs-lookup"><span data-stu-id="004e2-119">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="004e2-120">Používat poskytovatele, nainstalujte svůj balíček NuGet a volání metody rozšíření zprostředkovatele na instanci <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span><span class="sxs-lookup"><span data-stu-id="004e2-120">To use a provider, install its NuGet package and call the provider's extension method on an instance of <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="004e2-121">ASP.NET Core [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) poskytuje `ILoggerFactory` instance.</span><span class="sxs-lookup"><span data-stu-id="004e2-121">ASP.NET Core [dependency injection (DI)](xref:fundamentals/dependency-injection) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="004e2-122">`AddConsole` a `AddDebug` metody rozšíření jsou definovány v [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) a [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) balíčky.</span><span class="sxs-lookup"><span data-stu-id="004e2-122">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="004e2-123">Každá metoda rozšíření volá `ILoggerFactory.AddProvider` metodu instance zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-123">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span>

> [!NOTE]
> <span data-ttu-id="004e2-124">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) přidá poskytovatele protokolování v `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="004e2-124">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adds logging providers in the `Startup.Configure` method.</span></span> <span data-ttu-id="004e2-125">K získání výstupu protokolu z kódu, který se spustí dříve, přidejte poskytovatelů protokolování v `Startup` konstruktoru třídy.</span><span class="sxs-lookup"><span data-stu-id="004e2-125">To obtain log output from code that executes earlier, add logging providers in the `Startup` class constructor.</span></span>

::: moniker-end

<span data-ttu-id="004e2-126">Další informace o [vestavěné protokolování poskytovatelé](#built-in-logging-providers) a [zprostředkovatele přihlášení třetí strany](#third-party-logging-providers) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="004e2-126">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="004e2-127">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="004e2-127">Create logs</span></span>

<span data-ttu-id="004e2-128">Získat <xref:Microsoft.Extensions.Logging.ILogger%601> objekt z DI.</span><span class="sxs-lookup"><span data-stu-id="004e2-128">Get an <xref:Microsoft.Extensions.Logging.ILogger%601> object from DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="004e2-129">Následující příklad řadič vytvoří `Information` a `Warning` protokoly.</span><span class="sxs-lookup"><span data-stu-id="004e2-129">The following controller example creates `Information` and `Warning` logs.</span></span> <span data-ttu-id="004e2-130">*Kategorie* je `TodoApiSample.Controllers.TodoController` (plně kvalifikovaný název třídy `TodoController` v ukázkové aplikaci):</span><span class="sxs-lookup"><span data-stu-id="004e2-130">The *category* is `TodoApiSample.Controllers.TodoController` (the fully qualified class name of `TodoController` in the sample app):</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="004e2-131">Následující příklad stránky Razor vytvoří protokolů pomocí `Information` jako *úroveň* a `TodoApiSample.Pages.AboutModel` jako *kategorie*:</span><span class="sxs-lookup"><span data-stu-id="004e2-131">The following Razor Pages example creates logs with `Information` as the *level* and `TodoApiSample.Pages.AboutModel` as the *category*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="004e2-132">V předchozím příkladu se vytvoří protokolů pomocí `Information` a `Warning` jako *úroveň* a `TodoController` třídy jako *kategorie*.</span><span class="sxs-lookup"><span data-stu-id="004e2-132">The preceding example creates logs with `Information` and `Warning` as the *level* and `TodoController` class as the *category*.</span></span> 

::: moniker-end

<span data-ttu-id="004e2-133">Protokol *úroveň* označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="004e2-133">The Log *level* indicates the severity of the logged event.</span></span> <span data-ttu-id="004e2-134">Protokol *kategorie* je řetězec, který je spojen s každou protokolu.</span><span class="sxs-lookup"><span data-stu-id="004e2-134">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="004e2-135">`ILogger<T>` Instance vytvářejí protokoly, které mají plně kvalifikovaný název typu `T` kategorii.</span><span class="sxs-lookup"><span data-stu-id="004e2-135">The `ILogger<T>` instance creates logs that have the fully qualified name of type `T` as the category.</span></span> <span data-ttu-id="004e2-136">[Úrovně](#log-level) a [kategorie](#log-category) jsou vysvětlené podrobněji dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="004e2-136">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="004e2-137">Vytvořit protokoly v po spuštění</span><span class="sxs-lookup"><span data-stu-id="004e2-137">Create logs in Startup</span></span>

<span data-ttu-id="004e2-138">Zápis protokolů `Startup` třídy, patří `ILogger` parametr v konstruktoru podpis:</span><span class="sxs-lookup"><span data-stu-id="004e2-138">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a><span data-ttu-id="004e2-139">Vytvořit protokoly v aplikaci</span><span class="sxs-lookup"><span data-stu-id="004e2-139">Create logs in Program</span></span>

<span data-ttu-id="004e2-140">Zápis protokolů `Program` třídy, získat `ILogger` instanci z DI:</span><span class="sxs-lookup"><span data-stu-id="004e2-140">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="004e2-141">Žádné metody na asynchronní protokolovací nástroj</span><span class="sxs-lookup"><span data-stu-id="004e2-141">No asynchronous logger methods</span></span>

<span data-ttu-id="004e2-142">Protokolování by měl být tak rychle, že se vyplatí výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="004e2-142">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="004e2-143">Pokud vaše úložiště dat protokolování je pomalá, nemáte přímý zápis.</span><span class="sxs-lookup"><span data-stu-id="004e2-143">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="004e2-144">Vezměte v úvahu zpočátku zápis zpráv protokolu do rychlého úložiště a pak později přesunout k úložišti pomalé.</span><span class="sxs-lookup"><span data-stu-id="004e2-144">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="004e2-145">Například pokud jste protokolování na SQL Server, nechcete provést přímo v `Log` metody, protože `Log` metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="004e2-145">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="004e2-146">Místo toho synchronně přidání protokolu zpráv do fronty v paměti a mají pracovní proces na pozadí zpráv z fronty pro asynchronní práci z odesílání dat do SQL serveru.</span><span class="sxs-lookup"><span data-stu-id="004e2-146">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="004e2-147">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="004e2-147">Configuration</span></span>

<span data-ttu-id="004e2-148">Zprostředkovatel konfigurace protokolování poskytuje jeden nebo více poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="004e2-148">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="004e2-149">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="004e2-149">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="004e2-150">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="004e2-150">Command-line arguments.</span></span>
* <span data-ttu-id="004e2-151">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="004e2-151">Environment variables.</span></span>
* <span data-ttu-id="004e2-152">Objekty .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="004e2-152">In-memory .NET objects.</span></span>
* <span data-ttu-id="004e2-153">Nešifrované [manažera tajných](xref:security/app-secrets) úložiště.</span><span class="sxs-lookup"><span data-stu-id="004e2-153">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="004e2-154">Uložení šifrovaného uživatelského, jako například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="004e2-154">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="004e2-155">Vlastní zprostředkovatelé (nainstalované nebo vytváření).</span><span class="sxs-lookup"><span data-stu-id="004e2-155">Custom providers (installed or created).</span></span>

<span data-ttu-id="004e2-156">Například konfigurace protokolování běžně poskytované `Logging` části souborů s nastavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="004e2-156">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="004e2-157">Následující příklad ukazuje obsah typické *appsettings. Development.JSON* souboru:</span><span class="sxs-lookup"><span data-stu-id="004e2-157">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="004e2-158">`Logging` Vlastnost může mít `LogLevel` a protokolu vlastnosti zprostředkovatele (konzoly se zobrazí).</span><span class="sxs-lookup"><span data-stu-id="004e2-158">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="004e2-159">`LogLevel` Vlastnosti v části `Logging` Určuje minimální [úroveň](#log-level) k protokolování pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="004e2-159">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="004e2-160">V tomto příkladu `System` a `Microsoft` kategorie protokolu `Information` úroveň a všech ostatních protokolů na `Debug` úroveň.</span><span class="sxs-lookup"><span data-stu-id="004e2-160">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="004e2-161">Další vlastnosti v části `Logging` určit konkrétní zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="004e2-161">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="004e2-162">V příkladu se pro zprostředkovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="004e2-162">The example is for the Console provider.</span></span> <span data-ttu-id="004e2-163">Pokud poskytovatel podporuje [protokolu obory](#log-scopes), `IncludeScopes` označuje, zda jsou povolena.</span><span class="sxs-lookup"><span data-stu-id="004e2-163">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="004e2-164">Vlastnost zprostředkovatele (například `Console` v příkladu) může také určit `LogLevel` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="004e2-164">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="004e2-165">`LogLevel` v části zprostředkovatele Určuje úrovně pro přihlášení pro daného poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-165">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="004e2-166">Pokud úrovně jsou určené v `Logging.{providername}.LogLevel`, má přednost před nic nastavit `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="004e2-166">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

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

<span data-ttu-id="004e2-167">`LogLevel` klíče představují názvy protokolů.</span><span class="sxs-lookup"><span data-stu-id="004e2-167">`LogLevel` keys represent log names.</span></span> <span data-ttu-id="004e2-168">`Default` Klíč platí do protokolů, které nejsou výslovně uvedena.</span><span class="sxs-lookup"><span data-stu-id="004e2-168">The `Default` key applies to logs not explicitly listed.</span></span> <span data-ttu-id="004e2-169">Hodnota představuje [úrovně protokolování](#log-level) použitý pro daný protokol.</span><span class="sxs-lookup"><span data-stu-id="004e2-169">The value represents the [log level](#log-level) applied to the given log.</span></span>

::: moniker-end

<span data-ttu-id="004e2-170">Informace o implementaci zprostředkovatele konfigurace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="004e2-170">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="004e2-171">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="004e2-171">Sample logging output</span></span>

<span data-ttu-id="004e2-172">Ukázkový kód je znázorněno v předchozí části protokolů se objeví v konzole při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="004e2-172">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="004e2-173">Tady je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="004e2-173">Here's an example of console output:</span></span>

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

<span data-ttu-id="004e2-174">Protokoly předchozích nevygenerovaly se tím, že požadavek HTTP Get na ukázkovou aplikaci na `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="004e2-174">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="004e2-175">Tady je příklad stejného protokolů, jak se objeví v okně ladění, když spustíte ukázkovou aplikaci v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="004e2-175">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="004e2-176">Protokoly, které jsou vytvořené `ILogger` volání je znázorněno v předchozí části začínají řetězcem "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="004e2-176">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="004e2-177">Protokoly, které začínají řetězcem "Microsoft". kategorie jsou z kódu rozhraní framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="004e2-177">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="004e2-178">ASP.NET Core a kód aplikace používají stejné protokolování rozhraní API a poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="004e2-178">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="004e2-179">Zbývající část tohoto článku popisuje některé podrobnosti a možností pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="004e2-179">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="004e2-180">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="004e2-180">NuGet packages</span></span>

<span data-ttu-id="004e2-181">`ILogger` a `ILoggerFactory` rozhraní jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), a mají výchozí implementace pro ně [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="004e2-181">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="004e2-182">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="004e2-182">Log category</span></span>

<span data-ttu-id="004e2-183">Když `ILogger` je vytvořen objekt, *kategorie* je pro ni zadán.</span><span class="sxs-lookup"><span data-stu-id="004e2-183">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="004e2-184">Kategorie je součástí každé zprávě protokolu vytvořené z této instance `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="004e2-184">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="004e2-185">Kategorie může být libovolný řetězec, ale tato konvence je použití názvu třídy, jako je například "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="004e2-185">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="004e2-186">Použití `ILogger<T>` zobrazíte `ILogger` instanci, která používá plně kvalifikovaný typ název `T` jako kategorie:</span><span class="sxs-lookup"><span data-stu-id="004e2-186">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="004e2-187">Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="004e2-187">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="004e2-188">`ILogger<T>` je ekvivalentní volání `CreateLogger` názvem plně kvalifikovaný typ `T`.</span><span class="sxs-lookup"><span data-stu-id="004e2-188">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="004e2-189">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="004e2-189">Log level</span></span>

<span data-ttu-id="004e2-190">Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="004e2-190">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="004e2-191">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="004e2-191">The log level indicates the severity or importance.</span></span> <span data-ttu-id="004e2-192">Například můžete například napsat `Information` po skončení metody obvykle a protokolu `Warning` protokolu po návratu metody *404 Nenalezeno* stavový kód.</span><span class="sxs-lookup"><span data-stu-id="004e2-192">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="004e2-193">Následující kód vytvoří `Information` a `Warning` protokoly:</span><span class="sxs-lookup"><span data-stu-id="004e2-193">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="004e2-194">V předchozím kódu je první parametr [protokolu událost s ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="004e2-194">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="004e2-195">Druhý parametr je šablona zprávy s zástupné symboly pro argument hodnoty podle zbývající parametry metody.</span><span class="sxs-lookup"><span data-stu-id="004e2-195">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="004e2-196">Parametry metody jsou vysvětleny v [zpráv šablonu části](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="004e2-196">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="004e2-197">Metody, které obsahují v názvu metody na úrovni protokolu (například `LogInformation` a `LogWarning`) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="004e2-197">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="004e2-198">Tyto metody volat `Log` metodu, která přebírá `LogLevel` parametru.</span><span class="sxs-lookup"><span data-stu-id="004e2-198">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="004e2-199">Můžete volat `Log` metoda přímo spíše než jeden z těchto metod rozšíření, ale syntaxe je poměrně složitý.</span><span class="sxs-lookup"><span data-stu-id="004e2-199">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="004e2-200">Další informace najdete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a [rozšíření protokolovače zdrojový kód](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="004e2-200">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="004e2-201">ASP.NET Core definuje následující úrovně protokolu zde seřazené od nejnižší a nejvyšší závažnost.</span><span class="sxs-lookup"><span data-stu-id="004e2-201">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="004e2-202">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="004e2-202">Trace = 0</span></span>

  <span data-ttu-id="004e2-203">Pro informace, které je obvykle vhodné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="004e2-203">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="004e2-204">Tyto zprávy mohou obsahovat citlivé aplikaci data a by nemělo být povoleno v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="004e2-204">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="004e2-205">*Ve výchozím nastavení zakázané.*</span><span class="sxs-lookup"><span data-stu-id="004e2-205">*Disabled by default.*</span></span>

* <span data-ttu-id="004e2-206">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="004e2-206">Debug = 1</span></span>

  <span data-ttu-id="004e2-207">Informace, které mohou být užitečné pro vývoj a ladění.</span><span class="sxs-lookup"><span data-stu-id="004e2-207">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="004e2-208">Příklad: `Entering method Configure with flag set to true.` Povolit `Debug` úroveň zaznamená v produkčním prostředí pouze při řešení potíží, kvůli velkému počtu protokoly.</span><span class="sxs-lookup"><span data-stu-id="004e2-208">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="004e2-209">Informace o = 2</span><span class="sxs-lookup"><span data-stu-id="004e2-209">Information = 2</span></span>

  <span data-ttu-id="004e2-210">Pro sledování obecný tok z aplikace.</span><span class="sxs-lookup"><span data-stu-id="004e2-210">For tracking the general flow of the app.</span></span> <span data-ttu-id="004e2-211">Tyto protokoly mají obvykle dlouhodobé některá z hodnot.</span><span class="sxs-lookup"><span data-stu-id="004e2-211">These logs typically have some long-term value.</span></span> <span data-ttu-id="004e2-212">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="004e2-212">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="004e2-213">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="004e2-213">Warning = 3</span></span>

  <span data-ttu-id="004e2-214">Neobvyklé nebo neočekávaných událostí v aplikaci flow.</span><span class="sxs-lookup"><span data-stu-id="004e2-214">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="004e2-215">Ty mohou obsahovat chyby nebo jinými podmínkami, které není způsobit, že aplikace přestane, ale může být nutné prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="004e2-215">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="004e2-216">Zpracované výjimky jsou běžné místo, kde můžete použít `Warning` úrovně protokolování.</span><span class="sxs-lookup"><span data-stu-id="004e2-216">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="004e2-217">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="004e2-217">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="004e2-218">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="004e2-218">Error = 4</span></span>

  <span data-ttu-id="004e2-219">Chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="004e2-219">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="004e2-220">Tyto zprávy označují selhání aktuální aktivitu nebo operace (jako je například aktuální požadavek HTTP), ne k selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="004e2-220">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="004e2-221">Příklad zprávy protokolu: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="004e2-221">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="004e2-222">Kritická = 5</span><span class="sxs-lookup"><span data-stu-id="004e2-222">Critical = 5</span></span>

  <span data-ttu-id="004e2-223">Chyby, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="004e2-223">For failures that require immediate attention.</span></span> <span data-ttu-id="004e2-224">Příklady: scénářům ztráty dat, volné místo na disku.</span><span class="sxs-lookup"><span data-stu-id="004e2-224">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="004e2-225">Pomocí úroveň protokolu můžete řídit, kolik výstup protokolu je zapsán do konkrétního úložiště média nebo zobrazení okna.</span><span class="sxs-lookup"><span data-stu-id="004e2-225">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="004e2-226">Příklad:</span><span class="sxs-lookup"><span data-stu-id="004e2-226">For example:</span></span>

* <span data-ttu-id="004e2-227">V produkčním prostředí, odeslat `Trace` prostřednictvím `Information` úrovně do úložiště dat svazku.</span><span class="sxs-lookup"><span data-stu-id="004e2-227">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="004e2-228">Odeslat `Warning` prostřednictvím `Critical` na hodnotu data ukládat.</span><span class="sxs-lookup"><span data-stu-id="004e2-228">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="004e2-229">Během vývoje, odeslat `Warning` prostřednictvím `Critical` do konzoly a přidejte `Trace` prostřednictvím `Information` při řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="004e2-229">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="004e2-230">[Filtrování protokolu](#log-filtering) části dále v tomto článku vysvětluje, jak řídit které úrovně protokolu zpracovává zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-230">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="004e2-231">ASP.NET Core zapisuje protokoly pro události rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="004e2-231">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="004e2-232">Příklady protokolu dříve v tomto článku vyloučené protokoly níže `Information` úroveň, takže žádná `Debug` nebo `Trace` úrovně protokoly byly vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="004e2-232">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="004e2-233">Tady je příklad vytvořených spuštěním ukázkové aplikace nakonfigurovat tak, aby zobrazit protokoly konzoly `Debug` protokoly:</span><span class="sxs-lookup"><span data-stu-id="004e2-233">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="004e2-234">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="004e2-234">Log event ID</span></span>

<span data-ttu-id="004e2-235">Můžete zadat všechny protokoly *ID události*.</span><span class="sxs-lookup"><span data-stu-id="004e2-235">Each log can specify an *event ID*.</span></span> <span data-ttu-id="004e2-236">Ukázková aplikace dělá to pomocí místně definované `LoggingEvents` třídy:</span><span class="sxs-lookup"><span data-stu-id="004e2-236">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="004e2-237">ID události přidruží sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="004e2-237">An event ID associates a set of events.</span></span> <span data-ttu-id="004e2-238">Všechny protokoly týkající se zobrazení seznamu položek na stránce může být například 1001.</span><span class="sxs-lookup"><span data-stu-id="004e2-238">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="004e2-239">Poskytovatel protokolování může ukládat ID události v poli ID ve zprávě protokolování nebo dokonce vůbec.</span><span class="sxs-lookup"><span data-stu-id="004e2-239">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="004e2-240">Ladění zprostředkovatele nezobrazí ID událostí.</span><span class="sxs-lookup"><span data-stu-id="004e2-240">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="004e2-241">Konzola poskytovatel zobrazuje ID událostí do hranatých závorek za kategorií:</span><span class="sxs-lookup"><span data-stu-id="004e2-241">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="004e2-242">Šablona zprávy protokolu</span><span class="sxs-lookup"><span data-stu-id="004e2-242">Log message template</span></span>

<span data-ttu-id="004e2-243">Každý protokol Určuje zpráv šablonu.</span><span class="sxs-lookup"><span data-stu-id="004e2-243">Each log specifies a message template.</span></span> <span data-ttu-id="004e2-244">Šablona zprávy může obsahovat zástupné znaky, pro které jsou zadány argumenty.</span><span class="sxs-lookup"><span data-stu-id="004e2-244">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="004e2-245">Použijte názvy nahraďte zástupné symboly, nikoli čísla.</span><span class="sxs-lookup"><span data-stu-id="004e2-245">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="004e2-246">Pořadí zástupné symboly, nikoli jejich názvy, určuje, jaké parametry se používají k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="004e2-246">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="004e2-247">V následujícím kódu Všimněte si, že názvy parametrů jsou mimo pořadí v šablonu zprávy:</span><span class="sxs-lookup"><span data-stu-id="004e2-247">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="004e2-248">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:</span><span class="sxs-lookup"><span data-stu-id="004e2-248">This code creates a log message with the parameter values in sequence:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="004e2-249">Protokolovacího rozhraní funguje tak, aby zprostředkovatelé protokolování můžete implementovat [sémantického protokolování, označovaného také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="004e2-249">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="004e2-250">Samotné argumenty jsou předány do systému protokolování, ne jenom šablony formátovaná zpráva.</span><span class="sxs-lookup"><span data-stu-id="004e2-250">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="004e2-251">Tyto informace umožňuje poskytovatelům protokolování pro uložení hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="004e2-251">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="004e2-252">Předpokládejme například, protokolovacího nástroje metoda volání najdete takto:</span><span class="sxs-lookup"><span data-stu-id="004e2-252">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="004e2-253">Pokud jste odesílali protokoly do služby Azure Table Storage, může mít každá entita Azure Table `ID` a `RequestTime` vlastnosti, které zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="004e2-253">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="004e2-254">Dotaz můžete najít všechny protokoly v rámci konkrétní `RequestTime` rozsahu bez parsování časový limit textové zprávy.</span><span class="sxs-lookup"><span data-stu-id="004e2-254">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="004e2-255">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="004e2-255">Logging exceptions</span></span>

<span data-ttu-id="004e2-256">Protokolovací nástroj metody mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="004e2-256">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="004e2-257">Různí poskytovatelé zpracovat informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="004e2-257">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="004e2-258">Tady je příklad výstupu ladění zprostředkovatele z výše uvedeném kódu.</span><span class="sxs-lookup"><span data-stu-id="004e2-258">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="004e2-259">Filtrování protokolu</span><span class="sxs-lookup"><span data-stu-id="004e2-259">Log filtering</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="004e2-260">Můžete zadat minimální úroveň protokolování pro konkrétního zprostředkovatele a kategorie nebo pro všechny poskytovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="004e2-260">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="004e2-261">Všechny protokoly nižší než minimální úroveň nejsou předán tohoto poskytovatele, tak nemusíte získat zobrazení nebo uložené.</span><span class="sxs-lookup"><span data-stu-id="004e2-261">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="004e2-262">Chcete-li potlačit všechny protokoly, zadejte `LogLevel.None` jako minimální úroveň protokolování.</span><span class="sxs-lookup"><span data-stu-id="004e2-262">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="004e2-263">Celočíselnou hodnotu `LogLevel.None` je 6, která je vyšší než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="004e2-263">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="004e2-264">Vytvoření pravidla filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="004e2-264">Create filter rules in configuration</span></span>

<span data-ttu-id="004e2-265">Volání kódu šablony projektu `CreateDefaultBuilder` nastavit protokolování pro poskytovatele konzoly a ladění.</span><span class="sxs-lookup"><span data-stu-id="004e2-265">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="004e2-266">`CreateDefaultBuilder` Metoda také nastaví protokolování hledat konfiguraci `Logging` oddílu, psát kód podobný tomuto:</span><span class="sxs-lookup"><span data-stu-id="004e2-266">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=16)]

<span data-ttu-id="004e2-267">Konfigurační data Určuje minimální úrovně podle poskytovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="004e2-267">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="004e2-268">Tento dokument JSON vytvoří šest pravidla filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a pro všechny poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-268">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="004e2-269">Jedno pravidlo je vybrán pro každého zprostředkovatele při `ILogger` je vytvořen objekt.</span><span class="sxs-lookup"><span data-stu-id="004e2-269">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="004e2-270">Pravidla filtru v kódu</span><span class="sxs-lookup"><span data-stu-id="004e2-270">Filter rules in code</span></span>

<span data-ttu-id="004e2-271">Následující příklad ukazuje, jak zaregistrovat pravidla filtrování v kódu:</span><span class="sxs-lookup"><span data-stu-id="004e2-271">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="004e2-272">Druhá `AddFilter` Určuje zprostředkovatele, který ladění pomocí názvu typu.</span><span class="sxs-lookup"><span data-stu-id="004e2-272">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="004e2-273">První `AddFilter` platí pro všechny poskytovatele, protože ji neurčuje, typ zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-273">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="004e2-274">Jak pravidla filtrování se použijí</span><span class="sxs-lookup"><span data-stu-id="004e2-274">How filtering rules are applied</span></span>

<span data-ttu-id="004e2-275">Konfigurační data a `AddFilter` kód zobrazený v předchozích ukázkách vytvořit pravidla je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="004e2-275">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="004e2-276">Prvních šest pocházet z příklad konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="004e2-276">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="004e2-277">Číslo</span><span class="sxs-lookup"><span data-stu-id="004e2-277">Number</span></span> | <span data-ttu-id="004e2-278">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="004e2-278">Provider</span></span>      | <span data-ttu-id="004e2-279">Kategorie, které začínají...</span><span class="sxs-lookup"><span data-stu-id="004e2-279">Categories that begin with ...</span></span>          | <span data-ttu-id="004e2-280">Minimální úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="004e2-280">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="004e2-281">1</span><span class="sxs-lookup"><span data-stu-id="004e2-281">1</span></span>      | <span data-ttu-id="004e2-282">Ladit</span><span class="sxs-lookup"><span data-stu-id="004e2-282">Debug</span></span>         | <span data-ttu-id="004e2-283">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="004e2-283">All categories</span></span>                          | <span data-ttu-id="004e2-284">Informace o</span><span class="sxs-lookup"><span data-stu-id="004e2-284">Information</span></span>       |
| <span data-ttu-id="004e2-285">2</span><span class="sxs-lookup"><span data-stu-id="004e2-285">2</span></span>      | <span data-ttu-id="004e2-286">Konzola</span><span class="sxs-lookup"><span data-stu-id="004e2-286">Console</span></span>       | <span data-ttu-id="004e2-287">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="004e2-287">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="004e2-288">Upozornění</span><span class="sxs-lookup"><span data-stu-id="004e2-288">Warning</span></span>           |
| <span data-ttu-id="004e2-289">3</span><span class="sxs-lookup"><span data-stu-id="004e2-289">3</span></span>      | <span data-ttu-id="004e2-290">Konzola</span><span class="sxs-lookup"><span data-stu-id="004e2-290">Console</span></span>       | <span data-ttu-id="004e2-291">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="004e2-291">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="004e2-292">Ladit</span><span class="sxs-lookup"><span data-stu-id="004e2-292">Debug</span></span>             |
| <span data-ttu-id="004e2-293">4</span><span class="sxs-lookup"><span data-stu-id="004e2-293">4</span></span>      | <span data-ttu-id="004e2-294">Konzola</span><span class="sxs-lookup"><span data-stu-id="004e2-294">Console</span></span>       | <span data-ttu-id="004e2-295">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="004e2-295">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="004e2-296">Chyba</span><span class="sxs-lookup"><span data-stu-id="004e2-296">Error</span></span>             |
| <span data-ttu-id="004e2-297">5</span><span class="sxs-lookup"><span data-stu-id="004e2-297">5</span></span>      | <span data-ttu-id="004e2-298">Konzola</span><span class="sxs-lookup"><span data-stu-id="004e2-298">Console</span></span>       | <span data-ttu-id="004e2-299">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="004e2-299">All categories</span></span>                          | <span data-ttu-id="004e2-300">Informace o</span><span class="sxs-lookup"><span data-stu-id="004e2-300">Information</span></span>       |
| <span data-ttu-id="004e2-301">6</span><span class="sxs-lookup"><span data-stu-id="004e2-301">6</span></span>      | <span data-ttu-id="004e2-302">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="004e2-302">All providers</span></span> | <span data-ttu-id="004e2-303">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="004e2-303">All categories</span></span>                          | <span data-ttu-id="004e2-304">Ladit</span><span class="sxs-lookup"><span data-stu-id="004e2-304">Debug</span></span>             |
| <span data-ttu-id="004e2-305">7</span><span class="sxs-lookup"><span data-stu-id="004e2-305">7</span></span>      | <span data-ttu-id="004e2-306">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="004e2-306">All providers</span></span> | <span data-ttu-id="004e2-307">Systém</span><span class="sxs-lookup"><span data-stu-id="004e2-307">System</span></span>                                  | <span data-ttu-id="004e2-308">Ladit</span><span class="sxs-lookup"><span data-stu-id="004e2-308">Debug</span></span>             |
| <span data-ttu-id="004e2-309">8</span><span class="sxs-lookup"><span data-stu-id="004e2-309">8</span></span>      | <span data-ttu-id="004e2-310">Ladit</span><span class="sxs-lookup"><span data-stu-id="004e2-310">Debug</span></span>         | <span data-ttu-id="004e2-311">Microsoft</span><span class="sxs-lookup"><span data-stu-id="004e2-311">Microsoft</span></span>                               | <span data-ttu-id="004e2-312">Trasování</span><span class="sxs-lookup"><span data-stu-id="004e2-312">Trace</span></span>             |

<span data-ttu-id="004e2-313">Když `ILogger` je vytvořen objekt, `ILoggerFactory` objekt vybere jedno pravidlo na poskytovatele, který chcete použít pro tento protokolovač.</span><span class="sxs-lookup"><span data-stu-id="004e2-313">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="004e2-314">Všechny zprávy, autorem `ILogger` instance jsou filtrovány podle vybrané pravidla.</span><span class="sxs-lookup"><span data-stu-id="004e2-314">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="004e2-315">Nejspecifičtější pravidla pro každého zprostředkovatele a dvojice kategorie je vybrat z dostupných pravidla.</span><span class="sxs-lookup"><span data-stu-id="004e2-315">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="004e2-316">Následující požadovaný algoritmus se používá pro každého zprostředkovatele při `ILogger` se vytvoří pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="004e2-316">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="004e2-317">Vyberte všechna pravidla, které odpovídají zprostředkovateli nebo její alias.</span><span class="sxs-lookup"><span data-stu-id="004e2-317">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="004e2-318">Pokud není nalezena žádná shoda, vyberte všechna pravidla s poskytovatelem služby prázdný.</span><span class="sxs-lookup"><span data-stu-id="004e2-318">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="004e2-319">Z výsledků v předchozím kroku vyberte pravidla s nejdelší odpovídající předpona kategorie.</span><span class="sxs-lookup"><span data-stu-id="004e2-319">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="004e2-320">Pokud není nalezena žádná shoda, vyberte všechna pravidla, které nechcete zadat kategorii.</span><span class="sxs-lookup"><span data-stu-id="004e2-320">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="004e2-321">Pokud je vybraných víc pravidel, trvat, než **poslední** jeden.</span><span class="sxs-lookup"><span data-stu-id="004e2-321">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="004e2-322">Pokud jsou vybraná žádná pravidla, použijte `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="004e2-322">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="004e2-323">S předchozím seznam pravidel, Předpokládejme, že vytvoříte `ILogger` objektu pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="004e2-323">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="004e2-324">Ladění zprostředkovatele platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="004e2-324">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="004e2-325">Pravidlo 8 není nejvíce specifické, tak, aby se byla vybrána.</span><span class="sxs-lookup"><span data-stu-id="004e2-325">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="004e2-326">Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="004e2-326">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="004e2-327">Pravidlo 3 není nejvíce specifické.</span><span class="sxs-lookup"><span data-stu-id="004e2-327">Rule 3 is most specific.</span></span>

<span data-ttu-id="004e2-328">Výsledná `ILogger` instance odešle protokoly `Trace` úroveň a vyšší k poskytovateli ladění.</span><span class="sxs-lookup"><span data-stu-id="004e2-328">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="004e2-329">Protokoly z `Debug` úrovně a novější se odesílají do konzoly zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-329">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="004e2-330">Aliasy poskytovatele</span><span class="sxs-lookup"><span data-stu-id="004e2-330">Provider aliases</span></span>

<span data-ttu-id="004e2-331">Každý poskytovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="004e2-331">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="004e2-332">Pro předdefinované zprostředkovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="004e2-332">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="004e2-333">Konzola</span><span class="sxs-lookup"><span data-stu-id="004e2-333">Console</span></span>
* <span data-ttu-id="004e2-334">Ladit</span><span class="sxs-lookup"><span data-stu-id="004e2-334">Debug</span></span>
* <span data-ttu-id="004e2-335">EventLog</span><span class="sxs-lookup"><span data-stu-id="004e2-335">EventLog</span></span>
* <span data-ttu-id="004e2-336">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="004e2-336">AzureAppServicesFile</span></span>
* <span data-ttu-id="004e2-337">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="004e2-337">AzureAppServicesBlob</span></span>
* <span data-ttu-id="004e2-338">TraceSource</span><span class="sxs-lookup"><span data-stu-id="004e2-338">TraceSource</span></span>
* <span data-ttu-id="004e2-339">EventSource</span><span class="sxs-lookup"><span data-stu-id="004e2-339">EventSource</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="004e2-340">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="004e2-340">Default minimum level</span></span>

<span data-ttu-id="004e2-341">Je k dispozici minimální úroveň nastavení, která se projeví pouze v případě, že žádná pravidla z konfigurace nebo kódu platí pro daného poskytovatele a kategorii.</span><span class="sxs-lookup"><span data-stu-id="004e2-341">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="004e2-342">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="004e2-342">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="004e2-343">Pokud není explicitně nastavena na minimální úroveň, výchozí hodnota je `Information`, což znamená, že `Trace` a `Debug` protokoly jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="004e2-343">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="004e2-344">Funkce filtru</span><span class="sxs-lookup"><span data-stu-id="004e2-344">Filter functions</span></span>

<span data-ttu-id="004e2-345">Pro všechny poskytovatele a kategorie, které nemají přiřazené konfigurace nebo kódu pravidla je vyvolána funkce filtru.</span><span class="sxs-lookup"><span data-stu-id="004e2-345">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="004e2-346">Kód ve funkci má přístup k typu poskytovatele, kategorii a úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="004e2-346">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="004e2-347">Příklad:</span><span class="sxs-lookup"><span data-stu-id="004e2-347">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="004e2-348">Někteří poskytovatelé protokolování umožňují zadat při protokoly by měly být zapsána do úložiště média nebo ignorovat podle úroveň protokolu a kategorie.</span><span class="sxs-lookup"><span data-stu-id="004e2-348">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="004e2-349">`AddConsole` a `AddDebug` rozšiřující metody poskytují přetížení přijímající kritéria filtrování.</span><span class="sxs-lookup"><span data-stu-id="004e2-349">The `AddConsole` and `AddDebug` extension methods provide overloads that accept filtering criteria.</span></span> <span data-ttu-id="004e2-350">Následující ukázkový kód způsobí, že konzola poskytovatel ignorovat protokolech níže `Warning` úroveň, při ladění zprostředkovatele ignoruje protokoly, které vytvoří rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="004e2-350">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="004e2-351">`AddEventLog` Metoda má přetížení přijímající `EventLogSettings` instanci, která může obsahovat funkce filtrování v jeho `Filter` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="004e2-351">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="004e2-352">Zprostředkovatel TraceSource neposkytuje, některý z těchto přetížení, protože jeho úroveň protokolování a další parametry jsou založené na `SourceSwitch` a `TraceListener` používá.</span><span class="sxs-lookup"><span data-stu-id="004e2-352">The TraceSource provider doesn't provide any of those overloads, since its logging level and other parameters are based on the `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="004e2-353">Chcete-li nastavit pravidla filtrování pro všech zprostředkovatelů, které jsou registrovány `ILoggerFactory` použijte `WithFilter` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="004e2-353">To set filtering rules for all providers that are registered with an `ILoggerFactory` instance, use the `WithFilter` extension method.</span></span> <span data-ttu-id="004e2-354">Následující příklad omezuje framework protokoly (kategorie začíná řetězcem "Microsoft" nebo "Systém") a upozornění při protokolování na úrovni ladění pro protokoly vytvořené metodami kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="004e2-354">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while logging at debug level for logs created by application code.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="004e2-355">Chcete-li zabránit zapisovaná všechny protokoly, zadejte `LogLevel.None` jako minimální úroveň protokolování.</span><span class="sxs-lookup"><span data-stu-id="004e2-355">To prevent any logs from being written, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="004e2-356">Celočíselnou hodnotu `LogLevel.None` je 6, která je vyšší než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="004e2-356">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="004e2-357">`WithFilter` – Metoda rozšíření poskytuje [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="004e2-357">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="004e2-358">Metoda vrátí nový `ILoggerFactory` zaregistrovaný poskytovatel instanci, která bude filtrovat zprávy protokolu, který je předán všichni poskytovatelé protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="004e2-358">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="004e2-359">To nemá vliv na jiné `ILoggerFactory` instance, včetně původní `ILoggerFactory` instance.</span><span class="sxs-lookup"><span data-stu-id="004e2-359">It doesn't affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="004e2-360">Systém kategorií a úrovně</span><span class="sxs-lookup"><span data-stu-id="004e2-360">System categories and levels</span></span>

<span data-ttu-id="004e2-361">Tady jsou některé kategorie, která používá ASP.NET Core a Entity Framework Core s poznámky o co protokoly můžete očekávat od nich:</span><span class="sxs-lookup"><span data-stu-id="004e2-361">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="004e2-362">Kategorie</span><span class="sxs-lookup"><span data-stu-id="004e2-362">Category</span></span>                            | <span data-ttu-id="004e2-363">Poznámky</span><span class="sxs-lookup"><span data-stu-id="004e2-363">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="004e2-364">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="004e2-364">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="004e2-365">Diagnostika obecné ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="004e2-365">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="004e2-366">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="004e2-366">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="004e2-367">Jaké byly považovat za, nalezen a používání klíčů.</span><span class="sxs-lookup"><span data-stu-id="004e2-367">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="004e2-368">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="004e2-368">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="004e2-369">Hostitelé povolené.</span><span class="sxs-lookup"><span data-stu-id="004e2-369">Hosts allowed.</span></span> |
| <span data-ttu-id="004e2-370">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="004e2-370">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="004e2-371">Jak dlouho požadavky HTTP trvalo dokončení a kdy se spouští.</span><span class="sxs-lookup"><span data-stu-id="004e2-371">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="004e2-372">Po spuštění sestavení, hostování byly načteny.</span><span class="sxs-lookup"><span data-stu-id="004e2-372">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="004e2-373">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="004e2-373">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="004e2-374">Diagnostika MVC a syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="004e2-374">MVC and Razor diagnostics.</span></span> <span data-ttu-id="004e2-375">Model, navázání, spuštění filtru, kompilace zobrazení výběr akce.</span><span class="sxs-lookup"><span data-stu-id="004e2-375">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="004e2-376">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="004e2-376">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="004e2-377">Trasu odpovídající informace.</span><span class="sxs-lookup"><span data-stu-id="004e2-377">Route matching information.</span></span> |
| <span data-ttu-id="004e2-378">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="004e2-378">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="004e2-379">Připojení spuštění, zastavení a keep alive odpovědi.</span><span class="sxs-lookup"><span data-stu-id="004e2-379">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="004e2-380">Informace o certifikátu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="004e2-380">HTTPS certificate information.</span></span> |
| <span data-ttu-id="004e2-381">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="004e2-381">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="004e2-382">Zpracování souborů.</span><span class="sxs-lookup"><span data-stu-id="004e2-382">Files served.</span></span> |
| <span data-ttu-id="004e2-383">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="004e2-383">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="004e2-384">Obecné Entity Framework Core diagnostiky.</span><span class="sxs-lookup"><span data-stu-id="004e2-384">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="004e2-385">Aktivita a konfigurace, detekce změn migracemi databází.</span><span class="sxs-lookup"><span data-stu-id="004e2-385">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="004e2-386">Protokol obory</span><span class="sxs-lookup"><span data-stu-id="004e2-386">Log scopes</span></span>

 <span data-ttu-id="004e2-387">A *oboru* můžete seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="004e2-387">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="004e2-388">Toto seskupení je možné se připojit k každý protokol, který je vytvořen jako součást sady stejná data.</span><span class="sxs-lookup"><span data-stu-id="004e2-388">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="004e2-389">Každý protokol vytvořených jako součást zpracování transakcí může obsahovat třeba ID transakce.</span><span class="sxs-lookup"><span data-stu-id="004e2-389">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="004e2-390">Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metoda a bude trvat, dokud je uvolněn.</span><span class="sxs-lookup"><span data-stu-id="004e2-390">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="004e2-391">Volání protokolovací nástroj pro zabalení použít obor `using` blok:</span><span class="sxs-lookup"><span data-stu-id="004e2-391">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="004e2-392">Následující kód umožní obory pro zprostředkovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="004e2-392">The following code enables scopes for the console provider:</span></span>

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="004e2-393">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="004e2-393">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="004e2-394">Konfigurace `IncludeScopes` možnost protokolovací nástroj konzoly je nutné povolit protokolování na základě oboru.</span><span class="sxs-lookup"><span data-stu-id="004e2-394">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="004e2-395">Informace o konfiguraci, najdete v článku [konfigurace](#configuration) oddílu.</span><span class="sxs-lookup"><span data-stu-id="004e2-395">For information on configuration, see the [Configuration](#configuration) section.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="004e2-396">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="004e2-396">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="004e2-397">Konfigurace `IncludeScopes` možnost protokolovací nástroj konzoly je nutné povolit protokolování na základě oboru.</span><span class="sxs-lookup"><span data-stu-id="004e2-397">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="004e2-398">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="004e2-398">*Startup.cs*:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

<span data-ttu-id="004e2-399">Každé zprávě protokolu obsahuje rozsahem informací:</span><span class="sxs-lookup"><span data-stu-id="004e2-399">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="004e2-400">Poskytovatelé vestavěné protokolování</span><span class="sxs-lookup"><span data-stu-id="004e2-400">Built-in logging providers</span></span>

<span data-ttu-id="004e2-401">ASP.NET Core se celá dodává následující zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="004e2-401">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="004e2-402">Console</span><span class="sxs-lookup"><span data-stu-id="004e2-402">Console</span></span>](#console-provider)
* [<span data-ttu-id="004e2-403">Ladění</span><span class="sxs-lookup"><span data-stu-id="004e2-403">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="004e2-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="004e2-404">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="004e2-405">EventLog</span><span class="sxs-lookup"><span data-stu-id="004e2-405">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="004e2-406">TraceSource</span><span class="sxs-lookup"><span data-stu-id="004e2-406">TraceSource</span></span>](#tracesource-provider)

<span data-ttu-id="004e2-407">Možnosti pro [protokolování v Azure](#logging-in-azure) jsou popsané dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="004e2-407">Options for [Logging in Azure](#logging-in-azure) are covered later in this article.</span></span>

<span data-ttu-id="004e2-408">Informace o protokolování stdout najdete v tématu <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> a <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span><span class="sxs-lookup"><span data-stu-id="004e2-408">For information about stdout logging, see <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> and <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="004e2-409">Konzola zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="004e2-409">Console provider</span></span>

<span data-ttu-id="004e2-410">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) balíček zprostředkovatele odešle výstup protokolu konzoly.</span><span class="sxs-lookup"><span data-stu-id="004e2-410">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

<span data-ttu-id="004e2-411">[Přetížení AddConsole](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) umožňují předáním minimální úroveň protokolování, funkce filtru a logickou hodnotu, která označuje, zda jsou obory.</span><span class="sxs-lookup"><span data-stu-id="004e2-411">[AddConsole overloads](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) let you pass in a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span> <span data-ttu-id="004e2-412">Další možností je a zajistěte tak předání `IConfiguration` objektu, který můžete určit podporu obory a úrovní protokolování.</span><span class="sxs-lookup"><span data-stu-id="004e2-412">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span>

<span data-ttu-id="004e2-413">Zprostředkovatel konzoly má významný dopad na výkon a není obvykle vhodné pro použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="004e2-413">The console provider has a significant impact on performance and is generally not appropriate for use in production.</span></span>

<span data-ttu-id="004e2-414">Když vytvoříte nový projekt v sadě Visual Studio `AddConsole` metoda vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="004e2-414">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="004e2-415">Tento kód odkazuje `Logging` část *appSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="004e2-415">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

<span data-ttu-id="004e2-416">Nastavení zobrazené protokoly framework limit upozornění zároveň umožní aplikaci k přihlášení na úrovni ladění, jak je vysvětleno v [filtrování protokolu](#log-filtering) oddílu.</span><span class="sxs-lookup"><span data-stu-id="004e2-416">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="004e2-417">Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="004e2-417">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

::: moniker-end

<span data-ttu-id="004e2-418">Protokolování výstupu konzoly najdete ve složce projektu otevřete příkazový řádek a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="004e2-418">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```console
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="004e2-419">Ladění zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="004e2-419">Debug provider</span></span>

<span data-ttu-id="004e2-420">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) balíček zprostředkovatele zapíše výstup protokolu pomocí [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) třídy (`Debug.WriteLine` volání metody).</span><span class="sxs-lookup"><span data-stu-id="004e2-420">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="004e2-421">V systému Linux, tento zprostředkovatel zapisuje protokoly do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="004e2-421">On Linux, this provider writes logs to */var/log/message*.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

<span data-ttu-id="004e2-422">[Přetížení AddDebug](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) umožňují předáním minimální úroveň protokolování nebo funkce filtru.</span><span class="sxs-lookup"><span data-stu-id="004e2-422">[AddDebug overloads](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) let you pass in a minimum log level or a filter function.</span></span>

::: moniker-end

### <a name="eventsource-provider"></a><span data-ttu-id="004e2-423">Zprostředkovatel EventSource</span><span class="sxs-lookup"><span data-stu-id="004e2-423">EventSource provider</span></span>

<span data-ttu-id="004e2-424">Pro aplikace, které cílí ASP.NET Core 1.1.0 nebo novější, [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) balíček zprostředkovatele můžete implementovat trasování událostí.</span><span class="sxs-lookup"><span data-stu-id="004e2-424">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="004e2-425">Na Windows, používá [trasování událostí pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="004e2-425">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="004e2-426">Zprostředkovatel je multiplatformní, ale nejsou žádná událost kolekce a zobrazení nástroje ještě pro Linux nebo macOS.</span><span class="sxs-lookup"><span data-stu-id="004e2-426">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

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

<span data-ttu-id="004e2-427">Je dobrým způsobem, jak shromažďovat a zobrazovat protokoly použít [nástroje PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="004e2-427">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="004e2-428">Existují jiné nástroje pro prohlížení protokolů trasování událostí pro Windows, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows, protože ho vygeneroval technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="004e2-428">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span>

<span data-ttu-id="004e2-429">Ke konfiguraci PerfView pro shromažďování události zapsané podle tohoto zprostředkovatele, přidejte řetězec `*Microsoft-Extensions-Logging` k **dalších poskytovatelů** seznamu.</span><span class="sxs-lookup"><span data-stu-id="004e2-429">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="004e2-430">(Nenechte si ujít hvězdičku na začátku řetězce.)</span><span class="sxs-lookup"><span data-stu-id="004e2-430">(Don't miss the asterisk at the start of the string.)</span></span>

![Další zprostředkovatelé Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="004e2-432">Poskytovatel protokolu událostí Windows</span><span class="sxs-lookup"><span data-stu-id="004e2-432">Windows EventLog provider</span></span>

<span data-ttu-id="004e2-433">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) balíček zprostředkovatele odesílá výstup protokolu do protokolu událostí Windows.</span><span class="sxs-lookup"><span data-stu-id="004e2-433">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

<span data-ttu-id="004e2-434">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předáním `EventLogSettings` nebo minimální úroveň protokolování.</span><span class="sxs-lookup"><span data-stu-id="004e2-434">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

::: moniker-end

### <a name="tracesource-provider"></a><span data-ttu-id="004e2-435">TraceSource poskytovatele</span><span class="sxs-lookup"><span data-stu-id="004e2-435">TraceSource provider</span></span>

<span data-ttu-id="004e2-436">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá balíček zprostředkovatele <xref:System.Diagnostics.TraceSource> knihovny a poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="004e2-436">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

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

<span data-ttu-id="004e2-437">[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předáním přepínač zdroje a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="004e2-437">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="004e2-438">K používání tohoto poskytovatele, musí aplikace spouštět rozhraní .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="004e2-438">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="004e2-439">Zprostředkovatel může směrovat zprávy širokou škálu [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použít v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="004e2-439">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="004e2-440">Následující příklad nastaví `TraceSource` zprostředkovatele, který zaznamenává `Warning` a vyšší zprávu do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="004e2-440">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

## <a name="logging-in-azure"></a><span data-ttu-id="004e2-441">Protokolování v Azure</span><span class="sxs-lookup"><span data-stu-id="004e2-441">Logging in Azure</span></span>

<span data-ttu-id="004e2-442">Informace o protokolování v Azure najdete v následujících částech:</span><span class="sxs-lookup"><span data-stu-id="004e2-442">For information about logging in Azure, see the following sections:</span></span>

* [<span data-ttu-id="004e2-443">Zprostředkovatel služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="004e2-443">Azure App Service provider</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="004e2-444">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="004e2-444">Azure log streaming</span></span>](#azure-log-streaming)

::: moniker range=">= aspnetcore-1.1"

* [<span data-ttu-id="004e2-445">Protokolování trasování programu Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="004e2-445">Azure Application Insights trace logging</span></span>](#azure-application-insights-trace-logging)

::: moniker-end

### <a name="azure-app-service-provider"></a><span data-ttu-id="004e2-446">Zprostředkovatel služby Azure App Service</span><span class="sxs-lookup"><span data-stu-id="004e2-446">Azure App Service provider</span></span>

<span data-ttu-id="004e2-447">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) balíček zprostředkovatele zapisuje protokoly do textových souborů v systému souborů aplikace služby Azure App Service a na [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu služby Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="004e2-447">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="004e2-448">Balíček zprostředkovatele je k dispozici pro aplikace zaměřené na .NET Core 1.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="004e2-448">The provider package is available for apps targeting .NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="004e2-449">Pokud je zaměřen na .NET Core, mějte na paměti následující body:</span><span class="sxs-lookup"><span data-stu-id="004e2-449">If targeting .NET Core, note the following points:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="004e2-450">Balíček poskytovatel je součástí ASP.NET Core [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="004e2-450">The provider package is included in the ASP.NET Core [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="004e2-451">Balíček zprostředkovatele není součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="004e2-451">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="004e2-452">Pokud chcete použít poskytovatele, nainstalujte balíček.</span><span class="sxs-lookup"><span data-stu-id="004e2-452">To use the provider, install the package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="004e2-453">Pokud cílí na rozhraní .NET Framework nebo odkazující `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, přidejte do projektu balíček zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-453">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> <span data-ttu-id="004e2-454">Vyvolání `AddAzureWebAppDiagnostics`:</span><span class="sxs-lookup"><span data-stu-id="004e2-454">Invoke `AddAzureWebAppDiagnostics`:</span></span>

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

<span data-ttu-id="004e2-455"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Přetížení vám umožní předat v <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="004e2-455">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="004e2-456">Objekt nastavení můžete přepsat výchozí nastavení, jako je například protokolování výstupu šablony, názvu objektu blob a limit velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="004e2-456">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="004e2-457">(*Výstupu šablony* je zpráv šablonu, která se použije pro všechny protokoly kromě co je součástí `ILogger` volání metody.)</span><span class="sxs-lookup"><span data-stu-id="004e2-457">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="004e2-458">Chcete-li nakonfigurovat nastavení poskytovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="004e2-458">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

<span data-ttu-id="004e2-459">Když nasadíte aplikaci služby App Service, aplikace respektuje nastavení v [diagnostické protokoly](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) část **služby App Service** stránky na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="004e2-459">When you deploy to an App Service app, the application honors the settings in the [Diagnostic Logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="004e2-460">Když tato nastavení jsou aktualizovány, změny se projeví okamžitě bez nutnosti restartování nebo opětovné nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="004e2-460">When these settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

![Nastavení protokolování Azure](index/_static/azure-logging-settings.png)

<span data-ttu-id="004e2-462">Výchozím umístěním pro soubory protokolů je *D:\\domácí\\LogFiles\\aplikace* složky a výchozí název souboru je *diagnostiky yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="004e2-462">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="004e2-463">Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet souborů, které uchovávají se 2.</span><span class="sxs-lookup"><span data-stu-id="004e2-463">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="004e2-464">Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="004e2-464">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="004e2-465">Zprostředkovatel funguje pouze v případě projektu běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="004e2-465">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="004e2-466">Nemá žádný vliv, pokud projekt je spuštěn místně&mdash;nelze zapsat do místních souborů nebo místním vývojovým úložištěm objektů BLOB.</span><span class="sxs-lookup"><span data-stu-id="004e2-466">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

### <a name="azure-log-streaming"></a><span data-ttu-id="004e2-467">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="004e2-467">Azure log streaming</span></span>

<span data-ttu-id="004e2-468">Streamování protokolů Azure umožňuje zobrazit protokol aktivit v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="004e2-468">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="004e2-469">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="004e2-469">The app server</span></span>
* <span data-ttu-id="004e2-470">Webový server</span><span class="sxs-lookup"><span data-stu-id="004e2-470">The web server</span></span>
* <span data-ttu-id="004e2-471">Trasování neúspěšných žádostí</span><span class="sxs-lookup"><span data-stu-id="004e2-471">Failed request tracing</span></span>

<span data-ttu-id="004e2-472">Postup konfigurace, streamování protokolů Azure:</span><span class="sxs-lookup"><span data-stu-id="004e2-472">To configure Azure log streaming:</span></span>

* <span data-ttu-id="004e2-473">Přejděte **diagnostické protokoly** stránky na stránce portálu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="004e2-473">Navigate to the **Diagnostics Logs** page from your app's portal page.</span></span>
* <span data-ttu-id="004e2-474">Nastavte **protokolování aplikace (systém souborů)** k **na**.</span><span class="sxs-lookup"><span data-stu-id="004e2-474">Set **Application Logging (Filesystem)** to **On**.</span></span>

![Stránka portálu diagnostické protokoly Azure](index/_static/azure-diagnostic-logs.png)

<span data-ttu-id="004e2-476">Přejděte **streamování protokolů** stránku, abyste zobrazili zprávy aplikace.</span><span class="sxs-lookup"><span data-stu-id="004e2-476">Navigate to the **Log Streaming** page to view app messages.</span></span> <span data-ttu-id="004e2-477">Jsou aplikace prostřednictvím přihlášení `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="004e2-477">They're logged by the app through the `ILogger` interface.</span></span>

![Streamování protokolů Azure aplikace na portálu.](index/_static/azure-log-streaming.png)

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="004e2-479">Protokolování trasování programu Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="004e2-479">Azure Application Insights trace logging</span></span>

<span data-ttu-id="004e2-480">Sada SDK služby Application Insights můžete shromažďovat a sestavy protokoly generované protokolování infrastruktury ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="004e2-480">The Application Insights SDK can collect and report logs generated by the ASP.NET Core logging infrastructure.</span></span> <span data-ttu-id="004e2-481">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="004e2-481">For more information, see the following resources:</span></span>

* [<span data-ttu-id="004e2-482">Application Insights overview</span><span class="sxs-lookup"><span data-stu-id="004e2-482">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* [<span data-ttu-id="004e2-483">Application Insights pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="004e2-483">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* <span data-ttu-id="004e2-484">[Protokolování adaptéry Application Insights](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span><span class="sxs-lookup"><span data-stu-id="004e2-484">[Application Insights logging adapters](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span></span>
* [<span data-ttu-id="004e2-485">Application Insights ILogger implementace ukázky</span><span class="sxs-lookup"><span data-stu-id="004e2-485">Application Insights ILogger implementation samples</span></span>](/azure/azure-monitor/app/ilogger)

::: moniker-end

## <a name="third-party-logging-providers"></a><span data-ttu-id="004e2-486">Zprostředkovatele přihlášení třetí strany</span><span class="sxs-lookup"><span data-stu-id="004e2-486">Third-party logging providers</span></span>

<span data-ttu-id="004e2-487">Rozhraní protokolování třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="004e2-487">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="004e2-488">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="004e2-488">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="004e2-489">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub se vzorovými](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="004e2-489">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="004e2-490">[JSNLog](http://jsnlog.com/) ([úložiště GitHub se vzorovými](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="004e2-490">[JSNLog](http://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="004e2-491">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="004e2-491">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="004e2-492">[Loggr](http://loggr.net/) ([úložiště GitHub se vzorovými](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="004e2-492">[Loggr](http://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="004e2-493">[NLog](http://nlog-project.org/) ([úložiště GitHub se vzorovými](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="004e2-493">[NLog](http://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="004e2-494">[SENTRY](https://sentry.io/welcome/) ([úložiště GitHub se vzorovými](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="004e2-494">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="004e2-495">[Serilog](https://serilog.net/) ([úložiště GitHub se vzorovými](https://github.com/serilog/serilog-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="004e2-495">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-extensions-logging))</span></span>
* <span data-ttu-id="004e2-496">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště Github se vzorovými](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="004e2-496">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="004e2-497">Můžete provádět některé rozhraní třetích stran [sémantického protokolování, označovaného také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="004e2-497">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="004e2-498">Použití rozhraní třetích stran je podobný pomocí jedné z předdefinovaných poskytovatelů:</span><span class="sxs-lookup"><span data-stu-id="004e2-498">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="004e2-499">Přidání balíčku NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="004e2-499">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="004e2-500">Volání `ILoggerFactory`.</span><span class="sxs-lookup"><span data-stu-id="004e2-500">Call an `ILoggerFactory`.</span></span>

<span data-ttu-id="004e2-501">Další informace najdete v dokumentaci každého zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="004e2-501">For more information, see each provider's documentation.</span></span> <span data-ttu-id="004e2-502">Zprostředkovatelů přihlášení třetích stran nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="004e2-502">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="004e2-503">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="004e2-503">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

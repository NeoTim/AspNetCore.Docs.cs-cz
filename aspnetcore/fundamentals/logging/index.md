---
title: Přihlašování ASP.NET Core
author: tdykstra
description: Přečtěte si o protokolovacím rozhraní v ASP.NET Core. Seznamte se s vestavěnými zprostředkovateli protokolování a seznamte se s oblíbenými poskytovateli třetích stran.
ms.author: tdykstra
ms.custom: mvc
ms.date: 07/11/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 4fe677e69478284db2ccab655c35b5744b6f63f9
ms.sourcegitcommit: 059ab380744fa3be3b69aa90d431b563c57092cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68410911"
---
# <a name="logging-in-aspnet-core"></a><span data-ttu-id="88a34-104">Přihlašování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="88a34-104">Logging in ASP.NET Core</span></span>

<span data-ttu-id="88a34-105">[Steve Smith](https://ardalis.com/) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="88a34-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="88a34-106">ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran.</span><span class="sxs-lookup"><span data-stu-id="88a34-106">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="88a34-107">V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="88a34-107">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="88a34-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88a34-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="88a34-109">Přidat zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="88a34-109">Add providers</span></span>

<span data-ttu-id="88a34-110">Zprostředkovatel protokolování zobrazuje nebo ukládá protokoly.</span><span class="sxs-lookup"><span data-stu-id="88a34-110">A logging provider displays or stores logs.</span></span> <span data-ttu-id="88a34-111">Například poskytovatel konzoly zobrazuje protokoly v konzole nástroje a poskytovatel Azure Application Insights je ukládá do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="88a34-111">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="88a34-112">Protokoly lze odesílat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="88a34-112">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="88a34-113">Chcete-li přidat poskytovatele, zavolejte metodu `Add{provider name}` rozšíření poskytovatele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="88a34-113">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="88a34-114">Předchozí kód vyžaduje odkazy na `Microsoft.Extensions.Logging` a. `Microsoft.Extensions.Configuration`</span><span class="sxs-lookup"><span data-stu-id="88a34-114">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="88a34-115">Výchozí volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>šablony projektu, které přidává následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="88a34-115">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="88a34-116">Konzola</span><span class="sxs-lookup"><span data-stu-id="88a34-116">Console</span></span>
* <span data-ttu-id="88a34-117">Ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-117">Debug</span></span>
* <span data-ttu-id="88a34-118">EventSource (počínaje ASP.NET Core 2,2)</span><span class="sxs-lookup"><span data-stu-id="88a34-118">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="88a34-119">Pokud používáte `CreateDefaultBuilder`, můžete výchozí poskytovatele nahradit vašimi vlastními možnostmi.</span><span class="sxs-lookup"><span data-stu-id="88a34-119">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="88a34-120">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte poskytovatele, které chcete.</span><span class="sxs-lookup"><span data-stu-id="88a34-120">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="88a34-121">Chcete-li použít poskytovatele, nainstalujte balíček NuGet a zavolejte metodu rozšíření poskytovatele na instanci <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span><span class="sxs-lookup"><span data-stu-id="88a34-121">To use a provider, install its NuGet package and call the provider's extension method on an instance of <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="88a34-122">Tato`ILoggerFactory` instance poskytuje [vkládání závislostí ASP.NET Core (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="88a34-122">ASP.NET Core [dependency injection (DI)](xref:fundamentals/dependency-injection) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="88a34-123">Metody rozšíření `AddDebug`ajsou definovány v balíčcích [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) a [Microsoft. Extensions. Logging. Debug.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) `AddConsole`</span><span class="sxs-lookup"><span data-stu-id="88a34-123">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="88a34-124">Každá metoda rozšíření volá `ILoggerFactory.AddProvider` metodu, která předává instanci poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-124">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span>

> [!NOTE]
> <span data-ttu-id="88a34-125">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) Přidá zprostředkovatele protokolování do `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="88a34-125">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adds logging providers in the `Startup.Configure` method.</span></span> <span data-ttu-id="88a34-126">Chcete-li získat výstup protokolu z kódu, který byl proveden dříve, přidejte `Startup` zprostředkovatele protokolování do konstruktoru třídy.</span><span class="sxs-lookup"><span data-stu-id="88a34-126">To obtain log output from code that executes earlier, add logging providers in the `Startup` class constructor.</span></span>

::: moniker-end

<span data-ttu-id="88a34-127">Přečtěte si další informace o [integrovaných poskytovatelích protokolování](#built-in-logging-providers) a [zprostředkovatelích protokolování třetích stran](#third-party-logging-providers) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="88a34-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="88a34-128">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="88a34-128">Create logs</span></span>

<span data-ttu-id="88a34-129"><xref:Microsoft.Extensions.Logging.ILogger%601> Získá objekt z di.</span><span class="sxs-lookup"><span data-stu-id="88a34-129">Get an <xref:Microsoft.Extensions.Logging.ILogger%601> object from DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="88a34-130">Následující příklad řadiče vytvoří `Information` a `Warning` zaprotokoluje.</span><span class="sxs-lookup"><span data-stu-id="88a34-130">The following controller example creates `Information` and `Warning` logs.</span></span> <span data-ttu-id="88a34-131">*Kategorie* je `TodoApiSample.Controllers.TodoController` (plně `TodoController` kvalifikovaný název třídy v ukázkové aplikaci):</span><span class="sxs-lookup"><span data-stu-id="88a34-131">The *category* is `TodoApiSample.Controllers.TodoController` (the fully qualified class name of `TodoController` in the sample app):</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="88a34-132">Následující Razor Pages příklad vytvoří protokoly `Information` s jako *úroveň* a `TodoApiSample.Pages.AboutModel` jako *kategorii*:</span><span class="sxs-lookup"><span data-stu-id="88a34-132">The following Razor Pages example creates logs with `Information` as the *level* and `TodoApiSample.Pages.AboutModel` as the *category*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="88a34-133">Předchozí příklad vytvoří `Information` v *kategorii*a `Warning` jako *úroveň* a `TodoController` třídu protokoly.</span><span class="sxs-lookup"><span data-stu-id="88a34-133">The preceding example creates logs with `Information` and `Warning` as the *level* and `TodoController` class as the *category*.</span></span> 

::: moniker-end

<span data-ttu-id="88a34-134">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="88a34-134">The Log *level* indicates the severity of the logged event.</span></span> <span data-ttu-id="88a34-135">*Kategorie* protokolu je řetězec, který je spojený s každým protokolem.</span><span class="sxs-lookup"><span data-stu-id="88a34-135">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="88a34-136">Instance vytvoří protokoly, které mají plně kvalifikovaný název typu `T` jako kategorie. `ILogger<T>`</span><span class="sxs-lookup"><span data-stu-id="88a34-136">The `ILogger<T>` instance creates logs that have the fully qualified name of type `T` as the category.</span></span> <span data-ttu-id="88a34-137">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="88a34-137">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="88a34-138">Vytvořit protokoly při spuštění</span><span class="sxs-lookup"><span data-stu-id="88a34-138">Create logs in Startup</span></span>

<span data-ttu-id="88a34-139">Chcete-li zapsat protokoly `Startup` ve třídě, `ILogger` zahrňte parametr do signatury konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="88a34-139">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a><span data-ttu-id="88a34-140">Vytvořit protokoly v programu</span><span class="sxs-lookup"><span data-stu-id="88a34-140">Create logs in Program</span></span>

<span data-ttu-id="88a34-141">Chcete-li zapsat protokoly `Program` ve třídě, Získejte `ILogger` instanci od typu di:</span><span class="sxs-lookup"><span data-stu-id="88a34-141">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="88a34-142">Žádné metody asynchronního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="88a34-142">No asynchronous logger methods</span></span>

<span data-ttu-id="88a34-143">Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="88a34-143">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="88a34-144">Pokud je úložiště dat protokolování pomalé, nezapište ho přímo.</span><span class="sxs-lookup"><span data-stu-id="88a34-144">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="88a34-145">Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je později přesunout do pomalého úložiště.</span><span class="sxs-lookup"><span data-stu-id="88a34-145">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="88a34-146">Například pokud se přihlašujete k SQL Server, nechcete provádět přímo v `Log` metodě, `Log` protože metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="88a34-146">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="88a34-147">Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="88a34-147">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="88a34-148">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="88a34-148">Configuration</span></span>

<span data-ttu-id="88a34-149">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="88a34-149">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="88a34-150">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="88a34-150">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="88a34-151">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="88a34-151">Command-line arguments.</span></span>
* <span data-ttu-id="88a34-152">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="88a34-152">Environment variables.</span></span>
* <span data-ttu-id="88a34-153">Objekty rozhraní .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="88a34-153">In-memory .NET objects.</span></span>
* <span data-ttu-id="88a34-154">Úložiště nešifrovaného [tajného správce](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="88a34-154">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="88a34-155">Šifrované uživatelské úložiště, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="88a34-155">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="88a34-156">Vlastní poskytovatelé (nainstalováno nebo vytvořeno).</span><span class="sxs-lookup"><span data-stu-id="88a34-156">Custom providers (installed or created).</span></span>

<span data-ttu-id="88a34-157">Například konfigurace protokolování je běžně poskytována v `Logging` části soubory nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="88a34-157">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="88a34-158">Následující příklad ukazuje obsah typického *appSettings. Soubor Development. JSON* :</span><span class="sxs-lookup"><span data-stu-id="88a34-158">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="88a34-159">Vlastnost může mít `LogLevel` vlastnosti zprostředkovatele a log (zobrazí se konzola). `Logging`</span><span class="sxs-lookup"><span data-stu-id="88a34-159">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="88a34-160">Vlastnost v rámci `Logging` určuje minimální úroveň protokolu pro vybrané kategorie. [](#log-level) `LogLevel`</span><span class="sxs-lookup"><span data-stu-id="88a34-160">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="88a34-161">V tomto příkladu se `System` `Microsoft` kategorie přihlásí na `Information` úrovni a všechny ostatní protokoly na `Debug` úrovni.</span><span class="sxs-lookup"><span data-stu-id="88a34-161">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="88a34-162">Další vlastnosti v `Logging` části zadat zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="88a34-162">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="88a34-163">Příklad je pro poskytovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="88a34-163">The example is for the Console provider.</span></span> <span data-ttu-id="88a34-164">Pokud zprostředkovatel podporuje [obory protokolů](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="88a34-164">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="88a34-165">Vlastnost poskytovatele (například `Console` v příkladu) může také `LogLevel` určovat vlastnost.</span><span class="sxs-lookup"><span data-stu-id="88a34-165">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="88a34-166">`LogLevel`v rámci zprostředkovatele Určuje úrovně, které se mají protokolovat pro daného poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-166">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="88a34-167">Pokud jsou úrovně specifikovány `Logging.{providername}.LogLevel`v, přepíše cokoli nastavené `Logging.LogLevel`v.</span><span class="sxs-lookup"><span data-stu-id="88a34-167">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

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

<span data-ttu-id="88a34-168">`LogLevel`klíče udávají názvy protokolů.</span><span class="sxs-lookup"><span data-stu-id="88a34-168">`LogLevel` keys represent log names.</span></span> <span data-ttu-id="88a34-169">`Default` Klíč se vztahuje na protokoly, které nejsou výslovně uvedeny.</span><span class="sxs-lookup"><span data-stu-id="88a34-169">The `Default` key applies to logs not explicitly listed.</span></span> <span data-ttu-id="88a34-170">Hodnota představuje [úroveň protokolu](#log-level) použitou pro daný protokol.</span><span class="sxs-lookup"><span data-stu-id="88a34-170">The value represents the [log level](#log-level) applied to the given log.</span></span>

::: moniker-end

<span data-ttu-id="88a34-171">Informace o implementaci zprostředkovatelů konfigurace najdete v <xref:fundamentals/configuration/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="88a34-171">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="88a34-172">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="88a34-172">Sample logging output</span></span>

<span data-ttu-id="88a34-173">Pomocí ukázkového kódu zobrazeného v předchozí části se protokoly zobrazí v konzole nástroje při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="88a34-173">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="88a34-174">Tady je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="88a34-174">Here's an example of console output:</span></span>

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

<span data-ttu-id="88a34-175">Předchozí protokoly byly vygenerovány vytvořením žádosti HTTP GET do ukázkové aplikace na adrese `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="88a34-175">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="88a34-176">Tady je příklad stejného protokolu, který se zobrazí v okně ladění při spuštění ukázkové aplikace v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="88a34-176">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="88a34-177">Protokoly, které jsou vytvořené `ILogger` voláními, které jsou uvedené v předchozí části, začínají na "TodoApi. Controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="88a34-177">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="88a34-178">Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="88a34-178">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="88a34-179">ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="88a34-179">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="88a34-180">Zbývající část tohoto článku vysvětluje několik podrobností a možností protokolování.</span><span class="sxs-lookup"><span data-stu-id="88a34-180">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="88a34-181">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="88a34-181">NuGet packages</span></span>

<span data-ttu-id="88a34-182">Rozhraní `ILogger` a`ILoggerFactory` jsou v [Microsoft. Extensions. Loggings. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="88a34-182">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="88a34-183">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="88a34-183">Log category</span></span>

<span data-ttu-id="88a34-184">Při vytvoření objektu je pro něj zadána *kategorie.* `ILogger`</span><span class="sxs-lookup"><span data-stu-id="88a34-184">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="88a34-185">Tato kategorie je součástí každé zprávy protokolu vytvořené pomocí této instance `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="88a34-185">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="88a34-186">Kategorie může být libovolný řetězec, ale konvence používá název třídy, jako je například "TodoApi. Controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="88a34-186">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="88a34-187">Použijte `ILogger<T>` k `T` získání instance, která používá plně kvalifikovaný název typu jako kategorii: `ILogger`</span><span class="sxs-lookup"><span data-stu-id="88a34-187">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="88a34-188">Chcete-li explicitně zadat kategorii, `ILoggerFactory.CreateLogger`zavolejte:</span><span class="sxs-lookup"><span data-stu-id="88a34-188">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="88a34-189">`ILogger<T>`je ekvivalentní volání `CreateLogger` s plně kvalifikovaným `T`názvem typu.</span><span class="sxs-lookup"><span data-stu-id="88a34-189">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="88a34-190">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="88a34-190">Log level</span></span>

<span data-ttu-id="88a34-191">Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="88a34-191">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="88a34-192">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="88a34-192">The log level indicates the severity or importance.</span></span> <span data-ttu-id="88a34-193">Můžete například zapsat `Information` protokol, pokud metoda končí normálně `Warning` a protokol, když metoda vrátí stavový kód 404 Nenalezeno.</span><span class="sxs-lookup"><span data-stu-id="88a34-193">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="88a34-194">Následující kód vytváří `Information` a `Warning` protokoluje:</span><span class="sxs-lookup"><span data-stu-id="88a34-194">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="88a34-195">V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="88a34-195">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="88a34-196">Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="88a34-196">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="88a34-197">Parametry metody jsou vysvětleny v [části Šablona zprávy](#log-message-template) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="88a34-197">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="88a34-198">Metody protokolu, které zahrnují úroveň v názvu metody ( `LogInformation` například a `LogWarning`), jsou [metody rozšíření pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="88a34-198">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="88a34-199">Tyto metody volají `Log` metodu, která `LogLevel` přebírá parametr.</span><span class="sxs-lookup"><span data-stu-id="88a34-199">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="88a34-200">`Log` Metodu můžete zavolat přímo místo jedné z těchto rozšiřujících metod, ale syntaxe je poměrně složitá.</span><span class="sxs-lookup"><span data-stu-id="88a34-200">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="88a34-201">Další informace naleznete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a ve [zdrojovém kódu rozšíření protokolovacího](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)nástroje.</span><span class="sxs-lookup"><span data-stu-id="88a34-201">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="88a34-202">ASP.NET Core definuje následující úrovně protokolu seřazené od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="88a34-202">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="88a34-203">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="88a34-203">Trace = 0</span></span>

  <span data-ttu-id="88a34-204">Informace, které jsou obvykle užitečné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="88a34-204">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="88a34-205">Tyto zprávy mohou obsahovat citlivá data aplikací, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="88a34-205">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="88a34-206">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="88a34-206">*Disabled by default.*</span></span>

* <span data-ttu-id="88a34-207">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="88a34-207">Debug = 1</span></span>

  <span data-ttu-id="88a34-208">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="88a34-208">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="88a34-209">Příklad: `Entering method Configure with flag set to true.`Povolte `Debug` protokoly úrovně v produkčním prostředí jenom při řešení potíží, protože se jedná o velký objem protokolů.</span><span class="sxs-lookup"><span data-stu-id="88a34-209">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="88a34-210">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="88a34-210">Information = 2</span></span>

  <span data-ttu-id="88a34-211">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="88a34-211">For tracking the general flow of the app.</span></span> <span data-ttu-id="88a34-212">Tyto protokoly obvykle mají nějakou dlouhodobou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="88a34-212">These logs typically have some long-term value.</span></span> <span data-ttu-id="88a34-213">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="88a34-213">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="88a34-214">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="88a34-214">Warning = 3</span></span>

  <span data-ttu-id="88a34-215">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="88a34-215">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="88a34-216">Ty můžou zahrnovat chyby nebo jiné podmínky, které nezpůsobí zastavení aplikace, ale možná bude nutné je prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="88a34-216">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="88a34-217">Zpracování výjimek je běžné místo pro použití `Warning` úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="88a34-217">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="88a34-218">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="88a34-218">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="88a34-219">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="88a34-219">Error = 4</span></span>

  <span data-ttu-id="88a34-220">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="88a34-220">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="88a34-221">Tyto zprávy indikují selhání aktuální aktivity nebo operace (jako je aktuální požadavek HTTP), ne při selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="88a34-221">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="88a34-222">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="88a34-222">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="88a34-223">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="88a34-223">Critical = 5</span></span>

  <span data-ttu-id="88a34-224">Chyby, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="88a34-224">For failures that require immediate attention.</span></span> <span data-ttu-id="88a34-225">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="88a34-225">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="88a34-226">Úroveň protokolu můžete použít k určení, kolik výstupu protokolu se zapisuje do konkrétního úložného média nebo okna pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="88a34-226">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="88a34-227">Příklad:</span><span class="sxs-lookup"><span data-stu-id="88a34-227">For example:</span></span>

* <span data-ttu-id="88a34-228">V produkčním prostředí `Trace` můžete `Information` odesílat prostřednictvím úrovně úložiště dat svazku.</span><span class="sxs-lookup"><span data-stu-id="88a34-228">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="88a34-229">Odešlete `Warning`doúložištědats hodnotou.`Critical`</span><span class="sxs-lookup"><span data-stu-id="88a34-229">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="88a34-230">Během vývoje proveďte odeslání `Warning` `Critical` `Trace` do`Information` konzoly nástroje a při řešení potíží přidejte.</span><span class="sxs-lookup"><span data-stu-id="88a34-230">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="88a34-231">Část [filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovateli zpracovává.</span><span class="sxs-lookup"><span data-stu-id="88a34-231">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="88a34-232">ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="88a34-232">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="88a34-233">Příklady protokolů výše v tomto článku vyloučily nižší `Information` úrovně protokolů, takže se nevytvořily žádné `Debug` protokoly ani `Trace` protokoly na úrovni.</span><span class="sxs-lookup"><span data-stu-id="88a34-233">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="88a34-234">Tady je příklad protokolů konzoly vytvořených spuštěním ukázkové aplikace nakonfigurované pro zobrazení `Debug` protokolů:</span><span class="sxs-lookup"><span data-stu-id="88a34-234">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="88a34-235">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="88a34-235">Log event ID</span></span>

<span data-ttu-id="88a34-236">Každý protokol může určovat *ID události*.</span><span class="sxs-lookup"><span data-stu-id="88a34-236">Each log can specify an *event ID*.</span></span> <span data-ttu-id="88a34-237">Tato ukázková aplikace používá lokálně definovanou `LoggingEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="88a34-237">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="88a34-238">ID události přidružuje sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="88a34-238">An event ID associates a set of events.</span></span> <span data-ttu-id="88a34-239">Například všechny protokoly související se zobrazením seznamu položek na stránce může být 1001.</span><span class="sxs-lookup"><span data-stu-id="88a34-239">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="88a34-240">Zprostředkovatel protokolování může ukládat ID události v poli ID, ve zprávě protokolování nebo vůbec ne.</span><span class="sxs-lookup"><span data-stu-id="88a34-240">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="88a34-241">Zprostředkovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="88a34-241">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="88a34-242">Poskytovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="88a34-242">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="88a34-243">Šablona zprávy protokolu</span><span class="sxs-lookup"><span data-stu-id="88a34-243">Log message template</span></span>

<span data-ttu-id="88a34-244">Každý protokol Určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="88a34-244">Each log specifies a message template.</span></span> <span data-ttu-id="88a34-245">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="88a34-245">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="88a34-246">Použijte názvy zástupných symbolů, nikoli čísla.</span><span class="sxs-lookup"><span data-stu-id="88a34-246">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="88a34-247">Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry slouží k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="88a34-247">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="88a34-248">V následujícím kódu si všimněte, že názvy parametrů jsou v šabloně zprávy mimo pořadí:</span><span class="sxs-lookup"><span data-stu-id="88a34-248">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="88a34-249">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v posloupnosti:</span><span class="sxs-lookup"><span data-stu-id="88a34-249">This code creates a log message with the parameter values in sequence:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="88a34-250">Protokolovací rozhraní funguje tímto způsobem, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="88a34-250">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="88a34-251">Samotné argumenty jsou předány do protokolovacího systému, nikoli pouze ze šablony formátované zprávy.</span><span class="sxs-lookup"><span data-stu-id="88a34-251">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="88a34-252">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="88a34-252">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="88a34-253">Předpokládejme například, že volání metody protokolovacího nástroje vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="88a34-253">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="88a34-254">Pokud odesíláte protokoly do služby Azure Table Storage, Každá entita tabulky Azure může mít `ID` vlastnosti a `RequestTime` , což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="88a34-254">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="88a34-255">Dotaz může najít všechny protokoly v rámci určitého `RequestTime` rozsahu, aniž by došlo k analýze časového limitu textové zprávy.</span><span class="sxs-lookup"><span data-stu-id="88a34-255">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="88a34-256">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="88a34-256">Logging exceptions</span></span>

<span data-ttu-id="88a34-257">Metody protokolovacího nástroje mají přetížení, které umožňují předat výjimku, jak je uvedeno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="88a34-257">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="88a34-258">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="88a34-258">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="88a34-259">Tady je příklad výstupu poskytovatele ladění z kódu uvedeného výše.</span><span class="sxs-lookup"><span data-stu-id="88a34-259">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="88a34-260">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="88a34-260">Log filtering</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="88a34-261">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny poskytovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="88a34-261">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="88a34-262">Všechny protokoly nižší než minimální úroveň nebudou tomuto poskytovateli předány, takže se nebudou zobrazovat ani ukládat.</span><span class="sxs-lookup"><span data-stu-id="88a34-262">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="88a34-263">Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="88a34-263">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="88a34-264">Celočíselná hodnota `LogLevel.None` je 6, což je více než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="88a34-264">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="88a34-265">Vytvořit pravidla filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="88a34-265">Create filter rules in configuration</span></span>

<span data-ttu-id="88a34-266">Kód šablony projektu volá `CreateDefaultBuilder` k nastavení protokolování pro konzolu a zprostředkovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="88a34-266">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="88a34-267">Metoda také nastaví protokolování pro hledání konfigurace `Logging` v oddílu pomocí kódu podobného následujícímu: `CreateDefaultBuilder`</span><span class="sxs-lookup"><span data-stu-id="88a34-267">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17)]

<span data-ttu-id="88a34-268">Konfigurační data určují minimální úrovně protokolu podle poskytovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="88a34-268">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="88a34-269">Tento kód JSON vytvoří šest pravidel filtru: jeden pro poskytovatele ladění, čtyři pro poskytovatele konzoly a jeden pro všechny poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-269">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="88a34-270">Při `ILogger` vytvoření objektu se vybere jedno pravidlo pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-270">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="88a34-271">Filtrovat pravidla v kódu</span><span class="sxs-lookup"><span data-stu-id="88a34-271">Filter rules in code</span></span>

<span data-ttu-id="88a34-272">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="88a34-272">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="88a34-273">Druhý `AddFilter` Určuje poskytovatele ladění pomocí jeho názvu typu.</span><span class="sxs-lookup"><span data-stu-id="88a34-273">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="88a34-274">První `AddFilter` se vztahuje na všechny poskytovatele, protože neurčuje typ poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-274">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="88a34-275">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="88a34-275">How filtering rules are applied</span></span>

<span data-ttu-id="88a34-276">Konfigurační data a `AddFilter` kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="88a34-276">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="88a34-277">Prvních šest přicházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="88a34-277">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="88a34-278">Číslo</span><span class="sxs-lookup"><span data-stu-id="88a34-278">Number</span></span> | <span data-ttu-id="88a34-279">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="88a34-279">Provider</span></span>      | <span data-ttu-id="88a34-280">Kategorie, které začínají na...</span><span class="sxs-lookup"><span data-stu-id="88a34-280">Categories that begin with ...</span></span>          | <span data-ttu-id="88a34-281">Minimální úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="88a34-281">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="88a34-282">1</span><span class="sxs-lookup"><span data-stu-id="88a34-282">1</span></span>      | <span data-ttu-id="88a34-283">Ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-283">Debug</span></span>         | <span data-ttu-id="88a34-284">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="88a34-284">All categories</span></span>                          | <span data-ttu-id="88a34-285">Informace o</span><span class="sxs-lookup"><span data-stu-id="88a34-285">Information</span></span>       |
| <span data-ttu-id="88a34-286">2</span><span class="sxs-lookup"><span data-stu-id="88a34-286">2</span></span>      | <span data-ttu-id="88a34-287">Konzola</span><span class="sxs-lookup"><span data-stu-id="88a34-287">Console</span></span>       | <span data-ttu-id="88a34-288">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="88a34-288">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="88a34-289">Upozornění</span><span class="sxs-lookup"><span data-stu-id="88a34-289">Warning</span></span>           |
| <span data-ttu-id="88a34-290">3</span><span class="sxs-lookup"><span data-stu-id="88a34-290">3</span></span>      | <span data-ttu-id="88a34-291">Konzola</span><span class="sxs-lookup"><span data-stu-id="88a34-291">Console</span></span>       | <span data-ttu-id="88a34-292">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="88a34-292">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="88a34-293">Ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-293">Debug</span></span>             |
| <span data-ttu-id="88a34-294">4</span><span class="sxs-lookup"><span data-stu-id="88a34-294">4</span></span>      | <span data-ttu-id="88a34-295">Konzola</span><span class="sxs-lookup"><span data-stu-id="88a34-295">Console</span></span>       | <span data-ttu-id="88a34-296">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="88a34-296">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="88a34-297">Chyba</span><span class="sxs-lookup"><span data-stu-id="88a34-297">Error</span></span>             |
| <span data-ttu-id="88a34-298">5</span><span class="sxs-lookup"><span data-stu-id="88a34-298">5</span></span>      | <span data-ttu-id="88a34-299">Konzola</span><span class="sxs-lookup"><span data-stu-id="88a34-299">Console</span></span>       | <span data-ttu-id="88a34-300">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="88a34-300">All categories</span></span>                          | <span data-ttu-id="88a34-301">Informace o</span><span class="sxs-lookup"><span data-stu-id="88a34-301">Information</span></span>       |
| <span data-ttu-id="88a34-302">6</span><span class="sxs-lookup"><span data-stu-id="88a34-302">6</span></span>      | <span data-ttu-id="88a34-303">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="88a34-303">All providers</span></span> | <span data-ttu-id="88a34-304">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="88a34-304">All categories</span></span>                          | <span data-ttu-id="88a34-305">Ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-305">Debug</span></span>             |
| <span data-ttu-id="88a34-306">7</span><span class="sxs-lookup"><span data-stu-id="88a34-306">7</span></span>      | <span data-ttu-id="88a34-307">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="88a34-307">All providers</span></span> | <span data-ttu-id="88a34-308">Systém</span><span class="sxs-lookup"><span data-stu-id="88a34-308">System</span></span>                                  | <span data-ttu-id="88a34-309">Ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-309">Debug</span></span>             |
| <span data-ttu-id="88a34-310">8</span><span class="sxs-lookup"><span data-stu-id="88a34-310">8</span></span>      | <span data-ttu-id="88a34-311">Ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-311">Debug</span></span>         | <span data-ttu-id="88a34-312">Microsoft</span><span class="sxs-lookup"><span data-stu-id="88a34-312">Microsoft</span></span>                               | <span data-ttu-id="88a34-313">Přehled</span><span class="sxs-lookup"><span data-stu-id="88a34-313">Trace</span></span>             |

<span data-ttu-id="88a34-314">`ILogger` Při vytvoření`ILoggerFactory` objektu vybere objekt jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="88a34-314">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="88a34-315">Všechny zprávy napsané `ILogger` instancí jsou filtrovány podle vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="88a34-315">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="88a34-316">V dostupných pravidlech se vybere nejpřesnější pravidlo pro jednotlivé dvojice zprostředkovatel a kategorie.</span><span class="sxs-lookup"><span data-stu-id="88a34-316">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="88a34-317">Následující algoritmus se používá pro každého poskytovatele při `ILogger` vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="88a34-317">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="88a34-318">Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem.</span><span class="sxs-lookup"><span data-stu-id="88a34-318">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="88a34-319">Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="88a34-319">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="88a34-320">Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie.</span><span class="sxs-lookup"><span data-stu-id="88a34-320">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="88a34-321">Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="88a34-321">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="88a34-322">Pokud je vybráno více pravidel, vezměte **Poslední** z nich.</span><span class="sxs-lookup"><span data-stu-id="88a34-322">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="88a34-323">Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="88a34-323">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="88a34-324">V předchozím seznamu pravidel Předpokládejme, že vytvoříte `ILogger` objekt pro kategorii "Microsoft. AspNetCore. Mvc. Razor. RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="88a34-324">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="88a34-325">Pro poskytovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="88a34-325">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="88a34-326">Pravidlo 8 je nejvíce specifické, takže je to ten vybraný.</span><span class="sxs-lookup"><span data-stu-id="88a34-326">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="88a34-327">Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="88a34-327">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="88a34-328">Pravidlo 3 je nejvíce specifické.</span><span class="sxs-lookup"><span data-stu-id="88a34-328">Rule 3 is most specific.</span></span>

<span data-ttu-id="88a34-329">Výsledná `ILogger` instance odesílá `Trace` protokoly úrovně a výše do poskytovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="88a34-329">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="88a34-330">`Debug` Protokoly úrovně a výše jsou odesílány poskytovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="88a34-330">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="88a34-331">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="88a34-331">Provider aliases</span></span>

<span data-ttu-id="88a34-332">Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu.</span><span class="sxs-lookup"><span data-stu-id="88a34-332">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="88a34-333">Pro předdefinované poskytovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="88a34-333">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="88a34-334">Konzola</span><span class="sxs-lookup"><span data-stu-id="88a34-334">Console</span></span>
* <span data-ttu-id="88a34-335">Ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-335">Debug</span></span>
* <span data-ttu-id="88a34-336">EventSource</span><span class="sxs-lookup"><span data-stu-id="88a34-336">EventSource</span></span>
* <span data-ttu-id="88a34-337">EventLog</span><span class="sxs-lookup"><span data-stu-id="88a34-337">EventLog</span></span>
* <span data-ttu-id="88a34-338">TraceSource</span><span class="sxs-lookup"><span data-stu-id="88a34-338">TraceSource</span></span>
* <span data-ttu-id="88a34-339">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="88a34-339">AzureAppServicesFile</span></span>
* <span data-ttu-id="88a34-340">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="88a34-340">AzureAppServicesBlob</span></span>
* <span data-ttu-id="88a34-341">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="88a34-341">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="88a34-342">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="88a34-342">Default minimum level</span></span>

<span data-ttu-id="88a34-343">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že se pro daného zprostředkovatele a kategorii nepoužijí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="88a34-343">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="88a34-344">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="88a34-344">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="88a34-345">Pokud nenastavíte explicitně minimální úroveň, výchozí hodnota je `Information`, což znamená, že `Trace` protokoly a `Debug` budou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="88a34-345">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="88a34-346">Funkce filtru</span><span class="sxs-lookup"><span data-stu-id="88a34-346">Filter functions</span></span>

<span data-ttu-id="88a34-347">Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="88a34-347">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="88a34-348">Kód ve funkci má přístup k typu poskytovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="88a34-348">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="88a34-349">Příklad:</span><span class="sxs-lookup"><span data-stu-id="88a34-349">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="88a34-350">Někteří zprostředkovatelé protokolování umožňují určit, kdy se mají protokoly zapsat na paměťové médium nebo ignorovat na základě úrovně a kategorie protokolování.</span><span class="sxs-lookup"><span data-stu-id="88a34-350">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="88a34-351">Metody rozšíření `AddDebug` a poskytují přetížení, která přijímají kritéria filtrování. `AddConsole`</span><span class="sxs-lookup"><span data-stu-id="88a34-351">The `AddConsole` and `AddDebug` extension methods provide overloads that accept filtering criteria.</span></span> <span data-ttu-id="88a34-352">Následující vzorový kód způsobí, že poskytovatel konzoly ignoruje protokoly pod `Warning` úrovní, zatímco poskytovatel ladění ignoruje protokoly, které rozhraní vytvoří.</span><span class="sxs-lookup"><span data-stu-id="88a34-352">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="88a34-353">Metoda má přetížení, které `EventLogSettings` přebírá instanci, která může obsahovat funkci filtrování ve své `Filter` vlastnosti. `AddEventLog`</span><span class="sxs-lookup"><span data-stu-id="88a34-353">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="88a34-354">Poskytovatel TraceSource neposkytuje žádné z těchto přetížení, protože jeho úroveň protokolování a další parametry jsou založené na rozhraní `SourceSwitch` a `TraceListener` , které používá.</span><span class="sxs-lookup"><span data-stu-id="88a34-354">The TraceSource provider doesn't provide any of those overloads, since its logging level and other parameters are based on the `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="88a34-355">Chcete-li nastavit pravidla filtrování pro všechny poskytovatele, kteří jsou `ILoggerFactory` registrováni s instancí `WithFilter` , použijte metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="88a34-355">To set filtering rules for all providers that are registered with an `ILoggerFactory` instance, use the `WithFilter` extension method.</span></span> <span data-ttu-id="88a34-356">Níže uvedený příklad omezuje protokoly rozhraní (kategorie začíná na "Microsoft" nebo "System") na upozornění při protokolování na úrovni ladění pro protokoly vytvořené pomocí kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="88a34-356">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while logging at debug level for logs created by application code.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="88a34-357">Pokud chcete zabránit psaní jakýchkoli protokolů, zadejte `LogLevel.None` jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="88a34-357">To prevent any logs from being written, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="88a34-358">Celočíselná hodnota `LogLevel.None` je 6, což je více než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="88a34-358">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="88a34-359">Rozšiřující metoda je poskytována balíčkem NuGet [Microsoft. Extensions. Logging. Filter.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) `WithFilter`</span><span class="sxs-lookup"><span data-stu-id="88a34-359">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="88a34-360">Metoda vrátí novou `ILoggerFactory` instanci, která bude filtrovat zprávy protokolu předané všem poskytovatelům protokolovacích služeb, které jsou v ní zaregistrované.</span><span class="sxs-lookup"><span data-stu-id="88a34-360">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="88a34-361">Neovlivňuje žádné jiné `ILoggerFactory` instance, včetně původní `ILoggerFactory` instance.</span><span class="sxs-lookup"><span data-stu-id="88a34-361">It doesn't affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="88a34-362">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="88a34-362">System categories and levels</span></span>

<span data-ttu-id="88a34-363">Tady je několik kategorií používaných ASP.NET Core a Entity Framework Core s poznámkami o tom, jaké protokoly se mají od nich očekávat:</span><span class="sxs-lookup"><span data-stu-id="88a34-363">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="88a34-364">Kategorie</span><span class="sxs-lookup"><span data-stu-id="88a34-364">Category</span></span>                            | <span data-ttu-id="88a34-365">Poznámky</span><span class="sxs-lookup"><span data-stu-id="88a34-365">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="88a34-366">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="88a34-366">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="88a34-367">Obecná diagnostika ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="88a34-367">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="88a34-368">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="88a34-368">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="88a34-369">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="88a34-369">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="88a34-370">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="88a34-370">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="88a34-371">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="88a34-371">Hosts allowed.</span></span> |
| <span data-ttu-id="88a34-372">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="88a34-372">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="88a34-373">Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění.</span><span class="sxs-lookup"><span data-stu-id="88a34-373">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="88a34-374">Která hostující spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="88a34-374">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="88a34-375">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="88a34-375">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="88a34-376">Diagnostika MVC a Razor</span><span class="sxs-lookup"><span data-stu-id="88a34-376">MVC and Razor diagnostics.</span></span> <span data-ttu-id="88a34-377">Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="88a34-377">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="88a34-378">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="88a34-378">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="88a34-379">Informace o shodě trasy.</span><span class="sxs-lookup"><span data-stu-id="88a34-379">Route matching information.</span></span> |
| <span data-ttu-id="88a34-380">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="88a34-380">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="88a34-381">Připojení – spouštění, zastavování a udržování reakcí na Alive.</span><span class="sxs-lookup"><span data-stu-id="88a34-381">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="88a34-382">Informace o certifikátu HTTPS</span><span class="sxs-lookup"><span data-stu-id="88a34-382">HTTPS certificate information.</span></span> |
| <span data-ttu-id="88a34-383">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="88a34-383">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="88a34-384">Soubory byly obsluhovány.</span><span class="sxs-lookup"><span data-stu-id="88a34-384">Files served.</span></span> |
| <span data-ttu-id="88a34-385">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="88a34-385">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="88a34-386">Obecná diagnostika Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="88a34-386">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="88a34-387">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="88a34-387">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="88a34-388">Rozsahy protokolů</span><span class="sxs-lookup"><span data-stu-id="88a34-388">Log scopes</span></span>

 <span data-ttu-id="88a34-389">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="88a34-389">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="88a34-390">Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="88a34-390">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="88a34-391">Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="88a34-391">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="88a34-392">Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodou a trvá, dokud nebude uvolněn.</span><span class="sxs-lookup"><span data-stu-id="88a34-392">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="88a34-393">Použití oboru zabalením volání protokolovacího nástroje `using` v bloku:</span><span class="sxs-lookup"><span data-stu-id="88a34-393">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="88a34-394">Následující kód umožňuje obory pro poskytovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="88a34-394">The following code enables scopes for the console provider:</span></span>

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="88a34-395">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="88a34-395">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="88a34-396">Aby bylo možné povolit protokolování na základě oboru, je nutné nakonfigurovat možnost protokolovacího nástroje konzoly.`IncludeScopes`</span><span class="sxs-lookup"><span data-stu-id="88a34-396">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="88a34-397">Informace o konfiguraci najdete v části věnované [konfiguraci](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="88a34-397">For information on configuration, see the [Configuration](#configuration) section.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="88a34-398">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="88a34-398">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="88a34-399">Aby bylo možné povolit protokolování na základě oboru, je nutné nakonfigurovat možnost protokolovacího nástroje konzoly.`IncludeScopes`</span><span class="sxs-lookup"><span data-stu-id="88a34-399">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="88a34-400">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="88a34-400">*Startup.cs*:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

<span data-ttu-id="88a34-401">Každá zpráva protokolu obsahuje informace s vymezeným oborem:</span><span class="sxs-lookup"><span data-stu-id="88a34-401">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="88a34-402">Vestavění zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="88a34-402">Built-in logging providers</span></span>

<span data-ttu-id="88a34-403">ASP.NET Core dodává následující poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="88a34-403">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="88a34-404">Console</span><span class="sxs-lookup"><span data-stu-id="88a34-404">Console</span></span>](#console-provider)
* [<span data-ttu-id="88a34-405">Ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-405">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="88a34-406">EventSource</span><span class="sxs-lookup"><span data-stu-id="88a34-406">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="88a34-407">EventLog</span><span class="sxs-lookup"><span data-stu-id="88a34-407">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="88a34-408">TraceSource</span><span class="sxs-lookup"><span data-stu-id="88a34-408">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="88a34-409">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="88a34-409">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="88a34-410">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="88a34-410">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="88a34-411">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="88a34-411">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="88a34-412">Informace o stdout a protokolování ladění pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection></span><span class="sxs-lookup"><span data-stu-id="88a34-412">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="88a34-413">Poskytovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="88a34-413">Console provider</span></span>

<span data-ttu-id="88a34-414">Balíček poskytovatele [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="88a34-414">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

<span data-ttu-id="88a34-415">[AddConsole přetížení](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) umožňují předat minimální úroveň protokolu, funkci filtru a logickou hodnotu, která označuje, jestli jsou podporované obory.</span><span class="sxs-lookup"><span data-stu-id="88a34-415">[AddConsole overloads](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) let you pass in a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span> <span data-ttu-id="88a34-416">Další možností je předat `IConfiguration` objekt, který může určovat obory podpory a úrovně protokolování.</span><span class="sxs-lookup"><span data-stu-id="88a34-416">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span>

<span data-ttu-id="88a34-417">Možnosti pro poskytovatele konzoly naleznete v <xref:Microsoft.Extensions.Logging.Console.ConsoleLoggerOptions>tématu.</span><span class="sxs-lookup"><span data-stu-id="88a34-417">For Console provider options, see <xref:Microsoft.Extensions.Logging.Console.ConsoleLoggerOptions>.</span></span>

<span data-ttu-id="88a34-418">Poskytovatel konzoly má významný dopad na výkon a obecně není vhodný pro použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="88a34-418">The console provider has a significant impact on performance and is generally not appropriate for use in production.</span></span>

<span data-ttu-id="88a34-419">Při vytváření nového projektu v aplikaci Visual Studio bude `AddConsole` metoda vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="88a34-419">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="88a34-420">Tento kód odkazuje `Logging` na oddíl souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="88a34-420">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](index/samples/1.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="88a34-421">Nastavení zobrazovala možnost omezit protokoly rozhraní na upozornění, když aplikaci povolíte protokolování na úrovni ladění, jak je vysvětleno v části [filtrování protokolů](#log-filtering) .</span><span class="sxs-lookup"><span data-stu-id="88a34-421">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="88a34-422">Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="88a34-422">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

::: moniker-end

<span data-ttu-id="88a34-423">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="88a34-423">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```console
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="88a34-424">Poskytovatel ladění</span><span class="sxs-lookup"><span data-stu-id="88a34-424">Debug provider</span></span>

<span data-ttu-id="88a34-425">Balíček poskytovatele [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` volání metody).</span><span class="sxs-lookup"><span data-stu-id="88a34-425">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="88a34-426">V systému Linux tento poskytovatel zapisuje protokoly do */var/log/Message*.</span><span class="sxs-lookup"><span data-stu-id="88a34-426">On Linux, this provider writes logs to */var/log/message*.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

<span data-ttu-id="88a34-427">[Přetížení AddDebug](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) umožňují předat minimální úroveň protokolu nebo funkci filtru.</span><span class="sxs-lookup"><span data-stu-id="88a34-427">[AddDebug overloads](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) let you pass in a minimum log level or a filter function.</span></span>

::: moniker-end

### <a name="eventsource-provider"></a><span data-ttu-id="88a34-428">Zprostředkovatel EventSource</span><span class="sxs-lookup"><span data-stu-id="88a34-428">EventSource provider</span></span>

<span data-ttu-id="88a34-429">Pro aplikace cílené na ASP.NET Core 1.1.0 nebo novější může balíček zprostředkovatele [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) implementovat trasování událostí.</span><span class="sxs-lookup"><span data-stu-id="88a34-429">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="88a34-430">Ve Windows používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="88a34-430">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="88a34-431">Poskytovatel je pro různé platformy, ale pro Linux nebo macOS ještě nejsou k dispozici žádné nástroje pro shromažďování událostí a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="88a34-431">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

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

<span data-ttu-id="88a34-432">Dobrým způsobem, jak shromažďovat a zobrazovat protokoly, je použít [Nástroj PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="88a34-432">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="88a34-433">Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými službou ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="88a34-433">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span>

<span data-ttu-id="88a34-434">Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte `*Microsoft-Extensions-Logging` řetězec do seznamu **další poskytovatelé** .</span><span class="sxs-lookup"><span data-stu-id="88a34-434">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="88a34-435">(Na začátku řetězce nechybíš hvězdičku.)</span><span class="sxs-lookup"><span data-stu-id="88a34-435">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="88a34-437">Zprostředkovatel událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="88a34-437">Windows EventLog provider</span></span>

<span data-ttu-id="88a34-438">Balíček poskytovatele [Microsoft. Extensions. log. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="88a34-438">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

<span data-ttu-id="88a34-439">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat `EventLogSettings` nebo minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="88a34-439">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

::: moniker-end

### <a name="tracesource-provider"></a><span data-ttu-id="88a34-440">Poskytovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="88a34-440">TraceSource provider</span></span>

<span data-ttu-id="88a34-441">Balíček poskytovatele [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-441">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

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

<span data-ttu-id="88a34-442">[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="88a34-442">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="88a34-443">Aby bylo možné používat tohoto poskytovatele, aplikace musí běžet na .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="88a34-443">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="88a34-444">Poskytovatel může směrovat zprávy na celou řadu posluchačů [](/dotnet/framework/debug-trace-profile/trace-listeners), jako je <xref:System.Diagnostics.TextWriterTraceListener> například použití v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="88a34-444">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="88a34-445">Následující příklad konfiguruje `TraceSource` poskytovatele, který protokoluje `Warning` a rychlejší zprávy do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="88a34-445">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

### <a name="azure-app-service-provider"></a><span data-ttu-id="88a34-446">Poskytovatel Azure App Service</span><span class="sxs-lookup"><span data-stu-id="88a34-446">Azure App Service provider</span></span>

<span data-ttu-id="88a34-447">Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.</span><span class="sxs-lookup"><span data-stu-id="88a34-447">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="88a34-448">Balíček Provider je dostupný pro aplikace cílené na .NET Core 1,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="88a34-448">The provider package is available for apps targeting .NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="88a34-449">Při cílení na .NET Core Pamatujte na následující body:</span><span class="sxs-lookup"><span data-stu-id="88a34-449">If targeting .NET Core, note the following points:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="88a34-450">Balíček Provider je zahrnutý ve ASP.NET Core [Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="88a34-450">The provider package is included in the ASP.NET Core [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="88a34-451">Balíček poskytovatele není zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="88a34-451">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="88a34-452">Chcete-li použít poskytovatele, nainstalujte balíček.</span><span class="sxs-lookup"><span data-stu-id="88a34-452">To use the provider, install the package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="88a34-453">Pokud cílíte .NET Framework nebo odkazování `Microsoft.AspNetCore.App` na Metapackage, přidejte do projektu balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-453">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> <span data-ttu-id="88a34-454">Vyvolat `AddAzureWebAppDiagnostics`:</span><span class="sxs-lookup"><span data-stu-id="88a34-454">Invoke `AddAzureWebAppDiagnostics`:</span></span>

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

<span data-ttu-id="88a34-455"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Přetížení umožňuje <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>předat.</span><span class="sxs-lookup"><span data-stu-id="88a34-455">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="88a34-456">Objekt nastavení může přepsat výchozí nastavení, jako je například šablona výstupu protokolování, název objektu BLOB a omezení velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="88a34-456">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="88a34-457">(*Výstupní šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici `ILogger` při volání metody.)</span><span class="sxs-lookup"><span data-stu-id="88a34-457">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="88a34-458">Chcete-li konfigurovat nastavení zprostředkovatele <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> , <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>použijte a, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="88a34-458">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

<span data-ttu-id="88a34-459">Když nasadíte aplikaci do App Service, aplikace respektuje nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) stránky **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="88a34-459">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="88a34-460">Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="88a34-460">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="88a34-461">**Protokolování aplikace (systém souborů)**</span><span class="sxs-lookup"><span data-stu-id="88a34-461">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="88a34-462">**Protokolování aplikace (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="88a34-462">**Application Logging (Blob)**</span></span>

<span data-ttu-id="88a34-463">Výchozím umístěním pro soubory protokolu je složka *\\aplikace D:\\Home\\LogFiles* a výchozí název souboru je *Diagnostics-YYYYMMDD. txt*.</span><span class="sxs-lookup"><span data-stu-id="88a34-463">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="88a34-464">Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="88a34-464">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="88a34-465">Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/MM/DD/hh/{GUID}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="88a34-465">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="88a34-466">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="88a34-466">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="88a34-467">Nemá žádný vliv, pokud se projekt spouští místně&mdash;, nepíše do místních souborů nebo místního vývojového úložiště pro objekty blob.</span><span class="sxs-lookup"><span data-stu-id="88a34-467">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="88a34-468">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="88a34-468">Azure log streaming</span></span>

<span data-ttu-id="88a34-469">Streamování protokolů Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="88a34-469">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="88a34-470">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="88a34-470">The app server</span></span>
* <span data-ttu-id="88a34-471">Webový server</span><span class="sxs-lookup"><span data-stu-id="88a34-471">The web server</span></span>
* <span data-ttu-id="88a34-472">Trasování chybných požadavků</span><span class="sxs-lookup"><span data-stu-id="88a34-472">Failed request tracing</span></span>

<span data-ttu-id="88a34-473">Konfigurace streamování protokolů Azure:</span><span class="sxs-lookup"><span data-stu-id="88a34-473">To configure Azure log streaming:</span></span>

* <span data-ttu-id="88a34-474">Na stránce portálu vaší aplikace přejděte na stránku **protokoly App Service** .</span><span class="sxs-lookup"><span data-stu-id="88a34-474">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="88a34-475">Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="88a34-475">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="88a34-476">Vyberte **úroveň**protokolu.</span><span class="sxs-lookup"><span data-stu-id="88a34-476">Choose the log **Level**.</span></span>

<span data-ttu-id="88a34-477">Pokud chcete zobrazit zprávy aplikace, přejděte na stránku **streamu protokolu** .</span><span class="sxs-lookup"><span data-stu-id="88a34-477">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="88a34-478">Protokolují ho aplikace přes `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="88a34-478">They're logged by the app through the `ILogger` interface.</span></span>

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="88a34-479">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="88a34-479">Azure Application Insights trace logging</span></span>

<span data-ttu-id="88a34-480">Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="88a34-480">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="88a34-481">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie.</span><span class="sxs-lookup"><span data-stu-id="88a34-481">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="88a34-482">Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.</span><span class="sxs-lookup"><span data-stu-id="88a34-482">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="88a34-483">Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="88a34-483">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="88a34-484">Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-484">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="88a34-485">Nepoužívejte&mdash;balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , který je pro ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="88a34-485">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="88a34-486">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="88a34-486">For more information, see the following resources:</span></span>

* [<span data-ttu-id="88a34-487">Přehled Application Insights</span><span class="sxs-lookup"><span data-stu-id="88a34-487">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="88a34-488">[Application Insights pro ASP.NET Core aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou řadu Application Insights telemetrie spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="88a34-488">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="88a34-489">[ApplicationInsightsLoggerProvider pro protokoly .NET Core ILogger](/azure/azure-monitor/app/ilogger) – začněte sem, pokud chcete implementovat poskytovatele protokolování bez zbytku Application Insights telemetrie.</span><span class="sxs-lookup"><span data-stu-id="88a34-489">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="88a34-490">[Application Insights adaptéry protokolování](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="88a34-490">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="88a34-491">[Instalace, konfigurace a inicializace Application Insights sady SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="88a34-491">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>
::: moniker-end

## <a name="third-party-logging-providers"></a><span data-ttu-id="88a34-492">Zprostředkovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="88a34-492">Third-party logging providers</span></span>

<span data-ttu-id="88a34-493">Protokolovací architektury třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="88a34-493">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="88a34-494">[elmah.IO](https://elmah.io/) ([úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="88a34-494">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="88a34-495">[GELF](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Úložiště GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="88a34-495">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="88a34-496">[JSNLog](https://jsnlog.com/) ([Úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="88a34-496">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="88a34-497">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="88a34-497">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="88a34-498">[Loggr](https://loggr.net/) ([Úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="88a34-498">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="88a34-499">[NLOG](https://nlog-project.org/) ([Úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="88a34-499">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="88a34-500">[Sentry](https://sentry.io/welcome/) ([Úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="88a34-500">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="88a34-501">[Serilog](https://serilog.net/) ([Úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="88a34-501">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="88a34-502">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="88a34-502">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="88a34-503">Některé architektury třetích stran můžou provádět sémantické [protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="88a34-503">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="88a34-504">Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="88a34-504">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="88a34-505">Přidejte do projektu balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="88a34-505">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="88a34-506">`ILoggerFactory`Zavolejte.</span><span class="sxs-lookup"><span data-stu-id="88a34-506">Call an `ILoggerFactory`.</span></span>

<span data-ttu-id="88a34-507">Další informace najdete v dokumentaci pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="88a34-507">For more information, see each provider's documentation.</span></span> <span data-ttu-id="88a34-508">Microsoft nepodporuje zprostředkovatele protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="88a34-508">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88a34-509">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="88a34-509">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

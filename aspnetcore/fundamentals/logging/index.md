---
title: Protokolování v .NET Core a ASP.NET Core
author: rick-anderson
description: Naučte se používat protokolovací rozhraní poskytovanou balíčkem NuGet Microsoft. Extensions. Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330725"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="bef1c-103">Protokolování v .NET Core a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bef1c-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bef1c-104">Od [Kirka Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bef1c-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bef1c-105">.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="bef1c-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="bef1c-106">V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="bef1c-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="bef1c-107">Většina příkladů kódu, které jsou uvedené v tomto článku, se nachází v ASP.NET Corech aplikacích.</span><span class="sxs-lookup"><span data-stu-id="bef1c-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="bef1c-108">Části těchto fragmentů kódu specifické pro protokolování se vztahují na libovolnou aplikaci .NET Core, která používá [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="bef1c-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="bef1c-109">Šablony ASP.NET Core webové aplikace používají obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="bef1c-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bef1c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="bef1c-111">Zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-111">Logging providers</span></span>

<span data-ttu-id="bef1c-112">Zprostředkovatelé protokolování uchovávají protokoly, s výjimkou `Console` poskytovatele, který zobrazuje protokoly.</span><span class="sxs-lookup"><span data-stu-id="bef1c-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="bef1c-113">Poskytovatel Azure Application Insights například ukládá protokoly v Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="bef1c-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="bef1c-114">Je možné povolit více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="bef1c-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="bef1c-115">Výchozí ASP.NET Core šablony webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="bef1c-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="bef1c-116">Použijte [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="bef1c-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="bef1c-117">Zavolejte <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , čímž přidáte následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="bef1c-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="bef1c-118">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-118">Console</span></span>](#console)
  * [<span data-ttu-id="bef1c-119">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="bef1c-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="bef1c-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="bef1c-121">[EventLog](#welog): jenom Windows</span><span class="sxs-lookup"><span data-stu-id="bef1c-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="bef1c-122">Předchozí kód ukazuje `Program` třídu vytvořenou pomocí šablon webové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="bef1c-123">Další části obsahují ukázky založené na šablonách ASP.NET Core webových aplikací, které používají obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="bef1c-124">Další v tomto dokumentu jsou popsány v [jiných než hostitelských konzolových aplikacích](#nhca) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="bef1c-125">Chcete-li přepsat výchozí sadu zprostředkovatelů protokolování přidaných nástrojem `Host.CreateDefaultBuilder` , zavolejte `ClearProviders` a přidejte požadované poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="bef1c-126">Například následující kód:</span><span class="sxs-lookup"><span data-stu-id="bef1c-126">For example, the following code:</span></span>

* <span data-ttu-id="bef1c-127">Volání <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> pro odebrání všech <xref:Microsoft.Extensions.Logging.ILoggerProvider> instancí z tvůrce.</span><span class="sxs-lookup"><span data-stu-id="bef1c-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="bef1c-128">Přidá zprostředkovatele protokolování [konzoly](#console) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="bef1c-129">Další poskytovatele najdete v těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="bef1c-129">For additional providers, see:</span></span>

* [<span data-ttu-id="bef1c-130">Vestavění zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="bef1c-131">[Zprostředkovatelé protokolování třetích stran](#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="bef1c-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="bef1c-132">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="bef1c-132">Create logs</span></span>

<span data-ttu-id="bef1c-133">Chcete-li vytvořit protokoly, použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt z [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="bef1c-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="bef1c-134">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="bef1c-134">The following example:</span></span>

* <span data-ttu-id="bef1c-135">Vytvoří protokolovací nástroj, `ILogger<AboutModel>` který používá *kategorii* protokolu plně kvalifikovaného názvu typu `AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="bef1c-136">Kategorie protokolu je řetězec, který je spojený s každým protokolem.</span><span class="sxs-lookup"><span data-stu-id="bef1c-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="bef1c-137">Volání <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> do protokolu na `Information` úrovni.</span><span class="sxs-lookup"><span data-stu-id="bef1c-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="bef1c-138">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="bef1c-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="bef1c-139">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="bef1c-140">Informace o najdete v Blazor tématu [vytvoření protokolů v Blazor nástroji Blazor WebAssembly a](#clib) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="bef1c-141">[Při vytváření protokolů v nástroji Main a Startup](#clms) se dozvíte, jak vytvořit protokoly v `Main` a `Startup` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="bef1c-142">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-142">Configure logging</span></span>

<span data-ttu-id="bef1c-143">Konfigurace protokolování je běžně poskytována `Logging` oddílem *appSettings*. `{Environment}` soubory *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="bef1c-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="bef1c-144">Následující *appsettings.Development.jsv* souboru vygeneruje šablony ASP.NET Core webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="bef1c-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="bef1c-145">V předchozím kódu JSON:</span><span class="sxs-lookup"><span data-stu-id="bef1c-145">In the preceding JSON:</span></span>

* <span data-ttu-id="bef1c-146">`"Default"` `"Microsoft"` `"Microsoft.Hosting.Lifetime"` Jsou určeny kategorie, a.</span><span class="sxs-lookup"><span data-stu-id="bef1c-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="bef1c-147">`"Microsoft"`Kategorie se vztahuje na všechny kategorie, které začínají na `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="bef1c-148">Toto nastavení se vztahuje například na `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` kategorii.</span><span class="sxs-lookup"><span data-stu-id="bef1c-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="bef1c-149">`"Microsoft"`Protokol kategorie na úrovni protokolu `Warning` a vyšším.</span><span class="sxs-lookup"><span data-stu-id="bef1c-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="bef1c-150">`"Microsoft.Hosting.Lifetime"`Kategorie je konkrétnější než `"Microsoft"` kategorie, takže se v `"Microsoft.Hosting.Lifetime"` kategorii protokoluje na úrovni protokolu "informace" a vyšší.</span><span class="sxs-lookup"><span data-stu-id="bef1c-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="bef1c-151">Konkrétního poskytovatele protokolu není zadaný, takže `LogLevel` se použije na všechny povolené zprostředkovatele protokolování, s výjimkou protokolu [událostí systému Windows](#welog).</span><span class="sxs-lookup"><span data-stu-id="bef1c-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="bef1c-152">`Logging`Vlastnost může mít <xref:Microsoft.Extensions.Logging.LogLevel> vlastnosti a log Provider.</span><span class="sxs-lookup"><span data-stu-id="bef1c-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="bef1c-153">`LogLevel`Určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="bef1c-154">V předchozím kódu JSON `Information` `Warning` jsou zadány úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="bef1c-155">`LogLevel`označuje závažnost protokolu a rozsahů od 0 do 6:</span><span class="sxs-lookup"><span data-stu-id="bef1c-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="bef1c-156">`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, = `Error` 4, `Critical` = 5 a `None` = 6.</span><span class="sxs-lookup"><span data-stu-id="bef1c-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="bef1c-157">Je-li `LogLevel` zadán parametr, je protokolování povoleno pro zprávy na zadané úrovni a vyšší.</span><span class="sxs-lookup"><span data-stu-id="bef1c-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="bef1c-158">V předchozím formátu JSON `Default` kategorie je protokolována `Information` a vyšší.</span><span class="sxs-lookup"><span data-stu-id="bef1c-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="bef1c-159">Například zprávy, `Information` , `Warning` `Error` a `Critical` jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="bef1c-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="bef1c-160">Pokud `LogLevel` není zadaný, použije se jako výchozí `Information` úroveň protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="bef1c-161">Další informace najdete v tématu [úrovně protokolu](#llvl).</span><span class="sxs-lookup"><span data-stu-id="bef1c-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="bef1c-162">Vlastnost zprostředkovatele může určovat `LogLevel` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bef1c-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="bef1c-163">`LogLevel`v části poskytovatel Určuje úrovně, které se mají protokolovat pro daného zprostředkovatele, a přepíše nastavení protokolu bez poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="bef1c-164">V souboru zvažte následující *appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="bef1c-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="bef1c-165">Nastavení v `Logging.{providername}.LogLevel` Nastavení přepsání v `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="bef1c-166">V předchozím formátu JSON `Debug` je výchozí úroveň protokolu poskytovatele nastavená na `Information` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="bef1c-167">Předchozí nastavení určuje `Information` úroveň protokolu pro každou `Logging:Debug:` kategorii s výjimkou `Microsoft.Hosting` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="bef1c-168">Když je uvedena konkrétní kategorie, konkrétní kategorie přepíše výchozí kategorii.</span><span class="sxs-lookup"><span data-stu-id="bef1c-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="bef1c-169">V předchozím formátu JSON `Logging:Debug:LogLevel` Kategorie `"Microsoft.Hosting"` a `"Default"` přepisují nastavení v`Logging:LogLevel`</span><span class="sxs-lookup"><span data-stu-id="bef1c-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="bef1c-170">Minimální úroveň protokolu může být zadána pro některý z těchto:</span><span class="sxs-lookup"><span data-stu-id="bef1c-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="bef1c-171">Konkrétní poskytovatelé: například`Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="bef1c-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="bef1c-172">Konkrétní kategorie: například`Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="bef1c-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="bef1c-173">Všichni zprostředkovatelé a všechny kategorie:`Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="bef1c-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="bef1c-174">Všechny protokoly pod minimální úrovní ***nejsou:***</span><span class="sxs-lookup"><span data-stu-id="bef1c-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="bef1c-175">Předáno poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="bef1c-175">Passed to the provider.</span></span>
* <span data-ttu-id="bef1c-176">Protokolováno nebo zobrazeno.</span><span class="sxs-lookup"><span data-stu-id="bef1c-176">Logged or displayed.</span></span>

<span data-ttu-id="bef1c-177">Pokud chcete potlačit všechny protokoly, zadejte [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel).</span><span class="sxs-lookup"><span data-stu-id="bef1c-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="bef1c-178">`LogLevel.None`má hodnotu 6, která je vyšší než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="bef1c-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="bef1c-179">Pokud zprostředkovatel podporuje [obory protokolů](#logscopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="bef1c-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="bef1c-180">Další informace najdete v tématu [rozsahy protokolů](#logscopes) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="bef1c-181">Následující *appsettings.js* souboru obsahuje všechny poskytovatele povolené ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="bef1c-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="bef1c-182">V předchozí ukázce:</span><span class="sxs-lookup"><span data-stu-id="bef1c-182">In the preceding sample:</span></span>

* <span data-ttu-id="bef1c-183">Kategorie a úrovně nejsou navrhovanými hodnotami.</span><span class="sxs-lookup"><span data-stu-id="bef1c-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="bef1c-184">Ukázka je k dispozici pro zobrazení všech výchozích zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="bef1c-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="bef1c-185">Nastavení v `Logging.{providername}.LogLevel` Nastavení přepsání v `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="bef1c-186">Například úroveň v `Debug.LogLevel.Default` Přepisuje úroveň v `LogLevel.Default` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="bef1c-187">Použije se každý *alias* výchozího zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="bef1c-188">Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="bef1c-189">Předdefinovaná aliasy zprostředkovatelů jsou:</span><span class="sxs-lookup"><span data-stu-id="bef1c-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="bef1c-190">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-190">Console</span></span>
  * <span data-ttu-id="bef1c-191">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-191">Debug</span></span>
  * <span data-ttu-id="bef1c-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="bef1c-192">EventSource</span></span>
  * <span data-ttu-id="bef1c-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="bef1c-193">EventLog</span></span>
  * <span data-ttu-id="bef1c-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="bef1c-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="bef1c-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="bef1c-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="bef1c-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="bef1c-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="bef1c-197">Nastavení úrovně protokolu podle příkazového řádku, proměnných prostředí a jiné konfigurace</span><span class="sxs-lookup"><span data-stu-id="bef1c-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="bef1c-198">Úroveň protokolu může být nastavena libovolnými [poskytovateli konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="bef1c-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="bef1c-199">Následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="bef1c-199">The following commands:</span></span>

* <span data-ttu-id="bef1c-200">Nastavte klíč prostředí `Logging:LogLevel:Microsoft` na hodnotu `Information` ve Windows.</span><span class="sxs-lookup"><span data-stu-id="bef1c-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="bef1c-201">Otestujte nastavení při použití aplikace vytvořené pomocí šablon webových aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="bef1c-202">`dotnet run`Příkaz musí být spuštěn v adresáři projektu po použití `set` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="bef1c-203">Předchozí nastavení prostředí:</span><span class="sxs-lookup"><span data-stu-id="bef1c-203">The preceding environment setting:</span></span>

* <span data-ttu-id="bef1c-204">Je nastaveno pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.</span><span class="sxs-lookup"><span data-stu-id="bef1c-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="bef1c-205">Není přečteno prohlížeči spuštěným v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bef1c-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="bef1c-206">Následující příkaz [setx](/windows-server/administration/windows-commands/setx) taky nastavuje klíč a hodnotu prostředí ve Windows.</span><span class="sxs-lookup"><span data-stu-id="bef1c-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="bef1c-207">Na rozdíl `set` od `setx` platí, že nastavení jsou trvalá.</span><span class="sxs-lookup"><span data-stu-id="bef1c-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="bef1c-208">`/M`Přepínač nastaví proměnnou v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="bef1c-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="bef1c-209">Pokud se `/M` nepoužívá, je nastavena proměnná prostředí uživatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="bef1c-210">V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="bef1c-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="bef1c-211">Azure App Service nastavení aplikace:</span><span class="sxs-lookup"><span data-stu-id="bef1c-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="bef1c-212">Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.</span><span class="sxs-lookup"><span data-stu-id="bef1c-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="bef1c-213">Vystaveno jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-213">Exposed as environment variables.</span></span>

<span data-ttu-id="bef1c-214">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="bef1c-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="bef1c-215">Další informace o nastavení hodnot konfigurace ASP.NET Core pomocí proměnných prostředí naleznete v tématu [proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="bef1c-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="bef1c-216">Informace o používání dalších zdrojů konfigurace, včetně příkazového řádku, Azure Key Vault, konfigurace aplikace Azure, dalších formátů souborů a dalších, najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="bef1c-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="bef1c-217">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="bef1c-217">How filtering rules are applied</span></span>

<span data-ttu-id="bef1c-218">Při <xref:Microsoft.Extensions.Logging.ILogger%601> vytvoření objektu <xref:Microsoft.Extensions.Logging.ILoggerFactory> vybere objekt jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="bef1c-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="bef1c-219">Všechny zprávy napsané `ILogger` instancí jsou filtrovány podle vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="bef1c-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="bef1c-220">Z dostupných pravidel je vybráno pravidlo s nejpřesnější pro každou dvojici zprostředkovatelů a kategorií.</span><span class="sxs-lookup"><span data-stu-id="bef1c-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="bef1c-221">Následující algoritmus se používá pro každého poskytovatele při `ILogger` vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="bef1c-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="bef1c-222">Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem.</span><span class="sxs-lookup"><span data-stu-id="bef1c-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="bef1c-223">Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="bef1c-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="bef1c-224">Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="bef1c-225">Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="bef1c-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="bef1c-226">Pokud je vybráno více pravidel, vezměte **Poslední** z nich.</span><span class="sxs-lookup"><span data-stu-id="bef1c-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="bef1c-227">Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="bef1c-228">Výstup protokolování z příkazu dotnet Run a Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bef1c-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="bef1c-229">Zobrazí se protokoly vytvořené s [výchozím zprostředkovatelem protokolování](#lp) :</span><span class="sxs-lookup"><span data-stu-id="bef1c-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="bef1c-230">V nástroji Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bef1c-230">In Visual Studio</span></span>
  * <span data-ttu-id="bef1c-231">V okně výstup ladění při ladění.</span><span class="sxs-lookup"><span data-stu-id="bef1c-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="bef1c-232">V okně ASP.NET Core webový server.</span><span class="sxs-lookup"><span data-stu-id="bef1c-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="bef1c-233">V okně konzoly při spuštění aplikace s nástrojem `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="bef1c-234">Protokoly, které začínají na kategoriích Microsoft, jsou z ASP.NET Coreho kódu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bef1c-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="bef1c-235">ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="bef1c-236">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="bef1c-236">Log category</span></span>

<span data-ttu-id="bef1c-237">Při `ILogger` vytvoření objektu je zadána *kategorie* .</span><span class="sxs-lookup"><span data-stu-id="bef1c-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="bef1c-238">Tato kategorie je součástí každé zprávy protokolu vytvořené pomocí této instance `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="bef1c-239">Řetězec kategorie je libovolný, ale konvence používá název třídy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="bef1c-240">Například na řadiči může být název `"TodoApi.Controllers.TodoController"` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="bef1c-241">ASP.NET Core webové aplikace používají `ILogger<T>` k automatickému získání `ILogger` instance, která používá plně kvalifikovaný název typu `T` jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="bef1c-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="bef1c-242">Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="bef1c-243">`ILogger<T>`je ekvivalentní volání `CreateLogger` s plně kvalifikovaným názvem typu `T` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="bef1c-244">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-244">Log level</span></span>

<span data-ttu-id="bef1c-245">Následující tabulka uvádí <xref:Microsoft.Extensions.Logging.LogLevel> hodnoty, `Log{LogLevel}` způsob rozšíření pohodlí a navrhované použití:</span><span class="sxs-lookup"><span data-stu-id="bef1c-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="bef1c-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="bef1c-246">LogLevel</span></span> | <span data-ttu-id="bef1c-247">Hodnota</span><span class="sxs-lookup"><span data-stu-id="bef1c-247">Value</span></span> | <span data-ttu-id="bef1c-248">Metoda</span><span class="sxs-lookup"><span data-stu-id="bef1c-248">Method</span></span> | <span data-ttu-id="bef1c-249">Popis</span><span class="sxs-lookup"><span data-stu-id="bef1c-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="bef1c-250">Trasování</span><span class="sxs-lookup"><span data-stu-id="bef1c-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="bef1c-251">0</span><span class="sxs-lookup"><span data-stu-id="bef1c-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="bef1c-252">Obsahuje nejpodrobnější zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-252">Contain the most detailed messages.</span></span> <span data-ttu-id="bef1c-253">Tyto zprávy mohou obsahovat citlivá data aplikací.</span><span class="sxs-lookup"><span data-stu-id="bef1c-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="bef1c-254">Tyto zprávy jsou ve výchozím nastavení zakázané a ***neměly by být*** povolené v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="bef1c-255">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="bef1c-256">1</span><span class="sxs-lookup"><span data-stu-id="bef1c-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="bef1c-257">Pro ladění a vývoj.</span><span class="sxs-lookup"><span data-stu-id="bef1c-257">For debugging and development.</span></span> <span data-ttu-id="bef1c-258">Používejte s opatrností v produkčním prostředí kvůli vysokému objemu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="bef1c-259">Informace</span><span class="sxs-lookup"><span data-stu-id="bef1c-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="bef1c-260">2</span><span class="sxs-lookup"><span data-stu-id="bef1c-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="bef1c-261">Sleduje obecný tok aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="bef1c-262">Může mít dlouhodobou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-262">May have long-term value.</span></span> |
| [<span data-ttu-id="bef1c-263">Upozornění</span><span class="sxs-lookup"><span data-stu-id="bef1c-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="bef1c-264">3</span><span class="sxs-lookup"><span data-stu-id="bef1c-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="bef1c-265">Pro neobvyklé nebo neočekávané události.</span><span class="sxs-lookup"><span data-stu-id="bef1c-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="bef1c-266">Obvykle obsahuje chyby nebo podmínky, které nezpůsobí selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="bef1c-267">Chyba</span><span class="sxs-lookup"><span data-stu-id="bef1c-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="bef1c-268">4</span><span class="sxs-lookup"><span data-stu-id="bef1c-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="bef1c-269">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="bef1c-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="bef1c-270">Tyto zprávy označují selhání aktuální operace nebo žádosti, ne selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="bef1c-271">Kritické</span><span class="sxs-lookup"><span data-stu-id="bef1c-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="bef1c-272">5</span><span class="sxs-lookup"><span data-stu-id="bef1c-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="bef1c-273">Chyby, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="bef1c-273">For failures that require immediate attention.</span></span> <span data-ttu-id="bef1c-274">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="bef1c-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="bef1c-275">Žádný</span><span class="sxs-lookup"><span data-stu-id="bef1c-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="bef1c-276">6</span><span class="sxs-lookup"><span data-stu-id="bef1c-276">6</span></span> | | <span data-ttu-id="bef1c-277">Určuje, že kategorie protokolování by neměla zapisovat žádné zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="bef1c-278">V předchozí tabulce `LogLevel` je uveden od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="bef1c-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="bef1c-279">První parametr metody [protokolu](xref:Microsoft.Extensions.Logging.LoggerExtensions) , <xref:Microsoft.Extensions.Logging.LogLevel> , označuje závažnost protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="bef1c-280">Místo volání `Log(LogLevel, ...)` by většina vývojářů volala metody rozšíření [log {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="bef1c-281">`Log{LogLevel}`Rozšiřující metody [volají metodu protokolu a určují rozsah LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="bef1c-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="bef1c-282">Například následující dvě volání protokolování jsou funkčně ekvivalentní a tvoří stejný protokol:</span><span class="sxs-lookup"><span data-stu-id="bef1c-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="bef1c-283">`MyLogEvents.TestItem`je ID události.</span><span class="sxs-lookup"><span data-stu-id="bef1c-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="bef1c-284">`MyLogEvents`je součástí ukázkové aplikace a zobrazí se v části [ID události protokolu](#leid) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="bef1c-285">Následující kód vytváří `Information` a `Warning` protokoluje:</span><span class="sxs-lookup"><span data-stu-id="bef1c-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="bef1c-286">V předchozím kódu `Log{LogLevel}` je prvním parametrem `MyLogEvents.GetItem` [ID události protokolu](#leid).</span><span class="sxs-lookup"><span data-stu-id="bef1c-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="bef1c-287">Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="bef1c-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="bef1c-288">Parametry metody jsou vysvětleny v části [Šablona zprávy](#lmt) dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="bef1c-289">Zavolejte příslušnou `Log{LogLevel}` metodu, která určuje, kolik výstupu protokolu se zapisuje na konkrétní paměťové médium.</span><span class="sxs-lookup"><span data-stu-id="bef1c-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="bef1c-290">Příklad:</span><span class="sxs-lookup"><span data-stu-id="bef1c-290">For example:</span></span>

* <span data-ttu-id="bef1c-291">V produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="bef1c-291">In production:</span></span>
  * <span data-ttu-id="bef1c-292">Protokolování na úrovni `Trace` nebo `Information` vytváří velké množství podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="bef1c-293">Abyste mohli řídit náklady a nepřekračuje limity úložiště dat, protokolovat a vyhodnotit `Trace` `Information` zprávy na úrovni cloudového úložiště s nízkými náklady na velké objemy dat.</span><span class="sxs-lookup"><span data-stu-id="bef1c-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="bef1c-294">Zvažte omezení `Trace` a `Information` konkrétní kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="bef1c-295">Přihlášení `Warning` prostřednictvím `Critical` úrovní by mělo mít za následek několik zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="bef1c-296">Náklady a limity úložiště většinou nejsou obavy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="bef1c-297">Několik protokolů umožňuje větší flexibilitu v možnostech úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="bef1c-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="bef1c-298">Ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="bef1c-298">In development:</span></span>
  * <span data-ttu-id="bef1c-299">Nastavte na `Warning`.</span><span class="sxs-lookup"><span data-stu-id="bef1c-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="bef1c-300">Přidání `Trace` `Information` zpráv při řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="bef1c-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="bef1c-301">Pro omezení výstupu, nastavení `Trace` nebo `Information` pouze pro kategorie v rámci šetření.</span><span class="sxs-lookup"><span data-stu-id="bef1c-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="bef1c-302">ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="bef1c-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="bef1c-303">Zvažte například výstup protokolu pro:</span><span class="sxs-lookup"><span data-stu-id="bef1c-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="bef1c-304">RazorAplikace stránky vytvořená pomocí šablon ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="bef1c-305">Protokolování je nastaveno na`Logging:Console:LogLevel:Microsoft:Information`</span><span class="sxs-lookup"><span data-stu-id="bef1c-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="bef1c-306">Navigace na stránku ochrany osobních údajů:</span><span class="sxs-lookup"><span data-stu-id="bef1c-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="bef1c-307">Následující sady JSON `Logging:Console:LogLevel:Microsoft:Information` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="bef1c-308">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="bef1c-308">Log event ID</span></span>

<span data-ttu-id="bef1c-309">Každý protokol může určovat *ID události*.</span><span class="sxs-lookup"><span data-stu-id="bef1c-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="bef1c-310">Ukázková aplikace používá `MyLogEvents` třídu k definování ID událostí:</span><span class="sxs-lookup"><span data-stu-id="bef1c-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="bef1c-311">ID události přidružuje sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-311">An event ID associates a set of events.</span></span> <span data-ttu-id="bef1c-312">Například všechny protokoly související se zobrazením seznamu položek na stránce může být 1001.</span><span class="sxs-lookup"><span data-stu-id="bef1c-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="bef1c-313">Zprostředkovatel protokolování může ukládat ID události v poli ID, ve zprávě protokolování nebo vůbec ne.</span><span class="sxs-lookup"><span data-stu-id="bef1c-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="bef1c-314">Zprostředkovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="bef1c-315">Poskytovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="bef1c-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="bef1c-316">Někteří zprostředkovatelé protokolování ukládají ID události do pole, které umožňuje filtrování podle ID.</span><span class="sxs-lookup"><span data-stu-id="bef1c-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="bef1c-317">Šablona zprávy protokolu</span><span class="sxs-lookup"><span data-stu-id="bef1c-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="bef1c-318">Každé rozhraní API protokolu používá šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-318">Each log API uses a message template.</span></span> <span data-ttu-id="bef1c-319">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="bef1c-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="bef1c-320">Použijte názvy zástupných symbolů, nikoli čísla.</span><span class="sxs-lookup"><span data-stu-id="bef1c-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="bef1c-321">Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry slouží k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="bef1c-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="bef1c-322">V následujícím kódu jsou názvy parametrů mimo pořadí v šabloně zprávy:</span><span class="sxs-lookup"><span data-stu-id="bef1c-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="bef1c-323">Předchozí kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:</span><span class="sxs-lookup"><span data-stu-id="bef1c-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="bef1c-324">Tento přístup umožňuje zprostředkovatelům protokolování implementovat [sémantické nebo strukturované protokolování](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span><span class="sxs-lookup"><span data-stu-id="bef1c-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="bef1c-325">Samotné argumenty jsou předány do protokolovacího systému, nikoli pouze ze šablony formátované zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="bef1c-326">To umožňuje zprostředkovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="bef1c-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="bef1c-327">Zvažte například následující metodu protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="bef1c-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="bef1c-328">Například při protokolování do Azure Table Storage:</span><span class="sxs-lookup"><span data-stu-id="bef1c-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="bef1c-329">Každá entita tabulky Azure může mít `ID` `RequestTime` vlastnosti a.</span><span class="sxs-lookup"><span data-stu-id="bef1c-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="bef1c-330">Tabulky s vlastnostmi zjednodušují dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="bef1c-331">Dotaz může například najít všechny protokoly v rámci určitého `RequestTime` rozsahu bez nutnosti analyzovat časový limit textové zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="bef1c-332">Protokolovat výjimky</span><span class="sxs-lookup"><span data-stu-id="bef1c-332">Log exceptions</span></span>

<span data-ttu-id="bef1c-333">Metody protokolovacího nástroje mají přetížení, která přijímají parametr výjimky:</span><span class="sxs-lookup"><span data-stu-id="bef1c-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="bef1c-334">Protokolování výjimek je specifické pro konkrétního zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="bef1c-335">Výchozí úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-335">Default log level</span></span>

<span data-ttu-id="bef1c-336">Pokud není nastavená výchozí úroveň protokolování, je výchozí hodnota úrovně protokolu `Information` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="bef1c-337">Zvažte například následující webovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="bef1c-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="bef1c-338">Vytvořeno pomocí šablon webové aplikace ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="bef1c-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="bef1c-339">*appsettings.js* a *appsettings.Development.jspři* odstranění nebo přejmenování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="bef1c-340">V předchozím nastavení se při přechodu na soukromí nebo na domovské stránce `Trace` `Debug` `Information` v názvu kategorie vytvoří mnoho zpráv, a `Microsoft` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="bef1c-341">Následující kód nastaví výchozí úroveň protokolování, pokud výchozí úroveň protokolu není nastavena v konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="bef1c-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="bef1c-342">Obecně platí, že úrovně protokolu by měly být zadány v konfiguraci a nikoli kódu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="bef1c-343">Filter – funkce</span><span class="sxs-lookup"><span data-stu-id="bef1c-343">Filter function</span></span>

<span data-ttu-id="bef1c-344">Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="bef1c-345">Předchozí kód zobrazí protokoly konzoly, pokud kategorie obsahuje `Controller` nebo `Microsoft` a úroveň protokolu je `Information` nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="bef1c-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="bef1c-346">Obecně platí, že úrovně protokolu by měly být zadány v konfiguraci a nikoli kódu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="bef1c-347">Kategorie ASP.NET Core a EF Core</span><span class="sxs-lookup"><span data-stu-id="bef1c-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="bef1c-348">Následující tabulka obsahuje některé kategorie používané ASP.NET Core a Entity Framework Core s poznámkami k protokolům:</span><span class="sxs-lookup"><span data-stu-id="bef1c-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="bef1c-349">Kategorie</span><span class="sxs-lookup"><span data-stu-id="bef1c-349">Category</span></span>                            | <span data-ttu-id="bef1c-350">Poznámky</span><span class="sxs-lookup"><span data-stu-id="bef1c-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="bef1c-351">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="bef1c-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="bef1c-352">Obecná diagnostika ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="bef1c-353">Microsoft. AspNetCore. DataProtection</span><span class="sxs-lookup"><span data-stu-id="bef1c-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="bef1c-354">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="bef1c-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="bef1c-355">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="bef1c-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="bef1c-356">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="bef1c-356">Hosts allowed.</span></span> |
| <span data-ttu-id="bef1c-357">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="bef1c-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="bef1c-358">Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění.</span><span class="sxs-lookup"><span data-stu-id="bef1c-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="bef1c-359">Která hostující spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="bef1c-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="bef1c-360">Microsoft. AspNetCore. Mvc</span><span class="sxs-lookup"><span data-stu-id="bef1c-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="bef1c-361">MVC a Razor Diagnostika.</span><span class="sxs-lookup"><span data-stu-id="bef1c-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="bef1c-362">Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="bef1c-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="bef1c-363">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="bef1c-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="bef1c-364">Informace o shodě trasy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-364">Route matching information.</span></span> |
| <span data-ttu-id="bef1c-365">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="bef1c-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="bef1c-366">Připojení – spouštění, zastavování a udržování reakcí na Alive.</span><span class="sxs-lookup"><span data-stu-id="bef1c-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="bef1c-367">Informace o certifikátu HTTPS</span><span class="sxs-lookup"><span data-stu-id="bef1c-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="bef1c-368">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="bef1c-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="bef1c-369">Soubory byly obsluhovány.</span><span class="sxs-lookup"><span data-stu-id="bef1c-369">Files served.</span></span> |
| <span data-ttu-id="bef1c-370">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="bef1c-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="bef1c-371">Obecná diagnostika Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="bef1c-372">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="bef1c-373">Chcete-li zobrazit více kategorií v okně konzoly, nastavte **appsettings.Development.jsna** následující:</span><span class="sxs-lookup"><span data-stu-id="bef1c-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="bef1c-374">Rozsahy protokolů</span><span class="sxs-lookup"><span data-stu-id="bef1c-374">Log scopes</span></span>

 <span data-ttu-id="bef1c-375">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="bef1c-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="bef1c-376">Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="bef1c-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="bef1c-377">Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="bef1c-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="bef1c-378">Rozsah:</span><span class="sxs-lookup"><span data-stu-id="bef1c-378">A scope:</span></span>

* <span data-ttu-id="bef1c-379">Je <xref:System.IDisposable> typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodou.</span><span class="sxs-lookup"><span data-stu-id="bef1c-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="bef1c-380">Potrvá, dokud nebude zrušeno.</span><span class="sxs-lookup"><span data-stu-id="bef1c-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="bef1c-381">Následující poskytovatelé podporují obory:</span><span class="sxs-lookup"><span data-stu-id="bef1c-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="bef1c-382">AzureAppServicesFile a AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="bef1c-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="bef1c-383">Použití oboru zabalením volání protokolovacího nástroje v `using` bloku:</span><span class="sxs-lookup"><span data-stu-id="bef1c-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="bef1c-384">Následující JSON umožňuje obory pro poskytovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="bef1c-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="bef1c-385">Následující kód umožňuje obory pro poskytovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="bef1c-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="bef1c-386">Obecně platí, že by protokolování mělo být specifikováno v konfiguraci a nikoli kódu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="bef1c-387">Vestavění zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-387">Built-in logging providers</span></span>

<span data-ttu-id="bef1c-388">ASP.NET Core obsahuje následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="bef1c-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="bef1c-389">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-389">Console</span></span>](#console)
* [<span data-ttu-id="bef1c-390">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-390">Debug</span></span>](#debug)
* [<span data-ttu-id="bef1c-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="bef1c-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="bef1c-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="bef1c-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="bef1c-393">AzureAppServicesFile a AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="bef1c-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="bef1c-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="bef1c-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="bef1c-395">Informace o `stdout` a ladění protokolování pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .</span><span class="sxs-lookup"><span data-stu-id="bef1c-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="bef1c-396">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-396">Console</span></span>

<span data-ttu-id="bef1c-397">`Console`Zprostředkovatel protokoluje výstup do konzoly.</span><span class="sxs-lookup"><span data-stu-id="bef1c-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="bef1c-398">Další informace o zobrazení `Console` protokolů ve vývoji najdete v tématu [protokolování výstupu z dotnet Run a Visual Studio](#dnrvs).</span><span class="sxs-lookup"><span data-stu-id="bef1c-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="bef1c-399">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-399">Debug</span></span>

<span data-ttu-id="bef1c-400">`Debug`Zprostředkovatel zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="bef1c-401">Volání pro `System.Diagnostics.Debug.WriteLine` zápis do `Debug` poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="bef1c-402">V systému Linux `Debug` je umístění protokolu zprostředkovatele závislé na distribuci a může to být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="bef1c-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="bef1c-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="bef1c-403">*/var/log/message*</span></span>
* <span data-ttu-id="bef1c-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="bef1c-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="bef1c-405">Zdroj události</span><span class="sxs-lookup"><span data-stu-id="bef1c-405">Event Source</span></span>

<span data-ttu-id="bef1c-406">`EventSource`Zprostředkovatel zapisuje do zdroje událostí pro různé platformy s názvem `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="bef1c-407">Ve Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="bef1c-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="bef1c-408">nástroje pro trasování dotnet</span><span class="sxs-lookup"><span data-stu-id="bef1c-408">dotnet trace tooling</span></span>

<span data-ttu-id="bef1c-409">Nástroj [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) je globální nástroj CLI pro různé platformy, který umožňuje shromažďování trasování .NET Core pro běžící proces.</span><span class="sxs-lookup"><span data-stu-id="bef1c-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="bef1c-410">Nástroj shromažďuje <xref:Microsoft.Extensions.Logging.EventSource> data zprostředkovatele pomocí <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .</span><span class="sxs-lookup"><span data-stu-id="bef1c-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="bef1c-411">Pokyny k instalaci naleznete v tématu [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="bef1c-412">Shromažďování trasování z aplikace pomocí trasovacích nástrojů dotnet:</span><span class="sxs-lookup"><span data-stu-id="bef1c-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="bef1c-413">Spusťte aplikaci pomocí `dotnet run` příkazu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="bef1c-414">Určení identifikátoru procesu (PID) aplikace .NET Core:</span><span class="sxs-lookup"><span data-stu-id="bef1c-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="bef1c-415">Ve Windows použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="bef1c-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="bef1c-416">Správce úloh (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="bef1c-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="bef1c-417">tasklist – příkaz</span><span class="sxs-lookup"><span data-stu-id="bef1c-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="bef1c-418">Příkaz PowerShellu Get-Process</span><span class="sxs-lookup"><span data-stu-id="bef1c-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="bef1c-419">V systému Linux použijte [příkaz pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="bef1c-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="bef1c-420">Vyhledejte kód PID pro proces, který má stejný název jako sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="bef1c-421">Spusťte `dotnet trace` příkaz.</span><span class="sxs-lookup"><span data-stu-id="bef1c-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="bef1c-422">Obecná syntaxe příkazu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="bef1c-423">Při použití příkazového prostředí PowerShellu uzavřete `--providers` hodnotu do jednoduchých uvozovek ( `'` ):</span><span class="sxs-lookup"><span data-stu-id="bef1c-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="bef1c-424">Na platformách jiných než Windows přidejte `-f speedscope` možnost změny formátu výstupní trasovacího souboru na `speedscope` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="bef1c-425">Klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="bef1c-425">Keyword</span></span> | <span data-ttu-id="bef1c-426">Popis</span><span class="sxs-lookup"><span data-stu-id="bef1c-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="bef1c-427">1</span><span class="sxs-lookup"><span data-stu-id="bef1c-427">1</span></span>       | <span data-ttu-id="bef1c-428">Protokoluje meta události týkající se `LoggingEventSource` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="bef1c-429">Neprotokoluje události z `ILogger` ).</span><span class="sxs-lookup"><span data-stu-id="bef1c-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="bef1c-430">2</span><span class="sxs-lookup"><span data-stu-id="bef1c-430">2</span></span>       | <span data-ttu-id="bef1c-431">Zapne `Message` událost při `ILogger.Log()` volání metody.</span><span class="sxs-lookup"><span data-stu-id="bef1c-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="bef1c-432">Poskytuje informace v programovém (neformátovaném) způsobu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="bef1c-433">4</span><span class="sxs-lookup"><span data-stu-id="bef1c-433">4</span></span>       | <span data-ttu-id="bef1c-434">Zapne `FormatMessage` událost při `ILogger.Log()` volání metody.</span><span class="sxs-lookup"><span data-stu-id="bef1c-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="bef1c-435">Poskytuje formátovanou verzi řetězce informací.</span><span class="sxs-lookup"><span data-stu-id="bef1c-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="bef1c-436">8</span><span class="sxs-lookup"><span data-stu-id="bef1c-436">8</span></span>       | <span data-ttu-id="bef1c-437">Zapne `MessageJson` událost při `ILogger.Log()` volání metody.</span><span class="sxs-lookup"><span data-stu-id="bef1c-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="bef1c-438">Poskytuje reprezentace argumentů ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="bef1c-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="bef1c-439">Úroveň události</span><span class="sxs-lookup"><span data-stu-id="bef1c-439">Event Level</span></span> | <span data-ttu-id="bef1c-440">Popis</span><span class="sxs-lookup"><span data-stu-id="bef1c-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="bef1c-441">0</span><span class="sxs-lookup"><span data-stu-id="bef1c-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="bef1c-442">1</span><span class="sxs-lookup"><span data-stu-id="bef1c-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="bef1c-443">2</span><span class="sxs-lookup"><span data-stu-id="bef1c-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="bef1c-444">3</span><span class="sxs-lookup"><span data-stu-id="bef1c-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="bef1c-445">4</span><span class="sxs-lookup"><span data-stu-id="bef1c-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="bef1c-446">5</span><span class="sxs-lookup"><span data-stu-id="bef1c-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="bef1c-447">`FilterSpecs`položky pro `{Logger Category}` a `{Event Level}` reprezentují další podmínky filtrování protokolů.</span><span class="sxs-lookup"><span data-stu-id="bef1c-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="bef1c-448">Jednotlivé `FilterSpecs` položky oddělte středníkem ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="bef1c-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="bef1c-449">Příklad použití příkazového prostředí systému Windows (**bez** jednoduchých uvozovek kolem `--providers` hodnoty):</span><span class="sxs-lookup"><span data-stu-id="bef1c-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="bef1c-450">Předchozí příkaz se aktivuje:</span><span class="sxs-lookup"><span data-stu-id="bef1c-450">The preceding command activates:</span></span>

   * <span data-ttu-id="bef1c-451">Protokolovací nástroj zdroje událostí pro vytváření formátovaných řetězců ( `4` ) pro chyby ( `2` ).</span><span class="sxs-lookup"><span data-stu-id="bef1c-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="bef1c-452">`Microsoft.AspNetCore.Hosting`protokolování na `Informational` úrovni protokolování ( `4` ).</span><span class="sxs-lookup"><span data-stu-id="bef1c-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="bef1c-453">Kliknutím na klávesu ENTER nebo stisknutím kombinace kláves CTRL + C zastavte nástroje pro trasování dotnet.</span><span class="sxs-lookup"><span data-stu-id="bef1c-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="bef1c-454">Trasování je uloženo s názvem *Trace. nettrace* ve složce, ve které `dotnet trace` je příkaz spuštěn.</span><span class="sxs-lookup"><span data-stu-id="bef1c-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="bef1c-455">Otevřete trasování pomocí [PerfView](#perfview).</span><span class="sxs-lookup"><span data-stu-id="bef1c-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="bef1c-456">Otevřete soubor *Trace. nettrace* a prozkoumejte události trasování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="bef1c-457">Pokud aplikace nevytváří hostitele pomocí `CreateDefaultBuilder` , přidejte [poskytovatele zdroje událostí](#event-source-provider) do konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="bef1c-458">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-458">For more information, see:</span></span>

* <span data-ttu-id="bef1c-459">[Trasování pro nástroj Analýza výkonu (dotnet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentace k .NET Core)</span><span class="sxs-lookup"><span data-stu-id="bef1c-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="bef1c-460">[Trasování pro nástroj Analýza výkonu (dotnet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentace k úložišti GitHub/Diagnostika)</span><span class="sxs-lookup"><span data-stu-id="bef1c-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="bef1c-461">[LoggingEventSource – třída](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (prohlížeč rozhraní .NET API)</span><span class="sxs-lookup"><span data-stu-id="bef1c-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="bef1c-462">[LoggingEventSource reference Source (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): Chcete-li získat zdroj odkazů pro jinou verzi, změňte větev na `release/{Version}` , kde `{Version}` je požadovaná verze ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="bef1c-463">[PerfView](#perfview): užitečné pro zobrazení trasování zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="bef1c-464">PerfView</span><span class="sxs-lookup"><span data-stu-id="bef1c-464">Perfview</span></span>

<span data-ttu-id="bef1c-465">K shromažďování a zobrazování protokolů použijte [Nástroj PerfView](https://github.com/Microsoft/perfview) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="bef1c-466">Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="bef1c-467">Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte řetězec `*Microsoft-Extensions-Logging` do seznamu **další poskytovatelé** .</span><span class="sxs-lookup"><span data-stu-id="bef1c-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="bef1c-468">Nechybíš na `*` začátku řetězce.</span><span class="sxs-lookup"><span data-stu-id="bef1c-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="bef1c-469">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="bef1c-469">Windows EventLog</span></span>

<span data-ttu-id="bef1c-470">`EventLog`Zprostředkovatel odešle výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="bef1c-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="bef1c-471">Na rozdíl od jiných zprostředkovatelů `EventLog` nedědí zprostředkovatel ***not*** výchozí nastavení bez zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="bef1c-472">Pokud `EventLog` není nastavení protokolu zadané, použijí se ve [výchozím nastavení možnost LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span><span class="sxs-lookup"><span data-stu-id="bef1c-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="bef1c-473">Chcete-li protokolovat události nižší než <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , explicitně nastavte úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="bef1c-474">Následující příklad nastaví výchozí úroveň protokolu událostí na <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="bef1c-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="bef1c-475">[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) můžou být předána <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="bef1c-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="bef1c-476">Pokud `null` není zadaný, použijí se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="bef1c-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="bef1c-477">`LogName`: "Application"</span><span class="sxs-lookup"><span data-stu-id="bef1c-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="bef1c-478">`SourceName`: ".NET runtime"</span><span class="sxs-lookup"><span data-stu-id="bef1c-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="bef1c-479">`MachineName`: Používá se název místního počítače.</span><span class="sxs-lookup"><span data-stu-id="bef1c-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="bef1c-480">Následující kód změní `SourceName` výchozí hodnotu z na `".NET Runtime"` `MyLogs` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="bef1c-481">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="bef1c-481">Azure App Service</span></span>

<span data-ttu-id="bef1c-482">Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="bef1c-483">Balíček poskytovatele není zahrnutý ve sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bef1c-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="bef1c-484">Chcete-li použít poskytovatele, přidejte do projektu balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="bef1c-485">Chcete-li konfigurovat nastavení zprostředkovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="bef1c-486">Při nasazení do Azure App Service aplikace používá nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) stránky **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="bef1c-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="bef1c-487">Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="bef1c-488">**Protokolování aplikace (systém souborů)**</span><span class="sxs-lookup"><span data-stu-id="bef1c-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="bef1c-489">**Protokolování aplikace (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="bef1c-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="bef1c-490">Výchozí umístění souborů protokolu je ve složce \*D: Home program \\ \\ Logfiles \\ \* a výchozí název souboru je *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="bef1c-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="bef1c-491">Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="bef1c-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="bef1c-492">Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/mm/dd/hh/{guid} -applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="bef1c-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="bef1c-493">Tento poskytovatel se zaprotokoluje, jenom když se projekt spustí v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="bef1c-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="bef1c-494">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="bef1c-494">Azure log streaming</span></span>

<span data-ttu-id="bef1c-495">Streamování protokolů Azure podporuje zobrazení aktivity protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="bef1c-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="bef1c-496">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="bef1c-496">The app server</span></span>
* <span data-ttu-id="bef1c-497">Webový server</span><span class="sxs-lookup"><span data-stu-id="bef1c-497">The web server</span></span>
* <span data-ttu-id="bef1c-498">Trasování chybných požadavků</span><span class="sxs-lookup"><span data-stu-id="bef1c-498">Failed request tracing</span></span>

<span data-ttu-id="bef1c-499">Konfigurace streamování protokolů Azure:</span><span class="sxs-lookup"><span data-stu-id="bef1c-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="bef1c-500">Na stránce portálu aplikace přejděte na stránku **protokoly App Service** .</span><span class="sxs-lookup"><span data-stu-id="bef1c-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="bef1c-501">Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="bef1c-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="bef1c-502">Vyberte **úroveň**protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-502">Choose the log **Level**.</span></span> <span data-ttu-id="bef1c-503">Toto nastavení platí jenom pro streamování protokolů Azure.</span><span class="sxs-lookup"><span data-stu-id="bef1c-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="bef1c-504">Pokud chcete zobrazit protokoly, přejděte na stránku **streamu protokolu** .</span><span class="sxs-lookup"><span data-stu-id="bef1c-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="bef1c-505">Protokolované zprávy jsou protokolovány pomocí `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bef1c-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="bef1c-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="bef1c-506">Azure Application Insights</span></span>

<span data-ttu-id="bef1c-507">Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span><span class="sxs-lookup"><span data-stu-id="bef1c-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="bef1c-508">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="bef1c-509">Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.</span><span class="sxs-lookup"><span data-stu-id="bef1c-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="bef1c-510">Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="bef1c-511">Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="bef1c-512">Balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) je určen pro ASP.NET 4. x, nikoli ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="bef1c-513">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="bef1c-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="bef1c-514">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="bef1c-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="bef1c-515">[Application Insights pro ASP.NET Core aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou řadu Application Insights telemetrie spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="bef1c-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="bef1c-516">[ApplicationInsightsLoggerProvider pro protokoly .NET Core ILogger](/azure/azure-monitor/app/ilogger) – začněte sem, pokud chcete implementovat poskytovatele protokolování bez zbytku Application Insights telemetrie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="bef1c-517">[Application Insights adaptéry protokolování](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="bef1c-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="bef1c-518">[Instalace, konfigurace a inicializace Application Insights sady SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="bef1c-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="bef1c-519">Zprostředkovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="bef1c-519">Third-party logging providers</span></span>

<span data-ttu-id="bef1c-520">Protokolovací architektury třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bef1c-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="bef1c-521">[elmah.IO](https://elmah.io/) ([úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="bef1c-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="bef1c-522">[GELF](https://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="bef1c-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="bef1c-523">[JSNLog](https://jsnlog.com/) ([úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="bef1c-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="bef1c-524">[KissLog.NET](https://kisslog.net/) ([úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="bef1c-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="bef1c-525">[Log4Net](https://logging.apache.org/log4net/) ([úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="bef1c-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="bef1c-526">[Loggr](https://loggr.net/) ([úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="bef1c-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="bef1c-527">[NLOG](https://nlog-project.org/) ([úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="bef1c-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="bef1c-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([úložiště GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="bef1c-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="bef1c-529">[Sentry](https://sentry.io/welcome/) ([úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="bef1c-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="bef1c-530">[Serilog](https://serilog.net/) ([úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="bef1c-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="bef1c-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="bef1c-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="bef1c-532">Některé architektury třetích stran můžou provádět [sémantické protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="bef1c-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="bef1c-533">Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="bef1c-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="bef1c-534">Přidejte do projektu balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="bef1c-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="bef1c-535">Zavolejte `ILoggerFactory` metodu rozšíření poskytovanou protokolovacím rozhraním.</span><span class="sxs-lookup"><span data-stu-id="bef1c-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="bef1c-536">Další informace najdete v dokumentaci pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="bef1c-537">Microsoft nepodporuje zprostředkovatele protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="bef1c-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="bef1c-538">Konzolová aplikace bez hostitele</span><span class="sxs-lookup"><span data-stu-id="bef1c-538">Non-host console app</span></span>

<span data-ttu-id="bef1c-539">Příklad použití obecného hostitele v aplikaci jiné než webové konzoly najdete v souboru *program.cs* [ukázkové aplikace úlohy na pozadí](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).</span><span class="sxs-lookup"><span data-stu-id="bef1c-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="bef1c-540">Kód protokolování pro aplikace bez obecného hostitele se liší v způsobu [Přidání zprostředkovatelů](#add-providers) a [vytváření protokolovacích](#create-logs)nástrojů.</span><span class="sxs-lookup"><span data-stu-id="bef1c-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="bef1c-541">Zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-541">Logging providers</span></span>

<span data-ttu-id="bef1c-542">V konzolové aplikaci, která není hostitelem, zavolejte `Add{provider name}` metodu rozšíření poskytovatele při vytváření `LoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="bef1c-543">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="bef1c-543">Create logs</span></span>

<span data-ttu-id="bef1c-544">K vytváření protokolů použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="bef1c-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="bef1c-545">Použijte `LoggerFactory` k vytvoření `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="bef1c-546">Následující příklad vytvoří protokolovací `LoggingConsoleApp.Program` Nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="bef1c-547">V následujících příkladech ASP.NET základního nástroje se k vytváření protokolů s jako úroveň používá protokolovací nástroj `Information` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="bef1c-548">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="bef1c-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="bef1c-549">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="bef1c-550">Přihlášení během konstrukce hostitele</span><span class="sxs-lookup"><span data-stu-id="bef1c-550">Log during host construction</span></span>

<span data-ttu-id="bef1c-551">Protokolování během konstrukce hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="bef1c-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="bef1c-552">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="bef1c-552">However, a separate logger can be used.</span></span> <span data-ttu-id="bef1c-553">V následujícím příkladu se k přihlášení používá protokolovací nástroj [Serilog](https://serilog.net/) `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="bef1c-554">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="bef1c-555">Konfigurace služby, která závisí na ILogger</span><span class="sxs-lookup"><span data-stu-id="bef1c-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="bef1c-556">Vložení konstruktoru protokolovacího nástroje do `Startup` sady Works v dřívějších verzích ASP.NET Core, protože pro webového hostitele je vytvořen samostatný kontejner di.</span><span class="sxs-lookup"><span data-stu-id="bef1c-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="bef1c-557">Informace o tom, proč je pro obecného hostitele vytvořeno jenom jeden kontejner, najdete v části [oznámení o změně](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="bef1c-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="bef1c-558">Chcete-li nakonfigurovat službu, která závisí na `ILogger<T>` , použijte injektáže konstruktoru nebo poskytněte metodu pro vytváření.</span><span class="sxs-lookup"><span data-stu-id="bef1c-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="bef1c-559">Přístup k metodě pro vytváření je doporučen pouze v případě, že není k dispozici žádná jiná možnost.</span><span class="sxs-lookup"><span data-stu-id="bef1c-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="bef1c-560">Představte si třeba službu, která potřebuje `ILogger<T>` instanci poskytovanou výrazem di:</span><span class="sxs-lookup"><span data-stu-id="bef1c-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="bef1c-561">Předchozí zvýrazněný kód je [Func](/dotnet/api/system.func-2) , který se spouští při prvním, kdy musí kontejner di vytvořit instanci `MyService` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="bef1c-562">Tímto způsobem můžete získat přístup k libovolné registrované službě.</span><span class="sxs-lookup"><span data-stu-id="bef1c-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="bef1c-563">Vytváření protokolů v Main</span><span class="sxs-lookup"><span data-stu-id="bef1c-563">Create logs in Main</span></span>

<span data-ttu-id="bef1c-564">Následující kód se přihlásí po `Main` získání `ILogger` instance z di po sestavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="bef1c-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="bef1c-565">Vytvořit protokoly při spuštění</span><span class="sxs-lookup"><span data-stu-id="bef1c-565">Create logs in Startup</span></span>

<span data-ttu-id="bef1c-566">Následující kód zapisuje protokoly v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="bef1c-567">Zápis protokolů před dokončením nastavení DI Container v metodě není `Startup.ConfigureServices` podporován:</span><span class="sxs-lookup"><span data-stu-id="bef1c-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="bef1c-568">Vkládání protokolovacího nástroje do `Startup` konstruktoru není podporováno.</span><span class="sxs-lookup"><span data-stu-id="bef1c-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="bef1c-569">Vkládání protokolovacího nástroje do `Startup.ConfigureServices` signatury metody není podporováno.</span><span class="sxs-lookup"><span data-stu-id="bef1c-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="bef1c-570">Důvodem tohoto omezení je, že protokolování závisí na DI a na konfiguraci, která v systému zapíná na DI.</span><span class="sxs-lookup"><span data-stu-id="bef1c-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="bef1c-571">Kontejner DI není nastavený, dokud se `ConfigureServices` nedokončí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="bef1c-572">Informace o konfiguraci služby, která závisí na `ILogger<T>` nebo proč konstruktor, který se má `Startup` přepracovat v dřívějších verzích, najdete v tématu [Konfigurace služby, která závisí na ILogger](#csdi) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="bef1c-573">Žádné metody asynchronního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="bef1c-573">No asynchronous logger methods</span></span>

<span data-ttu-id="bef1c-574">Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="bef1c-575">Pokud je úložiště dat protokolování pomalé, nezapište ho přímo.</span><span class="sxs-lookup"><span data-stu-id="bef1c-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="bef1c-576">Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je přesunout do pomalého úložiště později.</span><span class="sxs-lookup"><span data-stu-id="bef1c-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="bef1c-577">Například když se přihlásíte k SQL Server, neprovádějte přímo v `Log` metodě, protože `Log` metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="bef1c-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="bef1c-578">Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bef1c-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="bef1c-579">Další informace najdete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHubu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="bef1c-580">Změna úrovní protokolu v běžící aplikaci</span><span class="sxs-lookup"><span data-stu-id="bef1c-580">Change log levels in a running app</span></span>

<span data-ttu-id="bef1c-581">Rozhraní API pro protokolování neobsahuje scénář pro změnu úrovní protokolu, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="bef1c-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="bef1c-582">Někteří poskytovatelé konfigurace ale mohou znovu načíst konfiguraci, která se projeví okamžitě při konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="bef1c-583">Například [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider)znovu načte konfiguraci protokolování do výchozího nastavení.</span><span class="sxs-lookup"><span data-stu-id="bef1c-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="bef1c-584">Pokud se konfigurace v kódu změní, když je aplikace spuštěná, může aplikace volat [IConfigurationRoot. Load](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby se aktualizovala konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="bef1c-585">ILogger a ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="bef1c-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="bef1c-586"><xref:Microsoft.Extensions.Logging.ILogger%601>Rozhraní a <xref:Microsoft.Extensions.Logging.ILoggerFactory> implementace jsou součástí .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="bef1c-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="bef1c-587">Jsou také k dispozici v následujících balíčcích NuGet:</span><span class="sxs-lookup"><span data-stu-id="bef1c-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="bef1c-588">Rozhraní jsou v [Microsoft. Extensions. Logging. abstrakce](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="bef1c-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="bef1c-589">Výchozí implementace jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="bef1c-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="bef1c-590">Použít pravidla filtru protokolů v kódu</span><span class="sxs-lookup"><span data-stu-id="bef1c-590">Apply log filter rules in code</span></span>

<span data-ttu-id="bef1c-591">Upřednostňovaným přístupem k nastavení pravidel filtru protokolů je použití [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="bef1c-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="bef1c-592">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="bef1c-593">`logging.AddFilter("System", LogLevel.Debug)`Určuje `System` kategorii a úroveň protokolu `Debug` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="bef1c-594">Filtr se použije pro všechny poskytovatele, protože nebyl nakonfigurovaný konkrétní zprostředkovatel.</span><span class="sxs-lookup"><span data-stu-id="bef1c-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="bef1c-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`upřesňuj</span><span class="sxs-lookup"><span data-stu-id="bef1c-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="bef1c-596">`Debug`Zprostředkovatel protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="bef1c-597">Úroveň protokolu `Information` a vyšší.</span><span class="sxs-lookup"><span data-stu-id="bef1c-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="bef1c-598">Všechny kategorie počínaje `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="bef1c-599">Vytvoření vlastního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="bef1c-599">Create a custom logger</span></span>

<span data-ttu-id="bef1c-600">K přidání vlastního protokolovacího nástroje přidejte <xref:Microsoft.Extensions.Logging.ILoggerProvider> <xref:Microsoft.Extensions.Logging.ILoggerFactory> :</span><span class="sxs-lookup"><span data-stu-id="bef1c-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="bef1c-601">`ILoggerProvider`Vytvoří jednu nebo více `ILogger` instancí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="bef1c-602">`ILogger`Instance používá rozhraní k protokolování informací.</span><span class="sxs-lookup"><span data-stu-id="bef1c-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="bef1c-603">Ukázka vlastní konfigurace protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="bef1c-603">Sample custom logger configuration</span></span>

<span data-ttu-id="bef1c-604">Ukázka:</span><span class="sxs-lookup"><span data-stu-id="bef1c-604">The sample:</span></span>

* <span data-ttu-id="bef1c-605">Je navržený jako velmi základní vzorek, který nastavuje barvu konzoly protokolu podle ID události a úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="bef1c-606">Protokolovací nástroje se obecně nemění podle ID události a nevztahují se na úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="bef1c-607">Vytvoří různé položky konzoly barev na úrovni protokolu a ID události s použitím následujícího typu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="bef1c-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="bef1c-608">Předchozí kód nastaví výchozí úroveň na `Warning` a barvu na `Yellow` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="bef1c-609">Pokud `EventId` je nastavené na 0, protokolují se všechny události.</span><span class="sxs-lookup"><span data-stu-id="bef1c-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="bef1c-610">Vytvoření vlastního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="bef1c-610">Create the custom logger</span></span>

<span data-ttu-id="bef1c-611">`ILogger`Název kategorie implementace je obvykle zdrojem protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="bef1c-612">Například typ, ve kterém je vytvořen protokolovací nástroj:</span><span class="sxs-lookup"><span data-stu-id="bef1c-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="bef1c-613">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="bef1c-613">The preceding code:</span></span>

* <span data-ttu-id="bef1c-614">Vytvoří instanci protokolovacího nástroje pro název kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="bef1c-615">Vrátí `logLevel == _config.LogLevel` `IsEnabled` se změnami, takže každá z nich `logLevel` má jedinečný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="bef1c-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="bef1c-616">Obecně platí, že protokolovací nástroje by měly být povolené i pro všechny vyšší úrovně protokolu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="bef1c-617">Vytvoření vlastního LoggerProvider</span><span class="sxs-lookup"><span data-stu-id="bef1c-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="bef1c-618">`LoggerProvider`Je třída, která vytváří instance protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="bef1c-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="bef1c-619">Možná není nutné vytvořit instanci protokolovacího nástroje na kategorii, ale to dává smysl pro některé protokolovací nástroje, jako je NLog nebo log4net.</span><span class="sxs-lookup"><span data-stu-id="bef1c-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="bef1c-620">V případě potřeby také můžete zvolit různé cíle pro výstup protokolování na kategorii:</span><span class="sxs-lookup"><span data-stu-id="bef1c-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="bef1c-621">V předchozím kódu <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> vytvoří jednu instanci pro `ColorConsoleLogger` název kategorie a uloží ji do [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) ;</span><span class="sxs-lookup"><span data-stu-id="bef1c-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="bef1c-622">Využití a registrace vlastního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="bef1c-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="bef1c-623">Zaregistrujte protokolovací nástroj v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="bef1c-624">Pro předchozí kód zadejte alespoň jednu metodu rozšíření pro `ILoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="bef1c-625">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bef1c-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="bef1c-626">V úložišti [GitHub.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) by se měly vytvářet chyby protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bef1c-627">Tím, že [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bef1c-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bef1c-628">.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="bef1c-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="bef1c-629">V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="bef1c-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="bef1c-630">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bef1c-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="bef1c-631">Přidat zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="bef1c-631">Add providers</span></span>

<span data-ttu-id="bef1c-632">Zprostředkovatel protokolování zobrazuje nebo ukládá protokoly.</span><span class="sxs-lookup"><span data-stu-id="bef1c-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="bef1c-633">Například poskytovatel konzoly zobrazuje protokoly v konzole nástroje a poskytovatel Azure Application Insights je ukládá do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="bef1c-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="bef1c-634">Protokoly lze odesílat do více cílů přidáním více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="bef1c-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="bef1c-635">Chcete-li přidat poskytovatele, zavolejte `Add{provider name}` metodu rozšíření poskytovatele v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bef1c-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="bef1c-636">Předchozí kód vyžaduje odkazy na `Microsoft.Extensions.Logging` a `Microsoft.Extensions.Configuration` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="bef1c-637">Výchozí volání šablony projektu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , které přidává následující zprostředkovatele protokolování:</span><span class="sxs-lookup"><span data-stu-id="bef1c-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="bef1c-638">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-638">Console</span></span>
* <span data-ttu-id="bef1c-639">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-639">Debug</span></span>
* <span data-ttu-id="bef1c-640">EventSource (počínaje ASP.NET Core 2,2)</span><span class="sxs-lookup"><span data-stu-id="bef1c-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="bef1c-641">Pokud používáte `CreateDefaultBuilder` , můžete výchozí poskytovatele nahradit vašimi vlastními možnostmi.</span><span class="sxs-lookup"><span data-stu-id="bef1c-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="bef1c-642">Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> a přidejte poskytovatele, které chcete.</span><span class="sxs-lookup"><span data-stu-id="bef1c-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="bef1c-643">Přečtěte si další informace o [integrovaných poskytovatelích protokolování](#built-in-logging-providers) a [zprostředkovatelích protokolování třetích stran](#third-party-logging-providers) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="bef1c-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="bef1c-644">Vytvořit protokoly</span><span class="sxs-lookup"><span data-stu-id="bef1c-644">Create logs</span></span>

<span data-ttu-id="bef1c-645">K vytváření protokolů použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt.</span><span class="sxs-lookup"><span data-stu-id="bef1c-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="bef1c-646">V rámci webové aplikace nebo hostované služby Získejte `ILogger` od vložení závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="bef1c-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="bef1c-647">V konzolových aplikacích bez hostitele použijte `LoggerFactory` k vytvoření `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="bef1c-648">Následující ASP.NET Core příklad vytvoří protokolovací `TodoApiSample.Pages.AboutModel` Nástroj s jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="bef1c-649">*Kategorie* protokolu je řetězec, který je spojený s každým protokolem.</span><span class="sxs-lookup"><span data-stu-id="bef1c-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="bef1c-650">`ILogger<T>`Instance poskytovaná di vytvoří protokoly, které mají plně kvalifikovaný název typu `T` jako kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="bef1c-651">V následujících příkladech ASP.NET Core a konzolových aplikací se k vytváření protokolů s jako úroveň používá protokolovací nástroj `Information` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="bef1c-652">*Úroveň* protokolu označuje závažnost protokolované události.</span><span class="sxs-lookup"><span data-stu-id="bef1c-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="bef1c-653">[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="bef1c-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="bef1c-654">Vytvořit protokoly při spuštění</span><span class="sxs-lookup"><span data-stu-id="bef1c-654">Create logs in Startup</span></span>

<span data-ttu-id="bef1c-655">Chcete-li zapsat protokoly ve `Startup` třídě, zahrňte `ILogger` parametr do signatury konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="bef1c-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="bef1c-656">Vytvořit protokoly ve třídě program</span><span class="sxs-lookup"><span data-stu-id="bef1c-656">Create logs in the Program class</span></span>

<span data-ttu-id="bef1c-657">Chcete-li zapsat protokoly ve `Program` třídě, Získejte `ILogger` instanci od typu di:</span><span class="sxs-lookup"><span data-stu-id="bef1c-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="bef1c-658">Protokolování během konstrukce hostitele není přímo podporováno.</span><span class="sxs-lookup"><span data-stu-id="bef1c-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="bef1c-659">Lze však použít samostatný protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="bef1c-659">However, a separate logger can be used.</span></span> <span data-ttu-id="bef1c-660">V následujícím příkladu se k přihlášení používá protokolovací nástroj [Serilog](https://serilog.net/) `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="bef1c-661">`AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="bef1c-662">Žádné metody asynchronního protokolovacího nástroje</span><span class="sxs-lookup"><span data-stu-id="bef1c-662">No asynchronous logger methods</span></span>

<span data-ttu-id="bef1c-663">Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="bef1c-664">Pokud je úložiště dat protokolování pomalé, nezapište ho přímo.</span><span class="sxs-lookup"><span data-stu-id="bef1c-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="bef1c-665">Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je později přesunout do pomalého úložiště.</span><span class="sxs-lookup"><span data-stu-id="bef1c-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="bef1c-666">Například pokud se přihlašujete k SQL Server, nechcete provádět přímo v `Log` metodě, protože `Log` metody jsou synchronní.</span><span class="sxs-lookup"><span data-stu-id="bef1c-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="bef1c-667">Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="bef1c-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="bef1c-668">Další informace najdete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHubu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="bef1c-669">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="bef1c-669">Configuration</span></span>

<span data-ttu-id="bef1c-670">Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="bef1c-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="bef1c-671">Formáty souborů (INI, JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="bef1c-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="bef1c-672">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="bef1c-672">Command-line arguments.</span></span>
* <span data-ttu-id="bef1c-673">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-673">Environment variables.</span></span>
* <span data-ttu-id="bef1c-674">Objekty rozhraní .NET v paměti.</span><span class="sxs-lookup"><span data-stu-id="bef1c-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="bef1c-675">Úložiště nešifrovaného [tajného správce](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="bef1c-676">Šifrované uživatelské úložiště, například [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="bef1c-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="bef1c-677">Vlastní poskytovatelé (nainstalováno nebo vytvořeno).</span><span class="sxs-lookup"><span data-stu-id="bef1c-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="bef1c-678">Například konfigurace protokolování je běžně poskytována v `Logging` části soubory nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="bef1c-679">Následující příklad ukazuje obsah typické *appsettings.Development.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="bef1c-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="bef1c-680">`Logging`Vlastnost může mít `LogLevel` vlastnosti zprostředkovatele a log (zobrazí se konzola).</span><span class="sxs-lookup"><span data-stu-id="bef1c-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="bef1c-681">`LogLevel`Vlastnost v rámci `Logging` Určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="bef1c-682">V tomto příkladu se `System` `Microsoft` kategorie přihlásí na `Information` úrovni a všechny ostatní protokoly na `Debug` úrovni.</span><span class="sxs-lookup"><span data-stu-id="bef1c-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="bef1c-683">Další vlastnosti v části `Logging` zadat zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="bef1c-684">Příklad je pro poskytovatele konzoly.</span><span class="sxs-lookup"><span data-stu-id="bef1c-684">The example is for the Console provider.</span></span> <span data-ttu-id="bef1c-685">Pokud zprostředkovatel podporuje [obory protokolů](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="bef1c-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="bef1c-686">Vlastnost poskytovatele (například `Console` v příkladu) může také určovat `LogLevel` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bef1c-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="bef1c-687">`LogLevel`v rámci zprostředkovatele Určuje úrovně, které se mají protokolovat pro daného poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="bef1c-688">Pokud jsou úrovně specifikovány v `Logging.{providername}.LogLevel` , přepíše cokoli nastavené v `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="bef1c-689">Zvažte například následující kód JSON:</span><span class="sxs-lookup"><span data-stu-id="bef1c-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="bef1c-690">V předchozím formátu JSON `Console` nastavení zprostředkovatele přepíše předchozí (výchozí) úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="bef1c-691">Rozhraní API pro protokolování neobsahuje scénář pro změnu úrovní protokolu, když je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="bef1c-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="bef1c-692">Někteří poskytovatelé konfigurace ale mohou znovu načíst konfiguraci, která se projeví okamžitě při konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="bef1c-693">Například [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider), který je přidán nástrojem `CreateDefaultBuilder` ke čtení souborů nastavení, ve výchozím nastavení znovu načte konfiguraci protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="bef1c-694">Pokud se konfigurace v kódu změní, když je aplikace spuštěná, může aplikace volat [IConfigurationRoot. Load](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby se aktualizovala konfigurace protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="bef1c-695">Informace o implementaci zprostředkovatelů konfigurace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="bef1c-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="bef1c-696">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-696">Sample logging output</span></span>

<span data-ttu-id="bef1c-697">Pomocí ukázkového kódu zobrazeného v předchozí části se protokoly zobrazí v konzole nástroje při spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="bef1c-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="bef1c-698">Tady je příklad výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="bef1c-698">Here's an example of console output:</span></span>

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

<span data-ttu-id="bef1c-699">Předchozí protokoly byly vygenerovány vytvořením žádosti HTTP GET do ukázkové aplikace na adrese `http://localhost:5000/api/todo/0` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="bef1c-700">Tady je příklad stejného protokolu, který se zobrazí v okně ladění při spuštění ukázkové aplikace v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="bef1c-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="bef1c-701">Protokoly, které jsou vytvořené voláními, které jsou `ILogger` uvedené v předchozí části, začínají na "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="bef1c-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="bef1c-702">Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bef1c-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="bef1c-703">ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="bef1c-704">Zbývající část tohoto článku vysvětluje několik podrobností a možností protokolování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="bef1c-705">Balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="bef1c-705">NuGet packages</span></span>

<span data-ttu-id="bef1c-706">`ILogger`Rozhraní a `ILoggerFactory` jsou v [Microsoft. Extensions. Loggings. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="bef1c-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="bef1c-707">Kategorie protokolu</span><span class="sxs-lookup"><span data-stu-id="bef1c-707">Log category</span></span>

<span data-ttu-id="bef1c-708">Při `ILogger` vytvoření objektu je pro něj zadána *kategorie* .</span><span class="sxs-lookup"><span data-stu-id="bef1c-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="bef1c-709">Tato kategorie je součástí každé zprávy protokolu vytvořené pomocí této instance `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="bef1c-710">Kategorie může být libovolný řetězec, ale konvence používá název třídy, jako je například "TodoApi. Controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="bef1c-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="bef1c-711">Použijte `ILogger<T>` k získání `ILogger` instance, která používá plně kvalifikovaný název typu `T` jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="bef1c-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="bef1c-712">Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="bef1c-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="bef1c-713">`ILogger<T>`je ekvivalentní volání `CreateLogger` s plně kvalifikovaným názvem typu `T` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="bef1c-714">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-714">Log level</span></span>

<span data-ttu-id="bef1c-715">Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="bef1c-716">Úroveň protokolu označuje závažnost nebo důležitost.</span><span class="sxs-lookup"><span data-stu-id="bef1c-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="bef1c-717">Můžete například zapsat `Information` protokol, pokud metoda končí normálně a `Warning` protokol, když metoda vrátí stavový kód *404 Nenalezeno* .</span><span class="sxs-lookup"><span data-stu-id="bef1c-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="bef1c-718">Následující kód vytváří `Information` a `Warning` protokoluje:</span><span class="sxs-lookup"><span data-stu-id="bef1c-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="bef1c-719">V předchozím kódu `MyLogEvents.GetItem` `MyLogEvents.GetItemNotFound` parametry a jsou [ID události protokolu](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="bef1c-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="bef1c-720">Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody.</span><span class="sxs-lookup"><span data-stu-id="bef1c-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="bef1c-721">Parametry metody jsou vysvětleny v [části Šablona zprávy protokolu](#lmt) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="bef1c-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="bef1c-722">Metody protokolu, které zahrnují úroveň v názvu metody (například `LogInformation` a `LogWarning` ), jsou [metody rozšíření pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="bef1c-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="bef1c-723">Tyto metody volají `Log` metodu, která přebírá `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="bef1c-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="bef1c-724">Metodu můžete zavolat `Log` přímo místo jedné z těchto rozšiřujících metod, ale syntaxe je poměrně složitá.</span><span class="sxs-lookup"><span data-stu-id="bef1c-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="bef1c-725">Další informace naleznete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a ve [zdrojovém kódu rozšíření protokolovacího](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)nástroje.</span><span class="sxs-lookup"><span data-stu-id="bef1c-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="bef1c-726">ASP.NET Core definuje následující úrovně protokolu seřazené od nejnižší k nejvyšší závažnosti.</span><span class="sxs-lookup"><span data-stu-id="bef1c-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="bef1c-727">Trasování = 0</span><span class="sxs-lookup"><span data-stu-id="bef1c-727">Trace = 0</span></span>

  <span data-ttu-id="bef1c-728">Informace, které jsou obvykle užitečné pouze pro ladění.</span><span class="sxs-lookup"><span data-stu-id="bef1c-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="bef1c-729">Tyto zprávy mohou obsahovat citlivá data aplikací, a proto by neměly být povoleny v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="bef1c-730">*Ve výchozím nastavení zakázáno.*</span><span class="sxs-lookup"><span data-stu-id="bef1c-730">*Disabled by default.*</span></span>

* <span data-ttu-id="bef1c-731">Ladění = 1</span><span class="sxs-lookup"><span data-stu-id="bef1c-731">Debug = 1</span></span>

  <span data-ttu-id="bef1c-732">Informace, které mohou být užitečné při vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="bef1c-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="bef1c-733">Příklad: `Entering method Configure with flag set to true.` Povolte `Debug` protokoly úrovně v produkčním prostředí jenom při řešení potíží, protože se jedná o velký objem protokolů.</span><span class="sxs-lookup"><span data-stu-id="bef1c-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="bef1c-734">Informace = 2</span><span class="sxs-lookup"><span data-stu-id="bef1c-734">Information = 2</span></span>

  <span data-ttu-id="bef1c-735">Pro sledování obecného toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="bef1c-736">Tyto protokoly obvykle mají nějakou dlouhodobou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="bef1c-737">Příklad: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="bef1c-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="bef1c-738">Upozornění = 3</span><span class="sxs-lookup"><span data-stu-id="bef1c-738">Warning = 3</span></span>

  <span data-ttu-id="bef1c-739">Pro neobvyklé nebo neočekávané události v toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="bef1c-740">Ty můžou zahrnovat chyby nebo jiné podmínky, které nezpůsobí zastavení aplikace, ale možná bude nutné je prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="bef1c-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="bef1c-741">Zpracování výjimek je běžné místo pro použití `Warning` úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="bef1c-742">Příklad: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="bef1c-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="bef1c-743">Chyba = 4</span><span class="sxs-lookup"><span data-stu-id="bef1c-743">Error = 4</span></span>

  <span data-ttu-id="bef1c-744">Pro chyby a výjimky, které nelze zpracovat.</span><span class="sxs-lookup"><span data-stu-id="bef1c-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="bef1c-745">Tyto zprávy indikují selhání aktuální aktivity nebo operace (jako je aktuální požadavek HTTP), ne při selhání celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="bef1c-746">Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="bef1c-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="bef1c-747">Kritické = 5</span><span class="sxs-lookup"><span data-stu-id="bef1c-747">Critical = 5</span></span>

  <span data-ttu-id="bef1c-748">Chyby, které vyžadují okamžitou pozornost.</span><span class="sxs-lookup"><span data-stu-id="bef1c-748">For failures that require immediate attention.</span></span> <span data-ttu-id="bef1c-749">Příklady: scénáře ztráty dat, nedostatek místa na disku.</span><span class="sxs-lookup"><span data-stu-id="bef1c-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="bef1c-750">Úroveň protokolu můžete použít k určení, kolik výstupu protokolu se zapisuje do konkrétního úložného média nebo okna pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bef1c-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="bef1c-751">Příklad:</span><span class="sxs-lookup"><span data-stu-id="bef1c-751">For example:</span></span>

* <span data-ttu-id="bef1c-752">V produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="bef1c-752">In production:</span></span>
  * <span data-ttu-id="bef1c-753">Protokolování na úrovni `Trace` přes `Information` vytváří velké množství podrobných zpráv protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="bef1c-754">Pokud chcete řídit náklady a nepřekročit limity úložiště dat, protokolujte `Trace` `Information` zprávy na úrovni do úložiště dat s nízkými náklady na velké objemy dat.</span><span class="sxs-lookup"><span data-stu-id="bef1c-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="bef1c-755">Přihlášení `Warning` přes `Critical` úrovně obvykle vytváří méně a menší zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="bef1c-756">Proto náklady a limity úložiště většinou nejsou obavy, což má za následek větší flexibilitu výběru úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="bef1c-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="bef1c-757">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="bef1c-757">During development:</span></span>
  * <span data-ttu-id="bef1c-758">Umožňuje protokolovat `Warning` `Critical` zprávy do konzoly.</span><span class="sxs-lookup"><span data-stu-id="bef1c-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="bef1c-759">`Trace` `Information` Při odstraňování potíží přidejte zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="bef1c-760">Část [filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovateli zpracovává.</span><span class="sxs-lookup"><span data-stu-id="bef1c-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="bef1c-761">ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="bef1c-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="bef1c-762">Příklady protokolů výše v tomto článku vyloučily nižší `Information` úrovně protokolů, takže se `Debug` `Trace` nevytvořily žádné protokoly ani protokoly na úrovni.</span><span class="sxs-lookup"><span data-stu-id="bef1c-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="bef1c-763">Tady je příklad protokolů konzoly vytvořených spuštěním ukázkové aplikace nakonfigurované pro zobrazení `Debug` protokolů:</span><span class="sxs-lookup"><span data-stu-id="bef1c-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="bef1c-764">ID události protokolu</span><span class="sxs-lookup"><span data-stu-id="bef1c-764">Log event ID</span></span>

<span data-ttu-id="bef1c-765">Každý protokol může určovat *ID události*.</span><span class="sxs-lookup"><span data-stu-id="bef1c-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="bef1c-766">Tato ukázková aplikace používá lokálně definovanou `LoggingEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="bef1c-767">ID události přidružuje sadu událostí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-767">An event ID associates a set of events.</span></span> <span data-ttu-id="bef1c-768">Například všechny protokoly související se zobrazením seznamu položek na stránce může být 1001.</span><span class="sxs-lookup"><span data-stu-id="bef1c-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="bef1c-769">Zprostředkovatel protokolování může ukládat ID události v poli ID, ve zprávě protokolování nebo vůbec ne.</span><span class="sxs-lookup"><span data-stu-id="bef1c-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="bef1c-770">Zprostředkovatel ladění nezobrazuje ID událostí.</span><span class="sxs-lookup"><span data-stu-id="bef1c-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="bef1c-771">Poskytovatel konzoly zobrazuje ID událostí v závorkách za kategorií:</span><span class="sxs-lookup"><span data-stu-id="bef1c-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="bef1c-772">Šablona zprávy protokolu</span><span class="sxs-lookup"><span data-stu-id="bef1c-772">Log message template</span></span>

<span data-ttu-id="bef1c-773">Každý protokol Určuje šablonu zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-773">Each log specifies a message template.</span></span> <span data-ttu-id="bef1c-774">Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty.</span><span class="sxs-lookup"><span data-stu-id="bef1c-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="bef1c-775">Použijte názvy zástupných symbolů, nikoli čísla.</span><span class="sxs-lookup"><span data-stu-id="bef1c-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="bef1c-776">Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry slouží k zadání jejich hodnot.</span><span class="sxs-lookup"><span data-stu-id="bef1c-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="bef1c-777">V následujícím kódu si všimněte, že názvy parametrů jsou v šabloně zprávy mimo pořadí:</span><span class="sxs-lookup"><span data-stu-id="bef1c-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="bef1c-778">Tento kód vytvoří zprávu protokolu s hodnotami parametrů v posloupnosti:</span><span class="sxs-lookup"><span data-stu-id="bef1c-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="bef1c-779">Protokolovací rozhraní funguje tímto způsobem, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="bef1c-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="bef1c-780">Samotné argumenty jsou předány do protokolovacího systému, nikoli pouze ze šablony formátované zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="bef1c-781">Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole.</span><span class="sxs-lookup"><span data-stu-id="bef1c-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="bef1c-782">Předpokládejme například, že volání metody protokolovacího nástroje vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="bef1c-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="bef1c-783">Pokud odesíláte protokoly do služby Azure Table Storage, Každá entita tabulky Azure může mít `ID` vlastnosti a `RequestTime` , což zjednodušuje dotazy na data protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="bef1c-784">Dotaz může najít všechny protokoly v rámci určitého `RequestTime` rozsahu, aniž by došlo k analýze časového limitu textové zprávy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="bef1c-785">Protokolování výjimek</span><span class="sxs-lookup"><span data-stu-id="bef1c-785">Logging exceptions</span></span>

<span data-ttu-id="bef1c-786">Metody protokolovacího nástroje mají přetížení, které umožňují předat výjimku, jak je uvedeno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="bef1c-787">Různí poskytovatelé zpracovávají informace o výjimce různými způsoby.</span><span class="sxs-lookup"><span data-stu-id="bef1c-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="bef1c-788">Tady je příklad výstupu poskytovatele ladění z kódu uvedeného výše.</span><span class="sxs-lookup"><span data-stu-id="bef1c-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="bef1c-789">Filtrování protokolů</span><span class="sxs-lookup"><span data-stu-id="bef1c-789">Log filtering</span></span>

<span data-ttu-id="bef1c-790">Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny poskytovatele nebo všechny kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="bef1c-791">Všechny protokoly nižší než minimální úroveň nebudou tomuto poskytovateli předány, takže se nebudou zobrazovat ani ukládat.</span><span class="sxs-lookup"><span data-stu-id="bef1c-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="bef1c-792">Chcete-li potlačit všechny protokoly, zadejte `LogLevel.None` jako minimální úroveň protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="bef1c-793">Celočíselná hodnota `LogLevel.None` je 6, což je více než `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="bef1c-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="bef1c-794">Vytvořit pravidla filtru v konfiguraci</span><span class="sxs-lookup"><span data-stu-id="bef1c-794">Create filter rules in configuration</span></span>

<span data-ttu-id="bef1c-795">Kód šablony projektu volá `CreateDefaultBuilder` k nastavení protokolování pro poskytovatele, konzolu, ladění a EventSource (ASP.NET Core 2,2 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="bef1c-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="bef1c-796">`CreateDefaultBuilder`Metoda nastaví protokolování tak, aby hledalo konfiguraci v `Logging` části, jak je vysvětleno [výše v tomto článku](#configuration).</span><span class="sxs-lookup"><span data-stu-id="bef1c-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="bef1c-797">Konfigurační data určují minimální úrovně protokolu podle poskytovatele a kategorie, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="bef1c-798">Tento kód JSON vytvoří šest pravidel filtru: jeden pro poskytovatele ladění, čtyři pro poskytovatele konzoly a jeden pro všechny poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="bef1c-799">Při vytvoření objektu se vybere jedno pravidlo pro každého poskytovatele `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="bef1c-800">Filtrovat pravidla v kódu</span><span class="sxs-lookup"><span data-stu-id="bef1c-800">Filter rules in code</span></span>

<span data-ttu-id="bef1c-801">Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:</span><span class="sxs-lookup"><span data-stu-id="bef1c-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="bef1c-802">Druhý `AddFilter` Určuje poskytovatele ladění pomocí jeho názvu typu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="bef1c-803">První `AddFilter` se vztahuje na všechny poskytovatele, protože neurčuje typ poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="bef1c-804">Jak se používají pravidla filtrování</span><span class="sxs-lookup"><span data-stu-id="bef1c-804">How filtering rules are applied</span></span>

<span data-ttu-id="bef1c-805">Konfigurační data a `AddFilter` kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="bef1c-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="bef1c-806">Prvních šest přicházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="bef1c-807">Číslo</span><span class="sxs-lookup"><span data-stu-id="bef1c-807">Number</span></span> | <span data-ttu-id="bef1c-808">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="bef1c-808">Provider</span></span>      | <span data-ttu-id="bef1c-809">Kategorie, které začínají na...</span><span class="sxs-lookup"><span data-stu-id="bef1c-809">Categories that begin with ...</span></span>          | <span data-ttu-id="bef1c-810">Minimální úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="bef1c-811">1</span><span class="sxs-lookup"><span data-stu-id="bef1c-811">1</span></span>      | <span data-ttu-id="bef1c-812">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-812">Debug</span></span>         | <span data-ttu-id="bef1c-813">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="bef1c-813">All categories</span></span>                          | <span data-ttu-id="bef1c-814">Informace</span><span class="sxs-lookup"><span data-stu-id="bef1c-814">Information</span></span>       |
| <span data-ttu-id="bef1c-815">2</span><span class="sxs-lookup"><span data-stu-id="bef1c-815">2</span></span>      | <span data-ttu-id="bef1c-816">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-816">Console</span></span>       | <span data-ttu-id="bef1c-817">Microsoft. AspNetCore. Mvc. Razor .. Vnitřních</span><span class="sxs-lookup"><span data-stu-id="bef1c-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="bef1c-818">Upozornění</span><span class="sxs-lookup"><span data-stu-id="bef1c-818">Warning</span></span>           |
| <span data-ttu-id="bef1c-819">3</span><span class="sxs-lookup"><span data-stu-id="bef1c-819">3</span></span>      | <span data-ttu-id="bef1c-820">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-820">Console</span></span>       | <span data-ttu-id="bef1c-821">Microsoft. AspNetCore. Mvc. Razor ..Razor</span><span class="sxs-lookup"><span data-stu-id="bef1c-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="bef1c-822">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-822">Debug</span></span>             |
| <span data-ttu-id="bef1c-823">4</span><span class="sxs-lookup"><span data-stu-id="bef1c-823">4</span></span>      | <span data-ttu-id="bef1c-824">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-824">Console</span></span>       | <span data-ttu-id="bef1c-825">Microsoft. AspNetCore. Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="bef1c-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="bef1c-826">Chyba</span><span class="sxs-lookup"><span data-stu-id="bef1c-826">Error</span></span>             |
| <span data-ttu-id="bef1c-827">5</span><span class="sxs-lookup"><span data-stu-id="bef1c-827">5</span></span>      | <span data-ttu-id="bef1c-828">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-828">Console</span></span>       | <span data-ttu-id="bef1c-829">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="bef1c-829">All categories</span></span>                          | <span data-ttu-id="bef1c-830">Informace</span><span class="sxs-lookup"><span data-stu-id="bef1c-830">Information</span></span>       |
| <span data-ttu-id="bef1c-831">6</span><span class="sxs-lookup"><span data-stu-id="bef1c-831">6</span></span>      | <span data-ttu-id="bef1c-832">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="bef1c-832">All providers</span></span> | <span data-ttu-id="bef1c-833">Všechny kategorie</span><span class="sxs-lookup"><span data-stu-id="bef1c-833">All categories</span></span>                          | <span data-ttu-id="bef1c-834">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-834">Debug</span></span>             |
| <span data-ttu-id="bef1c-835">7</span><span class="sxs-lookup"><span data-stu-id="bef1c-835">7</span></span>      | <span data-ttu-id="bef1c-836">Všichni poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="bef1c-836">All providers</span></span> | <span data-ttu-id="bef1c-837">Systém</span><span class="sxs-lookup"><span data-stu-id="bef1c-837">System</span></span>                                  | <span data-ttu-id="bef1c-838">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-838">Debug</span></span>             |
| <span data-ttu-id="bef1c-839">8</span><span class="sxs-lookup"><span data-stu-id="bef1c-839">8</span></span>      | <span data-ttu-id="bef1c-840">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-840">Debug</span></span>         | <span data-ttu-id="bef1c-841">Partnerský vztah Microsoftu</span><span class="sxs-lookup"><span data-stu-id="bef1c-841">Microsoft</span></span>                               | <span data-ttu-id="bef1c-842">Trasování</span><span class="sxs-lookup"><span data-stu-id="bef1c-842">Trace</span></span>             |

<span data-ttu-id="bef1c-843">Při `ILogger` vytvoření objektu `ILoggerFactory` vybere objekt jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj.</span><span class="sxs-lookup"><span data-stu-id="bef1c-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="bef1c-844">Všechny zprávy napsané `ILogger` instancí jsou filtrovány podle vybraných pravidel.</span><span class="sxs-lookup"><span data-stu-id="bef1c-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="bef1c-845">V dostupných pravidlech se vybere nejpřesnější pravidlo pro jednotlivé dvojice zprostředkovatel a kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="bef1c-846">Následující algoritmus se používá pro každého poskytovatele při `ILogger` vytvoření pro danou kategorii:</span><span class="sxs-lookup"><span data-stu-id="bef1c-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="bef1c-847">Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem.</span><span class="sxs-lookup"><span data-stu-id="bef1c-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="bef1c-848">Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="bef1c-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="bef1c-849">Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="bef1c-850">Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.</span><span class="sxs-lookup"><span data-stu-id="bef1c-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="bef1c-851">Pokud je vybráno více pravidel, vezměte **Poslední** z nich.</span><span class="sxs-lookup"><span data-stu-id="bef1c-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="bef1c-852">Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="bef1c-853">V předchozím seznamu pravidel Předpokládejme, že vytvoříte `ILogger` objekt pro kategorii "Microsoft. AspNetCore. Mvc. Razor . Razor ViewEngine":</span><span class="sxs-lookup"><span data-stu-id="bef1c-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="bef1c-854">Pro poskytovatele ladění platí pravidla 1, 6 a 8.</span><span class="sxs-lookup"><span data-stu-id="bef1c-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="bef1c-855">Pravidlo 8 je nejvíce specifické, takže je to ten vybraný.</span><span class="sxs-lookup"><span data-stu-id="bef1c-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="bef1c-856">Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6.</span><span class="sxs-lookup"><span data-stu-id="bef1c-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="bef1c-857">Pravidlo 3 je nejvíce specifické.</span><span class="sxs-lookup"><span data-stu-id="bef1c-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="bef1c-858">Výsledná `ILogger` instance odesílá protokoly `Trace` úrovně a výše do poskytovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="bef1c-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="bef1c-859">Protokoly `Debug` úrovně a výše jsou odesílány poskytovateli konzoly.</span><span class="sxs-lookup"><span data-stu-id="bef1c-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="bef1c-860">Aliasy zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="bef1c-860">Provider aliases</span></span>

<span data-ttu-id="bef1c-861">Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="bef1c-862">Pro předdefinované poskytovatele použijte následující aliasy:</span><span class="sxs-lookup"><span data-stu-id="bef1c-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="bef1c-863">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-863">Console</span></span>
* <span data-ttu-id="bef1c-864">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-864">Debug</span></span>
* <span data-ttu-id="bef1c-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="bef1c-865">EventSource</span></span>
* <span data-ttu-id="bef1c-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="bef1c-866">EventLog</span></span>
* <span data-ttu-id="bef1c-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="bef1c-867">TraceSource</span></span>
* <span data-ttu-id="bef1c-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="bef1c-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="bef1c-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="bef1c-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="bef1c-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="bef1c-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="bef1c-871">Výchozí minimální úroveň</span><span class="sxs-lookup"><span data-stu-id="bef1c-871">Default minimum level</span></span>

<span data-ttu-id="bef1c-872">Existuje nastavení minimální úrovně, které se projeví pouze v případě, že se pro daného zprostředkovatele a kategorii nepoužijí žádná pravidla z konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="bef1c-873">Následující příklad ukazuje, jak nastavit minimální úroveň:</span><span class="sxs-lookup"><span data-stu-id="bef1c-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="bef1c-874">Pokud nenastavíte explicitně minimální úroveň, výchozí hodnota je `Information` , což znamená, že `Trace` protokoly a `Debug` budou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="bef1c-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="bef1c-875">Funkce filtrování</span><span class="sxs-lookup"><span data-stu-id="bef1c-875">Filter functions</span></span>

<span data-ttu-id="bef1c-876">Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="bef1c-877">Kód ve funkci má přístup k typu poskytovatele, kategorii a úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="bef1c-878">Příklad:</span><span class="sxs-lookup"><span data-stu-id="bef1c-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="bef1c-879">Systémové kategorie a úrovně</span><span class="sxs-lookup"><span data-stu-id="bef1c-879">System categories and levels</span></span>

<span data-ttu-id="bef1c-880">Tady je několik kategorií používaných ASP.NET Core a Entity Framework Core s poznámkami o tom, jaké protokoly se mají od nich očekávat:</span><span class="sxs-lookup"><span data-stu-id="bef1c-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="bef1c-881">Kategorie</span><span class="sxs-lookup"><span data-stu-id="bef1c-881">Category</span></span>                            | <span data-ttu-id="bef1c-882">Poznámky</span><span class="sxs-lookup"><span data-stu-id="bef1c-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="bef1c-883">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="bef1c-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="bef1c-884">Obecná diagnostika ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="bef1c-885">Microsoft. AspNetCore. DataProtection</span><span class="sxs-lookup"><span data-stu-id="bef1c-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="bef1c-886">Které klíče byly zváženy, nalezeny a použity.</span><span class="sxs-lookup"><span data-stu-id="bef1c-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="bef1c-887">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="bef1c-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="bef1c-888">Hostitelé povoleni.</span><span class="sxs-lookup"><span data-stu-id="bef1c-888">Hosts allowed.</span></span> |
| <span data-ttu-id="bef1c-889">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="bef1c-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="bef1c-890">Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění.</span><span class="sxs-lookup"><span data-stu-id="bef1c-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="bef1c-891">Která hostující spouštěcí sestavení byla načtena.</span><span class="sxs-lookup"><span data-stu-id="bef1c-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="bef1c-892">Microsoft. AspNetCore. Mvc</span><span class="sxs-lookup"><span data-stu-id="bef1c-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="bef1c-893">MVC a Razor Diagnostika.</span><span class="sxs-lookup"><span data-stu-id="bef1c-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="bef1c-894">Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce.</span><span class="sxs-lookup"><span data-stu-id="bef1c-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="bef1c-895">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="bef1c-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="bef1c-896">Informace o shodě trasy.</span><span class="sxs-lookup"><span data-stu-id="bef1c-896">Route matching information.</span></span> |
| <span data-ttu-id="bef1c-897">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="bef1c-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="bef1c-898">Připojení – spouštění, zastavování a udržování reakcí na Alive.</span><span class="sxs-lookup"><span data-stu-id="bef1c-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="bef1c-899">Informace o certifikátu HTTPS</span><span class="sxs-lookup"><span data-stu-id="bef1c-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="bef1c-900">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="bef1c-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="bef1c-901">Soubory byly obsluhovány.</span><span class="sxs-lookup"><span data-stu-id="bef1c-901">Files served.</span></span> |
| <span data-ttu-id="bef1c-902">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="bef1c-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="bef1c-903">Obecná diagnostika Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="bef1c-904">Databázová aktivita a konfigurace, detekce změn, migrace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="bef1c-905">Rozsahy protokolů</span><span class="sxs-lookup"><span data-stu-id="bef1c-905">Log scopes</span></span>

 <span data-ttu-id="bef1c-906">*Obor* může seskupit sadu logických operací.</span><span class="sxs-lookup"><span data-stu-id="bef1c-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="bef1c-907">Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady.</span><span class="sxs-lookup"><span data-stu-id="bef1c-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="bef1c-908">Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.</span><span class="sxs-lookup"><span data-stu-id="bef1c-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="bef1c-909">Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodou a trvá, dokud nebude uvolněn.</span><span class="sxs-lookup"><span data-stu-id="bef1c-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="bef1c-910">Použití oboru zabalením volání protokolovacího nástroje v `using` bloku:</span><span class="sxs-lookup"><span data-stu-id="bef1c-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="bef1c-911">Následující kód umožňuje obory pro poskytovatele konzoly:</span><span class="sxs-lookup"><span data-stu-id="bef1c-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="bef1c-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bef1c-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="bef1c-913">`IncludeScopes`Aby bylo možné povolit protokolování na základě oboru, je nutné nakonfigurovat možnost protokolovacího nástroje konzoly.</span><span class="sxs-lookup"><span data-stu-id="bef1c-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="bef1c-914">Informace o konfiguraci najdete v části věnované [konfiguraci](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="bef1c-915">Každá zpráva protokolu obsahuje informace s vymezeným oborem:</span><span class="sxs-lookup"><span data-stu-id="bef1c-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="bef1c-916">Vestavění zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="bef1c-916">Built-in logging providers</span></span>

<span data-ttu-id="bef1c-917">ASP.NET Core dodává následující poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="bef1c-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="bef1c-918">Konzola</span><span class="sxs-lookup"><span data-stu-id="bef1c-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="bef1c-919">Ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="bef1c-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="bef1c-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="bef1c-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="bef1c-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="bef1c-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="bef1c-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="bef1c-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="bef1c-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="bef1c-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="bef1c-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="bef1c-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="bef1c-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="bef1c-926">Informace o stdout a protokolování ladění pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .</span><span class="sxs-lookup"><span data-stu-id="bef1c-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="bef1c-927">Poskytovatel konzoly</span><span class="sxs-lookup"><span data-stu-id="bef1c-927">Console provider</span></span>

<span data-ttu-id="bef1c-928">Balíček poskytovatele [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="bef1c-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="bef1c-929">Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="bef1c-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="bef1c-930">Poskytovatel ladění</span><span class="sxs-lookup"><span data-stu-id="bef1c-930">Debug provider</span></span>

<span data-ttu-id="bef1c-931">Balíček poskytovatele [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) ( `Debug.WriteLine` volání metody).</span><span class="sxs-lookup"><span data-stu-id="bef1c-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="bef1c-932">V systému Linux tento poskytovatel zapisuje protokoly do */var/log/Message*.</span><span class="sxs-lookup"><span data-stu-id="bef1c-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="bef1c-933">Zprostředkovatel zdroje událostí</span><span class="sxs-lookup"><span data-stu-id="bef1c-933">Event Source provider</span></span>

<span data-ttu-id="bef1c-934">Balíček zprostředkovatele [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje do zdroje událostí pro různé platformy s názvem `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="bef1c-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="bef1c-935">Ve Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="bef1c-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="bef1c-936">Poskytovatel zdroje událostí se přidá automaticky, když `CreateDefaultBuilder` se volá k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="bef1c-937">K shromažďování a zobrazování protokolů použijte [Nástroj PerfView](https://github.com/Microsoft/perfview) .</span><span class="sxs-lookup"><span data-stu-id="bef1c-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="bef1c-938">Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="bef1c-939">Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte řetězec `*Microsoft-Extensions-Logging` do seznamu **další poskytovatelé** .</span><span class="sxs-lookup"><span data-stu-id="bef1c-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="bef1c-940">(Na začátku řetězce nechybíš hvězdičku.)</span><span class="sxs-lookup"><span data-stu-id="bef1c-940">(Don't miss the asterisk at the start of the string.)</span></span>

![PerfView další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="bef1c-942">Zprostředkovatel událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="bef1c-942">Windows EventLog provider</span></span>

<span data-ttu-id="bef1c-943">Balíček poskytovatele [Microsoft. Extensions. log. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="bef1c-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="bef1c-944">[AddEventLog přetížení](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="bef1c-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="bef1c-945">Pokud `null` není zadaný, použijí se následující výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="bef1c-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="bef1c-946">`LogName`: "Application"</span><span class="sxs-lookup"><span data-stu-id="bef1c-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="bef1c-947">`SourceName`: ".NET runtime"</span><span class="sxs-lookup"><span data-stu-id="bef1c-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="bef1c-948">`MachineName`: Používá se název místního počítače.</span><span class="sxs-lookup"><span data-stu-id="bef1c-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="bef1c-949">Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level).</span><span class="sxs-lookup"><span data-stu-id="bef1c-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="bef1c-950">Následující příklad nastaví výchozí úroveň protokolu událostí na <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="bef1c-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="bef1c-951">Poskytovatel TraceSource</span><span class="sxs-lookup"><span data-stu-id="bef1c-951">TraceSource provider</span></span>

<span data-ttu-id="bef1c-952">Balíček poskytovatele [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="bef1c-953">[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.</span><span class="sxs-lookup"><span data-stu-id="bef1c-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="bef1c-954">Aby bylo možné používat tohoto poskytovatele, aplikace musí běžet na .NET Framework (spíše než .NET Core).</span><span class="sxs-lookup"><span data-stu-id="bef1c-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="bef1c-955">Poskytovatel může směrovat zprávy na celou řadu [posluchačů](/dotnet/framework/debug-trace-profile/trace-listeners), jako je například <xref:System.Diagnostics.TextWriterTraceListener> použití v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bef1c-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="bef1c-956">Poskytovatel Azure App Service</span><span class="sxs-lookup"><span data-stu-id="bef1c-956">Azure App Service provider</span></span>

<span data-ttu-id="bef1c-957">Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="bef1c-958">Balíček poskytovatele není zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="bef1c-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="bef1c-959">Při cílení na .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` Metapackage přidejte do projektu balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="bef1c-960"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>Přetížení umožňuje předat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> .</span><span class="sxs-lookup"><span data-stu-id="bef1c-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="bef1c-961">Objekt nastavení může přepsat výchozí nastavení, jako je například šablona výstupu protokolování, název objektu BLOB a omezení velikosti souboru.</span><span class="sxs-lookup"><span data-stu-id="bef1c-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="bef1c-962">(*Výstupní šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici při `ILogger` volání metody.)</span><span class="sxs-lookup"><span data-stu-id="bef1c-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="bef1c-963">Když nasadíte aplikaci do App Service, aplikace respektuje nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) stránky **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="bef1c-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="bef1c-964">Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="bef1c-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="bef1c-965">**Protokolování aplikace (systém souborů)**</span><span class="sxs-lookup"><span data-stu-id="bef1c-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="bef1c-966">**Protokolování aplikace (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="bef1c-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="bef1c-967">Výchozí umístění souborů protokolu je ve složce \*D: Home program \\ \\ Logfiles \\ \* a výchozí název souboru je *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="bef1c-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="bef1c-968">Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2.</span><span class="sxs-lookup"><span data-stu-id="bef1c-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="bef1c-969">Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/mm/dd/hh/{guid} -applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="bef1c-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="bef1c-970">Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure.</span><span class="sxs-lookup"><span data-stu-id="bef1c-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="bef1c-971">Nemá žádný vliv, pokud se projekt spouští místně &mdash; , nepíše do místních souborů nebo místního vývojového úložiště pro objekty blob.</span><span class="sxs-lookup"><span data-stu-id="bef1c-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="bef1c-972">Streamování protokolů Azure</span><span class="sxs-lookup"><span data-stu-id="bef1c-972">Azure log streaming</span></span>

<span data-ttu-id="bef1c-973">Streamování protokolů Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:</span><span class="sxs-lookup"><span data-stu-id="bef1c-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="bef1c-974">Aplikační server</span><span class="sxs-lookup"><span data-stu-id="bef1c-974">The app server</span></span>
* <span data-ttu-id="bef1c-975">Webový server</span><span class="sxs-lookup"><span data-stu-id="bef1c-975">The web server</span></span>
* <span data-ttu-id="bef1c-976">Trasování chybných požadavků</span><span class="sxs-lookup"><span data-stu-id="bef1c-976">Failed request tracing</span></span>

<span data-ttu-id="bef1c-977">Konfigurace streamování protokolů Azure:</span><span class="sxs-lookup"><span data-stu-id="bef1c-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="bef1c-978">Na stránce portálu vaší aplikace přejděte na stránku **protokoly App Service** .</span><span class="sxs-lookup"><span data-stu-id="bef1c-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="bef1c-979">Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="bef1c-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="bef1c-980">Vyberte **úroveň**protokolu.</span><span class="sxs-lookup"><span data-stu-id="bef1c-980">Choose the log **Level**.</span></span> <span data-ttu-id="bef1c-981">Toto nastavení platí jenom pro streamování protokolů Azure, ne pro jiné poskytovatele protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bef1c-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="bef1c-982">Pokud chcete zobrazit zprávy aplikace, přejděte na stránku **streamu protokolu** .</span><span class="sxs-lookup"><span data-stu-id="bef1c-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="bef1c-983">Protokolují ho aplikace přes `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bef1c-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="bef1c-984">Protokolování trasování Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="bef1c-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="bef1c-985">Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="bef1c-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="bef1c-986">Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="bef1c-987">Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.</span><span class="sxs-lookup"><span data-stu-id="bef1c-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="bef1c-988">Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bef1c-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="bef1c-989">Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="bef1c-990">Nepoužívejte balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) &mdash; , který je pro ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="bef1c-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="bef1c-991">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="bef1c-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="bef1c-992">Přehled služby Application Insights</span><span class="sxs-lookup"><span data-stu-id="bef1c-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="bef1c-993">[Application Insights pro ASP.NET Core aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou řadu Application Insights telemetrie spolu s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="bef1c-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="bef1c-994">[ApplicationInsightsLoggerProvider pro protokoly .NET Core ILogger](/azure/azure-monitor/app/ilogger) – začněte sem, pokud chcete implementovat poskytovatele protokolování bez zbytku Application Insights telemetrie.</span><span class="sxs-lookup"><span data-stu-id="bef1c-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="bef1c-995">[Application Insights adaptéry protokolování](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="bef1c-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="bef1c-996">[Instalace, konfigurace a inicializace Application Insights sady SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="bef1c-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="bef1c-997">Zprostředkovatelé protokolování třetích stran</span><span class="sxs-lookup"><span data-stu-id="bef1c-997">Third-party logging providers</span></span>

<span data-ttu-id="bef1c-998">Protokolovací architektury třetích stran, které pracují s ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bef1c-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="bef1c-999">[elmah.IO](https://elmah.io/) ([úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="bef1c-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="bef1c-1000">[GELF](https://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="bef1c-1001">[JSNLog](https://jsnlog.com/) ([úložiště GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="bef1c-1002">[KissLog.NET](https://kisslog.net/) ([úložiště GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="bef1c-1003">[Log4Net](https://logging.apache.org/log4net/) ([úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="bef1c-1004">[Loggr](https://loggr.net/) ([úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="bef1c-1005">[NLOG](https://nlog-project.org/) ([úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="bef1c-1006">[Sentry](https://sentry.io/welcome/) ([úložiště GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="bef1c-1007">[Serilog](https://serilog.net/) ([úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="bef1c-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="bef1c-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="bef1c-1009">Některé architektury třetích stran můžou provádět [sémantické protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="bef1c-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="bef1c-1010">Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="bef1c-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="bef1c-1011">Přidejte do projektu balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="bef1c-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="bef1c-1012">Zavolejte `ILoggerFactory` metodu rozšíření poskytovanou protokolovacím rozhraním.</span><span class="sxs-lookup"><span data-stu-id="bef1c-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="bef1c-1013">Další informace najdete v dokumentaci pro každého poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="bef1c-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="bef1c-1014">Microsoft nepodporuje zprostředkovatele protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="bef1c-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bef1c-1015">Další materiály</span><span class="sxs-lookup"><span data-stu-id="bef1c-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end

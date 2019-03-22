---
title: .NET Generic Host
author: guardrex
description: Další informace o obecných hostitele ASP.NET Core, který je zodpovědný za spouštění a životního cyklu správy aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/28/2018
uid: fundamentals/host/generic-host
ms.openlocfilehash: 817b7b3b420520992f18f1f207b412bc4555bdfa
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327388"
---
# <a name="net-generic-host"></a><span data-ttu-id="d5134-103">.NET Generic Host</span><span class="sxs-lookup"><span data-stu-id="d5134-103">.NET Generic Host</span></span>

<span data-ttu-id="d5134-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d5134-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="d5134-105">Aplikace ASP.NET Core, konfigurace a spouštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-105">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="d5134-106">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="d5134-106">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="d5134-107">Tento článek popisuje obecný hostitele ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které není zpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5134-107">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="d5134-108">Účelem obecný hostitele je oddělit kanálu HTTP z hostitele webového rozhraní API umožňující širší škálu scénářů hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-108">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="d5134-109">Zasílání zpráv, úlohy na pozadí a další úlohy jiným protokolem než HTTP podle obecného hostitele je výhodné společné funkce, jako jsou konfigurace, injektáž závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="d5134-109">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="d5134-110">Obecný hostitele je nového v ASP.NET Core 2.1 a není vhodné pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="d5134-110">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="d5134-111">Pro web scénářích hostování, použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="d5134-111">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d5134-112">Obecný hostitele, nahradí webového hostitele v budoucí verzi a fungovat jako primární hostitele rozhraní API scénáře jiným protokolem než HTTP a protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5134-112">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

<span data-ttu-id="d5134-113">Aplikace ASP.NET Core, konfigurace a spouštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-113">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="d5134-114">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="d5134-114">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="d5134-115">Tento článek popisuje obecný hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="d5134-115">This article covers the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="d5134-116">Obecný hostitele se liší od webového hostitele v tom, že odděluje kanálu HTTP z hostitele webového rozhraní API umožňující širší škálu scénářů hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-116">Generic Host differs from Web Host in that it decouples the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="d5134-117">Zasílání zpráv, úlohy na pozadí a další úlohy jiným protokolem než HTTP lze použít obecný hostitele a využívat společné funkce, jako jsou konfigurace, injektáž závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="d5134-117">Messaging, background tasks, and other non-HTTP workloads can use Generic Host and benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="d5134-118">Od verze ASP.NET Core 3.0, obecný hostitele se doporučuje pro úlohy jiným protokolem než HTTP a protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5134-118">Starting in ASP.NET Core 3.0, Generic Host is recommended for both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d5134-119">Implementaci serveru HTTP, pokud je zahrnuto, běží jako implementace <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d5134-119">An HTTP server implementation, if included, runs as an implementation of <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="d5134-120">`IHostedService` je rozhraní, které lze použít pro jiné procesy.</span><span class="sxs-lookup"><span data-stu-id="d5134-120">`IHostedService` is an interface that can be used for other workloads as well.</span></span>

<span data-ttu-id="d5134-121">Webového hostitele už se nedoporučuje pro webové aplikace, ale zůstává k dispozici z důvodu zpětné kompatibility.</span><span class="sxs-lookup"><span data-stu-id="d5134-121">Web Host is no longer recommended for web apps but remains available for backward compatibility.</span></span>

> [!NOTE]
> <span data-ttu-id="d5134-122">Tato zbývající část tohoto článku se ještě neaktualizoval 3.0.</span><span class="sxs-lookup"><span data-stu-id="d5134-122">This remainder of this article has not yet been updated for 3.0.</span></span>

::: moniker-end

<span data-ttu-id="d5134-123">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5134-123">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d5134-124">Při spuštění ukázkové aplikace [Visual Studio Code](https://code.visualstudio.com/), použijte *externí nebo integrovaného terminálu*.</span><span class="sxs-lookup"><span data-stu-id="d5134-124">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="d5134-125">Nejdou spustit v ukázce `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="d5134-125">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="d5134-126">Nastavení konzoly ve Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="d5134-126">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="d5134-127">Otevřít *.vscode/launch.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="d5134-127">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="d5134-128">V **.NET Core spuštění (konzola)** konfigurace, vyhledejte **konzoly** položka.</span><span class="sxs-lookup"><span data-stu-id="d5134-128">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="d5134-129">Nastavte hodnotu na buď `externalTerminal` nebo `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="d5134-129">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="d5134-130">Úvod</span><span class="sxs-lookup"><span data-stu-id="d5134-130">Introduction</span></span>

<span data-ttu-id="d5134-131">Je k dispozici v knihovně obecný hostitele <xref:Microsoft.Extensions.Hosting> obor názvů a poskytuje [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="d5134-131">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="d5134-132">`Microsoft.Extensions.Hosting` Je součástí balíčku [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="d5134-132">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="d5134-133"><xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem k provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="d5134-133"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="d5134-134">Každý `IHostedService` implementace provádí v pořadí podle [službu registrace v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="d5134-134">Each `IHostedService` implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="d5134-135"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> je volána v každé `IHostedService` při spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí reverzní registrace při řádné vypnutí hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-135"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each `IHostedService` when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d5134-136">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="d5134-136">Set up a host</span></span>

<span data-ttu-id="d5134-137"><xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní komponenty, knihovny a aplikace použít k inicializaci, vytvoření a spuštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="d5134-137"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="d5134-138">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d5134-138">Options</span></span>

<span data-ttu-id="d5134-139"><xref:Microsoft.Extensions.Hosting.HostOptions> Konfigurace možností <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="d5134-139"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d5134-140">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="d5134-140">Shutdown timeout</span></span>

<span data-ttu-id="d5134-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d5134-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d5134-142">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="d5134-142">The default value is five seconds.</span></span>

<span data-ttu-id="d5134-143">Následující možnost konfigurace v `Program.Main` zvyšuje výchozí pět druhý vypnutí časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="d5134-143">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="d5134-144">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="d5134-144">Default services</span></span>

<span data-ttu-id="d5134-145">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="d5134-145">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="d5134-146">[Prostředí](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="d5134-146">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="d5134-147">[Konfigurace](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="d5134-147">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="d5134-148"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="d5134-148"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="d5134-149"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="d5134-149"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="d5134-150">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="d5134-150">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="d5134-151">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="d5134-151">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d5134-152">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="d5134-152">Host configuration</span></span>

<span data-ttu-id="d5134-153">Konfigurace hostitele bylo vytvořeno.</span><span class="sxs-lookup"><span data-stu-id="d5134-153">Host configuration is created by:</span></span>

* <span data-ttu-id="d5134-154">Volání metody rozšíření u <xref:Microsoft.Extensions.Hosting.IHostBuilder> nastavit [obsahu kořenové](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="d5134-154">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="d5134-155">Čtení konfigurace od poskytovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d5134-155">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="d5134-156">Rozšiřující metody</span><span class="sxs-lookup"><span data-stu-id="d5134-156">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d5134-157">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="d5134-157">Application key (name)</span></span>

<span data-ttu-id="d5134-158">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) vlastnost nastaven z konfigurace hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-158">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="d5134-159">Pokud chcete explicitně nastavit hodnotu, použijte [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="d5134-159">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="d5134-160">**Klíč**: applicationName</span><span class="sxs-lookup"><span data-stu-id="d5134-160">**Key**: applicationName</span></span>  
<span data-ttu-id="d5134-161">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d5134-161">**Type**: *string*</span></span>  
<span data-ttu-id="d5134-162">**Výchozí**: Název sestavení obsahujícího položku aplikaci přejděte.</span><span class="sxs-lookup"><span data-stu-id="d5134-162">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d5134-163">**Sada s použitím**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="d5134-163">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="d5134-164">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d5134-164">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="d5134-165">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="d5134-165">Content root</span></span>

<span data-ttu-id="d5134-166">Toto nastavení určuje, kde začíná hostitele vyhledávání obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="d5134-166">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="d5134-167">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="d5134-167">**Key**: contentRoot</span></span>  
<span data-ttu-id="d5134-168">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d5134-168">**Type**: *string*</span></span>  
<span data-ttu-id="d5134-169">**Výchozí**: Výchozí hodnota je do složky, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5134-169">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d5134-170">**Sada s použitím**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d5134-170">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="d5134-171">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d5134-171">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d5134-172">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="d5134-172">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="d5134-173">Prostředí</span><span class="sxs-lookup"><span data-stu-id="d5134-173">Environment</span></span>

<span data-ttu-id="d5134-174">Nastaví aplikace [prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d5134-174">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="d5134-175">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="d5134-175">**Key**: environment</span></span>  
<span data-ttu-id="d5134-176">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="d5134-176">**Type**: *string*</span></span>  
<span data-ttu-id="d5134-177">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="d5134-177">**Default**: Production</span></span>  
<span data-ttu-id="d5134-178">**Sada s použitím**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d5134-178">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="d5134-179">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d5134-179">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d5134-180">Prostředí můžete nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d5134-180">The environment can be set to any value.</span></span> <span data-ttu-id="d5134-181">Hodnoty definované v rámci rozhraní zahrnují `Development`, `Staging`, a `Production`.</span><span class="sxs-lookup"><span data-stu-id="d5134-181">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d5134-182">Hodnoty se velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="d5134-182">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="d5134-183">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="d5134-183">ConfigureHostConfiguration</span></span>

<span data-ttu-id="d5134-184">`ConfigureHostConfiguration` používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-184">`ConfigureHostConfiguration` uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="d5134-185">Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5134-185">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="d5134-186">`ConfigureHostConfiguration` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="d5134-186">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d5134-187">Hostitel používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.</span><span class="sxs-lookup"><span data-stu-id="d5134-187">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d5134-188">Konfigurace hostitele automaticky toky pro konfiguraci aplikací ([ConfigureAppConfiguration](#configureappconfiguration) a zbývající části aplikace).</span><span class="sxs-lookup"><span data-stu-id="d5134-188">Host configuration automatically flows to app configuration ([ConfigureAppConfiguration](#configureappconfiguration) and the rest of the app).</span></span>

<span data-ttu-id="d5134-189">Žádní poskytovatelé jsou zahrnuté ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="d5134-189">No providers are included by default.</span></span> <span data-ttu-id="d5134-190">Je nutné explicitně zadat jakýkoli poskytovatelé konfigurace aplikace vyžaduje v `ConfigureHostConfiguration`, včetně:</span><span class="sxs-lookup"><span data-stu-id="d5134-190">You must explicitly specify whatever configuration providers the app requires in `ConfigureHostConfiguration`, including:</span></span>

* <span data-ttu-id="d5134-191">Konfigurace rozhraní File (např. z *hostsettings.json* souboru).</span><span class="sxs-lookup"><span data-stu-id="d5134-191">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="d5134-192">Konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="d5134-192">Environment variable configuration.</span></span>
* <span data-ttu-id="d5134-193">Konfigurace argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d5134-193">Command-line argument configuration.</span></span>
* <span data-ttu-id="d5134-194">Žádné další požadované konfigurace poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="d5134-194">Any other required configuration providers.</span></span>

<span data-ttu-id="d5134-195">Je povolená souboru konfigurace hostitele tak, že zadáte základní cesty aplikace s `SetBasePath` následovanou voláním do jednoho z [souboru poskytovatelé konfigurace](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d5134-195">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="d5134-196">Ukázková aplikace používá soubor JSON, *hostsettings.json*a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> využívat v souboru nastavení konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-196">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="d5134-197">Chcete-li přidat [konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="d5134-197">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="d5134-198">`AddEnvironmentVariables` přijímá volitelný uživatelsky definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="d5134-198">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="d5134-199">Ukázková aplikace používá předponou `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="d5134-199">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="d5134-200">Předpona, která se odebere, když jsou proměnné prostředí načteny.</span><span class="sxs-lookup"><span data-stu-id="d5134-200">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d5134-201">Když je ukázková aplikace hostitel nakonfigurovaný, hodnotu proměnné prostředí pro `PREFIX_ENVIRONMENT` stane hodnota konfigurace hostitele `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="d5134-201">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d5134-202">Během vývoje. při použití [sady Visual Studio](https://www.visualstudio.com/) nebo spuštěním aplikace s `dotnet run`, lze nastavit proměnné prostředí *Properties/launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="d5134-202">During development when using [Visual Studio](https://www.visualstudio.com/) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="d5134-203">V [Visual Studio Code](https://code.visualstudio.com/), lze nastavit proměnné prostředí *.vscode/launch.json* souboru během vývoje.</span><span class="sxs-lookup"><span data-stu-id="d5134-203">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="d5134-204">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d5134-204">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d5134-205">[Příkazový řádek konfigurace](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d5134-205">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="d5134-206">Konfigurace příkazového řádku je tak, aby povolovala argumenty příkazového řádku k přepsání konfigurace poskytované starší poskytovatelé konfigurace přidáni jako poslední.</span><span class="sxs-lookup"><span data-stu-id="d5134-206">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="d5134-207">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d5134-207">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="d5134-208">Je možné poskytnout další konfigurace [applicationName](#application-key-name) a [contentRoot](#content-root) klíče.</span><span class="sxs-lookup"><span data-stu-id="d5134-208">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="d5134-209">Příklad `HostBuilder` konfiguraci pomocí `ConfigureHostConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="d5134-209">Example `HostBuilder` configuration using `ConfigureHostConfiguration`:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="d5134-210">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d5134-210">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d5134-211">Konfigurace aplikace je vytvořen zavoláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace.</span><span class="sxs-lookup"><span data-stu-id="d5134-211">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="d5134-212">`ConfigureAppConfiguration` používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d5134-212">`ConfigureAppConfiguration` uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="d5134-213">`ConfigureAppConfiguration` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="d5134-213">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d5134-214">Aplikace používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.</span><span class="sxs-lookup"><span data-stu-id="d5134-214">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="d5134-215">Konfigurace vytvořil `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="d5134-215">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="d5134-216">Konfigurace aplikace automaticky obdrží konfigurace hostitele poskytované [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d5134-216">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="d5134-217">Příklad aplikace konfigurace pomocí `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="d5134-217">Example app configuration using `ConfigureAppConfiguration`:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="d5134-218">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d5134-218">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="d5134-219">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="d5134-219">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="d5134-220">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="d5134-220">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="d5134-221">Přesunutí souborů nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="d5134-221">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="d5134-222">Ukázková aplikace přesune své soubory JSON aplikace nastavení a *hostsettings.json* následujícím `<Content>` položky:</span><span class="sxs-lookup"><span data-stu-id="d5134-222">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

## <a name="configureservices"></a><span data-ttu-id="d5134-223">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d5134-223">ConfigureServices</span></span>

<span data-ttu-id="d5134-224"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> Přidá do aplikace služeb [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="d5134-224"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d5134-225">`ConfigureServices` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="d5134-225">`ConfigureServices` can be called multiple times with additive results.</span></span>

<span data-ttu-id="d5134-226">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d5134-226">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d5134-227">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d5134-227">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="d5134-228">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá `AddHostedService` metodu rozšíření k přidání služby pro události doby života `LifetimeEventsHostedService`a úlohu na pozadí vypršel časový limit `TimedHostedService`, do aplikace:</span><span class="sxs-lookup"><span data-stu-id="d5134-228">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="d5134-229">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="d5134-229">ConfigureLogging</span></span>

<span data-ttu-id="d5134-230"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci zadaných <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d5134-230"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="d5134-231">`ConfigureLogging` může být volána více než jednou s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="d5134-231">`ConfigureLogging` may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="d5134-232">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="d5134-232">UseConsoleLifetime</span></span>

<span data-ttu-id="d5134-233"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> čeká na `Ctrl+C`/SIGINT nebo SIGTERM a volání <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> zahájíte proces vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d5134-233"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for `Ctrl+C`/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="d5134-234">`UseConsoleLifetime` odblokuje rozšíření, jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d5134-234">`UseConsoleLifetime` unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="d5134-235"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> je zaregistrované předem. jako výchozí implementace životnost.</span><span class="sxs-lookup"><span data-stu-id="d5134-235"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="d5134-236">Poslední doba života zaregistrovaný se používá.</span><span class="sxs-lookup"><span data-stu-id="d5134-236">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="d5134-237">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="d5134-237">Container configuration</span></span>

<span data-ttu-id="d5134-238">Pro podporu připojení v ostatních kontejnerech, může přijmout hostitele <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="d5134-238">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="d5134-239">Poskytuje objekt pro vytváření, které nejsou součástí registrace kontejnerů DI, ale místo toho je vnitřní hostitel používá k vytvoření kontejneru pro konkrétní DI.</span><span class="sxs-lookup"><span data-stu-id="d5134-239">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="d5134-240">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí objekt pro vytváření použitý k vytvoření poskytovatele služby app service.</span><span class="sxs-lookup"><span data-stu-id="d5134-240">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="d5134-241">Konfigurace vlastního kontejneru je spravován <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metody.</span><span class="sxs-lookup"><span data-stu-id="d5134-241">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="d5134-242">`ConfigureContainer` poskytuje možnosti silného typu pro konfiguraci kontejneru nad základního hostitele rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d5134-242">`ConfigureContainer` provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="d5134-243">`ConfigureContainer` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="d5134-243">`ConfigureContainer` can be called multiple times with additive results.</span></span>

<span data-ttu-id="d5134-244">Vytvoření služby kontejneru pro aplikace:</span><span class="sxs-lookup"><span data-stu-id="d5134-244">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="d5134-245">Poskytuje objekt pro vytváření služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="d5134-245">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="d5134-246">Použijte objekt pro vytváření a konfigurace kontejneru vlastní služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d5134-246">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="d5134-247">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="d5134-247">Extensibility</span></span>

<span data-ttu-id="d5134-248">Rozšiřitelnost hostitele se provádí pomocí metody rozšíření na `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d5134-248">Host extensibility is performed with extension methods on `IHostBuilder`.</span></span> <span data-ttu-id="d5134-249">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje `IHostBuilder` implementaci [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) příklad jsme vám ukázali v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d5134-249">The following example shows how an extension method extends an `IHostBuilder` implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="d5134-250">Vytvoří aplikaci `UseHostedService` metodu rozšíření k registraci hostované služby předaný `T`:</span><span class="sxs-lookup"><span data-stu-id="d5134-250">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="d5134-251">Spravovat hostitele</span><span class="sxs-lookup"><span data-stu-id="d5134-251">Manage the host</span></span>

<span data-ttu-id="d5134-252"><xref:Microsoft.Extensions.Hosting.IHost> Implementace je zodpovědný za spouštění a zastavování `IHostedService` implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="d5134-252">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the `IHostedService` implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d5134-253">Spustit</span><span class="sxs-lookup"><span data-stu-id="d5134-253">Run</span></span>

<span data-ttu-id="d5134-254"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spuštění aplikace a blokuje volající vlákno, dokud nebude ukončen hostitele:</span><span class="sxs-lookup"><span data-stu-id="d5134-254"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="d5134-255">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d5134-255">RunAsync</span></span>

<span data-ttu-id="d5134-256"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spuštění aplikace a vrátí `Task` , která se dokončí, když se aktivuje token zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="d5134-256"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a `Task` that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="d5134-257">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d5134-257">RunConsoleAsync</span></span>

<span data-ttu-id="d5134-258"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestaví a spustí hostitele a čeká na `Ctrl+C`/SIGINT nebo SIGTERM vypnout.</span><span class="sxs-lookup"><span data-stu-id="d5134-258"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for `Ctrl+C`/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="d5134-259">Počáteční a StopAsync</span><span class="sxs-lookup"><span data-stu-id="d5134-259">Start and StopAsync</span></span>

<span data-ttu-id="d5134-260"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Hostitel spustí synchronně.</span><span class="sxs-lookup"><span data-stu-id="d5134-260"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="d5134-261"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusí se zastavit hostitele v rámci zadaného časového limitu.</span><span class="sxs-lookup"><span data-stu-id="d5134-261"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="d5134-262">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="d5134-262">StartAsync and StopAsync</span></span>

<span data-ttu-id="d5134-263"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5134-263"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="d5134-264"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> Ukončí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d5134-264"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="d5134-265">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d5134-265">WaitForShutdown</span></span>

<span data-ttu-id="d5134-266"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spouští přes <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako například <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (čeká na `Ctrl+C`/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d5134-266"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for `Ctrl+C`/SIGINT or SIGTERM).</span></span> <span data-ttu-id="d5134-267">`WaitForShutdown` volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d5134-267">`WaitForShutdown` calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="d5134-268">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d5134-268">WaitForShutdownAsync</span></span>

<span data-ttu-id="d5134-269"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí `Task` , která se dokončí při vypnutí se spouští přes daný token a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d5134-269"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a `Task` that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="d5134-270">Vnější ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="d5134-270">External control</span></span>

<span data-ttu-id="d5134-271">Vnější ovládací prvek hostitele lze dosáhnout pomocí metody, které je možné volat externě:</span><span class="sxs-lookup"><span data-stu-id="d5134-271">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="d5134-272"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> volá se na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, která čeká, dokud neskončí než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="d5134-272"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d5134-273">To umožňuje zpoždění spuštění, dokud signalizován externí událostí.</span><span class="sxs-lookup"><span data-stu-id="d5134-273">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d5134-274">IHostingEnvironment rozhraní</span><span class="sxs-lookup"><span data-stu-id="d5134-274">IHostingEnvironment interface</span></span>

<span data-ttu-id="d5134-275"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o aplikaci prvku hostitelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="d5134-275"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="d5134-276">Použít [konstruktor vkládání](xref:fundamentals/dependency-injection) získat `IHostingEnvironment` Chcete-li použít její vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d5134-276">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="d5134-277">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d5134-277">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d5134-278">IApplicationLifetime rozhraní</span><span class="sxs-lookup"><span data-stu-id="d5134-278">IApplicationLifetime interface</span></span>

<span data-ttu-id="d5134-279"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje po spuštění a vypnutí aktivity, včetně žádostí o řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d5134-279"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="d5134-280">Tři vlastnosti v rozhraní jsou tokeny zrušení použije k registraci `Action` metody, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d5134-280">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d5134-281">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="d5134-281">Cancellation Token</span></span> | <span data-ttu-id="d5134-282">Při aktivaci&#8230;</span><span class="sxs-lookup"><span data-stu-id="d5134-282">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="d5134-283">Hostitel plně byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="d5134-283">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="d5134-284">Hostitel se dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d5134-284">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d5134-285">By měl zpracovat všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="d5134-285">All requests should be processed.</span></span> <span data-ttu-id="d5134-286">Vypnutí blokuje, dokud se tato událost se dokončí.</span><span class="sxs-lookup"><span data-stu-id="d5134-286">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="d5134-287">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="d5134-287">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d5134-288">Žádosti se možná ještě zpracovávají.</span><span class="sxs-lookup"><span data-stu-id="d5134-288">Requests may still be processing.</span></span> <span data-ttu-id="d5134-289">Vypnutí blokuje, dokud se tato událost se dokončí.</span><span class="sxs-lookup"><span data-stu-id="d5134-289">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="d5134-290">Vložit konstruktoru `IApplicationLifetime` service do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="d5134-290">Constructor-inject the `IApplicationLifetime` service into any class.</span></span> <span data-ttu-id="d5134-291">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá konstruktor injektáž do `LifetimeEventsHostedService` třídy ( `IHostedService` implementace) k registraci události.</span><span class="sxs-lookup"><span data-stu-id="d5134-291">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an `IHostedService` implementation) to register the events.</span></span>

<span data-ttu-id="d5134-292">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="d5134-292">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="d5134-293"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> požádá o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5134-293"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="d5134-294">Následující třídy používá `StopApplication` řádně vypnout aplikaci při třídy `Shutdown` volání metody:</span><span class="sxs-lookup"><span data-stu-id="d5134-294">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="d5134-295">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5134-295">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
* [<span data-ttu-id="d5134-296">Hostování úložiště ukázek na Githubu</span><span class="sxs-lookup"><span data-stu-id="d5134-296">Hosting repo samples on GitHub</span></span>](https://github.com/aspnet/Hosting/tree/release/2.1/samples)

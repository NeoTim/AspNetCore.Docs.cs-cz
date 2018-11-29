---
title: Obecný hostitele .NET
author: guardrex
description: Další informace o obecných Host v .NET, který je zodpovědný za spouštění a životního cyklu správy aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/28/2018
uid: fundamentals/host/generic-host
ms.openlocfilehash: 4d435984d8169b558ab026ef8541c90f7a2a96b9
ms.sourcegitcommit: 0fc89b80bb1952852ecbcf3c5c156459b02a6ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52618152"
---
# <a name="net-generic-host"></a><span data-ttu-id="c3a2c-103">Obecný hostitele .NET</span><span class="sxs-lookup"><span data-stu-id="c3a2c-103">.NET Generic Host</span></span>

<span data-ttu-id="c3a2c-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c3a2c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c3a2c-105">Konfigurace aplikace .NET core a spouštění *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-105">.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="c3a2c-106">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-106">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="c3a2c-107">Toto téma obsahuje obecný hostitele ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), což je užitečné pro hostování aplikací, které není zpracovávají požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-107">This topic covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is useful for hosting apps that don't process HTTP requests.</span></span> <span data-ttu-id="c3a2c-108">Pro pokrytí webového hostitele (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>), najdete v článku <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-108">For coverage of the Web Host (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>), see <xref:fundamentals/host/web-host>.</span></span>

<span data-ttu-id="c3a2c-109">Cílem obecný hostitele je oddělit kanálu HTTP z hostitele webového rozhraní API umožňující širší škálu scénářů hostitele.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-109">The goal of the Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="c3a2c-110">Zasílání zpráv, úlohy na pozadí a další úlohy jiným protokolem než HTTP podle obecného hostitele, který je výhodné společné funkce, jako jsou konfigurace, injektáž závislostí (DI) a protokolování.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-110">Messaging, background tasks, and other non-HTTP workloads based on the Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="c3a2c-111">Obecný hostitele je nového v ASP.NET Core 2.1 a není vhodné pro scénáře hostování webů.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-111">The Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="c3a2c-112">Pro web scénářích hostování, použijte [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-112">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="c3a2c-113">Obecný hostitele je ve vývoji a nahraďte webového hostitele v budoucí verzi fungovat jako primární hostitele rozhraní API scénáře jiným protokolem než HTTP a protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-113">The Generic Host is under development to replace the Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="c3a2c-114">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3a2c-114">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c3a2c-115">Při spuštění ukázkové aplikace [Visual Studio Code](https://code.visualstudio.com/), použijte *externí nebo integrovaného terminálu*.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-115">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="c3a2c-116">Nejdou spustit v ukázce `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-116">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="c3a2c-117">Nastavení konzoly ve Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-117">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="c3a2c-118">Otevřít *.vscode/launch.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-118">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="c3a2c-119">V **.NET Core spuštění (konzola)** konfigurace, vyhledejte **konzoly** položka.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-119">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="c3a2c-120">Nastavte hodnotu na buď `externalTerminal` nebo `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-120">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="c3a2c-121">Úvod</span><span class="sxs-lookup"><span data-stu-id="c3a2c-121">Introduction</span></span>

<span data-ttu-id="c3a2c-122">Je k dispozici v knihovně obecný hostitele <xref:Microsoft.Extensions.Hosting> obor názvů a poskytuje [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-122">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="c3a2c-123">`Microsoft.Extensions.Hosting` Je součástí balíčku [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-123">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="c3a2c-124"><xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem k provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-124"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="c3a2c-125">Každý `IHostedService` implementace provádí v pořadí podle [službu registrace v ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-125">Each `IHostedService` implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="c3a2c-126"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> je volána v každé `IHostedService` při spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí reverzní registrace při řádné vypnutí hostitele.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-126"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each `IHostedService` when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="c3a2c-127">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="c3a2c-127">Set up a host</span></span>

<span data-ttu-id="c3a2c-128"><xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní komponenty, knihovny a aplikace použít k inicializaci, vytvoření a spuštění hostitele:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-128"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="c3a2c-129">Možnosti</span><span class="sxs-lookup"><span data-stu-id="c3a2c-129">Options</span></span>

<span data-ttu-id="c3a2c-130"><xref:Microsoft.Extensions.Hosting.HostOptions> Konfigurace možností <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-130"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="c3a2c-131">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="c3a2c-131">Shutdown timeout</span></span>

<span data-ttu-id="c3a2c-132"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-132"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="c3a2c-133">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-133">The default value is five seconds.</span></span>

<span data-ttu-id="c3a2c-134">Následující možnost konfigurace v `Program.Main` zvyšuje výchozí pět druhý vypnutí časový limit na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-134">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="c3a2c-135">Výchozí služby</span><span class="sxs-lookup"><span data-stu-id="c3a2c-135">Default services</span></span>

<span data-ttu-id="c3a2c-136">Během inicializace hostitele jsou registrovány následující služby:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-136">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="c3a2c-137">[Prostředí](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="c3a2c-137">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="c3a2c-138">[Konfigurace](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="c3a2c-138">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="c3a2c-139"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="c3a2c-139"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="c3a2c-140"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="c3a2c-140"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="c3a2c-141">[Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="c3a2c-141">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="c3a2c-142">[Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="c3a2c-142">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="c3a2c-143">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="c3a2c-143">Host configuration</span></span>

<span data-ttu-id="c3a2c-144">Konfigurace hostitele bylo vytvořeno.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-144">Host configuration is created by:</span></span>

* <span data-ttu-id="c3a2c-145">Volání metody rozšíření u <xref:Microsoft.Extensions.Hosting.IHostBuilder> nastavit [obsahu kořenové](#content-root) a [prostředí](#environment).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-145">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="c3a2c-146">Čtení konfigurace od poskytovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-146">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="c3a2c-147">Rozšiřující metody</span><span class="sxs-lookup"><span data-stu-id="c3a2c-147">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="c3a2c-148">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="c3a2c-148">Application key (name)</span></span>

<span data-ttu-id="c3a2c-149">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) vlastnost nastaven z konfigurace hostitele během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-149">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="c3a2c-150">Pokud chcete explicitně nastavit hodnotu, použijte [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="c3a2c-150">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="c3a2c-151">**Klíč**: applicationName</span><span class="sxs-lookup"><span data-stu-id="c3a2c-151">**Key**: applicationName</span></span>  
<span data-ttu-id="c3a2c-152">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="c3a2c-152">**Type**: *string*</span></span>  
<span data-ttu-id="c3a2c-153">**Výchozí**: název sestavení obsahující vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-153">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="c3a2c-154">**Sada s použitím**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="c3a2c-154">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="c3a2c-155">**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="c3a2c-155">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="c3a2c-156">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="c3a2c-156">Content root</span></span>

<span data-ttu-id="c3a2c-157">Toto nastavení určuje, kde začíná hostitele vyhledávání obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-157">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="c3a2c-158">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="c3a2c-158">**Key**: contentRoot</span></span>  
<span data-ttu-id="c3a2c-159">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="c3a2c-159">**Type**: *string*</span></span>  
<span data-ttu-id="c3a2c-160">**Výchozí**: výchozí hodnota je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-160">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="c3a2c-161">**Sada s použitím**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="c3a2c-161">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="c3a2c-162">**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="c3a2c-162">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="c3a2c-163">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-163">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="c3a2c-164">Prostředí</span><span class="sxs-lookup"><span data-stu-id="c3a2c-164">Environment</span></span>

<span data-ttu-id="c3a2c-165">Nastaví aplikace [prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-165">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="c3a2c-166">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="c3a2c-166">**Key**: environment</span></span>  
<span data-ttu-id="c3a2c-167">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="c3a2c-167">**Type**: *string*</span></span>  
<span data-ttu-id="c3a2c-168">**Výchozí**: produkčního prostředí</span><span class="sxs-lookup"><span data-stu-id="c3a2c-168">**Default**: Production</span></span>  
<span data-ttu-id="c3a2c-169">**Sada s použitím**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="c3a2c-169">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="c3a2c-170">**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="c3a2c-170">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="c3a2c-171">Prostředí můžete nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-171">The environment can be set to any value.</span></span> <span data-ttu-id="c3a2c-172">Hodnoty definované v rámci rozhraní zahrnují `Development`, `Staging`, a `Production`.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-172">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="c3a2c-173">Hodnoty se velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-173">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="c3a2c-174">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="c3a2c-174">ConfigureHostConfiguration</span></span>

<span data-ttu-id="c3a2c-175">`ConfigureHostConfiguration` používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-175">`ConfigureHostConfiguration` uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="c3a2c-176">Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-176">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="c3a2c-177">`ConfigureHostConfiguration` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-177">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="c3a2c-178">Hostitel používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-178">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="c3a2c-179">Konfigurace hostitele automaticky toky pro konfiguraci aplikací ([ConfigureAppConfiguration](#configureappconfiguration) a zbývající části aplikace).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-179">Host configuration automatically flows to app configuration ([ConfigureAppConfiguration](#configureappconfiguration) and the rest of the app).</span></span>

<span data-ttu-id="c3a2c-180">Žádní poskytovatelé jsou zahrnuté ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-180">No providers are included by default.</span></span> <span data-ttu-id="c3a2c-181">Je nutné explicitně zadat jakýkoli poskytovatelé konfigurace aplikace vyžaduje v `ConfigureHostConfiguration`, včetně:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-181">You must explicitly specify whatever configuration providers the app requires in `ConfigureHostConfiguration`, including:</span></span>

* <span data-ttu-id="c3a2c-182">Konfigurace rozhraní File (např. z *hostsettings.json* souboru).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-182">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="c3a2c-183">Konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-183">Environment variable configuration.</span></span>
* <span data-ttu-id="c3a2c-184">Konfigurace argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-184">Command-line argument configuration.</span></span>
* <span data-ttu-id="c3a2c-185">Žádné další požadované konfigurace poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-185">Any other required configuration providers.</span></span>

<span data-ttu-id="c3a2c-186">Je povolená souboru konfigurace hostitele tak, že zadáte základní cesty aplikace s `SetBasePath` následovanou voláním do jednoho z [souboru poskytovatelé konfigurace](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-186">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="c3a2c-187">Ukázková aplikace používá soubor JSON, *hostsettings.json*a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> využívat v souboru nastavení konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-187">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="c3a2c-188">Chcete-li přidat [konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-188">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="c3a2c-189">`AddEnvironmentVariables` přijímá volitelný uživatelsky definovanou předponu.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-189">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="c3a2c-190">Ukázková aplikace používá předponou `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-190">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="c3a2c-191">Předpona, která se odebere, když jsou proměnné prostředí načteny.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-191">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="c3a2c-192">Když je ukázková aplikace hostitel nakonfigurovaný, hodnotu proměnné prostředí pro `PREFIX_ENVIRONMENT` stane hodnota konfigurace hostitele `environment` klíč.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-192">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="c3a2c-193">Během vývoje. při použití [sady Visual Studio](https://www.visualstudio.com/) nebo spuštěním aplikace s `dotnet run`, lze nastavit proměnné prostředí *Properties/launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-193">During development when using [Visual Studio](https://www.visualstudio.com/) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="c3a2c-194">V [Visual Studio Code](https://code.visualstudio.com/), lze nastavit proměnné prostředí *.vscode/launch.json* souboru během vývoje.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-194">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="c3a2c-195">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-195">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="c3a2c-196">[Příkazový řádek konfigurace](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-196">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="c3a2c-197">Konfigurace příkazového řádku je tak, aby povolovala argumenty příkazového řádku k přepsání konfigurace poskytované starší poskytovatelé konfigurace přidáni jako poslední.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-197">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="c3a2c-198">*hostsettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-198">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="c3a2c-199">Je možné poskytnout další konfigurace [applicationName](#application-key-name) a [contentRoot](#content-root) klíče.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-199">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="c3a2c-200">Příklad `HostBuilder` konfiguraci pomocí `ConfigureHostConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-200">Example `HostBuilder` configuration using `ConfigureHostConfiguration`:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="c3a2c-201">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="c3a2c-201">ConfigureAppConfiguration</span></span>

<span data-ttu-id="c3a2c-202">Konfigurace aplikace je vytvořen zavoláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-202">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="c3a2c-203">`ConfigureAppConfiguration` používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-203">`ConfigureAppConfiguration` uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="c3a2c-204">`ConfigureAppConfiguration` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-204">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="c3a2c-205">Aplikace používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-205">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="c3a2c-206">Konfigurace vytvořil `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-206">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="c3a2c-207">Konfigurace aplikace automaticky obdrží konfigurace hostitele poskytované [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-207">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="c3a2c-208">Příklad aplikace konfigurace pomocí `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-208">Example app configuration using `ConfigureAppConfiguration`:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="c3a2c-209">*appSettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-209">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="c3a2c-210">*appSettings. Development.JSON*:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-210">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="c3a2c-211">*appSettings. Production.JSON*:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-211">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="c3a2c-212">Přesunutí souborů nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-212">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="c3a2c-213">Ukázková aplikace přesune své soubory JSON aplikace nastavení a *hostsettings.json* následujícím `<Content>` položky:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-213">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

## <a name="configureservices"></a><span data-ttu-id="c3a2c-214">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="c3a2c-214">ConfigureServices</span></span>

<span data-ttu-id="c3a2c-215"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> Přidá do aplikace služeb [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-215"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c3a2c-216">`ConfigureServices` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-216">`ConfigureServices` can be called multiple times with additive results.</span></span>

<span data-ttu-id="c3a2c-217">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-217">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="c3a2c-218">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-218">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="c3a2c-219">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá `AddHostedService` metodu rozšíření k přidání služby pro události doby života `LifetimeEventsHostedService`a úlohu na pozadí vypršel časový limit `TimedHostedService`, do aplikace:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-219">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="c3a2c-220">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="c3a2c-220">ConfigureLogging</span></span>

<span data-ttu-id="c3a2c-221"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci zadaných <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-221"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="c3a2c-222">`ConfigureLogging` může být volána více než jednou s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-222">`ConfigureLogging` may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="c3a2c-223">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="c3a2c-223">UseConsoleLifetime</span></span>

<span data-ttu-id="c3a2c-224"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> čeká na `Ctrl+C`/SIGINT nebo SIGTERM a volání <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> zahájíte proces vypnutí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-224"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for `Ctrl+C`/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="c3a2c-225">`UseConsoleLifetime` odblokuje rozšíření, jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-225">`UseConsoleLifetime` unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="c3a2c-226"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> je zaregistrované předem. jako výchozí implementace životnost.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-226"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="c3a2c-227">Poslední doba života zaregistrovaný se používá.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-227">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="c3a2c-228">Konfigurace kontejneru</span><span class="sxs-lookup"><span data-stu-id="c3a2c-228">Container configuration</span></span>

<span data-ttu-id="c3a2c-229">Pro podporu připojení v ostatních kontejnerech, může přijmout hostitele <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory`1>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-229">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory`1>.</span></span> <span data-ttu-id="c3a2c-230">Poskytuje objekt pro vytváření, které nejsou součástí registrace kontejnerů DI, ale místo toho je vnitřní hostitel používá k vytvoření kontejneru pro konkrétní DI.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-230">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="c3a2c-231">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí objekt pro vytváření použitý k vytvoření poskytovatele služby app service.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-231">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="c3a2c-232">Konfigurace vlastního kontejneru je spravován <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metody.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-232">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="c3a2c-233">`ConfigureContainer` poskytuje možnosti silného typu pro konfiguraci kontejneru nad základního hostitele rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-233">`ConfigureContainer` provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="c3a2c-234">`ConfigureContainer` můžete volat vícekrát s přičítáním výsledky.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-234">`ConfigureContainer` can be called multiple times with additive results.</span></span>

<span data-ttu-id="c3a2c-235">Vytvoření služby kontejneru pro aplikace:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-235">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="c3a2c-236">Poskytuje objekt pro vytváření služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-236">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="c3a2c-237">Použijte objekt pro vytváření a konfigurace kontejneru vlastní služby pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-237">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="c3a2c-238">Rozšiřitelnost</span><span class="sxs-lookup"><span data-stu-id="c3a2c-238">Extensibility</span></span>

<span data-ttu-id="c3a2c-239">Rozšiřitelnost hostitele se provádí pomocí metody rozšíření na `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-239">Host extensibility is performed with extension methods on `IHostBuilder`.</span></span> <span data-ttu-id="c3a2c-240">Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje `IHostBuilder` implementaci [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) příklad jsme vám ukázali v <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-240">The following example shows how an extension method extends an `IHostBuilder` implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="c3a2c-241">Vytvoří aplikaci `UseHostedService` metodu rozšíření k registraci hostované služby předaný `T`:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-241">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="c3a2c-242">Spravovat hostitele</span><span class="sxs-lookup"><span data-stu-id="c3a2c-242">Manage the host</span></span>

<span data-ttu-id="c3a2c-243"><xref:Microsoft.Extensions.Hosting.IHost> Implementace je zodpovědný za spouštění a zastavování `IHostedService` implementace, které jsou registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-243">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the `IHostedService` implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="c3a2c-244">Spustit</span><span class="sxs-lookup"><span data-stu-id="c3a2c-244">Run</span></span>

<span data-ttu-id="c3a2c-245"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spuštění aplikace a blokuje volající vlákno, dokud nebude ukončen hostitele:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-245"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="c3a2c-246">RunAsync</span><span class="sxs-lookup"><span data-stu-id="c3a2c-246">RunAsync</span></span>

<span data-ttu-id="c3a2c-247"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spuštění aplikace a vrátí `Task` , která se dokončí, když se aktivuje token zrušení nebo vypnutí:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-247"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a `Task` that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="c3a2c-248">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="c3a2c-248">RunConsoleAsync</span></span>

<span data-ttu-id="c3a2c-249"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestaví a spustí hostitele a čeká na `Ctrl+C`/SIGINT nebo SIGTERM vypnout.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-249"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for `Ctrl+C`/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="c3a2c-250">Počáteční a StopAsync</span><span class="sxs-lookup"><span data-stu-id="c3a2c-250">Start and StopAsync</span></span>

<span data-ttu-id="c3a2c-251"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Hostitel spustí synchronně.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-251"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="c3a2c-252"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusí se zastavit hostitele v rámci zadaného časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-252"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="c3a2c-253">StartAsync a StopAsync</span><span class="sxs-lookup"><span data-stu-id="c3a2c-253">StartAsync and StopAsync</span></span>

<span data-ttu-id="c3a2c-254"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-254"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="c3a2c-255"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> Ukončí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-255"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="c3a2c-256">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="c3a2c-256">WaitForShutdown</span></span>

<span data-ttu-id="c3a2c-257"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spouští přes <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako například <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (čeká na `Ctrl+C`/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="c3a2c-257"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for `Ctrl+C`/SIGINT or SIGTERM).</span></span> <span data-ttu-id="c3a2c-258">`WaitForShutdown` volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-258">`WaitForShutdown` calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="c3a2c-259">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="c3a2c-259">WaitForShutdownAsync</span></span>

<span data-ttu-id="c3a2c-260"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí `Task` , která se dokončí při vypnutí se spouští přes daný token a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-260"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a `Task` that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="c3a2c-261">Vnější ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="c3a2c-261">External control</span></span>

<span data-ttu-id="c3a2c-262">Vnější ovládací prvek hostitele lze dosáhnout pomocí metody, které je možné volat externě:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-262">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="c3a2c-263"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> volá se na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, která čeká, dokud neskončí než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-263"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="c3a2c-264">To umožňuje zpoždění spuštění, dokud signalizován externí událostí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-264">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="c3a2c-265">IHostingEnvironment rozhraní</span><span class="sxs-lookup"><span data-stu-id="c3a2c-265">IHostingEnvironment interface</span></span>

<span data-ttu-id="c3a2c-266"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o aplikaci prvku hostitelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-266"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="c3a2c-267">Použít [konstruktor vkládání](xref:fundamentals/dependency-injection) získat `IHostingEnvironment` Chcete-li použít její vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-267">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="c3a2c-268">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-268">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="c3a2c-269">IApplicationLifetime rozhraní</span><span class="sxs-lookup"><span data-stu-id="c3a2c-269">IApplicationLifetime interface</span></span>

<span data-ttu-id="c3a2c-270"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje po spuštění a vypnutí aktivity, včetně žádostí o řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-270"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="c3a2c-271">Tři vlastnosti v rozhraní jsou tokeny zrušení použije k registraci `Action` metody, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-271">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="c3a2c-272">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="c3a2c-272">Cancellation Token</span></span> | <span data-ttu-id="c3a2c-273">Při aktivaci&#8230;</span><span class="sxs-lookup"><span data-stu-id="c3a2c-273">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="c3a2c-274">Hostitel plně byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-274">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="c3a2c-275">Hostitel se dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-275">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="c3a2c-276">By měl zpracovat všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-276">All requests should be processed.</span></span> <span data-ttu-id="c3a2c-277">Vypnutí blokuje, dokud se tato událost se dokončí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-277">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="c3a2c-278">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-278">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="c3a2c-279">Žádosti se možná ještě zpracovávají.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-279">Requests may still be processing.</span></span> <span data-ttu-id="c3a2c-280">Vypnutí blokuje, dokud se tato událost se dokončí.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-280">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="c3a2c-281">Vložit konstruktoru `IApplicationLifetime` service do libovolné třídy.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-281">Constructor-inject the `IApplicationLifetime` service into any class.</span></span> <span data-ttu-id="c3a2c-282">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá konstruktor injektáž do `LifetimeEventsHostedService` třídy ( `IHostedService` implementace) k registraci události.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-282">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an `IHostedService` implementation) to register the events.</span></span>

<span data-ttu-id="c3a2c-283">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-283">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="c3a2c-284"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> požádá o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c3a2c-284"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="c3a2c-285">Následující třídy používá `StopApplication` řádně vypnout aplikaci při třídy `Shutdown` volání metody:</span><span class="sxs-lookup"><span data-stu-id="c3a2c-285">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c3a2c-286">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c3a2c-286">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
* [<span data-ttu-id="c3a2c-287">Hostování úložiště ukázek na Githubu</span><span class="sxs-lookup"><span data-stu-id="c3a2c-287">Hosting repo samples on GitHub</span></span>](https://github.com/aspnet/Hosting/tree/release/2.1/samples)

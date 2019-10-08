---
title: Zjišťovat změny pomocí tokenů změn v ASP.NET Core
author: guardrex
description: Naučte se používat změny tokenů ke sledování změn.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: bb30d7a4c7dc82200821c60a49c314b246562111
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007208"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="c1c4b-103">Zjišťovat změny pomocí tokenů změn v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1c4b-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="c1c4b-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1c4b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1c4b-105">*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="c1c4b-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1c4b-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="c1c4b-107">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="c1c4b-107">IChangeToken interface</span></span>

<span data-ttu-id="c1c4b-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> šíří oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="c1c4b-109">`IChangeToken` se nachází v oboru názvů <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c1c4b-110">Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-110">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="c1c4b-111">`IChangeToken` má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="c1c4b-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="c1c4b-113">Pokud je `ActiveChangedCallbacks` nastavená na `false`, zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="c1c4b-114">Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="c1c4b-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> přijímá hodnotu, která indikuje, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="c1c4b-116">Rozhraní `IChangeToken` zahrnuje metodu [RegisterChangeCallback (Action @ no__t-2Object >, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="c1c4b-117">před vyvoláním zpětného volání musí být nastavená hodnota `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="c1c4b-118">ChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="c1c4b-118">ChangeToken class</span></span>

<span data-ttu-id="c1c4b-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="c1c4b-120">`ChangeToken` se nachází v oboru názvů <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c1c4b-121">Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-121">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="c1c4b-122">Metoda [ChangeToken. prochange (Func @ no__t-1IChangeToken >, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje `Action` pro volání při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="c1c4b-123">`Func<IChangeToken>` vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="c1c4b-124">`Action` se volá, když se změní token.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="c1c4b-125">Přetížení [ChangeToken. Change @ no__t-1TState > (Func @ no__t-2IChangeToken >, Action @ no__t-3TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) převezme další parametr `TState`, který se předává do příjemce tokenu `Action`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="c1c4b-126">`OnChange` vrátí <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="c1c4b-127">Volání <xref:System.IDisposable.Dispose*> zastaví naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="c1c4b-128">Příklad použití tokenů změny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1c4b-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="c1c4b-129">Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="c1c4b-130">Pro sledování změn souborů vytvoří metoda <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="c1c4b-131">k položkám mezipaměti lze přidat tokeny `IChangeToken` a aktivovat tak vyřazení mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="c1c4b-132">Pro změny `TOptions` má výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> přetížení, které přijímá jednu nebo více instancí <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="c1c4b-133">Každá instance vrátí `IChangeToken` pro registraci zpětného volání upozornění na změnu pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="c1c4b-134">Monitorování pro změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="c1c4b-134">Monitor for configuration changes</span></span>

<span data-ttu-id="c1c4b-135">Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="c1c4b-136">Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, která přijímá parametr `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="c1c4b-137">`reloadOnChange` označuje, zda má být konfigurace znovu načtena při změně souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="c1c4b-138">Toto nastavení se zobrazí v metodě <xref:Microsoft.Extensions.Hosting.Host> pro usnadnění <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-138">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="c1c4b-139">Konfigurace na základě souborů je reprezentována <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="c1c4b-140">`FileConfigurationSource` používá ke sledování souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="c1c4b-141">Ve výchozím nastavení je `IFileMonitor` k dispozici pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, který pomocí <xref:System.IO.FileSystemWatcher> monitoruje změny konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="c1c4b-142">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="c1c4b-143">Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spouštějí vlastní kód @ no__t-1The vzorová aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="c1c4b-144">@No__t konfiguračního souboru – 0 může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="c1c4b-145">Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="c1c4b-146">Ukázka používá algoritmus hash souborů SHA1.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="c1c4b-147">Opakování je implementováno pomocí exponenciálního zálohování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="c1c4b-148">Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="c1c4b-149">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="c1c4b-150">Jednoduchý token změny spuštění</span><span class="sxs-lookup"><span data-stu-id="c1c4b-150">Simple startup change token</span></span>

<span data-ttu-id="c1c4b-151">Registrace příjemce tokenu `Action` zpětného volání pro oznámení o změně v tokenu pro opětovné načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="c1c4b-152">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="c1c4b-153">`config.GetReloadToken()` poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="c1c4b-154">Zpětné volání je metoda `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="c1c4b-155">@No__t-0 zpětného volání se používá k předání do `IWebHostEnvironment`, což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appSettings. Vývoj. JSON* při ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-155">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="c1c4b-156">Hodnoty hash souborů se používají k zabránění spuštění příkazu `WriteConsole` v důsledku více než zpětných volání tokenu v případě, že se konfigurační soubor změnil pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="c1c4b-157">Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="c1c4b-158">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="c1c4b-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="c1c4b-159">Ukázka implementuje:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-159">The sample implements:</span></span>

* <span data-ttu-id="c1c4b-160">Základní monitorování spouštěcího tokenu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="c1c4b-161">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-161">Monitoring as a service.</span></span>
* <span data-ttu-id="c1c4b-162">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="c1c4b-163">Ukázka vytvoří rozhraní `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="c1c4b-164">*Rozšíření/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="c1c4b-165">Konstruktor implementované třídy, `ConfigurationMonitor`, zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="c1c4b-166">@no__t – 0 dodá token.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="c1c4b-167">`InvokeChanged` je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="c1c4b-168">@No__t-0 v této instanci je odkaz na instanci `IConfigurationMonitor`, která se používá pro přístup ke stavu monitorování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="c1c4b-169">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-169">Two properties are used:</span></span>

* <span data-ttu-id="c1c4b-170">`MonitoringEnabled` &ndash; označuje, zda by zpětné volání mělo spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="c1c4b-171">`CurrentState` &ndash; popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="c1c4b-172">Metoda `InvokeChanged` je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="c1c4b-173">Nespustí svůj kód, pokud `MonitoringEnabled` není `true`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="c1c4b-174">Vypíše aktuální `state` ve výstupu `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="c1c4b-175">Instance `ConfigurationMonitor` je registrována jako služba v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="c1c4b-176">Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="c1c4b-177">Instance `IConfigurationMonitor` je vložena do `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="c1c4b-178">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c1c4b-179">Monitorování konfigurace (`_monitor`) slouží k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="c1c4b-180">Když se aktivuje `OnPostStartMonitoring`, monitorování je povolené a aktuální stav se vymaže.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="c1c4b-181">Když se aktivuje `OnPostStopMonitoring`, monitorování je zakázané a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="c1c4b-182">Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="c1c4b-183">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="c1c4b-184">Monitorovat změny souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="c1c4b-184">Monitor cached file changes</span></span>

<span data-ttu-id="c1c4b-185">Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="c1c4b-186">Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="c1c4b-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="c1c4b-187">Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="c1c4b-188">Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="c1c4b-189">Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="c1c4b-190">Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="c1c4b-191">Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="c1c4b-192">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="c1c4b-193">Ukázka používá `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="c1c4b-194">Vrátit obsah souboru</span><span class="sxs-lookup"><span data-stu-id="c1c4b-194">Return file content.</span></span>
* <span data-ttu-id="c1c4b-195">Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="c1c4b-196">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="c1c4b-197">Pro zpracování vyhledávání souborů uložených v mezipaměti se vytvoří `FileService`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="c1c4b-198">Volání metody `GetFileContent` se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Služba/služba. cs*).</span><span class="sxs-lookup"><span data-stu-id="c1c4b-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="c1c4b-199">Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="c1c4b-200">Obsah souboru se získá pomocí `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="c1c4b-201">Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="c1c4b-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="c1c4b-202">Zpětné volání tokenu se aktivuje při úpravě souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="c1c4b-203">Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) .</span><span class="sxs-lookup"><span data-stu-id="c1c4b-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="c1c4b-204">Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="c1c4b-205">V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-205">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="c1c4b-206">`IWebHostEnvironment.ContentRootFileProvider` se používá k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider>, ukazujícího na @no__t aplikace – 2.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-206">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="c1c4b-207">@No__t-0 se získá s použitím [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="c1c4b-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="c1c4b-208">@No__t-0 je registrován v kontejneru služby spolu se službou mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="c1c4b-209">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="c1c4b-210">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="c1c4b-211">V metodě @no__t 0 stránky indexu (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="c1c4b-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="c1c4b-212">CompositeChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="c1c4b-212">CompositeChangeToken class</span></span>

<span data-ttu-id="c1c4b-213">Pro reprezentaci jedné nebo více instancí @no__t 0 v jednom objektu použijte třídu <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="c1c4b-214">`HasChanged` v sestavách složeného tokenu `true`, pokud je @no__t 2 `true`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="c1c4b-215">`ActiveChangeCallbacks` v sestavách složeného tokenu `true`, pokud je @no__t 2 `true`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="c1c4b-216">Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c1c4b-217">*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-217">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="c1c4b-218">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1c4b-218">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="c1c4b-219">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="c1c4b-219">IChangeToken interface</span></span>

<span data-ttu-id="c1c4b-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> šíří oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="c1c4b-221">`IChangeToken` se nachází v oboru názvů <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-221">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c1c4b-222">Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="c1c4b-222">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="c1c4b-223">`IChangeToken` má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-223">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="c1c4b-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="c1c4b-225">Pokud je `ActiveChangedCallbacks` nastavená na `false`, zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-225">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="c1c4b-226">Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-226">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="c1c4b-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> přijímá hodnotu, která indikuje, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="c1c4b-228">Rozhraní `IChangeToken` zahrnuje metodu [RegisterChangeCallback (Action @ no__t-2Object >, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-228">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="c1c4b-229">před vyvoláním zpětného volání musí být nastavená hodnota `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-229">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="c1c4b-230">ChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="c1c4b-230">ChangeToken class</span></span>

<span data-ttu-id="c1c4b-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="c1c4b-232">`ChangeToken` se nachází v oboru názvů <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-232">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="c1c4b-233">Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="c1c4b-233">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="c1c4b-234">Metoda [ChangeToken. prochange (Func @ no__t-1IChangeToken >, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje `Action` pro volání při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-234">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="c1c4b-235">`Func<IChangeToken>` vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-235">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="c1c4b-236">`Action` se volá, když se změní token.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-236">`Action` is called when the token changes.</span></span>

<span data-ttu-id="c1c4b-237">Přetížení [ChangeToken. Change @ no__t-1TState > (Func @ no__t-2IChangeToken >, Action @ no__t-3TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) převezme další parametr `TState`, který se předává do příjemce tokenu `Action`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-237">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="c1c4b-238">`OnChange` vrátí <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-238">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="c1c4b-239">Volání <xref:System.IDisposable.Dispose*> zastaví naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-239">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="c1c4b-240">Příklad použití tokenů změny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1c4b-240">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="c1c4b-241">Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-241">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="c1c4b-242">Pro sledování změn souborů vytvoří metoda <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-242">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="c1c4b-243">k položkám mezipaměti lze přidat tokeny `IChangeToken` a aktivovat tak vyřazení mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-243">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="c1c4b-244">Pro změny `TOptions` má výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> přetížení, které přijímá jednu nebo více instancí <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-244">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="c1c4b-245">Každá instance vrátí `IChangeToken` pro registraci zpětného volání upozornění na změnu pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-245">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="c1c4b-246">Monitorování pro změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="c1c4b-246">Monitor for configuration changes</span></span>

<span data-ttu-id="c1c4b-247">Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-247">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="c1c4b-248">Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, která přijímá parametr `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-248">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="c1c4b-249">`reloadOnChange` označuje, zda má být konfigurace znovu načtena při změně souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-249">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="c1c4b-250">Toto nastavení se zobrazí v metodě <xref:Microsoft.AspNetCore.WebHost> pro usnadnění <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-250">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="c1c4b-251">Konfigurace na základě souborů je reprezentována <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-251">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="c1c4b-252">`FileConfigurationSource` používá ke sledování souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-252">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="c1c4b-253">Ve výchozím nastavení je `IFileMonitor` k dispozici pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, který pomocí <xref:System.IO.FileSystemWatcher> monitoruje změny konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-253">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="c1c4b-254">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-254">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="c1c4b-255">Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spouštějí vlastní kód @ no__t-1The vzorová aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-255">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="c1c4b-256">@No__t konfiguračního souboru – 0 může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-256">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="c1c4b-257">Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-257">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="c1c4b-258">Ukázka používá algoritmus hash souborů SHA1.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-258">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="c1c4b-259">Opakování je implementováno pomocí exponenciálního zálohování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-259">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="c1c4b-260">Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-260">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="c1c4b-261">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-261">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="c1c4b-262">Jednoduchý token změny spuštění</span><span class="sxs-lookup"><span data-stu-id="c1c4b-262">Simple startup change token</span></span>

<span data-ttu-id="c1c4b-263">Registrace příjemce tokenu `Action` zpětného volání pro oznámení o změně v tokenu pro opětovné načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-263">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="c1c4b-264">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-264">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="c1c4b-265">`config.GetReloadToken()` poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-265">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="c1c4b-266">Zpětné volání je metoda `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-266">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="c1c4b-267">@No__t-0 zpětného volání se používá k předání do `IHostingEnvironment`, což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appSettings. Vývoj. JSON* při ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-267">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="c1c4b-268">Hodnoty hash souborů se používají k zabránění spuštění příkazu `WriteConsole` v důsledku více než zpětných volání tokenu v případě, že se konfigurační soubor změnil pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-268">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="c1c4b-269">Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-269">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="c1c4b-270">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="c1c4b-270">Monitor configuration changes as a service</span></span>

<span data-ttu-id="c1c4b-271">Ukázka implementuje:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-271">The sample implements:</span></span>

* <span data-ttu-id="c1c4b-272">Základní monitorování spouštěcího tokenu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-272">Basic startup token monitoring.</span></span>
* <span data-ttu-id="c1c4b-273">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-273">Monitoring as a service.</span></span>
* <span data-ttu-id="c1c4b-274">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-274">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="c1c4b-275">Ukázka vytvoří rozhraní `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-275">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="c1c4b-276">*Rozšíření/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-276">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="c1c4b-277">Konstruktor implementované třídy, `ConfigurationMonitor`, zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-277">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="c1c4b-278">@no__t – 0 dodá token.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-278">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="c1c4b-279">`InvokeChanged` je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-279">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="c1c4b-280">@No__t-0 v této instanci je odkaz na instanci `IConfigurationMonitor`, která se používá pro přístup ke stavu monitorování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-280">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="c1c4b-281">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-281">Two properties are used:</span></span>

* <span data-ttu-id="c1c4b-282">`MonitoringEnabled` &ndash; označuje, zda by zpětné volání mělo spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-282">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="c1c4b-283">`CurrentState` &ndash; popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-283">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="c1c4b-284">Metoda `InvokeChanged` je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-284">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="c1c4b-285">Nespustí svůj kód, pokud `MonitoringEnabled` není `true`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-285">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="c1c4b-286">Vypíše aktuální `state` ve výstupu `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-286">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="c1c4b-287">Instance `ConfigurationMonitor` je registrována jako služba v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-287">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="c1c4b-288">Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-288">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="c1c4b-289">Instance `IConfigurationMonitor` je vložena do `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-289">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="c1c4b-290">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-290">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c1c4b-291">Monitorování konfigurace (`_monitor`) slouží k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-291">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="c1c4b-292">Když se aktivuje `OnPostStartMonitoring`, monitorování je povolené a aktuální stav se vymaže.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-292">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="c1c4b-293">Když se aktivuje `OnPostStopMonitoring`, monitorování je zakázané a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-293">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="c1c4b-294">Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-294">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="c1c4b-295">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-295">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="c1c4b-296">Monitorovat změny souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="c1c4b-296">Monitor cached file changes</span></span>

<span data-ttu-id="c1c4b-297">Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-297">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="c1c4b-298">Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="c1c4b-298">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="c1c4b-299">Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-299">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="c1c4b-300">Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-300">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="c1c4b-301">Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-301">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="c1c4b-302">Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-302">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="c1c4b-303">Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-303">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="c1c4b-304">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-304">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="c1c4b-305">Ukázka používá `GetFileContent` do:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-305">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="c1c4b-306">Vrátit obsah souboru</span><span class="sxs-lookup"><span data-stu-id="c1c4b-306">Return file content.</span></span>
* <span data-ttu-id="c1c4b-307">Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-307">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="c1c4b-308">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-308">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="c1c4b-309">Pro zpracování vyhledávání souborů uložených v mezipaměti se vytvoří `FileService`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-309">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="c1c4b-310">Volání metody `GetFileContent` se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Služba/služba. cs*).</span><span class="sxs-lookup"><span data-stu-id="c1c4b-310">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="c1c4b-311">Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-311">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="c1c4b-312">Obsah souboru se získá pomocí `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-312">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="c1c4b-313">Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="c1c4b-313">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="c1c4b-314">Zpětné volání tokenu se aktivuje při úpravě souboru.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-314">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="c1c4b-315">Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) .</span><span class="sxs-lookup"><span data-stu-id="c1c4b-315">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="c1c4b-316">Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-316">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="c1c4b-317">V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-317">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="c1c4b-318">[IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> směřujícího do @no__t aplikace-2.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="c1c4b-319">@No__t-0 se získá s použitím [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="c1c4b-319">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="c1c4b-320">@No__t-0 je registrován v kontejneru služby spolu se službou mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-320">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="c1c4b-321">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c1c4b-321">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="c1c4b-322">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-322">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="c1c4b-323">V metodě @no__t 0 stránky indexu (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="c1c4b-323">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="c1c4b-324">CompositeChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="c1c4b-324">CompositeChangeToken class</span></span>

<span data-ttu-id="c1c4b-325">Pro reprezentaci jedné nebo více instancí @no__t 0 v jednom objektu použijte třídu <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-325">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="c1c4b-326">`HasChanged` v sestavách složeného tokenu `true`, pokud je @no__t 2 `true`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-326">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="c1c4b-327">`ActiveChangeCallbacks` v sestavách složeného tokenu `true`, pokud je @no__t 2 `true`.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-327">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="c1c4b-328">Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.</span><span class="sxs-lookup"><span data-stu-id="c1c4b-328">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c1c4b-329">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c1c4b-329">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

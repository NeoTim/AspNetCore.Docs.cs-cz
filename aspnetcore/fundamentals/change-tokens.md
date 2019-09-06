---
title: Zjišťovat změny pomocí tokenů změn v ASP.NET Core
author: guardrex
description: Naučte se používat změny tokenů ke sledování změn.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/27/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 86cde7b60f5c398fc6bb215b593643c05565cf3c
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384712"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="f899f-103">Zjišťovat změny pomocí tokenů změn v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f899f-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="f899f-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f899f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f899f-105">*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="f899f-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="f899f-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f899f-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="f899f-107">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="f899f-107">IChangeToken interface</span></span>

<span data-ttu-id="f899f-108"><xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="f899f-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="f899f-109">`IChangeToken`nachází se <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> v oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f899f-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="f899f-110">Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f899f-110">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="f899f-111">`IChangeToken`má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f899f-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="f899f-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="f899f-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="f899f-113">Pokud `ActiveChangedCallbacks` je nastaveno na `false`, zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny.</span><span class="sxs-lookup"><span data-stu-id="f899f-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="f899f-114">Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.</span><span class="sxs-lookup"><span data-stu-id="f899f-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="f899f-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>přijímá hodnotu, která indikuje, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="f899f-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="f899f-116">Rozhraní zahrnuje metodu [RegisterChangeCallback (objekt akce\<>, objekt)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu. `IChangeToken`</span><span class="sxs-lookup"><span data-stu-id="f899f-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="f899f-117">`HasChanged`musí být nastavena před vyvoláním zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="f899f-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="f899f-118">ChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="f899f-118">ChangeToken class</span></span>

<span data-ttu-id="f899f-119"><xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="f899f-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="f899f-120">`ChangeToken`nachází se <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> v oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f899f-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="f899f-121">Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f899f-121">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="f899f-122">Metoda [ChangeToken. (Func\<IChangeToken >, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje `Action` , aby se volala při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="f899f-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="f899f-123">`Func<IChangeToken>`vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="f899f-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="f899f-124">`Action`se volá, když se změní token.</span><span class="sxs-lookup"><span data-stu-id="f899f-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="f899f-125">Přetížení [ChangeToken\<. TState >\<(Func IChangeToken >, Action\<TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce `Action` tokenu. .</span><span class="sxs-lookup"><span data-stu-id="f899f-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="f899f-126">`OnChange`<xref:System.IDisposable>vrátí.</span><span class="sxs-lookup"><span data-stu-id="f899f-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="f899f-127">Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="f899f-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="f899f-128">Příklad použití tokenů změny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f899f-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="f899f-129">Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="f899f-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="f899f-130">Pro sledování změn souborů vytvoří <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> `IChangeToken` metoda pro zadané soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="f899f-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="f899f-131">`IChangeToken`tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="f899f-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="f899f-132">Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> pro má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí.</span><span class="sxs-lookup"><span data-stu-id="f899f-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="f899f-133">Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="f899f-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="f899f-134">Monitorování pro změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="f899f-134">Monitor for configuration changes</span></span>

<span data-ttu-id="f899f-135">Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="f899f-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="f899f-136">Tyto soubory jsou konfigurovány pomocí metody <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , která přijímá `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="f899f-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="f899f-137">`reloadOnChange`Určuje, zda má být při změnách souborů znovu načtena konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f899f-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="f899f-138">Toto nastavení se zobrazí v <xref:Microsoft.Extensions.Hosting.Host> metodě <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>usnadnění:</span><span class="sxs-lookup"><span data-stu-id="f899f-138">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="f899f-139">Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="f899f-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="f899f-140">`FileConfigurationSource`Nástroj <xref:Microsoft.Extensions.FileProviders.IFileProvider> používá k monitorování souborů.</span><span class="sxs-lookup"><span data-stu-id="f899f-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="f899f-141">Ve výchozím nastavení `IFileMonitor` je k dispozici <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>pomocí, který nástroj <xref:System.IO.FileSystemWatcher> používá ke sledování změn konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="f899f-142">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f899f-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="f899f-143">Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód&mdash;. Ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="f899f-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="f899f-144">Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="f899f-145">Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="f899f-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="f899f-146">Ukázka používá algoritmus hash souborů SHA1.</span><span class="sxs-lookup"><span data-stu-id="f899f-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="f899f-147">Opakování je implementováno pomocí exponenciálního zálohování.</span><span class="sxs-lookup"><span data-stu-id="f899f-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="f899f-148">Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="f899f-149">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="f899f-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="f899f-150">Jednoduchý token změny spuštění</span><span class="sxs-lookup"><span data-stu-id="f899f-150">Simple startup change token</span></span>

<span data-ttu-id="f899f-151">Zaregistrujte zpětné `Action` volání příjemce tokenu pro oznámení změn do tokenu opětovného načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f899f-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="f899f-152">V `Startup.Configure`nástroji:</span><span class="sxs-lookup"><span data-stu-id="f899f-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f899f-153">`config.GetReloadToken()`poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="f899f-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="f899f-154">Zpětné volání je `InvokeChanged` metoda:</span><span class="sxs-lookup"><span data-stu-id="f899f-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f899f-155">Zpětné volání se používá k předání `IWebHostEnvironment`do, což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například appSettings. `state`  *Vývoj. JSON* při ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f899f-155">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="f899f-156">Hodnoty hash souborů se používají k zabránění spuštění `WriteConsole` příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="f899f-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="f899f-157">Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.</span><span class="sxs-lookup"><span data-stu-id="f899f-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="f899f-158">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="f899f-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="f899f-159">Ukázka implementuje:</span><span class="sxs-lookup"><span data-stu-id="f899f-159">The sample implements:</span></span>

* <span data-ttu-id="f899f-160">Základní monitorování spouštěcího tokenu.</span><span class="sxs-lookup"><span data-stu-id="f899f-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="f899f-161">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="f899f-161">Monitoring as a service.</span></span>
* <span data-ttu-id="f899f-162">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="f899f-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="f899f-163">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f899f-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="f899f-164">*Rozšíření/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="f899f-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="f899f-165">Konstruktor implementované třídy `ConfigurationMonitor`,, zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="f899f-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="f899f-166">`config.GetReloadToken()`dodává token.</span><span class="sxs-lookup"><span data-stu-id="f899f-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="f899f-167">`InvokeChanged`je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="f899f-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="f899f-168">V této instanci je odkaz `IConfigurationMonitor` na instanci, která se používá pro přístup ke stavu monitorování. `state`</span><span class="sxs-lookup"><span data-stu-id="f899f-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="f899f-169">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f899f-169">Two properties are used:</span></span>

* <span data-ttu-id="f899f-170">`MonitoringEnabled`&ndash; Určuje, zda zpětné volání má spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="f899f-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="f899f-171">`CurrentState`&ndash; Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f899f-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="f899f-172">`InvokeChanged` Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="f899f-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="f899f-173">Nespustí svůj kód, `MonitoringEnabled` Pokud `true`není.</span><span class="sxs-lookup"><span data-stu-id="f899f-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="f899f-174">Vypíše aktuální `state` `WriteConsole` výstup do výstupu.</span><span class="sxs-lookup"><span data-stu-id="f899f-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="f899f-175">Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f899f-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f899f-176">Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f899f-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="f899f-177">Instance `IConfigurationMonitor` je vložena `IndexModel`do.</span><span class="sxs-lookup"><span data-stu-id="f899f-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="f899f-178">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f899f-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="f899f-179">Nástroj Configuration monitor (`_monitor`) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f899f-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="f899f-180">Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže.</span><span class="sxs-lookup"><span data-stu-id="f899f-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="f899f-181">Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.</span><span class="sxs-lookup"><span data-stu-id="f899f-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="f899f-182">Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.</span><span class="sxs-lookup"><span data-stu-id="f899f-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="f899f-183">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f899f-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="f899f-184">Monitorovat změny souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="f899f-184">Monitor cached file changes</span></span>

<span data-ttu-id="f899f-185">Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="f899f-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="f899f-186">Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="f899f-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="f899f-187">Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="f899f-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="f899f-188">Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="f899f-189">Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="f899f-190">Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="f899f-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="f899f-191">Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="f899f-192">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="f899f-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="f899f-193">Ukázka používá `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="f899f-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="f899f-194">Vrátit obsah souboru</span><span class="sxs-lookup"><span data-stu-id="f899f-194">Return file content.</span></span>
* <span data-ttu-id="f899f-195">Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="f899f-196">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="f899f-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="f899f-197">Vytvoří `FileService` se pro zpracování vyhledávání souborů uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="f899f-198">Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*). `GetFileContent`</span><span class="sxs-lookup"><span data-stu-id="f899f-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="f899f-199">Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="f899f-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="f899f-200">Obsah souboru se získá pomocí `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="f899f-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="f899f-201">Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="f899f-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="f899f-202">Zpětné volání tokenu se aktivuje při úpravě souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="f899f-203">Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) .</span><span class="sxs-lookup"><span data-stu-id="f899f-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="f899f-204">Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="f899f-205">V následujícím příkladu jsou soubory uloženy v kořenu obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f899f-205">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="f899f-206">`IWebHostEnvironment.ContentRootFileProvider`slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši v `IWebHostEnvironment.ContentRootPath`aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f899f-206">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="f899f-207">Je získaný pomocí [IFileInfo. PhysicalPath.](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) `filePath`</span><span class="sxs-lookup"><span data-stu-id="f899f-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="f899f-208">`FileService` Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="f899f-209">V `Startup.ConfigureServices`nástroji:</span><span class="sxs-lookup"><span data-stu-id="f899f-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f899f-210">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="f899f-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="f899f-211">V `OnGet` metodě stránky indexu (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="f899f-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="f899f-212">CompositeChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="f899f-212">CompositeChangeToken class</span></span>

<span data-ttu-id="f899f-213">Pro reprezentaci jedné nebo `IChangeToken` více instancí v jednom objektu <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> použijte třídu.</span><span class="sxs-lookup"><span data-stu-id="f899f-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="f899f-214">`HasChanged`v sestavách `true` složeného tokenu, pokud `true`je nějaký reprezentovaný token `HasChanged` .</span><span class="sxs-lookup"><span data-stu-id="f899f-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="f899f-215">`ActiveChangeCallbacks`v sestavách `true` složeného tokenu, pokud `true`je nějaký reprezentovaný token `ActiveChangeCallbacks` .</span><span class="sxs-lookup"><span data-stu-id="f899f-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="f899f-216">Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.</span><span class="sxs-lookup"><span data-stu-id="f899f-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f899f-217">*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="f899f-217">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="f899f-218">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f899f-218">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="f899f-219">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="f899f-219">IChangeToken interface</span></span>

<span data-ttu-id="f899f-220"><xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="f899f-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="f899f-221">`IChangeToken`nachází se <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> v oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f899f-221">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="f899f-222">Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="f899f-222">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="f899f-223">`IChangeToken`má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f899f-223">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="f899f-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="f899f-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="f899f-225">Pokud `ActiveChangedCallbacks` je nastaveno na `false`, zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny.</span><span class="sxs-lookup"><span data-stu-id="f899f-225">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="f899f-226">Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.</span><span class="sxs-lookup"><span data-stu-id="f899f-226">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="f899f-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>přijímá hodnotu, která indikuje, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="f899f-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="f899f-228">Rozhraní zahrnuje metodu [RegisterChangeCallback (objekt akce\<>, objekt)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu. `IChangeToken`</span><span class="sxs-lookup"><span data-stu-id="f899f-228">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="f899f-229">`HasChanged`musí být nastavena před vyvoláním zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="f899f-229">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="f899f-230">ChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="f899f-230">ChangeToken class</span></span>

<span data-ttu-id="f899f-231"><xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="f899f-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="f899f-232">`ChangeToken`nachází se <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> v oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f899f-232">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="f899f-233">Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="f899f-233">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="f899f-234">Metoda [ChangeToken. (Func\<IChangeToken >, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje `Action` , aby se volala při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="f899f-234">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="f899f-235">`Func<IChangeToken>`vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="f899f-235">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="f899f-236">`Action`se volá, když se změní token.</span><span class="sxs-lookup"><span data-stu-id="f899f-236">`Action` is called when the token changes.</span></span>

<span data-ttu-id="f899f-237">Přetížení [ChangeToken\<. TState >\<(Func IChangeToken >, Action\<TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce `Action` tokenu. .</span><span class="sxs-lookup"><span data-stu-id="f899f-237">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="f899f-238">`OnChange`<xref:System.IDisposable>vrátí.</span><span class="sxs-lookup"><span data-stu-id="f899f-238">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="f899f-239">Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="f899f-239">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="f899f-240">Příklad použití tokenů změny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f899f-240">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="f899f-241">Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="f899f-241">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="f899f-242">Pro sledování změn souborů vytvoří <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> `IChangeToken` metoda pro zadané soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="f899f-242">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="f899f-243">`IChangeToken`tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="f899f-243">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="f899f-244">Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> pro má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí.</span><span class="sxs-lookup"><span data-stu-id="f899f-244">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="f899f-245">Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="f899f-245">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="f899f-246">Monitorování pro změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="f899f-246">Monitor for configuration changes</span></span>

<span data-ttu-id="f899f-247">Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="f899f-247">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="f899f-248">Tyto soubory jsou konfigurovány pomocí metody <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , která přijímá `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="f899f-248">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="f899f-249">`reloadOnChange`Určuje, zda má být při změnách souborů znovu načtena konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f899f-249">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="f899f-250">Toto nastavení se zobrazí v <xref:Microsoft.AspNetCore.WebHost> metodě <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>usnadnění:</span><span class="sxs-lookup"><span data-stu-id="f899f-250">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="f899f-251">Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="f899f-251">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="f899f-252">`FileConfigurationSource`Nástroj <xref:Microsoft.Extensions.FileProviders.IFileProvider> používá k monitorování souborů.</span><span class="sxs-lookup"><span data-stu-id="f899f-252">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="f899f-253">Ve výchozím nastavení `IFileMonitor` je k dispozici <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>pomocí, který nástroj <xref:System.IO.FileSystemWatcher> používá ke sledování změn konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-253">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="f899f-254">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f899f-254">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="f899f-255">Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód&mdash;. Ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="f899f-255">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="f899f-256">Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-256">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="f899f-257">Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="f899f-257">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="f899f-258">Ukázka používá algoritmus hash souborů SHA1.</span><span class="sxs-lookup"><span data-stu-id="f899f-258">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="f899f-259">Opakování je implementováno pomocí exponenciálního zálohování.</span><span class="sxs-lookup"><span data-stu-id="f899f-259">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="f899f-260">Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-260">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="f899f-261">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="f899f-261">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="f899f-262">Jednoduchý token změny spuštění</span><span class="sxs-lookup"><span data-stu-id="f899f-262">Simple startup change token</span></span>

<span data-ttu-id="f899f-263">Zaregistrujte zpětné `Action` volání příjemce tokenu pro oznámení změn do tokenu opětovného načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f899f-263">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="f899f-264">V `Startup.Configure`nástroji:</span><span class="sxs-lookup"><span data-stu-id="f899f-264">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f899f-265">`config.GetReloadToken()`poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="f899f-265">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="f899f-266">Zpětné volání je `InvokeChanged` metoda:</span><span class="sxs-lookup"><span data-stu-id="f899f-266">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f899f-267">Zpětné volání se používá k předání `IHostingEnvironment`do, což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například appSettings. `state`  *Vývoj. JSON* při ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f899f-267">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="f899f-268">Hodnoty hash souborů se používají k zabránění spuštění `WriteConsole` příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="f899f-268">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="f899f-269">Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.</span><span class="sxs-lookup"><span data-stu-id="f899f-269">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="f899f-270">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="f899f-270">Monitor configuration changes as a service</span></span>

<span data-ttu-id="f899f-271">Ukázka implementuje:</span><span class="sxs-lookup"><span data-stu-id="f899f-271">The sample implements:</span></span>

* <span data-ttu-id="f899f-272">Základní monitorování spouštěcího tokenu.</span><span class="sxs-lookup"><span data-stu-id="f899f-272">Basic startup token monitoring.</span></span>
* <span data-ttu-id="f899f-273">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="f899f-273">Monitoring as a service.</span></span>
* <span data-ttu-id="f899f-274">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="f899f-274">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="f899f-275">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f899f-275">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="f899f-276">*Rozšíření/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="f899f-276">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="f899f-277">Konstruktor implementované třídy `ConfigurationMonitor`,, zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="f899f-277">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="f899f-278">`config.GetReloadToken()`dodává token.</span><span class="sxs-lookup"><span data-stu-id="f899f-278">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="f899f-279">`InvokeChanged`je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="f899f-279">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="f899f-280">V této instanci je odkaz `IConfigurationMonitor` na instanci, která se používá pro přístup ke stavu monitorování. `state`</span><span class="sxs-lookup"><span data-stu-id="f899f-280">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="f899f-281">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f899f-281">Two properties are used:</span></span>

* <span data-ttu-id="f899f-282">`MonitoringEnabled`&ndash; Určuje, zda zpětné volání má spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="f899f-282">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="f899f-283">`CurrentState`&ndash; Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f899f-283">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="f899f-284">`InvokeChanged` Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="f899f-284">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="f899f-285">Nespustí svůj kód, `MonitoringEnabled` Pokud `true`není.</span><span class="sxs-lookup"><span data-stu-id="f899f-285">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="f899f-286">Vypíše aktuální `state` `WriteConsole` výstup do výstupu.</span><span class="sxs-lookup"><span data-stu-id="f899f-286">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="f899f-287">Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f899f-287">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f899f-288">Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f899f-288">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="f899f-289">Instance `IConfigurationMonitor` je vložena `IndexModel`do.</span><span class="sxs-lookup"><span data-stu-id="f899f-289">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="f899f-290">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f899f-290">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="f899f-291">Nástroj Configuration monitor (`_monitor`) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f899f-291">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="f899f-292">Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže.</span><span class="sxs-lookup"><span data-stu-id="f899f-292">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="f899f-293">Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.</span><span class="sxs-lookup"><span data-stu-id="f899f-293">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="f899f-294">Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.</span><span class="sxs-lookup"><span data-stu-id="f899f-294">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="f899f-295">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f899f-295">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="f899f-296">Monitorovat změny souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="f899f-296">Monitor cached file changes</span></span>

<span data-ttu-id="f899f-297">Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="f899f-297">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="f899f-298">Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="f899f-298">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="f899f-299">Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="f899f-299">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="f899f-300">Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-300">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="f899f-301">Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-301">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="f899f-302">Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="f899f-302">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="f899f-303">Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-303">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="f899f-304">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="f899f-304">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="f899f-305">Ukázka používá `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="f899f-305">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="f899f-306">Vrátit obsah souboru</span><span class="sxs-lookup"><span data-stu-id="f899f-306">Return file content.</span></span>
* <span data-ttu-id="f899f-307">Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-307">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="f899f-308">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="f899f-308">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="f899f-309">Vytvoří `FileService` se pro zpracování vyhledávání souborů uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-309">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="f899f-310">Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*). `GetFileContent`</span><span class="sxs-lookup"><span data-stu-id="f899f-310">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="f899f-311">Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="f899f-311">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="f899f-312">Obsah souboru se získá pomocí `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="f899f-312">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="f899f-313">Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="f899f-313">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="f899f-314">Zpětné volání tokenu se aktivuje při úpravě souboru.</span><span class="sxs-lookup"><span data-stu-id="f899f-314">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="f899f-315">Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) .</span><span class="sxs-lookup"><span data-stu-id="f899f-315">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="f899f-316">Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-316">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="f899f-317">V následujícím příkladu jsou soubory uloženy v kořenu obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f899f-317">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="f899f-318">[IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f899f-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="f899f-319">Je získaný pomocí [IFileInfo. PhysicalPath.](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) `filePath`</span><span class="sxs-lookup"><span data-stu-id="f899f-319">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="f899f-320">`FileService` Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="f899f-320">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="f899f-321">V `Startup.ConfigureServices`nástroji:</span><span class="sxs-lookup"><span data-stu-id="f899f-321">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f899f-322">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="f899f-322">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="f899f-323">V `OnGet` metodě stránky indexu (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="f899f-323">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="f899f-324">CompositeChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="f899f-324">CompositeChangeToken class</span></span>

<span data-ttu-id="f899f-325">Pro reprezentaci jedné nebo `IChangeToken` více instancí v jednom objektu <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> použijte třídu.</span><span class="sxs-lookup"><span data-stu-id="f899f-325">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="f899f-326">`HasChanged`v sestavách `true` složeného tokenu, pokud `true`je nějaký reprezentovaný token `HasChanged` .</span><span class="sxs-lookup"><span data-stu-id="f899f-326">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="f899f-327">`ActiveChangeCallbacks`v sestavách `true` složeného tokenu, pokud `true`je nějaký reprezentovaný token `ActiveChangeCallbacks` .</span><span class="sxs-lookup"><span data-stu-id="f899f-327">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="f899f-328">Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.</span><span class="sxs-lookup"><span data-stu-id="f899f-328">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f899f-329">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f899f-329">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

---
title: Detekce změn pomocí tokenů změn v ASP.NET core
author: rick-anderson
description: Přečtěte si, jak pomocí tokenů změn sledovat změny.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 70451e219f1295b854e2f84aac55f0cfd1786b19
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656343"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="36f4b-103">Detekce změn pomocí tokenů změn v ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="36f4b-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36f4b-104">*Token změny* je univerzální, nízkoúrovňový stavební blok používaný ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="36f4b-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="36f4b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="36f4b-106">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="36f4b-106">IChangeToken interface</span></span>

<span data-ttu-id="36f4b-107"><xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="36f4b-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="36f4b-108">`IChangeToken`sídlí v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="36f4b-109">Balíček [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet je implicitně k dispozici ASP.NET aplikacím Core.</span><span class="sxs-lookup"><span data-stu-id="36f4b-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="36f4b-110">`IChangeToken`má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="36f4b-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="36f4b-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="36f4b-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="36f4b-112">Pokud `ActiveChangedCallbacks` je `false`nastavena na , zpětné volání je `HasChanged` nikdy volána a aplikace musí dotazování na změny.</span><span class="sxs-lookup"><span data-stu-id="36f4b-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="36f4b-113">Je také možné token nikdy zrušit, pokud dojde k žádné změny nebo základní naslouchací proces změny je uvolněna nebo zakázána.</span><span class="sxs-lookup"><span data-stu-id="36f4b-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="36f4b-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>obdrží hodnotu, která označuje, zda došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="36f4b-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="36f4b-115">Rozhraní `IChangeToken` obsahuje [Metodu RegisterChangeCallback(Action\<Object>, Object),](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) která registruje zpětné volání, které je vyvoláno při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="36f4b-116">`HasChanged`musí být nastavena před vyvolání zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="36f4b-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="36f4b-117">Třída ChangeToken</span><span class="sxs-lookup"><span data-stu-id="36f4b-117">ChangeToken class</span></span>

<span data-ttu-id="36f4b-118"><xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída používaná k šíření oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="36f4b-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="36f4b-119">`ChangeToken`sídlí v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="36f4b-120">Balíček [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet je implicitně k dispozici ASP.NET aplikacím Core.</span><span class="sxs-lookup"><span data-stu-id="36f4b-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="36f4b-121">Metoda [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje volání, `Action` které se stane při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="36f4b-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="36f4b-122">`Func<IChangeToken>`vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="36f4b-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="36f4b-123">`Action`při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="36f4b-124">[\<ChangeToken.OnChange TState\<>(Func IChangeToken\<>, Akce TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přetížení trvá další `Action` `TState` parametr, který je předán do tokenu spotřebitele .</span><span class="sxs-lookup"><span data-stu-id="36f4b-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="36f4b-125">`OnChange`vrátí <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="36f4b-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="36f4b-126">Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="36f4b-127">Příklad použití tokenů změn v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36f4b-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="36f4b-128">Tokeny změn se používají v prominentních oblastech ASP.NET core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="36f4b-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="36f4b-129">Pro sledování změn <xref:Microsoft.Extensions.FileProviders.IFileProvider>souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` 's pro zadané soubory nebo složku, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="36f4b-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="36f4b-130">`IChangeToken`tokeny lze přidat do položek mezipaměti pro aktivaci vyřazovacích položek mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="36f4b-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="36f4b-131">Pro `TOptions` změny má <xref:Microsoft.Extensions.Options.OptionsMonitor`1> výchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> implementace přetížení, který přijímá <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> jednu nebo více instancí.</span><span class="sxs-lookup"><span data-stu-id="36f4b-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="36f4b-132">Každá instance `IChangeToken` vrátí zaregistrovat zpětné volání oznámení o změně pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="36f4b-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="36f4b-133">Sledování změn konfigurace</span><span class="sxs-lookup"><span data-stu-id="36f4b-133">Monitor for configuration changes</span></span>

<span data-ttu-id="36f4b-134">Ve výchozím nastavení používají ASP.NET šablony Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.json*a *nastavení aplikací. Production.json*) pro načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="36f4b-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="36f4b-135">Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean),](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> která přijímá `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="36f4b-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="36f4b-136">`reloadOnChange`označuje, zda by měla být konfigurace znovu načtena při změnách souborů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="36f4b-137">Toto nastavení <xref:Microsoft.Extensions.Hosting.Host> se <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>zobrazí metodou pohodlí :</span><span class="sxs-lookup"><span data-stu-id="36f4b-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="36f4b-138">Konfigurace založená na <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>souborech je reprezentována .</span><span class="sxs-lookup"><span data-stu-id="36f4b-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="36f4b-139">`FileConfigurationSource`používá <xref:Microsoft.Extensions.FileProviders.IFileProvider> ke sledování souborů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="36f4b-140">Ve výchozím `IFileMonitor` nastavení je k <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>dispozici <xref:System.IO.FileSystemWatcher> aplikace , která používá ke sledování změn konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="36f4b-141">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36f4b-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="36f4b-142">Pokud se změní některý ze souborů *nastavení aplikace,* obě&mdash;implementace monitorování souborů spustí vlastní kód, ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="36f4b-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="36f4b-143">Konfigurační `FileSystemWatcher` soubor může aktivovat více tokenů zpětná volání pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="36f4b-144">Chcete-li zajistit, že vlastní kód je spuštěn pouze jednou při aktivaci více token zpětné vzpěr, ukázkové implementace kontroluje hodnotu hashe souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="36f4b-145">Ukázka používá hašování souboru SHA1.</span><span class="sxs-lookup"><span data-stu-id="36f4b-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="36f4b-146">Opakování je implementováno s exponenciální back-off.</span><span class="sxs-lookup"><span data-stu-id="36f4b-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="36f4b-147">Opakování je k dispozici, protože může dojít k uzamčení souboru, který dočasně zabraňuje výpočtu nové hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="36f4b-148">*Utility / Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="36f4b-149">Jednoduchý token změny při spuštění</span><span class="sxs-lookup"><span data-stu-id="36f4b-149">Simple startup change token</span></span>

<span data-ttu-id="36f4b-150">Zaregistrujte token `Action` spotřebitele zpětné volání pro oznámení o změně na token opětovného načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36f4b-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="36f4b-151">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36f4b-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="36f4b-152">`config.GetReloadToken()`poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="36f4b-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="36f4b-153">Zpětné volání je `InvokeChanged` metoda:</span><span class="sxs-lookup"><span data-stu-id="36f4b-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="36f4b-154">Zpětné `state` volání slouží k předání v `IWebHostEnvironment`, což je užitečné pro zadání správného konfiguračního souboru *nastavení aplikace* ke sledování (například *nastavení aplikací. Development.json,* když v vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="36f4b-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="36f4b-155">Stavy hashe souborů se `WriteConsole` používají k zabránění spuštění příkazu vícekrát z důvodu více zpětných volání tokenů, pokud se konfigurační soubor změnil pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="36f4b-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="36f4b-156">Tento systém běží tak dlouho, dokud je aplikace spuštěná a nemůže být zakázána uživatelem.</span><span class="sxs-lookup"><span data-stu-id="36f4b-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="36f4b-157">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="36f4b-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="36f4b-158">Vzorek implementuje:</span><span class="sxs-lookup"><span data-stu-id="36f4b-158">The sample implements:</span></span>

* <span data-ttu-id="36f4b-159">Základní monitorování spouštěcích tokenů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="36f4b-160">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="36f4b-160">Monitoring as a service.</span></span>
* <span data-ttu-id="36f4b-161">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="36f4b-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="36f4b-162">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="36f4b-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="36f4b-163">*Rozšíření/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="36f4b-164">Konstruktor implementované třídy `ConfigurationMonitor`, zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="36f4b-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="36f4b-165">`config.GetReloadToken()`dodává token.</span><span class="sxs-lookup"><span data-stu-id="36f4b-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="36f4b-166">`InvokeChanged`je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="36f4b-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="36f4b-167">V `state` tomto případě je odkaz `IConfigurationMonitor` na instanci, která se používá pro přístup ke stavu monitorování.</span><span class="sxs-lookup"><span data-stu-id="36f4b-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="36f4b-168">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="36f4b-168">Two properties are used:</span></span>

* <span data-ttu-id="36f4b-169">`MonitoringEnabled`&ndash; Označuje, zda by zpětné volání mělo spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="36f4b-169">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="36f4b-170">`CurrentState`&ndash; Popisuje aktuální stav monitorování pro použití v ui.</span><span class="sxs-lookup"><span data-stu-id="36f4b-170">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="36f4b-171">Metoda `InvokeChanged` je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="36f4b-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="36f4b-172">Nespustí svůj kód, `MonitoringEnabled` `true`pokud není .</span><span class="sxs-lookup"><span data-stu-id="36f4b-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="36f4b-173">Výstupy proud `state` v `WriteConsole` jeho výstupu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="36f4b-174">Instance `ConfigurationMonitor` je registrována jako `Startup.ConfigureServices`služba v :</span><span class="sxs-lookup"><span data-stu-id="36f4b-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="36f4b-175">Stránka Index nabízí uživateli kontrolu nad monitorováním konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36f4b-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="36f4b-176">Instance `IConfigurationMonitor` je injekčně `IndexModel`do .</span><span class="sxs-lookup"><span data-stu-id="36f4b-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="36f4b-177">*Stránky/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="36f4b-178">Konfigurační`_monitor`monitor ( ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu zpětné vazby uživatelského uživatelského nastavení:</span><span class="sxs-lookup"><span data-stu-id="36f4b-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="36f4b-179">Při `OnPostStartMonitoring` aktivaci je povoleno monitorování a aktuální stav je vymazán.</span><span class="sxs-lookup"><span data-stu-id="36f4b-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="36f4b-180">Při `OnPostStopMonitoring` aktivaci monitorování je zakázáno a stav je nastaven tak, aby odrážely, že monitorování nedochází.</span><span class="sxs-lookup"><span data-stu-id="36f4b-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="36f4b-181">Tlačítka v ui povolit a zakázat monitorování.</span><span class="sxs-lookup"><span data-stu-id="36f4b-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="36f4b-182">*Stránky/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="36f4b-183">Sledování změn souborů uložených v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="36f4b-183">Monitor cached file changes</span></span>

<span data-ttu-id="36f4b-184">Obsah souboru lze uložit do <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>mezipaměti v paměti pomocí .</span><span class="sxs-lookup"><span data-stu-id="36f4b-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="36f4b-185">Ukládání do mezipaměti je popsáno v tématu [Cache in-memory.](xref:performance/caching/memory)</span><span class="sxs-lookup"><span data-stu-id="36f4b-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="36f4b-186">Bez dalších kroků, jako je například implementace popsané níže, *jsou zastaralá* (zastaralá) data vrácena z mezipaměti, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="36f4b-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="36f4b-187">Například nezohlednění stavu zdrojového souboru uloženého v mezipaměti při obnovení [klouzavé](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) doby vypršení platnosti vede k zastaralým datům souboru uloženým v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="36f4b-188">Každý požadavek na data obnovuje posuvné období vypršení platnosti, ale soubor je nikdy znovu načíst do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="36f4b-189">Všechny funkce aplikace, které používají obsah souboru uložený v mezipaměti, podléhají možnému příjmu zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="36f4b-190">Použití tokenů změny ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souboru v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="36f4b-191">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="36f4b-192">Vzorek používá `GetFileContent` k:</span><span class="sxs-lookup"><span data-stu-id="36f4b-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="36f4b-193">Vrátit obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-193">Return file content.</span></span>
* <span data-ttu-id="36f4b-194">Implementujte algoritmus opakování s exponenciálním back-off, který se vztahuje na případy, kdy zámek souboru dočasně zabraňuje čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="36f4b-195">*Utility / Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="36f4b-196">A `FileService` je vytvořen pro zpracování vyhledávání souborů uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="36f4b-197">Volání `GetFileContent` metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit jej volajícímu (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="36f4b-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="36f4b-198">Pokud obsah uložený v mezipaměti není pomocí klíče mezipaměti nalezen, provedou se následující akce:</span><span class="sxs-lookup"><span data-stu-id="36f4b-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="36f4b-199">Obsah souboru se `GetFileContent`získává pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="36f4b-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="36f4b-200">Token změny je získán od poskytovatele souboru s [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="36f4b-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="36f4b-201">Zpětné volání tokenu se aktivuje při změně souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="36f4b-202">Obsah souboru je uložen do mezipaměti s [klouzavou dobou vypršení platnosti.](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)</span><span class="sxs-lookup"><span data-stu-id="36f4b-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="36f4b-203">Token změny je připojen s [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) vyřadit položku mezipaměti, pokud se soubor změní, zatímco je uložen do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="36f4b-204">V následujícím příkladu jsou soubory uloženy v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root)aplikace .</span><span class="sxs-lookup"><span data-stu-id="36f4b-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="36f4b-205">`IWebHostEnvironment.ContentRootFileProvider`slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazování na `IWebHostEnvironment.ContentRootPath`aplikaci .</span><span class="sxs-lookup"><span data-stu-id="36f4b-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="36f4b-206">Získá `filePath` se pomocí [aplikace IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="36f4b-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="36f4b-207">Je `FileService` registrován v kontejneru služby spolu se službou ukládání do mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="36f4b-208">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36f4b-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="36f4b-209">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="36f4b-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="36f4b-210">V `OnGet` metodě stránky Rejstřík *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="36f4b-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="36f4b-211">Třída CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="36f4b-211">CompositeChangeToken class</span></span>

<span data-ttu-id="36f4b-212">Pro reprezentaci `IChangeToken` jedné nebo více instancí <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> v jednom objektu použijte třídu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="36f4b-213">`HasChanged`na sestavy složené `true` tokenu, `HasChanged` `true`pokud je nějaký reprezentované token .</span><span class="sxs-lookup"><span data-stu-id="36f4b-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="36f4b-214">`ActiveChangeCallbacks`na sestavy složené `true` tokenu, `ActiveChangeCallbacks` `true`pokud je nějaký reprezentované token .</span><span class="sxs-lookup"><span data-stu-id="36f4b-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="36f4b-215">Pokud dojde k více souběžných událostí změny, je vyvoláno zpětné volání složené změny jednou.</span><span class="sxs-lookup"><span data-stu-id="36f4b-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36f4b-216">*Token změny* je univerzální, nízkoúrovňový stavební blok používaný ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="36f4b-217">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="36f4b-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="36f4b-218">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="36f4b-218">IChangeToken interface</span></span>

<span data-ttu-id="36f4b-219"><xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="36f4b-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="36f4b-220">`IChangeToken`sídlí v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="36f4b-221">Pro aplikace, které nepoužívají [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček [balíčku Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="36f4b-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="36f4b-222">`IChangeToken`má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="36f4b-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="36f4b-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="36f4b-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="36f4b-224">Pokud `ActiveChangedCallbacks` je `false`nastavena na , zpětné volání je `HasChanged` nikdy volána a aplikace musí dotazování na změny.</span><span class="sxs-lookup"><span data-stu-id="36f4b-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="36f4b-225">Je také možné token nikdy zrušit, pokud dojde k žádné změny nebo základní naslouchací proces změny je uvolněna nebo zakázána.</span><span class="sxs-lookup"><span data-stu-id="36f4b-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="36f4b-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>obdrží hodnotu, která označuje, zda došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="36f4b-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="36f4b-227">Rozhraní `IChangeToken` obsahuje [Metodu RegisterChangeCallback(Action\<Object>, Object),](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) která registruje zpětné volání, které je vyvoláno při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="36f4b-228">`HasChanged`musí být nastavena před vyvolání zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="36f4b-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="36f4b-229">Třída ChangeToken</span><span class="sxs-lookup"><span data-stu-id="36f4b-229">ChangeToken class</span></span>

<span data-ttu-id="36f4b-230"><xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída používaná k šíření oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="36f4b-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="36f4b-231">`ChangeToken`sídlí v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="36f4b-232">Pro aplikace, které nepoužívají [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček [balíčku Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="36f4b-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="36f4b-233">Metoda [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje volání, `Action` které se stane při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="36f4b-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="36f4b-234">`Func<IChangeToken>`vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="36f4b-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="36f4b-235">`Action`při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="36f4b-236">[\<ChangeToken.OnChange TState\<>(Func IChangeToken\<>, Akce TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přetížení trvá další `Action` `TState` parametr, který je předán do tokenu spotřebitele .</span><span class="sxs-lookup"><span data-stu-id="36f4b-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="36f4b-237">`OnChange`vrátí <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="36f4b-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="36f4b-238">Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="36f4b-239">Příklad použití tokenů změn v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36f4b-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="36f4b-240">Tokeny změn se používají v prominentních oblastech ASP.NET core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="36f4b-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="36f4b-241">Pro sledování změn <xref:Microsoft.Extensions.FileProviders.IFileProvider>souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` 's pro zadané soubory nebo složku, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="36f4b-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="36f4b-242">`IChangeToken`tokeny lze přidat do položek mezipaměti pro aktivaci vyřazovacích položek mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="36f4b-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="36f4b-243">Pro `TOptions` změny má <xref:Microsoft.Extensions.Options.OptionsMonitor`1> výchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> implementace přetížení, který přijímá <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> jednu nebo více instancí.</span><span class="sxs-lookup"><span data-stu-id="36f4b-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="36f4b-244">Každá instance `IChangeToken` vrátí zaregistrovat zpětné volání oznámení o změně pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="36f4b-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="36f4b-245">Sledování změn konfigurace</span><span class="sxs-lookup"><span data-stu-id="36f4b-245">Monitor for configuration changes</span></span>

<span data-ttu-id="36f4b-246">Ve výchozím nastavení používají ASP.NET šablony Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.json*a *nastavení aplikací. Production.json*) pro načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="36f4b-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="36f4b-247">Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean),](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> která přijímá `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="36f4b-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="36f4b-248">`reloadOnChange`označuje, zda by měla být konfigurace znovu načtena při změnách souborů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="36f4b-249">Toto nastavení <xref:Microsoft.AspNetCore.WebHost> se <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>zobrazí metodou pohodlí :</span><span class="sxs-lookup"><span data-stu-id="36f4b-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="36f4b-250">Konfigurace založená na <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>souborech je reprezentována .</span><span class="sxs-lookup"><span data-stu-id="36f4b-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="36f4b-251">`FileConfigurationSource`používá <xref:Microsoft.Extensions.FileProviders.IFileProvider> ke sledování souborů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="36f4b-252">Ve výchozím `IFileMonitor` nastavení je k <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>dispozici <xref:System.IO.FileSystemWatcher> aplikace , která používá ke sledování změn konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="36f4b-253">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36f4b-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="36f4b-254">Pokud se změní některý ze souborů *nastavení aplikace,* obě&mdash;implementace monitorování souborů spustí vlastní kód, ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="36f4b-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="36f4b-255">Konfigurační `FileSystemWatcher` soubor může aktivovat více tokenů zpětná volání pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="36f4b-256">Chcete-li zajistit, že vlastní kód je spuštěn pouze jednou při aktivaci více token zpětné vzpěr, ukázkové implementace kontroluje hodnotu hashe souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="36f4b-257">Ukázka používá hašování souboru SHA1.</span><span class="sxs-lookup"><span data-stu-id="36f4b-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="36f4b-258">Opakování je implementováno s exponenciální back-off.</span><span class="sxs-lookup"><span data-stu-id="36f4b-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="36f4b-259">Opakování je k dispozici, protože může dojít k uzamčení souboru, který dočasně zabraňuje výpočtu nové hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="36f4b-260">*Utility / Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="36f4b-261">Jednoduchý token změny při spuštění</span><span class="sxs-lookup"><span data-stu-id="36f4b-261">Simple startup change token</span></span>

<span data-ttu-id="36f4b-262">Zaregistrujte token `Action` spotřebitele zpětné volání pro oznámení o změně na token opětovného načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36f4b-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="36f4b-263">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36f4b-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="36f4b-264">`config.GetReloadToken()`poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="36f4b-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="36f4b-265">Zpětné volání je `InvokeChanged` metoda:</span><span class="sxs-lookup"><span data-stu-id="36f4b-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="36f4b-266">Zpětné `state` volání slouží k předání v `IHostingEnvironment`, což je užitečné pro zadání správného konfiguračního souboru *nastavení aplikace* ke sledování (například *nastavení aplikací. Development.json,* když v vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="36f4b-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="36f4b-267">Stavy hashe souborů se `WriteConsole` používají k zabránění spuštění příkazu vícekrát z důvodu více zpětných volání tokenů, pokud se konfigurační soubor změnil pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="36f4b-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="36f4b-268">Tento systém běží tak dlouho, dokud je aplikace spuštěná a nemůže být zakázána uživatelem.</span><span class="sxs-lookup"><span data-stu-id="36f4b-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="36f4b-269">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="36f4b-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="36f4b-270">Vzorek implementuje:</span><span class="sxs-lookup"><span data-stu-id="36f4b-270">The sample implements:</span></span>

* <span data-ttu-id="36f4b-271">Základní monitorování spouštěcích tokenů.</span><span class="sxs-lookup"><span data-stu-id="36f4b-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="36f4b-272">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="36f4b-272">Monitoring as a service.</span></span>
* <span data-ttu-id="36f4b-273">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="36f4b-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="36f4b-274">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="36f4b-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="36f4b-275">*Rozšíření/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="36f4b-276">Konstruktor implementované třídy `ConfigurationMonitor`, zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="36f4b-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="36f4b-277">`config.GetReloadToken()`dodává token.</span><span class="sxs-lookup"><span data-stu-id="36f4b-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="36f4b-278">`InvokeChanged`je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="36f4b-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="36f4b-279">V `state` tomto případě je odkaz `IConfigurationMonitor` na instanci, která se používá pro přístup ke stavu monitorování.</span><span class="sxs-lookup"><span data-stu-id="36f4b-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="36f4b-280">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="36f4b-280">Two properties are used:</span></span>

* <span data-ttu-id="36f4b-281">`MonitoringEnabled`&ndash; Označuje, zda by zpětné volání mělo spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="36f4b-281">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="36f4b-282">`CurrentState`&ndash; Popisuje aktuální stav monitorování pro použití v ui.</span><span class="sxs-lookup"><span data-stu-id="36f4b-282">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="36f4b-283">Metoda `InvokeChanged` je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="36f4b-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="36f4b-284">Nespustí svůj kód, `MonitoringEnabled` `true`pokud není .</span><span class="sxs-lookup"><span data-stu-id="36f4b-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="36f4b-285">Výstupy proud `state` v `WriteConsole` jeho výstupu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="36f4b-286">Instance `ConfigurationMonitor` je registrována jako `Startup.ConfigureServices`služba v :</span><span class="sxs-lookup"><span data-stu-id="36f4b-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="36f4b-287">Stránka Index nabízí uživateli kontrolu nad monitorováním konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36f4b-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="36f4b-288">Instance `IConfigurationMonitor` je injekčně `IndexModel`do .</span><span class="sxs-lookup"><span data-stu-id="36f4b-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="36f4b-289">*Stránky/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="36f4b-290">Konfigurační`_monitor`monitor ( ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu zpětné vazby uživatelského uživatelského nastavení:</span><span class="sxs-lookup"><span data-stu-id="36f4b-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="36f4b-291">Při `OnPostStartMonitoring` aktivaci je povoleno monitorování a aktuální stav je vymazán.</span><span class="sxs-lookup"><span data-stu-id="36f4b-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="36f4b-292">Při `OnPostStopMonitoring` aktivaci monitorování je zakázáno a stav je nastaven tak, aby odrážely, že monitorování nedochází.</span><span class="sxs-lookup"><span data-stu-id="36f4b-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="36f4b-293">Tlačítka v ui povolit a zakázat monitorování.</span><span class="sxs-lookup"><span data-stu-id="36f4b-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="36f4b-294">*Stránky/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="36f4b-295">Sledování změn souborů uložených v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="36f4b-295">Monitor cached file changes</span></span>

<span data-ttu-id="36f4b-296">Obsah souboru lze uložit do <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>mezipaměti v paměti pomocí .</span><span class="sxs-lookup"><span data-stu-id="36f4b-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="36f4b-297">Ukládání do mezipaměti je popsáno v tématu [Cache in-memory.](xref:performance/caching/memory)</span><span class="sxs-lookup"><span data-stu-id="36f4b-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="36f4b-298">Bez dalších kroků, jako je například implementace popsané níže, *jsou zastaralá* (zastaralá) data vrácena z mezipaměti, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="36f4b-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="36f4b-299">Například nezohlednění stavu zdrojového souboru uloženého v mezipaměti při obnovení [klouzavé](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) doby vypršení platnosti vede k zastaralým datům souboru uloženým v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="36f4b-300">Každý požadavek na data obnovuje posuvné období vypršení platnosti, ale soubor je nikdy znovu načíst do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="36f4b-301">Všechny funkce aplikace, které používají obsah souboru uložený v mezipaměti, podléhají možnému příjmu zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="36f4b-302">Použití tokenů změny ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souboru v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="36f4b-303">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="36f4b-304">Vzorek používá `GetFileContent` k:</span><span class="sxs-lookup"><span data-stu-id="36f4b-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="36f4b-305">Vrátit obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-305">Return file content.</span></span>
* <span data-ttu-id="36f4b-306">Implementujte algoritmus opakování s exponenciálním back-off, který se vztahuje na případy, kdy zámek souboru dočasně zabraňuje čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="36f4b-307">*Utility / Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="36f4b-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="36f4b-308">A `FileService` je vytvořen pro zpracování vyhledávání souborů uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="36f4b-309">Volání `GetFileContent` metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit jej volajícímu (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="36f4b-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="36f4b-310">Pokud obsah uložený v mezipaměti není pomocí klíče mezipaměti nalezen, provedou se následující akce:</span><span class="sxs-lookup"><span data-stu-id="36f4b-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="36f4b-311">Obsah souboru se `GetFileContent`získává pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="36f4b-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="36f4b-312">Token změny je získán od poskytovatele souboru s [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="36f4b-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="36f4b-313">Zpětné volání tokenu se aktivuje při změně souboru.</span><span class="sxs-lookup"><span data-stu-id="36f4b-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="36f4b-314">Obsah souboru je uložen do mezipaměti s [klouzavou dobou vypršení platnosti.](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)</span><span class="sxs-lookup"><span data-stu-id="36f4b-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="36f4b-315">Token změny je připojen s [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) vyřadit položku mezipaměti, pokud se soubor změní, zatímco je uložen do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="36f4b-316">V následujícím příkladu jsou soubory uloženy v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root)aplikace .</span><span class="sxs-lookup"><span data-stu-id="36f4b-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="36f4b-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá <xref:Microsoft.Extensions.FileProviders.IFileProvider> k získání ukazující <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>na aplikaci .</span><span class="sxs-lookup"><span data-stu-id="36f4b-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="36f4b-318">Získá `filePath` se pomocí [aplikace IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="36f4b-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="36f4b-319">Je `FileService` registrován v kontejneru služby spolu se službou ukládání do mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="36f4b-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="36f4b-320">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36f4b-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="36f4b-321">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="36f4b-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="36f4b-322">V `OnGet` metodě stránky Rejstřík *(Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="36f4b-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="36f4b-323">Třída CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="36f4b-323">CompositeChangeToken class</span></span>

<span data-ttu-id="36f4b-324">Pro reprezentaci `IChangeToken` jedné nebo více instancí <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> v jednom objektu použijte třídu.</span><span class="sxs-lookup"><span data-stu-id="36f4b-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="36f4b-325">`HasChanged`na sestavy složené `true` tokenu, `HasChanged` `true`pokud je nějaký reprezentované token .</span><span class="sxs-lookup"><span data-stu-id="36f4b-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="36f4b-326">`ActiveChangeCallbacks`na sestavy složené `true` tokenu, `ActiveChangeCallbacks` `true`pokud je nějaký reprezentované token .</span><span class="sxs-lookup"><span data-stu-id="36f4b-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="36f4b-327">Pokud dojde k více souběžných událostí změny, je vyvoláno zpětné volání složené změny jednou.</span><span class="sxs-lookup"><span data-stu-id="36f4b-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="36f4b-328">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="36f4b-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

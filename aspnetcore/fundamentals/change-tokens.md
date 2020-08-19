---
title: Zjišťovat změny pomocí tokenů změn v ASP.NET Core
author: rick-anderson
description: Naučte se používat změny tokenů ke sledování změn.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: 891cce975c5852b0192fd7ff22b21060d1dac8ac
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634914"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="137ca-103">Zjišťovat změny pomocí tokenů změn v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="137ca-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="137ca-104">*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="137ca-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="137ca-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="137ca-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="137ca-106">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="137ca-106">IChangeToken interface</span></span>

<span data-ttu-id="137ca-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="137ca-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="137ca-108">`IChangeToken` nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="137ca-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="137ca-109">Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="137ca-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="137ca-110">`IChangeToken` má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="137ca-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="137ca-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="137ca-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="137ca-112">Pokud `ActiveChangedCallbacks` je nastaveno na `false` , zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny.</span><span class="sxs-lookup"><span data-stu-id="137ca-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="137ca-113">Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.</span><span class="sxs-lookup"><span data-stu-id="137ca-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="137ca-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> přijímá hodnotu, která indikuje, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="137ca-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="137ca-115">`IChangeToken`Rozhraní zahrnuje metodu [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="137ca-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="137ca-116">`HasChanged` musí být nastavena před vyvoláním zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="137ca-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="137ca-117">ChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="137ca-117">ChangeToken class</span></span>

<span data-ttu-id="137ca-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="137ca-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="137ca-119">`ChangeToken` nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="137ca-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="137ca-120">Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="137ca-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="137ca-121">Metoda [ChangeToken. při změně (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje, `Action` aby se volala při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="137ca-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="137ca-122">`Func<IChangeToken>` vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="137ca-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="137ca-123">`Action` se volá, když se změní token.</span><span class="sxs-lookup"><span data-stu-id="137ca-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="137ca-124">Přetížení [ChangeToken.-Change \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="137ca-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="137ca-125">`OnChange` Vrátí <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="137ca-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="137ca-126">Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="137ca-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="137ca-127">Příklad použití tokenů změny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="137ca-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="137ca-128">Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="137ca-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="137ca-129">Pro sledování změn souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="137ca-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="137ca-130">`IChangeToken` tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="137ca-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="137ca-131">Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace pro <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí.</span><span class="sxs-lookup"><span data-stu-id="137ca-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="137ca-132">Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="137ca-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="137ca-133">Monitorování pro změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="137ca-133">Monitor for configuration changes</span></span>

<span data-ttu-id="137ca-134">Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*a *appsettings.Production.json*) k načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="137ca-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="137ca-135">Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , která přijímá `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="137ca-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="137ca-136">`reloadOnChange` Určuje, zda má být při změnách souborů znovu načtena konfigurace.</span><span class="sxs-lookup"><span data-stu-id="137ca-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="137ca-137">Toto nastavení se zobrazí v <xref:Microsoft.Extensions.Hosting.Host> metodě usnadnění <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="137ca-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="137ca-138">Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="137ca-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="137ca-139">`FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>Nástroj používá k monitorování souborů.</span><span class="sxs-lookup"><span data-stu-id="137ca-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="137ca-140">Ve výchozím nastavení `IFileMonitor` je k dispozici pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , který <xref:System.IO.FileSystemWatcher> Nástroj používá ke sledování změn konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="137ca-141">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="137ca-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="137ca-142">Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód &mdash; . Ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="137ca-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="137ca-143">Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="137ca-144">Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="137ca-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="137ca-145">Ukázka používá algoritmus hash souborů SHA1.</span><span class="sxs-lookup"><span data-stu-id="137ca-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="137ca-146">Opakování je implementováno pomocí exponenciálního zálohování.</span><span class="sxs-lookup"><span data-stu-id="137ca-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="137ca-147">Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="137ca-148">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="137ca-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="137ca-149">Jednoduchý token změny spuštění</span><span class="sxs-lookup"><span data-stu-id="137ca-149">Simple startup change token</span></span>

<span data-ttu-id="137ca-150">Zaregistrujte zpětné volání příjemce tokenu `Action` pro oznámení změn do tokenu opětovného načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="137ca-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="137ca-151">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="137ca-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="137ca-152">`config.GetReloadToken()` poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="137ca-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="137ca-153">Zpětné volání je `InvokeChanged` metoda:</span><span class="sxs-lookup"><span data-stu-id="137ca-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="137ca-154">`state`Zpětné volání se používá k předání do `IWebHostEnvironment` , což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appsettings.Development.jsv* případě, kdy ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="137ca-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="137ca-155">Hodnoty hash souborů se používají k zabránění `WriteConsole` spuštění příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="137ca-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="137ca-156">Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.</span><span class="sxs-lookup"><span data-stu-id="137ca-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="137ca-157">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="137ca-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="137ca-158">Ukázka implementuje:</span><span class="sxs-lookup"><span data-stu-id="137ca-158">The sample implements:</span></span>

* <span data-ttu-id="137ca-159">Základní monitorování spouštěcího tokenu.</span><span class="sxs-lookup"><span data-stu-id="137ca-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="137ca-160">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="137ca-160">Monitoring as a service.</span></span>
* <span data-ttu-id="137ca-161">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="137ca-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="137ca-162">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="137ca-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="137ca-163">*Rozšíření/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="137ca-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="137ca-164">Konstruktor implementované třídy, `ConfigurationMonitor` , zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="137ca-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="137ca-165">`config.GetReloadToken()` dodává token.</span><span class="sxs-lookup"><span data-stu-id="137ca-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="137ca-166">`InvokeChanged` je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="137ca-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="137ca-167">`state`V této instanci je odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke stavu monitorování.</span><span class="sxs-lookup"><span data-stu-id="137ca-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="137ca-168">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="137ca-168">Two properties are used:</span></span>

* <span data-ttu-id="137ca-169">`MonitoringEnabled`: Určuje, zda zpětné volání má spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="137ca-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="137ca-170">`CurrentState`: Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="137ca-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="137ca-171">`InvokeChanged`Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="137ca-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="137ca-172">Nespustí svůj kód `MonitoringEnabled` , pokud není `true` .</span><span class="sxs-lookup"><span data-stu-id="137ca-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="137ca-173">Vypíše aktuální `state` výstup do `WriteConsole` výstupu.</span><span class="sxs-lookup"><span data-stu-id="137ca-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="137ca-174">Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="137ca-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="137ca-175">Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace.</span><span class="sxs-lookup"><span data-stu-id="137ca-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="137ca-176">Instance `IConfigurationMonitor` je vložena do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="137ca-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="137ca-177">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="137ca-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="137ca-178">Nástroj Configuration monitor ( `_monitor` ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="137ca-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="137ca-179">Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže.</span><span class="sxs-lookup"><span data-stu-id="137ca-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="137ca-180">Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.</span><span class="sxs-lookup"><span data-stu-id="137ca-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="137ca-181">Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.</span><span class="sxs-lookup"><span data-stu-id="137ca-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="137ca-182">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="137ca-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="137ca-183">Monitorovat změny souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="137ca-183">Monitor cached file changes</span></span>

<span data-ttu-id="137ca-184">Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="137ca-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="137ca-185">Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="137ca-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="137ca-186">Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="137ca-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="137ca-187">Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="137ca-188">Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="137ca-189">Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="137ca-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="137ca-190">Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="137ca-191">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="137ca-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="137ca-192">Ukázka používá `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="137ca-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="137ca-193">Vrátit obsah souboru</span><span class="sxs-lookup"><span data-stu-id="137ca-193">Return file content.</span></span>
* <span data-ttu-id="137ca-194">Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="137ca-195">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="137ca-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="137ca-196">Vytvoří se `FileService` pro zpracování vyhledávání souborů uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="137ca-197">`GetFileContent`Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*).</span><span class="sxs-lookup"><span data-stu-id="137ca-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="137ca-198">Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="137ca-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="137ca-199">Obsah souboru se získá pomocí `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="137ca-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="137ca-200">Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="137ca-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="137ca-201">Zpětné volání tokenu se aktivuje při úpravě souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="137ca-202">Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) .</span><span class="sxs-lookup"><span data-stu-id="137ca-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="137ca-203">Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="137ca-204">V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="137ca-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="137ca-205">`IWebHostEnvironment.ContentRootFileProvider` slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši v aplikaci `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="137ca-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="137ca-206">`filePath`Je získaný pomocí [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="137ca-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="137ca-207">`FileService`Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="137ca-208">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="137ca-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="137ca-209">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="137ca-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="137ca-210">V metodě stránky indexu `OnGet` (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="137ca-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="137ca-211">CompositeChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="137ca-211">CompositeChangeToken class</span></span>

<span data-ttu-id="137ca-212">Pro reprezentaci jedné nebo více `IChangeToken` instancí v jednom objektu použijte <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> třídu.</span><span class="sxs-lookup"><span data-stu-id="137ca-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="137ca-213">`HasChanged` v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `HasChanged` `true` .</span><span class="sxs-lookup"><span data-stu-id="137ca-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="137ca-214">`ActiveChangeCallbacks` v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `ActiveChangeCallbacks` `true` .</span><span class="sxs-lookup"><span data-stu-id="137ca-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="137ca-215">Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.</span><span class="sxs-lookup"><span data-stu-id="137ca-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="137ca-216">*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="137ca-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="137ca-217">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="137ca-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="137ca-218">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="137ca-218">IChangeToken interface</span></span>

<span data-ttu-id="137ca-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="137ca-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="137ca-220">`IChangeToken` nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="137ca-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="137ca-221">Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="137ca-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="137ca-222">`IChangeToken` má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="137ca-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="137ca-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="137ca-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="137ca-224">Pokud `ActiveChangedCallbacks` je nastaveno na `false` , zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny.</span><span class="sxs-lookup"><span data-stu-id="137ca-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="137ca-225">Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.</span><span class="sxs-lookup"><span data-stu-id="137ca-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="137ca-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> přijímá hodnotu, která indikuje, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="137ca-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="137ca-227">`IChangeToken`Rozhraní zahrnuje metodu [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="137ca-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="137ca-228">`HasChanged` musí být nastavena před vyvoláním zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="137ca-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="137ca-229">ChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="137ca-229">ChangeToken class</span></span>

<span data-ttu-id="137ca-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="137ca-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="137ca-231">`ChangeToken` nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="137ca-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="137ca-232">Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="137ca-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="137ca-233">Metoda [ChangeToken. při změně (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje, `Action` aby se volala při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="137ca-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="137ca-234">`Func<IChangeToken>` vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="137ca-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="137ca-235">`Action` se volá, když se změní token.</span><span class="sxs-lookup"><span data-stu-id="137ca-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="137ca-236">Přetížení [ChangeToken.-Change \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="137ca-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="137ca-237">`OnChange` Vrátí <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="137ca-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="137ca-238">Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="137ca-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="137ca-239">Příklad použití tokenů změny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="137ca-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="137ca-240">Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="137ca-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="137ca-241">Pro sledování změn souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="137ca-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="137ca-242">`IChangeToken` tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="137ca-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="137ca-243">Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace pro <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí.</span><span class="sxs-lookup"><span data-stu-id="137ca-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="137ca-244">Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="137ca-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="137ca-245">Monitorování pro změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="137ca-245">Monitor for configuration changes</span></span>

<span data-ttu-id="137ca-246">Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*a *appsettings.Production.json*) k načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="137ca-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="137ca-247">Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , která přijímá `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="137ca-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="137ca-248">`reloadOnChange` Určuje, zda má být při změnách souborů znovu načtena konfigurace.</span><span class="sxs-lookup"><span data-stu-id="137ca-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="137ca-249">Toto nastavení se zobrazí v <xref:Microsoft.AspNetCore.WebHost> metodě usnadnění <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="137ca-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="137ca-250">Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="137ca-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="137ca-251">`FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>Nástroj používá k monitorování souborů.</span><span class="sxs-lookup"><span data-stu-id="137ca-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="137ca-252">Ve výchozím nastavení `IFileMonitor` je k dispozici pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , který <xref:System.IO.FileSystemWatcher> Nástroj používá ke sledování změn konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="137ca-253">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="137ca-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="137ca-254">Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód &mdash; . Ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="137ca-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="137ca-255">Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="137ca-256">Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="137ca-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="137ca-257">Ukázka používá algoritmus hash souborů SHA1.</span><span class="sxs-lookup"><span data-stu-id="137ca-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="137ca-258">Opakování je implementováno pomocí exponenciálního zálohování.</span><span class="sxs-lookup"><span data-stu-id="137ca-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="137ca-259">Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="137ca-260">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="137ca-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="137ca-261">Jednoduchý token změny spuštění</span><span class="sxs-lookup"><span data-stu-id="137ca-261">Simple startup change token</span></span>

<span data-ttu-id="137ca-262">Zaregistrujte zpětné volání příjemce tokenu `Action` pro oznámení změn do tokenu opětovného načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="137ca-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="137ca-263">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="137ca-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="137ca-264">`config.GetReloadToken()` poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="137ca-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="137ca-265">Zpětné volání je `InvokeChanged` metoda:</span><span class="sxs-lookup"><span data-stu-id="137ca-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="137ca-266">`state`Zpětné volání se používá k předání do `IHostingEnvironment` , což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appsettings.Development.jsv* případě, kdy ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="137ca-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="137ca-267">Hodnoty hash souborů se používají k zabránění `WriteConsole` spuštění příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="137ca-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="137ca-268">Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.</span><span class="sxs-lookup"><span data-stu-id="137ca-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="137ca-269">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="137ca-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="137ca-270">Ukázka implementuje:</span><span class="sxs-lookup"><span data-stu-id="137ca-270">The sample implements:</span></span>

* <span data-ttu-id="137ca-271">Základní monitorování spouštěcího tokenu.</span><span class="sxs-lookup"><span data-stu-id="137ca-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="137ca-272">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="137ca-272">Monitoring as a service.</span></span>
* <span data-ttu-id="137ca-273">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="137ca-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="137ca-274">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="137ca-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="137ca-275">*Rozšíření/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="137ca-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="137ca-276">Konstruktor implementované třídy, `ConfigurationMonitor` , zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="137ca-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="137ca-277">`config.GetReloadToken()` dodává token.</span><span class="sxs-lookup"><span data-stu-id="137ca-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="137ca-278">`InvokeChanged` je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="137ca-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="137ca-279">`state`V této instanci je odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke stavu monitorování.</span><span class="sxs-lookup"><span data-stu-id="137ca-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="137ca-280">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="137ca-280">Two properties are used:</span></span>

* <span data-ttu-id="137ca-281">`MonitoringEnabled`: Určuje, zda zpětné volání má spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="137ca-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="137ca-282">`CurrentState`: Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="137ca-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="137ca-283">`InvokeChanged`Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="137ca-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="137ca-284">Nespustí svůj kód `MonitoringEnabled` , pokud není `true` .</span><span class="sxs-lookup"><span data-stu-id="137ca-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="137ca-285">Vypíše aktuální `state` výstup do `WriteConsole` výstupu.</span><span class="sxs-lookup"><span data-stu-id="137ca-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="137ca-286">Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="137ca-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="137ca-287">Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace.</span><span class="sxs-lookup"><span data-stu-id="137ca-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="137ca-288">Instance `IConfigurationMonitor` je vložena do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="137ca-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="137ca-289">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="137ca-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="137ca-290">Nástroj Configuration monitor ( `_monitor` ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="137ca-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="137ca-291">Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže.</span><span class="sxs-lookup"><span data-stu-id="137ca-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="137ca-292">Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.</span><span class="sxs-lookup"><span data-stu-id="137ca-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="137ca-293">Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.</span><span class="sxs-lookup"><span data-stu-id="137ca-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="137ca-294">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="137ca-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="137ca-295">Monitorovat změny souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="137ca-295">Monitor cached file changes</span></span>

<span data-ttu-id="137ca-296">Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="137ca-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="137ca-297">Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="137ca-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="137ca-298">Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="137ca-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="137ca-299">Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="137ca-300">Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="137ca-301">Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="137ca-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="137ca-302">Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="137ca-303">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="137ca-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="137ca-304">Ukázka používá `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="137ca-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="137ca-305">Vrátit obsah souboru</span><span class="sxs-lookup"><span data-stu-id="137ca-305">Return file content.</span></span>
* <span data-ttu-id="137ca-306">Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="137ca-307">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="137ca-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="137ca-308">Vytvoří se `FileService` pro zpracování vyhledávání souborů uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="137ca-309">`GetFileContent`Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*).</span><span class="sxs-lookup"><span data-stu-id="137ca-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="137ca-310">Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="137ca-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="137ca-311">Obsah souboru se získá pomocí `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="137ca-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="137ca-312">Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="137ca-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="137ca-313">Zpětné volání tokenu se aktivuje při úpravě souboru.</span><span class="sxs-lookup"><span data-stu-id="137ca-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="137ca-314">Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) .</span><span class="sxs-lookup"><span data-stu-id="137ca-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="137ca-315">Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="137ca-316">V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="137ca-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="137ca-317">[IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši v aplikaci <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> .</span><span class="sxs-lookup"><span data-stu-id="137ca-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="137ca-318">`filePath`Je získaný pomocí [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="137ca-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="137ca-319">`FileService`Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="137ca-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="137ca-320">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="137ca-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="137ca-321">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="137ca-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="137ca-322">V metodě stránky indexu `OnGet` (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="137ca-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="137ca-323">CompositeChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="137ca-323">CompositeChangeToken class</span></span>

<span data-ttu-id="137ca-324">Pro reprezentaci jedné nebo více `IChangeToken` instancí v jednom objektu použijte <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> třídu.</span><span class="sxs-lookup"><span data-stu-id="137ca-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="137ca-325">`HasChanged` v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `HasChanged` `true` .</span><span class="sxs-lookup"><span data-stu-id="137ca-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="137ca-326">`ActiveChangeCallbacks` v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `ActiveChangeCallbacks` `true` .</span><span class="sxs-lookup"><span data-stu-id="137ca-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="137ca-327">Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.</span><span class="sxs-lookup"><span data-stu-id="137ca-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="137ca-328">Další materiály</span><span class="sxs-lookup"><span data-stu-id="137ca-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

---
title: Zjištění změn s tokeny změn v ASP.NET Core
author: guardrex
description: Zjistěte, jak používat tokeny změn ke sledování změn.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/03/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 8b73b72d093b33edeb91bc78080e05aa312579ec
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561664"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="e0f20-103">Zjištění změn s tokeny změn v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0f20-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="e0f20-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0f20-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e0f20-105">A *změnit token* se používají ke sledování změny stavu stavební blok pro obecné účely, nízké úrovně.</span><span class="sxs-lookup"><span data-stu-id="e0f20-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="e0f20-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e0f20-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="e0f20-107">IChangeToken rozhraní</span><span class="sxs-lookup"><span data-stu-id="e0f20-107">IChangeToken interface</span></span>

<span data-ttu-id="e0f20-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="e0f20-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="e0f20-109">`IChangeToken` v se nachází <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="e0f20-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="e0f20-110">Pro aplikace, které nepoužívají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček pro [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="e0f20-110">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="e0f20-111">`IChangeToken` má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e0f20-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="e0f20-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> Označuje, pokud token proaktivně vyvolá zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="e0f20-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="e0f20-113">Pokud `ActiveChangedCallbacks` je nastavena na `false`zpětné volání se nikdy nevolá, a aplikace se musí dotazovat `HasChanged` změny.</span><span class="sxs-lookup"><span data-stu-id="e0f20-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="e0f20-114">Je také možné pro token nikdy zrušit, pokud žádné změny nebo je uvolněn nebo zakázané základní naslouchací proces změn.</span><span class="sxs-lookup"><span data-stu-id="e0f20-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="e0f20-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> Získá hodnotu označující, pokud došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="e0f20-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="e0f20-116">`IChangeToken` Rozhraní obsahuje [RegisterChangeCallback (akce\<objektu >, objekt)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) metody, které je zaregistruje zpětné volání, která je vyvolána při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="e0f20-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="e0f20-117">`HasChanged` musí být nastavena před vyvoláním zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="e0f20-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="e0f20-118">Třída ChangeToken</span><span class="sxs-lookup"><span data-stu-id="e0f20-118">ChangeToken class</span></span>

<span data-ttu-id="e0f20-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> slouží k šíření oznámení, že došlo ke změně statické třídě.</span><span class="sxs-lookup"><span data-stu-id="e0f20-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="e0f20-120">`ChangeToken` v se nachází <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="e0f20-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="e0f20-121">Pro aplikace, které nepoužívají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček pro [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="e0f20-121">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="e0f20-122">[ChangeToken.OnChange (Func\<IChangeToken >, akce)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) metoda registrů `Action` volat při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="e0f20-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="e0f20-123">`Func<IChangeToken>` vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="e0f20-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="e0f20-124">`Action` je volána při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="e0f20-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="e0f20-125">[ChangeToken.OnChange\<TState > (Func\<IChangeToken >, akce\<TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přijímá další přetížení `TState` parametr, který je předán do tokenu příjemce `Action`.</span><span class="sxs-lookup"><span data-stu-id="e0f20-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="e0f20-126">`OnChange` Vrátí <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="e0f20-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="e0f20-127">Volání <xref:System.IDisposable.Dispose*> token z naslouchání pro další změny se zastaví a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="e0f20-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="e0f20-128">Příklad používá změna tokenů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0f20-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="e0f20-129">Změna tokenů se viditelného oblasti ASP.NET Core používají k monitorování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="e0f20-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="e0f20-130">Pro sledování změn souborů, <xref:Microsoft.Extensions.FileProviders.IFileProvider>společnosti <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> metoda vytvoří `IChangeToken` určité soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="e0f20-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="e0f20-131">`IChangeToken` tokeny lze přidat na položky mezipaměti k aktivaci odsunuté mezipaměť při změně.</span><span class="sxs-lookup"><span data-stu-id="e0f20-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="e0f20-132">Pro `TOptions` změní výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> provádění <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> má přetížení, která přijímá jeden nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí.</span><span class="sxs-lookup"><span data-stu-id="e0f20-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="e0f20-133">Vrátí každá instance `IChangeToken` pro registraci zpětné volání oznámení změn pro možnosti sledování změn.</span><span class="sxs-lookup"><span data-stu-id="e0f20-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="e0f20-134">Monitorujte změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="e0f20-134">Monitor for configuration changes</span></span>

<span data-ttu-id="e0f20-135">Ve výchozím nastavení, použijte šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.JSON*, a *appsettings. Production.JSON*) k načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f20-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="e0f20-136">Tyto soubory jsou nakonfigurováni pomocí [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) rozšiřující metody na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , který přijme `reloadOnChange` parametru.</span><span class="sxs-lookup"><span data-stu-id="e0f20-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="e0f20-137">`reloadOnChange` Označuje, pokud byste znovu načíst konfiguraci na změny v souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f20-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="e0f20-138">Toto nastavení objeví ve <xref:Microsoft.AspNetCore.WebHost> Komfortní metoda <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="e0f20-138">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="e0f20-139">Konfigurace založená na souboru je reprezentován <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="e0f20-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="e0f20-140">`FileConfigurationSource` používá <xref:Microsoft.Extensions.FileProviders.IFileProvider> sledování souborů.</span><span class="sxs-lookup"><span data-stu-id="e0f20-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="e0f20-141">Ve výchozím nastavení `IFileMonitor` je poskytován <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, který používá <xref:System.IO.FileSystemWatcher> monitorovat změny v konfiguraci souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f20-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="e0f20-142">Ukázková aplikace předvádí dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e0f20-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="e0f20-143">Pokud je libovolná z *appsettings* změnit soubory, i sledování implementace souboru provést vlastní kód&mdash;ukázkové aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="e0f20-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="e0f20-144">Konfigurační soubor `FileSystemWatcher` může aktivovat více tokenů zpětných volání pro změnu souboru v jediné konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="e0f20-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="e0f20-145">Pokud chcete mít jistotu, že vlastní kód běží pouze po při aktivaci více zpětných volání tokenu, ukázková implementace zkontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="e0f20-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="e0f20-146">Ukázka používá algoritmus hash SHA1 souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f20-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="e0f20-147">Opakování je implementováno s exponenciální regresní.</span><span class="sxs-lookup"><span data-stu-id="e0f20-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="e0f20-148">Opakování je k dispozici, protože zamykání souborů může dojít, který brání dočasně computingu nová hodnota hash souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f20-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="e0f20-149">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="e0f20-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="e0f20-150">Jednoduché spouštění změnit token</span><span class="sxs-lookup"><span data-stu-id="e0f20-150">Simple startup change token</span></span>

<span data-ttu-id="e0f20-151">Zaregistrovat token příjemce `Action` zpětné volání pro upozornění na změnu na konfiguraci znovu načíst token.</span><span class="sxs-lookup"><span data-stu-id="e0f20-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="e0f20-152">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="e0f20-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="e0f20-153">`config.GetReloadToken()` poskytuje tento token.</span><span class="sxs-lookup"><span data-stu-id="e0f20-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="e0f20-154">Zpětné volání je `InvokeChanged` metody:</span><span class="sxs-lookup"><span data-stu-id="e0f20-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="e0f20-155">`state` Zpětného volání, které se používá a zajistěte tak předání `IHostingEnvironment`, což je vhodné při zadání správné *appsettings* konfiguračního souboru k monitorování (třeba *appsettings. Development.JSON* při ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="e0f20-155">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="e0f20-156">Hodnoty hash souboru se používají k zabránění `WriteConsole` příkaz spouštění více než jednou z důvodu více tokenů zpětná volání, pokud konfigurační soubor byl změněn pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="e0f20-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="e0f20-157">Tento systém běží, dokud aplikace běží a nedá se zakázat uživatelem.</span><span class="sxs-lookup"><span data-stu-id="e0f20-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="e0f20-158">Sledujte změny konfigurace, jako služba</span><span class="sxs-lookup"><span data-stu-id="e0f20-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="e0f20-159">Implementuje vzorku:</span><span class="sxs-lookup"><span data-stu-id="e0f20-159">The sample implements:</span></span>

* <span data-ttu-id="e0f20-160">Základní spuštění token monitorování.</span><span class="sxs-lookup"><span data-stu-id="e0f20-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="e0f20-161">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="e0f20-161">Monitoring as a service.</span></span>
* <span data-ttu-id="e0f20-162">Mechanismus pro povolení a zákaz monitorování.</span><span class="sxs-lookup"><span data-stu-id="e0f20-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="e0f20-163">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e0f20-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="e0f20-164">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="e0f20-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="e0f20-165">Konstruktor třídy implementované, `ConfigurationMonitor`, zaregistruje zpětné volání oznámení změn:</span><span class="sxs-lookup"><span data-stu-id="e0f20-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="e0f20-166">`config.GetReloadToken()` poskytuje tento token.</span><span class="sxs-lookup"><span data-stu-id="e0f20-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="e0f20-167">`InvokeChanged` je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="e0f20-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="e0f20-168">`state` v tomto případě se odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke sledování stavu.</span><span class="sxs-lookup"><span data-stu-id="e0f20-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="e0f20-169">Se používají dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e0f20-169">Two properties are used:</span></span>

* <span data-ttu-id="e0f20-170">`MonitoringEnabled` &ndash; Určuje zpětné volání vhodné spustit své vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="e0f20-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="e0f20-171">`CurrentState` &ndash; Popisuje aktuální monitorování stavu pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e0f20-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="e0f20-172">`InvokeChanged` Metoda je podobná dřívější přístup, s výjimkou, že:</span><span class="sxs-lookup"><span data-stu-id="e0f20-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="e0f20-173">Spuštěním jeho kódu, není-li `MonitoringEnabled` je `true`.</span><span class="sxs-lookup"><span data-stu-id="e0f20-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="e0f20-174">Vypíše aktuální `state` v jeho `WriteConsole` výstup.</span><span class="sxs-lookup"><span data-stu-id="e0f20-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="e0f20-175">Instance `ConfigurationMonitor` je zaregistrováno jako služba v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e0f20-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="e0f20-176">Indexovou stránku nabízí uživatelský ovládací prvek v konfiguraci monitorování.</span><span class="sxs-lookup"><span data-stu-id="e0f20-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="e0f20-177">Instance `IConfigurationMonitor` se vloží do `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="e0f20-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="e0f20-178">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e0f20-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="e0f20-179">Konfigurace monitorování (`_monitor`) slouží k povolení nebo zakázání monitorování a nastavte aktuální stav pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e0f20-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="e0f20-180">Když `OnPostStartMonitoring` je spuštěn, je zapnuté monitorování, a aktuální stav je vymazán.</span><span class="sxs-lookup"><span data-stu-id="e0f20-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="e0f20-181">Když `OnPostStopMonitoring` se aktivuje, monitorování je zakázáno a stav je nastaven tak, aby odrážely, že monitorování neprobíhá.</span><span class="sxs-lookup"><span data-stu-id="e0f20-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="e0f20-182">Tlačítka v uživatelském rozhraní povolit a zakázat monitorování.</span><span class="sxs-lookup"><span data-stu-id="e0f20-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="e0f20-183">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e0f20-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="e0f20-184">Sledování změn v mezipaměti souborů</span><span class="sxs-lookup"><span data-stu-id="e0f20-184">Monitor cached file changes</span></span>

<span data-ttu-id="e0f20-185">Obsah souboru může být uložený v mezipaměti v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="e0f20-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="e0f20-186">Ukládání do mezipaměti v paměti je popsána v [ukládat do mezipaměti v paměti](xref:performance/caching/memory) tématu.</span><span class="sxs-lookup"><span data-stu-id="e0f20-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="e0f20-187">Bez nutnosti převádět další kroky, jako je například implementace je popsáno níže, *zastaralé* (zastaralé) vrátí data z mezipaměti zdrojová data mění.</span><span class="sxs-lookup"><span data-stu-id="e0f20-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="e0f20-188">Například nebere v úvahu stavu uložené v mezipaměti zdrojového souboru při obnovování [klouzavé vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) období vede k datům zastaralých souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e0f20-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="e0f20-189">Každý požadavek pro data se tato možnost obnoví klouzavou dobu vypršení platnosti, ale soubor nikdy znovu načten do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e0f20-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="e0f20-190">Žádné aplikace funkce, které používají obsah uložený v mezipaměti v souboru se mohou pravděpodobně příjem starý obsah.</span><span class="sxs-lookup"><span data-stu-id="e0f20-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="e0f20-191">Použití tokenů změn v souboru ukládání do mezipaměti scénář brání přítomnost zastaralých souborů obsahu v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e0f20-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="e0f20-192">Ukázková aplikace předvádí implementace přístupu.</span><span class="sxs-lookup"><span data-stu-id="e0f20-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="e0f20-193">Ukázka používá `GetFileContent` na:</span><span class="sxs-lookup"><span data-stu-id="e0f20-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="e0f20-194">Vrátí obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f20-194">Return file content.</span></span>
* <span data-ttu-id="e0f20-195">Implementace algoritmu opakování s exponenciální regresní pro případy, soubor zámku kde dočasně brání, čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="e0f20-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="e0f20-196">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="e0f20-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="e0f20-197">A `FileService` je vytvořená za účelem zpracování vyhledávání v mezipaměti souborů.</span><span class="sxs-lookup"><span data-stu-id="e0f20-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="e0f20-198">`GetFileContent` Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátí řízení volajícímu (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="e0f20-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="e0f20-199">Pokud obsah uložený v mezipaměti není nalezena pomocí klíče mezipaměti, budou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="e0f20-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="e0f20-200">Obsah souboru se získá pomocí `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="e0f20-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="e0f20-201">Změnit token pochází od zprostředkovatele souborů s [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="e0f20-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="e0f20-202">Zpětné volání token, který se aktivuje, když se upraví soubor.</span><span class="sxs-lookup"><span data-stu-id="e0f20-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="e0f20-203">Obsah souboru je uložené v mezipaměti s [klouzavé vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) období.</span><span class="sxs-lookup"><span data-stu-id="e0f20-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="e0f20-204">Změnit token je připojen pomocí [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) vyřazení položka mezipaměti, pokud se soubor změní, zatímco je tam uložena.</span><span class="sxs-lookup"><span data-stu-id="e0f20-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="e0f20-205">V následujícím příkladu se soubory ukládají v kořenovém adresáři obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0f20-205">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="e0f20-206">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> odkazující na aplikace <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span><span class="sxs-lookup"><span data-stu-id="e0f20-206">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="e0f20-207">`filePath` Se získá pomocí [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="e0f20-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="e0f20-208">`FileService` Je registrován v kontejneru služby spolu s paměti služby ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e0f20-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="e0f20-209">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e0f20-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="e0f20-210">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="e0f20-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="e0f20-211">Na stránce Index `OnGet` – metoda (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="e0f20-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="e0f20-212">Třída CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="e0f20-212">CompositeChangeToken class</span></span>

<span data-ttu-id="e0f20-213">Představující jeden nebo více `IChangeToken` instance do jednoho objektu, používají <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> třídy.</span><span class="sxs-lookup"><span data-stu-id="e0f20-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="e0f20-214">`HasChanged` na složený token sestavy `true` Pokud žádný token `HasChanged` je `true`.</span><span class="sxs-lookup"><span data-stu-id="e0f20-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="e0f20-215">`ActiveChangeCallbacks` na složený token sestavy `true` Pokud žádný token `ActiveChangeCallbacks` je `true`.</span><span class="sxs-lookup"><span data-stu-id="e0f20-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="e0f20-216">Pokud dojde k události více souběžných změny, je složený změnu zpětné volání vyvolat jednou.</span><span class="sxs-lookup"><span data-stu-id="e0f20-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0f20-217">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e0f20-217">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

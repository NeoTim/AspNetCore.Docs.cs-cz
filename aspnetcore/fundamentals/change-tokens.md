---
title: Zjištění změn s tokeny změn v ASP.NET Core
author: guardrex
description: Zjistěte, jak používat tokeny změn ke sledování změn.
ms.author: riande
ms.date: 11/10/2017
uid: fundamentals/change-tokens
ms.openlocfilehash: f7dad52fc19b6dc5c8668ad852e69acd65a72162
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900597"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="d06d3-103">Zjištění změn s tokeny změn v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d06d3-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="d06d3-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d06d3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d06d3-105">A *změnit token* je použít ke sledování změn stavební blok pro obecné účely, nízké úrovně.</span><span class="sxs-lookup"><span data-stu-id="d06d3-105">A *change token* is a general-purpose, low-level building block used to track changes.</span></span>

<span data-ttu-id="d06d3-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d06d3-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="d06d3-107">IChangeToken rozhraní</span><span class="sxs-lookup"><span data-stu-id="d06d3-107">IChangeToken interface</span></span>

<span data-ttu-id="d06d3-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="d06d3-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propagates notifications that a change has occurred.</span></span> <span data-ttu-id="d06d3-109">`IChangeToken` v se nachází [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="d06d3-109">`IChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="d06d3-110">Pro aplikace, které nepoužívají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější), odkaz [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) balíček NuGet v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-110">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="d06d3-111">`IChangeToken` má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="d06d3-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="d06d3-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) označují, pokud token proaktivně vyvolá zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="d06d3-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="d06d3-113">Pokud `ActiveChangedCallbacks` je nastavena na `false`zpětné volání se nikdy nevolá, a aplikace se musí dotazovat `HasChanged` změny.</span><span class="sxs-lookup"><span data-stu-id="d06d3-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="d06d3-114">Je také možné pro token nikdy zrušit, pokud žádné změny nebo je uvolněn nebo zakázané základní naslouchací proces změn.</span><span class="sxs-lookup"><span data-stu-id="d06d3-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="d06d3-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) získá hodnotu označující, pokud došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="d06d3-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) gets a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="d06d3-116">Rozhraní obsahuje jednu metodu [RegisterChangeCallback (akce&lt;objekt&gt;, objekt)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), který zaregistruje zpětné volání, která je vyvolána při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-116">The interface has one method, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="d06d3-117">`HasChanged` musí být nastavena před vyvoláním zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="d06d3-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="d06d3-118">Třída ChangeToken</span><span class="sxs-lookup"><span data-stu-id="d06d3-118">ChangeToken class</span></span>

<span data-ttu-id="d06d3-119">`ChangeToken` slouží k šíření oznámení, že došlo ke změně statické třídě.</span><span class="sxs-lookup"><span data-stu-id="d06d3-119">`ChangeToken` is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="d06d3-120">`ChangeToken` v se nachází [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="d06d3-120">`ChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="d06d3-121">Pro aplikace, které nepoužívají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), odkaz [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) balíček NuGet v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-121">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="d06d3-122">`ChangeToken` [OnChange (Func&lt;IChangeToken&gt;, akce)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) metoda registrů `Action` volat při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="d06d3-122">The `ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="d06d3-123">`Func<IChangeToken>` vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="d06d3-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="d06d3-124">`Action` je volána při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="d06d3-125">`ChangeToken` má [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, akce&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) přetížení, které přijímá Další `TState` parametr, který je předán do tokenu příjemce `Action`.</span><span class="sxs-lookup"><span data-stu-id="d06d3-125">`ChangeToken` has an [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) overload that takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="d06d3-126">`OnChange` Vrátí [IDisposable](/dotnet/api/system.idisposable).</span><span class="sxs-lookup"><span data-stu-id="d06d3-126">`OnChange` returns an [IDisposable](/dotnet/api/system.idisposable).</span></span> <span data-ttu-id="d06d3-127">Volání [Dispose](/dotnet/api/system.idisposable.dispose) token z naslouchání pro další změny se zastaví a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-127">Calling [Dispose](/dotnet/api/system.idisposable.dispose) stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="d06d3-128">Příklad používá změna tokenů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d06d3-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="d06d3-129">Změna tokenů se používají v viditelného oblastech sledování změny objektů ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d06d3-129">Change tokens are used in prominent areas of ASP.NET Core monitoring changes to objects:</span></span>

* <span data-ttu-id="d06d3-130">Pro sledování změn souborů, [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)společnosti [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) metoda vytvoří `IChangeToken` určité soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="d06d3-130">For monitoring changes to files, [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)'s [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="d06d3-131">`IChangeToken` tokeny lze přidat na položky mezipaměti k aktivaci odsunuté mezipaměť při změně.</span><span class="sxs-lookup"><span data-stu-id="d06d3-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="d06d3-132">Pro `TOptions` změní výchozí [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) provádění [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) má přetížení, která přijímá jeden nebo více [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1)instancí.</span><span class="sxs-lookup"><span data-stu-id="d06d3-132">For `TOptions` changes, the default [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) implementation of [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) has an overload that accepts one or more [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1) instances.</span></span> <span data-ttu-id="d06d3-133">Vrátí každá instance `IChangeToken` pro registraci zpětné volání oznámení změn pro možnosti sledování změn.</span><span class="sxs-lookup"><span data-stu-id="d06d3-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitoring-for-configuration-changes"></a><span data-ttu-id="d06d3-134">Sledování změn konfigurace</span><span class="sxs-lookup"><span data-stu-id="d06d3-134">Monitoring for configuration changes</span></span>

<span data-ttu-id="d06d3-135">Ve výchozím nastavení, použijte šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.JSON*, a *appsettings. Production.JSON*) k načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="d06d3-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="d06d3-136">Tyto soubory jsou nakonfigurováni pomocí [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) rozšiřující metody na [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) , který přijme `reloadOnChange` parametr (ASP.NET Core 1.1 a novější).</span><span class="sxs-lookup"><span data-stu-id="d06d3-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) extension method on [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) that accepts a `reloadOnChange` parameter (ASP.NET Core 1.1 and later).</span></span> <span data-ttu-id="d06d3-137">`reloadOnChange` Označuje, pokud byste znovu načíst konfiguraci na změny v souboru.</span><span class="sxs-lookup"><span data-stu-id="d06d3-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="d06d3-138">Toto nastavení najdete v článku [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) Komfortní metoda [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):</span><span class="sxs-lookup"><span data-stu-id="d06d3-138">See this setting in the [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) convenience method [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

<span data-ttu-id="d06d3-139">Konfigurace založená na souboru je reprezentován [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource).</span><span class="sxs-lookup"><span data-stu-id="d06d3-139">File-based configuration is represented by [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource).</span></span> <span data-ttu-id="d06d3-140">`FileConfigurationSource` používá [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) sledování souborů.</span><span class="sxs-lookup"><span data-stu-id="d06d3-140">`FileConfigurationSource` uses [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) to monitor files.</span></span>

<span data-ttu-id="d06d3-141">Ve výchozím nastavení `IFileMonitor` je poskytován [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), který používá [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) monitorovat změny v konfiguraci souboru.</span><span class="sxs-lookup"><span data-stu-id="d06d3-141">By default, the `IFileMonitor` is provided by a [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), which uses [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) to monitor for configuration file changes.</span></span>

<span data-ttu-id="d06d3-142">Ukázková aplikace předvádí dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d06d3-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="d06d3-143">Pokud *appsettings.json* změny souborů nebo prostředí verze souboru se změní, každá implementace spustí vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="d06d3-143">If either the *appsettings.json* file changes or the Environment version of the file changes, each implementation executes custom code.</span></span> <span data-ttu-id="d06d3-144">Ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="d06d3-144">The sample app writes a message to the console.</span></span>

<span data-ttu-id="d06d3-145">Konfigurační soubor `FileSystemWatcher` může aktivovat více tokenů zpětných volání pro změnu souboru v jediné konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="d06d3-145">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="d06d3-146">Ukázková implementace chrání před tímto problémem kontrolou hodnoty hash souborů v konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="d06d3-146">The sample's implementation guards against this problem by checking file hashes on the configuration files.</span></span> <span data-ttu-id="d06d3-147">Kontrolu hodnoty hash souborů zajišťuje, že aspoň jeden konfigurační soubory se změnila před spuštěním vlastního kódu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-147">Checking file hashes ensures that at least one of the configuration files has changed before running the custom code.</span></span> <span data-ttu-id="d06d3-148">Ukázka používá algoritmus hash SHA1 souboru (*Utilities/Utilities.cs*):</span><span class="sxs-lookup"><span data-stu-id="d06d3-148">The sample uses SHA1 file hashing (*Utilities/Utilities.cs*):</span></span>

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   <span data-ttu-id="d06d3-149">Opakování je implementováno s exponenciální regresní.</span><span class="sxs-lookup"><span data-stu-id="d06d3-149">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="d06d3-150">Zkuste znovu je k dispozici, protože zamykání souborů může dojít, který brání dočasně computingu nová hodnota hash na jeden ze souborů.</span><span class="sxs-lookup"><span data-stu-id="d06d3-150">The re-try is present because file locking may occur that temporarily prevents computing a new hash on one of the files.</span></span>

### <a name="simple-startup-change-token"></a><span data-ttu-id="d06d3-151">Jednoduché spouštění změnit token</span><span class="sxs-lookup"><span data-stu-id="d06d3-151">Simple startup change token</span></span>

<span data-ttu-id="d06d3-152">Zaregistrovat token příjemce `Action` zpětné volání pro upozornění na změnu na token znovu načíst konfiguraci (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="d06d3-152">Register a token consumer `Action` callback for change notifications to the configuration reload token (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="d06d3-153">`config.GetReloadToken()` poskytuje tento token.</span><span class="sxs-lookup"><span data-stu-id="d06d3-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="d06d3-154">Zpětné volání je `InvokeChanged` metody:</span><span class="sxs-lookup"><span data-stu-id="d06d3-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

<span data-ttu-id="d06d3-155">`state` Zpětného volání, které se používá a zajistěte tak předání `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="d06d3-155">The `state` of the callback is used to pass in the `IHostingEnvironment`.</span></span> <span data-ttu-id="d06d3-156">To je užitečné k určení správné *appsettings* konfigurační soubor JSON pro monitorování, *appsettings.&lt; Prostředí&gt;.json*.</span><span class="sxs-lookup"><span data-stu-id="d06d3-156">This is useful to determine the correct *appsettings* configuration JSON file to monitor, *appsettings.&lt;Environment&gt;.json*.</span></span> <span data-ttu-id="d06d3-157">Hodnoty hash souboru se používají k zabránění `WriteConsole` příkaz spouštění více než jednou z důvodu více tokenů zpětná volání, pokud konfigurační soubor byl změněn pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="d06d3-157">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="d06d3-158">Tento systém běží, dokud aplikace běží a nedá se zakázat uživatelem.</span><span class="sxs-lookup"><span data-stu-id="d06d3-158">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitoring-configuration-changes-as-a-service"></a><span data-ttu-id="d06d3-159">Sledování změn konfigurace jako služba</span><span class="sxs-lookup"><span data-stu-id="d06d3-159">Monitoring configuration changes as a service</span></span>

<span data-ttu-id="d06d3-160">Implementuje vzorku:</span><span class="sxs-lookup"><span data-stu-id="d06d3-160">The sample implements:</span></span>

* <span data-ttu-id="d06d3-161">Základní spuštění token monitorování.</span><span class="sxs-lookup"><span data-stu-id="d06d3-161">Basic startup token monitoring.</span></span>
* <span data-ttu-id="d06d3-162">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="d06d3-162">Monitoring as a service.</span></span>
* <span data-ttu-id="d06d3-163">Mechanismus pro povolení a zákaz monitorování.</span><span class="sxs-lookup"><span data-stu-id="d06d3-163">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="d06d3-164">Ukázka vytvoří `IConfigurationMonitor` rozhraní (*Extensions/ConfigurationMonitor.cs*):</span><span class="sxs-lookup"><span data-stu-id="d06d3-164">The sample establishes an `IConfigurationMonitor` interface (*Extensions/ConfigurationMonitor.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="d06d3-165">Konstruktor třídy implementované, `ConfigurationMonitor`, zaregistruje zpětné volání oznámení změn:</span><span class="sxs-lookup"><span data-stu-id="d06d3-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="d06d3-166">`config.GetReloadToken()` poskytuje tento token.</span><span class="sxs-lookup"><span data-stu-id="d06d3-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="d06d3-167">`InvokeChanged` je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="d06d3-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="d06d3-168">`state` v tomto případě se odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke sledování stavu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that is used to access the monitoring state.</span></span> <span data-ttu-id="d06d3-169">Se používají dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="d06d3-169">Two properties are used:</span></span>

* <span data-ttu-id="d06d3-170">`MonitoringEnabled` Určuje zpětné volání vhodné spustit své vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="d06d3-170">`MonitoringEnabled` indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="d06d3-171">`CurrentState` Popisuje aktuální monitorování stavu pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d06d3-171">`CurrentState` describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="d06d3-172">`InvokeChanged` Metoda je podobná dřívější přístup, s výjimkou, že:</span><span class="sxs-lookup"><span data-stu-id="d06d3-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="d06d3-173">Spuštěním jeho kódu, není-li `MonitoringEnabled` je `true`.</span><span class="sxs-lookup"><span data-stu-id="d06d3-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="d06d3-174">Poznámky k aktuální `state` v jeho `WriteConsole` výstup.</span><span class="sxs-lookup"><span data-stu-id="d06d3-174">Notes the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="d06d3-175">Instance `ConfigurationMonitor` je zaregistrováno jako služba v `ConfigureServices` z *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d06d3-175">An instance `ConfigurationMonitor` is registered as a service in `ConfigureServices` of *Startup.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="d06d3-176">Indexovou stránku nabízí uživatelský ovládací prvek v konfiguraci monitorování.</span><span class="sxs-lookup"><span data-stu-id="d06d3-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="d06d3-177">Instance `IConfigurationMonitor` se vloží do `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="d06d3-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`:</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d06d3-178">Tlačítko povolí nebo zakáže monitorování:</span><span class="sxs-lookup"><span data-stu-id="d06d3-178">A button enables and disables monitoring:</span></span>

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="d06d3-179">Když `OnPostStartMonitoring` je spuštěn, je zapnuté monitorování, a aktuální stav je vymazán.</span><span class="sxs-lookup"><span data-stu-id="d06d3-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="d06d3-180">Když `OnPostStopMonitoring` se aktivuje, monitorování je zakázáno a stav je nastaven tak, aby odrážely, že monitorování neprobíhá.</span><span class="sxs-lookup"><span data-stu-id="d06d3-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

## <a name="monitoring-cached-file-changes"></a><span data-ttu-id="d06d3-181">Sledování změn souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="d06d3-181">Monitoring cached file changes</span></span>

<span data-ttu-id="d06d3-182">Obsah souboru může být uložený v mezipaměti v paměti pomocí [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span><span class="sxs-lookup"><span data-stu-id="d06d3-182">File content can be cached in-memory using [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="d06d3-183">Ukládání do mezipaměti v paměti je popsána v [ukládat do mezipaměti v paměti](xref:performance/caching/memory) tématu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-183">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="d06d3-184">Bez nutnosti převádět další kroky, jako je například implementace je popsáno níže, *zastaralé* (zastaralé) vrátí data z mezipaměti zdrojová data mění.</span><span class="sxs-lookup"><span data-stu-id="d06d3-184">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="d06d3-185">Bez zohlednění stav uložený v mezipaměti zdrojového souboru při obnovování [klouzavé vypršení platnosti](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) období vede k zastaralá data z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="d06d3-185">Not taking into account the status of a cached source file when renewing a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period leads to stale cache data.</span></span> <span data-ttu-id="d06d3-186">Každý požadavek pro data se tato možnost obnoví klouzavou dobu vypršení platnosti, ale soubor nikdy znovu načten do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="d06d3-186">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="d06d3-187">Žádné aplikace funkce, které používají obsah uložený v mezipaměti v souboru se mohou pravděpodobně příjem starý obsah.</span><span class="sxs-lookup"><span data-stu-id="d06d3-187">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="d06d3-188">Použití tokenů změn v souboru ukládání do mezipaměti scénář brání zastaralých souborů obsahu v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="d06d3-188">Using change tokens in a file caching scenario prevents stale file content in the cache.</span></span> <span data-ttu-id="d06d3-189">Ukázková aplikace předvádí implementace přístupu.</span><span class="sxs-lookup"><span data-stu-id="d06d3-189">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="d06d3-190">Ukázka používá `GetFileContent` na:</span><span class="sxs-lookup"><span data-stu-id="d06d3-190">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="d06d3-191">Vrátí obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="d06d3-191">Return file content.</span></span>
* <span data-ttu-id="d06d3-192">Implementace algoritmu opakování s exponenciální regresní pro případy, kde soubor zámku se dočasně nedaří soubor čtená.</span><span class="sxs-lookup"><span data-stu-id="d06d3-192">Implement a retry algorithm with exponential back-off to cover cases where a file lock is temporarily preventing a file from being read.</span></span>

<span data-ttu-id="d06d3-193">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="d06d3-193">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="d06d3-194">A `FileService` je vytvořená za účelem zpracování vyhledávání v mezipaměti souborů.</span><span class="sxs-lookup"><span data-stu-id="d06d3-194">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="d06d3-195">`GetFileContent` Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátí řízení volajícímu (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="d06d3-195">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="d06d3-196">Pokud obsah uložený v mezipaměti není nalezena pomocí klíče mezipaměti, budou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="d06d3-196">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="d06d3-197">Obsah souboru se získá pomocí `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="d06d3-197">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="d06d3-198">Změnit token pochází od zprostředkovatele souborů s [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch).</span><span class="sxs-lookup"><span data-stu-id="d06d3-198">A change token is obtained from the file provider with [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch).</span></span> <span data-ttu-id="d06d3-199">Zpětné volání token, který se aktivuje, když se upraví soubor.</span><span class="sxs-lookup"><span data-stu-id="d06d3-199">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="d06d3-200">Obsah souboru je uložené v mezipaměti s [klouzavé vypršení platnosti](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) období.</span><span class="sxs-lookup"><span data-stu-id="d06d3-200">The file content is cached with a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period.</span></span> <span data-ttu-id="d06d3-201">Změnit token je připojen pomocí [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) vyřazení položka mezipaměti, pokud se soubor změní, zatímco je tam uložena.</span><span class="sxs-lookup"><span data-stu-id="d06d3-201">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) to evict the cache entry if the file changes while it's cached.</span></span>

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="d06d3-202">`FileService` Je registrován v kontejneru služby spolu s paměti služby ukládání do mezipaměti (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="d06d3-202">The `FileService` is registered in the service container along with the memory caching service (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

<span data-ttu-id="d06d3-203">Model stránky načte obsah souboru pomocí služby (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="d06d3-203">The page model loads the file's content using the service (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="d06d3-204">Třída CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="d06d3-204">CompositeChangeToken class</span></span>

<span data-ttu-id="d06d3-205">Představující jeden nebo více `IChangeToken` instance do jednoho objektu, používají [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) třídy.</span><span class="sxs-lookup"><span data-stu-id="d06d3-205">For representing one or more `IChangeToken` instances in a single object, use the [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) class.</span></span>

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

<span data-ttu-id="d06d3-206">`HasChanged` na složený token sestavy `true` Pokud žádný token `HasChanged` je `true`.</span><span class="sxs-lookup"><span data-stu-id="d06d3-206">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="d06d3-207">`ActiveChangeCallbacks` na složený token sestavy `true` Pokud žádný token `ActiveChangeCallbacks` je `true`.</span><span class="sxs-lookup"><span data-stu-id="d06d3-207">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="d06d3-208">Pokud dojde k události více souběžných změny, zpětné volání složené změn je volána přesně jednou.</span><span class="sxs-lookup"><span data-stu-id="d06d3-208">If multiple concurrent change events occur, the composite change callback is invoked exactly one time.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d06d3-209">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d06d3-209">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

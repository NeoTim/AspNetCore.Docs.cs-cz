---
<span data-ttu-id="459ce-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="459ce-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="459ce-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="459ce-102">'Blazor'</span></span>
- <span data-ttu-id="459ce-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="459ce-103">'Identity'</span></span>
- <span data-ttu-id="459ce-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="459ce-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="459ce-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="459ce-105">'Razor'</span></span>
- <span data-ttu-id="459ce-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="459ce-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="459ce-107">Zjišťovat změny pomocí tokenů změn v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="459ce-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="459ce-108">*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="459ce-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="459ce-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="459ce-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="459ce-110">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="459ce-110">IChangeToken interface</span></span>

<span data-ttu-id="459ce-111"><xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="459ce-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="459ce-112">`IChangeToken`nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="459ce-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="459ce-113">Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="459ce-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="459ce-114">`IChangeToken`má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="459ce-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="459ce-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="459ce-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="459ce-116">Pokud `ActiveChangedCallbacks` je nastaveno na `false` , zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny.</span><span class="sxs-lookup"><span data-stu-id="459ce-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="459ce-117">Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.</span><span class="sxs-lookup"><span data-stu-id="459ce-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="459ce-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>přijímá hodnotu, která indikuje, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="459ce-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="459ce-119">`IChangeToken`Rozhraní zahrnuje metodu [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="459ce-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="459ce-120">`HasChanged`musí být nastavena před vyvoláním zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="459ce-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="459ce-121">ChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="459ce-121">ChangeToken class</span></span>

<span data-ttu-id="459ce-122"><xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="459ce-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="459ce-123">`ChangeToken`nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="459ce-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="459ce-124">Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="459ce-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="459ce-125">Metoda [ChangeToken. při změně (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje, `Action` aby se volala při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="459ce-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="459ce-126">`Func<IChangeToken>`vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="459ce-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="459ce-127">`Action`se volá, když se změní token.</span><span class="sxs-lookup"><span data-stu-id="459ce-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="459ce-128">Přetížení [ChangeToken.-Change \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="459ce-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="459ce-129">`OnChange`Vrátí <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="459ce-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="459ce-130">Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="459ce-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="459ce-131">Příklad použití tokenů změny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="459ce-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="459ce-132">Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="459ce-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="459ce-133">Pro sledování změn souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="459ce-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="459ce-134">`IChangeToken`tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="459ce-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="459ce-135">Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace pro <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí.</span><span class="sxs-lookup"><span data-stu-id="459ce-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="459ce-136">Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="459ce-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="459ce-137">Monitorování pro změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="459ce-137">Monitor for configuration changes</span></span>

<span data-ttu-id="459ce-138">Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="459ce-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="459ce-139">Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , která přijímá `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="459ce-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="459ce-140">`reloadOnChange`Určuje, zda má být při změnách souborů znovu načtena konfigurace.</span><span class="sxs-lookup"><span data-stu-id="459ce-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="459ce-141">Toto nastavení se zobrazí v <xref:Microsoft.Extensions.Hosting.Host> metodě usnadnění <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="459ce-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="459ce-142">Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="459ce-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="459ce-143">`FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>Nástroj používá k monitorování souborů.</span><span class="sxs-lookup"><span data-stu-id="459ce-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="459ce-144">Ve výchozím nastavení `IFileMonitor` je k dispozici pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , který <xref:System.IO.FileSystemWatcher> Nástroj používá ke sledování změn konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="459ce-145">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="459ce-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="459ce-146">Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód &mdash; . Ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="459ce-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="459ce-147">Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="459ce-148">Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="459ce-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="459ce-149">Ukázka používá algoritmus hash souborů SHA1.</span><span class="sxs-lookup"><span data-stu-id="459ce-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="459ce-150">Opakování je implementováno pomocí exponenciálního zálohování.</span><span class="sxs-lookup"><span data-stu-id="459ce-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="459ce-151">Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="459ce-152">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="459ce-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="459ce-153">Jednoduchý token změny spuštění</span><span class="sxs-lookup"><span data-stu-id="459ce-153">Simple startup change token</span></span>

<span data-ttu-id="459ce-154">Zaregistrujte zpětné volání příjemce tokenu `Action` pro oznámení změn do tokenu opětovného načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="459ce-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="459ce-155">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="459ce-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="459ce-156">`config.GetReloadToken()`poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="459ce-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="459ce-157">Zpětné volání je `InvokeChanged` metoda:</span><span class="sxs-lookup"><span data-stu-id="459ce-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="459ce-158">`state`Zpětné volání se používá k předání do `IWebHostEnvironment` , což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appSettings. Vývoj. JSON* při ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="459ce-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="459ce-159">Hodnoty hash souborů se používají k zabránění `WriteConsole` spuštění příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="459ce-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="459ce-160">Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.</span><span class="sxs-lookup"><span data-stu-id="459ce-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="459ce-161">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="459ce-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="459ce-162">Ukázka implementuje:</span><span class="sxs-lookup"><span data-stu-id="459ce-162">The sample implements:</span></span>

* <span data-ttu-id="459ce-163">Základní monitorování spouštěcího tokenu.</span><span class="sxs-lookup"><span data-stu-id="459ce-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="459ce-164">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="459ce-164">Monitoring as a service.</span></span>
* <span data-ttu-id="459ce-165">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="459ce-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="459ce-166">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="459ce-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="459ce-167">*Rozšíření/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="459ce-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="459ce-168">Konstruktor implementované třídy, `ConfigurationMonitor` , zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="459ce-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="459ce-169">`config.GetReloadToken()`dodává token.</span><span class="sxs-lookup"><span data-stu-id="459ce-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="459ce-170">`InvokeChanged`je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="459ce-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="459ce-171">`state`V této instanci je odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke stavu monitorování.</span><span class="sxs-lookup"><span data-stu-id="459ce-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="459ce-172">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="459ce-172">Two properties are used:</span></span>

* <span data-ttu-id="459ce-173">`MonitoringEnabled`: Určuje, zda zpětné volání má spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="459ce-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="459ce-174">`CurrentState`: Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="459ce-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="459ce-175">`InvokeChanged`Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="459ce-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="459ce-176">Nespustí svůj kód `MonitoringEnabled` , pokud není `true` .</span><span class="sxs-lookup"><span data-stu-id="459ce-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="459ce-177">Vypíše aktuální `state` výstup do `WriteConsole` výstupu.</span><span class="sxs-lookup"><span data-stu-id="459ce-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="459ce-178">Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="459ce-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="459ce-179">Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace.</span><span class="sxs-lookup"><span data-stu-id="459ce-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="459ce-180">Instance `IConfigurationMonitor` je vložena do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="459ce-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="459ce-181">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="459ce-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="459ce-182">Nástroj Configuration monitor ( `_monitor` ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="459ce-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="459ce-183">Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže.</span><span class="sxs-lookup"><span data-stu-id="459ce-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="459ce-184">Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.</span><span class="sxs-lookup"><span data-stu-id="459ce-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="459ce-185">Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.</span><span class="sxs-lookup"><span data-stu-id="459ce-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="459ce-186">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="459ce-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="459ce-187">Monitorovat změny souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="459ce-187">Monitor cached file changes</span></span>

<span data-ttu-id="459ce-188">Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="459ce-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="459ce-189">Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="459ce-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="459ce-190">Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="459ce-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="459ce-191">Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="459ce-192">Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="459ce-193">Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="459ce-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="459ce-194">Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="459ce-195">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="459ce-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="459ce-196">Ukázka používá `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="459ce-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="459ce-197">Vrátit obsah souboru</span><span class="sxs-lookup"><span data-stu-id="459ce-197">Return file content.</span></span>
* <span data-ttu-id="459ce-198">Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="459ce-199">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="459ce-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="459ce-200">Vytvoří se `FileService` pro zpracování vyhledávání souborů uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="459ce-201">`GetFileContent`Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*).</span><span class="sxs-lookup"><span data-stu-id="459ce-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="459ce-202">Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="459ce-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="459ce-203">Obsah souboru se získá pomocí `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="459ce-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="459ce-204">Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="459ce-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="459ce-205">Zpětné volání tokenu se aktivuje při úpravě souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="459ce-206">Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) .</span><span class="sxs-lookup"><span data-stu-id="459ce-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="459ce-207">Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="459ce-208">V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="459ce-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="459ce-209">`IWebHostEnvironment.ContentRootFileProvider`slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši v aplikaci `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="459ce-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="459ce-210">`filePath`Je získaný pomocí [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="459ce-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="459ce-211">`FileService`Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="459ce-212">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="459ce-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="459ce-213">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="459ce-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="459ce-214">V metodě stránky indexu `OnGet` (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="459ce-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="459ce-215">CompositeChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="459ce-215">CompositeChangeToken class</span></span>

<span data-ttu-id="459ce-216">Pro reprezentaci jedné nebo více `IChangeToken` instancí v jednom objektu použijte <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> třídu.</span><span class="sxs-lookup"><span data-stu-id="459ce-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="459ce-217">`HasChanged`v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `HasChanged` `true` .</span><span class="sxs-lookup"><span data-stu-id="459ce-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="459ce-218">`ActiveChangeCallbacks`v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `ActiveChangeCallbacks` `true` .</span><span class="sxs-lookup"><span data-stu-id="459ce-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="459ce-219">Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.</span><span class="sxs-lookup"><span data-stu-id="459ce-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="459ce-220">*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.</span><span class="sxs-lookup"><span data-stu-id="459ce-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="459ce-221">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="459ce-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="459ce-222">Rozhraní IChangeToken</span><span class="sxs-lookup"><span data-stu-id="459ce-222">IChangeToken interface</span></span>

<span data-ttu-id="459ce-223"><xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="459ce-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="459ce-224">`IChangeToken`nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="459ce-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="459ce-225">Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="459ce-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="459ce-226">`IChangeToken`má dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="459ce-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="459ce-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="459ce-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="459ce-228">Pokud `ActiveChangedCallbacks` je nastaveno na `false` , zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny.</span><span class="sxs-lookup"><span data-stu-id="459ce-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="459ce-229">Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.</span><span class="sxs-lookup"><span data-stu-id="459ce-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="459ce-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>přijímá hodnotu, která indikuje, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="459ce-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="459ce-231">`IChangeToken`Rozhraní zahrnuje metodu [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu.</span><span class="sxs-lookup"><span data-stu-id="459ce-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="459ce-232">`HasChanged`musí být nastavena před vyvoláním zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="459ce-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="459ce-233">ChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="459ce-233">ChangeToken class</span></span>

<span data-ttu-id="459ce-234"><xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="459ce-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="459ce-235">`ChangeToken`nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="459ce-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="459ce-236">Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .</span><span class="sxs-lookup"><span data-stu-id="459ce-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="459ce-237">Metoda [ChangeToken. při změně (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje, `Action` aby se volala při každé změně tokenu:</span><span class="sxs-lookup"><span data-stu-id="459ce-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="459ce-238">`Func<IChangeToken>`vytvoří token.</span><span class="sxs-lookup"><span data-stu-id="459ce-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="459ce-239">`Action`se volá, když se změní token.</span><span class="sxs-lookup"><span data-stu-id="459ce-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="459ce-240">Přetížení [ChangeToken.-Change \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce tokenu `Action` .</span><span class="sxs-lookup"><span data-stu-id="459ce-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="459ce-241">`OnChange`Vrátí <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="459ce-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="459ce-242">Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.</span><span class="sxs-lookup"><span data-stu-id="459ce-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="459ce-243">Příklad použití tokenů změny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="459ce-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="459ce-244">Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:</span><span class="sxs-lookup"><span data-stu-id="459ce-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="459ce-245">Pro sledování změn souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="459ce-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="459ce-246">`IChangeToken`tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.</span><span class="sxs-lookup"><span data-stu-id="459ce-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="459ce-247">Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace pro <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí.</span><span class="sxs-lookup"><span data-stu-id="459ce-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="459ce-248">Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.</span><span class="sxs-lookup"><span data-stu-id="459ce-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="459ce-249">Monitorování pro změny konfigurace</span><span class="sxs-lookup"><span data-stu-id="459ce-249">Monitor for configuration changes</span></span>

<span data-ttu-id="459ce-250">Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="459ce-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="459ce-251">Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , která přijímá `reloadOnChange` parametr.</span><span class="sxs-lookup"><span data-stu-id="459ce-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="459ce-252">`reloadOnChange`Určuje, zda má být při změnách souborů znovu načtena konfigurace.</span><span class="sxs-lookup"><span data-stu-id="459ce-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="459ce-253">Toto nastavení se zobrazí v <xref:Microsoft.AspNetCore.WebHost> metodě usnadnění <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="459ce-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="459ce-254">Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="459ce-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="459ce-255">`FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>Nástroj používá k monitorování souborů.</span><span class="sxs-lookup"><span data-stu-id="459ce-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="459ce-256">Ve výchozím nastavení `IFileMonitor` je k dispozici pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , který <xref:System.IO.FileSystemWatcher> Nástroj používá ke sledování změn konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="459ce-257">Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace.</span><span class="sxs-lookup"><span data-stu-id="459ce-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="459ce-258">Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód &mdash; . Ukázková aplikace zapíše zprávu do konzoly.</span><span class="sxs-lookup"><span data-stu-id="459ce-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="459ce-259">Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="459ce-260">Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů.</span><span class="sxs-lookup"><span data-stu-id="459ce-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="459ce-261">Ukázka používá algoritmus hash souborů SHA1.</span><span class="sxs-lookup"><span data-stu-id="459ce-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="459ce-262">Opakování je implementováno pomocí exponenciálního zálohování.</span><span class="sxs-lookup"><span data-stu-id="459ce-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="459ce-263">Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="459ce-264">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="459ce-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="459ce-265">Jednoduchý token změny spuštění</span><span class="sxs-lookup"><span data-stu-id="459ce-265">Simple startup change token</span></span>

<span data-ttu-id="459ce-266">Zaregistrujte zpětné volání příjemce tokenu `Action` pro oznámení změn do tokenu opětovného načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="459ce-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="459ce-267">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="459ce-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="459ce-268">`config.GetReloadToken()`poskytuje token.</span><span class="sxs-lookup"><span data-stu-id="459ce-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="459ce-269">Zpětné volání je `InvokeChanged` metoda:</span><span class="sxs-lookup"><span data-stu-id="459ce-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="459ce-270">`state`Zpětné volání se používá k předání do `IHostingEnvironment` , což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appSettings. Vývoj. JSON* při ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="459ce-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="459ce-271">Hodnoty hash souborů se používají k zabránění `WriteConsole` spuštění příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="459ce-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="459ce-272">Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.</span><span class="sxs-lookup"><span data-stu-id="459ce-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="459ce-273">Sledování změn konfigurace jako služby</span><span class="sxs-lookup"><span data-stu-id="459ce-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="459ce-274">Ukázka implementuje:</span><span class="sxs-lookup"><span data-stu-id="459ce-274">The sample implements:</span></span>

* <span data-ttu-id="459ce-275">Základní monitorování spouštěcího tokenu.</span><span class="sxs-lookup"><span data-stu-id="459ce-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="459ce-276">Monitorování jako služba.</span><span class="sxs-lookup"><span data-stu-id="459ce-276">Monitoring as a service.</span></span>
* <span data-ttu-id="459ce-277">Mechanismus pro povolení a zakázání monitorování.</span><span class="sxs-lookup"><span data-stu-id="459ce-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="459ce-278">Ukázka vytvoří `IConfigurationMonitor` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="459ce-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="459ce-279">*Rozšíření/ConfigurationMonitor. cs*:</span><span class="sxs-lookup"><span data-stu-id="459ce-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="459ce-280">Konstruktor implementované třídy, `ConfigurationMonitor` , zaregistruje zpětné volání pro oznámení o změně:</span><span class="sxs-lookup"><span data-stu-id="459ce-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="459ce-281">`config.GetReloadToken()`dodává token.</span><span class="sxs-lookup"><span data-stu-id="459ce-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="459ce-282">`InvokeChanged`je metoda zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="459ce-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="459ce-283">`state`V této instanci je odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke stavu monitorování.</span><span class="sxs-lookup"><span data-stu-id="459ce-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="459ce-284">Používají se dvě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="459ce-284">Two properties are used:</span></span>

* <span data-ttu-id="459ce-285">`MonitoringEnabled`: Určuje, zda zpětné volání má spustit vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="459ce-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="459ce-286">`CurrentState`: Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="459ce-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="459ce-287">`InvokeChanged`Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="459ce-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="459ce-288">Nespustí svůj kód `MonitoringEnabled` , pokud není `true` .</span><span class="sxs-lookup"><span data-stu-id="459ce-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="459ce-289">Vypíše aktuální `state` výstup do `WriteConsole` výstupu.</span><span class="sxs-lookup"><span data-stu-id="459ce-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="459ce-290">Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="459ce-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="459ce-291">Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace.</span><span class="sxs-lookup"><span data-stu-id="459ce-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="459ce-292">Instance `IConfigurationMonitor` je vložena do `IndexModel` .</span><span class="sxs-lookup"><span data-stu-id="459ce-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="459ce-293">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="459ce-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="459ce-294">Nástroj Configuration monitor ( `_monitor` ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="459ce-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="459ce-295">Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže.</span><span class="sxs-lookup"><span data-stu-id="459ce-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="459ce-296">Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.</span><span class="sxs-lookup"><span data-stu-id="459ce-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="459ce-297">Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.</span><span class="sxs-lookup"><span data-stu-id="459ce-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="459ce-298">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="459ce-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="459ce-299">Monitorovat změny souborů v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="459ce-299">Monitor cached file changes</span></span>

<span data-ttu-id="459ce-300">Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .</span><span class="sxs-lookup"><span data-stu-id="459ce-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="459ce-301">Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) .</span><span class="sxs-lookup"><span data-stu-id="459ce-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="459ce-302">Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.</span><span class="sxs-lookup"><span data-stu-id="459ce-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="459ce-303">Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="459ce-304">Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="459ce-305">Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.</span><span class="sxs-lookup"><span data-stu-id="459ce-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="459ce-306">Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="459ce-307">Ukázková aplikace ukazuje implementaci přístupu.</span><span class="sxs-lookup"><span data-stu-id="459ce-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="459ce-308">Ukázka používá `GetFileContent` :</span><span class="sxs-lookup"><span data-stu-id="459ce-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="459ce-309">Vrátit obsah souboru</span><span class="sxs-lookup"><span data-stu-id="459ce-309">Return file content.</span></span>
* <span data-ttu-id="459ce-310">Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="459ce-311">*Nástroje/nástroje. cs*:</span><span class="sxs-lookup"><span data-stu-id="459ce-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="459ce-312">Vytvoří se `FileService` pro zpracování vyhledávání souborů uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="459ce-313">`GetFileContent`Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*).</span><span class="sxs-lookup"><span data-stu-id="459ce-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="459ce-314">Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:</span><span class="sxs-lookup"><span data-stu-id="459ce-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="459ce-315">Obsah souboru se získá pomocí `GetFileContent` .</span><span class="sxs-lookup"><span data-stu-id="459ce-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="459ce-316">Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="459ce-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="459ce-317">Zpětné volání tokenu se aktivuje při úpravě souboru.</span><span class="sxs-lookup"><span data-stu-id="459ce-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="459ce-318">Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) .</span><span class="sxs-lookup"><span data-stu-id="459ce-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="459ce-319">Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="459ce-320">V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="459ce-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="459ce-321">[IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši v aplikaci <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> .</span><span class="sxs-lookup"><span data-stu-id="459ce-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="459ce-322">`filePath`Je získaný pomocí [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="459ce-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="459ce-323">`FileService`Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="459ce-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="459ce-324">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="459ce-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="459ce-325">Model stránky načte obsah souboru pomocí služby.</span><span class="sxs-lookup"><span data-stu-id="459ce-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="459ce-326">V metodě stránky indexu `OnGet` (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="459ce-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="459ce-327">CompositeChangeToken – třída</span><span class="sxs-lookup"><span data-stu-id="459ce-327">CompositeChangeToken class</span></span>

<span data-ttu-id="459ce-328">Pro reprezentaci jedné nebo více `IChangeToken` instancí v jednom objektu použijte <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> třídu.</span><span class="sxs-lookup"><span data-stu-id="459ce-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="459ce-329">`HasChanged`v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `HasChanged` `true` .</span><span class="sxs-lookup"><span data-stu-id="459ce-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="459ce-330">`ActiveChangeCallbacks`v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `ActiveChangeCallbacks` `true` .</span><span class="sxs-lookup"><span data-stu-id="459ce-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="459ce-331">Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.</span><span class="sxs-lookup"><span data-stu-id="459ce-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="459ce-332">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="459ce-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

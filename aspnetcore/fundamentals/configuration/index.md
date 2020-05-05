---
title: Konfigurace v ASP.NET Core
author: rick-anderson
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: c2a7ef9c1523bc179524f328905f3a4b1460a1a5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774493"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="0a5c9-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a5c9-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="0a5c9-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a5c9-105">Konfigurace v ASP.NET Core provádí použití jednoho nebo více [poskytovatelů konfigurace](#cp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="0a5c9-106">Poskytovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="0a5c9-107">Soubory nastavení, například *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="0a5c9-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="0a5c9-108">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-108">Environment variables</span></span>
* <span data-ttu-id="0a5c9-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0a5c9-109">Azure Key Vault</span></span>
* <span data-ttu-id="0a5c9-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="0a5c9-110">Azure App Configuration</span></span>
* <span data-ttu-id="0a5c9-111">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-111">Command-line arguments</span></span>
* <span data-ttu-id="0a5c9-112">Vlastní zprostředkovatelé, nainstalované nebo vytvořené</span><span class="sxs-lookup"><span data-stu-id="0a5c9-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="0a5c9-113">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="0a5c9-113">Directory files</span></span>
* <span data-ttu-id="0a5c9-114">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="0a5c9-114">In-memory .NET objects</span></span>

<span data-ttu-id="0a5c9-115">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a5c9-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="0a5c9-116">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-116">Default configuration</span></span>

<span data-ttu-id="0a5c9-117">ASP.NET Core webové aplikace vytvořené pomocí [dotnet New](/dotnet/core/tools/dotnet-new) nebo sady Visual Studio generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="0a5c9-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="0a5c9-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá existující `IConfiguration` jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="0a5c9-120">Ve výchozím případu konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj konfigurace _aplikace_ .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="0a5c9-121">[appSettings. JSON](#appsettingsjson) s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="0a5c9-122">*appSettings.* `Environment` *. JSON* s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="0a5c9-123">Například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="0a5c9-124">[Tajné kódy aplikace](xref:security/app-secrets) v případě, že aplikace `Development` běží v prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="0a5c9-125">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="0a5c9-126">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="0a5c9-127">Poskytovatelé konfigurace, kteří jsou přidáni později, přepíší předchozí nastavení klíče.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="0a5c9-128">Například pokud `MyKey` je nastavena v souboru *appSettings. JSON* i v prostředí, je použita hodnota prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="0a5c9-129">Pomocí výchozích zprostředkovatelů konfigurace přepíše [Poskytovatel konfigurace příkazového řádku](#command-line-configuration-provider) všechny ostatní poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="0a5c9-130">Další informace o `CreateDefaultBuilder`najdete v tématu [výchozí nastavení tvůrce](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="0a5c9-131">Následující kód zobrazuje povolené poskytovatele konfigurace v pořadí, v jakém byly přidány:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="0a5c9-132">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="0a5c9-132">appsettings.json</span></span>

<span data-ttu-id="0a5c9-133">Vezměte v úvahu následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="0a5c9-134">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-135">Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načtení konfigurace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="0a5c9-136">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="0a5c9-136">*appsettings.json*</span></span>
1. <span data-ttu-id="0a5c9-137">*appSettings.* `Environment` *. JSON* : například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="0a5c9-138">Verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="0a5c9-139">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0a5c9-140">*appSettings*. `Environment`. hodnoty *JSON* přepíší klíče v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="0a5c9-141">Ve výchozím nastavení by například:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-141">For example, by default:</span></span>

* <span data-ttu-id="0a5c9-142">Ve vývoji, *appSettings*. ***Vývoj***. konfigurace *JSON* Přepisuje hodnoty nalezené v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="0a5c9-143">V produkčním prostředí, *appSettings*. ***Provozní***prostředí. konfigurace *JSON* Přepisuje hodnoty nalezené v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="0a5c9-144">Například při nasazení aplikace do Azure.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="0a5c9-145">Vytvoření vazby hierarchických konfiguračních dat pomocí vzoru možností</span><span class="sxs-lookup"><span data-stu-id="0a5c9-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="0a5c9-146">Upřednostňovaným způsobem, jak číst související konfigurační hodnoty, je použít [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0a5c9-147">Například pro čtení následujících konfiguračních hodnot:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="0a5c9-148">Vytvořte následující `PositionOptions` třídu:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="0a5c9-149">Všechny vlastnosti Public pro čtení i zápis typu jsou vázané.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="0a5c9-150">Pole nejsou ***svázána*** .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="0a5c9-151">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-151">The following code:</span></span>

* <span data-ttu-id="0a5c9-152">Volá [ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) pro svázání `PositionOptions` třídy s `Position` oddílem.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="0a5c9-153">Zobrazí `Position` konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="0a5c9-155">`ConfigurationBinder.Get<T>`může být pohodlnější než použití `ConfigurationBinder.Bind`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="0a5c9-156">Následující kód ukazuje, jak použít `ConfigurationBinder.Get<T>` s `PositionOptions` třídou:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-157">Alternativním přístupem při použití ***vzoru možností*** je vytvořit vazby `Position` oddílu a přidat ho do [kontejneru služby pro vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0a5c9-158">V následujícím kódu `PositionOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="0a5c9-159">Pomocí předchozího kódu přečte následující kód možnosti pozice:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-160">Pomocí [výchozí](#default) konfigurace, *appSettings. JSON* a *appSettings.* `Environment`soubory *. JSON* jsou povolené pomocí [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="0a5c9-161">Změny provedené v souboru *appSettings. JSON* a *appSettings.* `Environment`soubor *. JSON* ***po*** spuštění aplikace si přečte [Poskytovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="0a5c9-162">Informace o přidání dalších konfiguračních souborů JSON najdete v části [Poskytovatel konfigurace JSON](#jcp) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="0a5c9-163">Správce zabezpečení a tajného klíče</span><span class="sxs-lookup"><span data-stu-id="0a5c9-163">Security and secret manager</span></span>

<span data-ttu-id="0a5c9-164">Pokyny pro konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="0a5c9-165">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="0a5c9-166">[Správce tajného klíče](xref:security/app-secrets) se dá použít k ukládání tajných kódů do vývoje.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="0a5c9-167">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="0a5c9-168">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="0a5c9-169">Ve [výchozím nastavení](#default)přečte [správce tajných klíčů](xref:security/app-secrets) nastavení konfigurace po souboru *appSettings. JSON* a *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="0a5c9-170">Další informace o ukládání hesel nebo jiných citlivých dat:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="0a5c9-171"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="0a5c9-172">Správce tajného kódu používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="0a5c9-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="0a5c9-174">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="0a5c9-175">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-175">Environment variables</span></span>

<span data-ttu-id="0a5c9-176">Použijete-li [výchozí](#default) konfiguraci <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> , načte konfiguraci z proměnných prostředí klíč-hodnota po čtení *appSettings. JSON*, *appSettings.* `Environment` *. JSON*a [správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="0a5c9-177">Proto hodnoty klíčů načtené z prostředí přepisují hodnoty načtené z hodnot *appSettings. JSON*, *appSettings.* `Environment` *. JSON*a správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="0a5c9-178">Následující `set` příkazy:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-178">The following `set` commands:</span></span>

* <span data-ttu-id="0a5c9-179">Nastavte klíče prostředí a hodnoty v [předchozím příkladu](#appsettingsjson) ve Windows.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="0a5c9-180">Při použití [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)otestujte nastavení.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="0a5c9-181">`dotnet run` Příkaz musí být spuštěn v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="0a5c9-182">Předchozí nastavení prostředí:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-182">The preceding environment settings:</span></span>

* <span data-ttu-id="0a5c9-183">Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="0a5c9-184">Nebudou přečteny pomocí prohlížečů spuštěných v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="0a5c9-185">K nastavení klíčů a hodnot prostředí ve Windows se dají použít následující příkazy [setx](/windows-server/administration/windows-commands/setx) .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="0a5c9-186">Na rozdíl `set`od `setx` platí, že nastavení jsou trvalá.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="0a5c9-187">`/M`nastaví proměnnou v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="0a5c9-188">Pokud se `/M` přepínač nepoužívá, je nastavena proměnná prostředí uživatele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="0a5c9-189">Chcete-li otestovat předchozí příkazy, přepište *appSettings. JSON* a *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="0a5c9-190">Se sadou Visual Studio: Ukončete a restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="0a5c9-191">Pomocí rozhraní příkazového řádku: spusťte nové příkazové okno a `dotnet run`zadejte.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="0a5c9-192">Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem pro určení předpony pro proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="0a5c9-193">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-193">In the preceding code:</span></span>

* <span data-ttu-id="0a5c9-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="0a5c9-195">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="0a5c9-196">Proměnné prostředí nastavené s `MyCustomPrefix_` předponou přepíšou [výchozí poskytovatele konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="0a5c9-197">To zahrnuje proměnné prostředí bez předpony.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="0a5c9-198">Pokud jsou načteny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="0a5c9-199">Vlastní předponu otestujete následujícími příkazy:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="0a5c9-200">[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty příkazového řádku s `DOTNET_` předponou a `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="0a5c9-201">`DOTNET_` Předpony `ASPNETCORE_` a jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="0a5c9-202">Další informace o konfiguraci hostitele a aplikace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="0a5c9-203">V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="0a5c9-204">Azure App Service nastavení aplikace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="0a5c9-205">Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="0a5c9-206">Vystaveno jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-206">Exposed as environment variables.</span></span>

<span data-ttu-id="0a5c9-207">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="0a5c9-208">Informace o připojovacích řetězcích Azure Database najdete v tématu [předpony připojovacího řetězce](#constr) .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="0a5c9-209">Příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="0a5c9-209">Command-line</span></span>

<span data-ttu-id="0a5c9-210">Použijete-li [výchozí](#default) konfiguraci <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> , načte konfiguraci z dvojice klíč-hodnota argumentu klíč-hodnota po následujících zdrojích konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="0a5c9-211">*appSettings. JSON* a *appSettings*. `Environment`. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="0a5c9-212">[Tajné kódy aplikací (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="0a5c9-213">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-213">Environment variables.</span></span>

<span data-ttu-id="0a5c9-214">Ve [výchozím nastavení](#default)jsou konfigurační hodnoty nastavené pro přepisy hodnot konfigurace na příkazovém řádku nastavené se všemi ostatními zprostředkovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="0a5c9-215">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-215">Command-line arguments</span></span>

<span data-ttu-id="0a5c9-216">Následující příkaz nastaví klíče a hodnoty pomocí `=`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="0a5c9-217">Následující příkaz nastaví klíče a hodnoty pomocí `/`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="0a5c9-218">Následující příkaz nastaví klíče a hodnoty pomocí `--`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="0a5c9-219">Hodnota klíče:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-219">The key value:</span></span>

* <span data-ttu-id="0a5c9-220">Musí následovat `=`po, nebo klíč musí mít předponu `--` nebo `/` , pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="0a5c9-221">Není vyžadováno, `=` Pokud je použit.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="0a5c9-222">Například, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="0a5c9-223">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, `=` které se používají s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="0a5c9-224">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="0a5c9-224">Switch mappings</span></span>

<span data-ttu-id="0a5c9-225">Mapování přepínačů povolují logiku nahrazení názvu **klíče** .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="0a5c9-226">Poskytněte slovník pro nahrazení přepínačů v <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodě.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="0a5c9-227">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="0a5c9-228">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota Dictionary zpátky, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="0a5c9-229">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou (`-`).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="0a5c9-230">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="0a5c9-231">Přepínače musí `-` začínat `--`na nebo.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="0a5c9-232">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="0a5c9-233">Chcete-li použít slovník mapování přepínačů, předejte jej do `AddCommandLine`volání:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="0a5c9-234">Následující kód ukazuje klíčové hodnoty pro nahrazené klíče:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-235">Spusťte následující příkaz pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="0a5c9-236">Poznámka: v současné `=` době nelze použít k nastavení hodnot nahrazení klíče jedinou pomlčkou `-`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="0a5c9-237">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="0a5c9-238">Následující příkaz funguje pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="0a5c9-239">Pro aplikace, které používají mapování přepínačů, by `CreateDefaultBuilder` volání nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="0a5c9-240">Volání metody nezahrnuje mapované přepínače a neexistuje způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0a5c9-241">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` `AddCommandLine` metoda metody mohla zpracovat argumenty i slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="0a5c9-242">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="0a5c9-242">Hierarchical configuration data</span></span>

<span data-ttu-id="0a5c9-243">Rozhraní API pro konfiguraci čte hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="0a5c9-244">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="0a5c9-245">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-246">Upřednostňovaným způsobem, jak číst hierarchická konfigurační data, je použít vzor možností.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="0a5c9-247">Další informace najdete v tématu [vázání hierarchických konfiguračních dat](#optpat) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="0a5c9-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>metody <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="0a5c9-249">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="0a5c9-250">Konfigurační klíče a hodnoty</span><span class="sxs-lookup"><span data-stu-id="0a5c9-250">Configuration keys and values</span></span>

<span data-ttu-id="0a5c9-251">Konfigurační klíče:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-251">Configuration keys:</span></span>

* <span data-ttu-id="0a5c9-252">Rozlišují se malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-252">Are case-insensitive.</span></span> <span data-ttu-id="0a5c9-253">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="0a5c9-254">Pokud je klíč a hodnota nastavená ve více poskytovatelích konfigurace, použije se hodnota z posledního přidaného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="0a5c9-255">Další informace najdete v tématu [výchozí konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="0a5c9-256">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="0a5c9-256">Hierarchical keys</span></span>
  * <span data-ttu-id="0a5c9-257">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky`:`() na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="0a5c9-258">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="0a5c9-259">Dvojité podtržítko, `__`, je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="0a5c9-260">V Azure Key Vault hierarchické klíče používají `--` jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="0a5c9-261">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) se `:` při načtení `--` tajných klíčů do konfigurace aplikace automaticky nahradí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="0a5c9-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="0a5c9-263">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#boa) .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="0a5c9-264">Konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-264">Configuration values:</span></span>

* <span data-ttu-id="0a5c9-265">Jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-265">Are strings.</span></span>
* <span data-ttu-id="0a5c9-266">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="0a5c9-267">Poskytovatelé konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-267">Configuration providers</span></span>

<span data-ttu-id="0a5c9-268">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="0a5c9-269">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="0a5c9-269">Provider</span></span> | <span data-ttu-id="0a5c9-270">Poskytuje konfiguraci z</span><span class="sxs-lookup"><span data-stu-id="0a5c9-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="0a5c9-271">Poskytovatel konfigurace Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0a5c9-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="0a5c9-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0a5c9-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="0a5c9-273">Poskytovatel konfigurace Azure App</span><span class="sxs-lookup"><span data-stu-id="0a5c9-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="0a5c9-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="0a5c9-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="0a5c9-275">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="0a5c9-276">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-276">Command-line parameters</span></span> |
| [<span data-ttu-id="0a5c9-277">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="0a5c9-278">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="0a5c9-278">Custom source</span></span> |
| [<span data-ttu-id="0a5c9-279">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="0a5c9-280">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-280">Environment variables</span></span> |
| [<span data-ttu-id="0a5c9-281">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="0a5c9-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="0a5c9-282">Soubory INI, JSON a XML</span><span class="sxs-lookup"><span data-stu-id="0a5c9-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="0a5c9-283">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="0a5c9-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="0a5c9-284">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="0a5c9-284">Directory files</span></span> |
| [<span data-ttu-id="0a5c9-285">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="0a5c9-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="0a5c9-286">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="0a5c9-286">In-memory collections</span></span> |
| [<span data-ttu-id="0a5c9-287">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="0a5c9-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="0a5c9-288">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="0a5c9-288">File in the user profile directory</span></span> |

<span data-ttu-id="0a5c9-289">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou zadáni poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="0a5c9-290">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="0a5c9-291">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="0a5c9-292">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="0a5c9-292">*appsettings.json*</span></span>
1. <span data-ttu-id="0a5c9-293">*appSettings*. `Environment`. formát *JSON*</span><span class="sxs-lookup"><span data-stu-id="0a5c9-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="0a5c9-294">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="0a5c9-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="0a5c9-295">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="0a5c9-296">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="0a5c9-297">Běžný postup je přidat poskytovatele konfigurace příkazového řádku na poslední v řadě poskytovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="0a5c9-298">Předchozí sekvence zprostředkovatelů se používá ve [výchozí konfiguraci](#default).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="0a5c9-299">Předpony připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="0a5c9-299">Connection string prefixes</span></span>

<span data-ttu-id="0a5c9-300">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro proměnné prostředí pro čtyři připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="0a5c9-301">Tyto připojovací řetězce jsou součástí konfigurace připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="0a5c9-302">Proměnné prostředí s předponami, které jsou uvedené v tabulce, se načtou do aplikace s [výchozí konfigurací](#default) nebo když není dodána žádná `AddEnvironmentVariables`předpona.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="0a5c9-303">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="0a5c9-303">Connection string prefix</span></span> | <span data-ttu-id="0a5c9-304">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="0a5c9-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="0a5c9-305">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="0a5c9-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="0a5c9-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="0a5c9-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="0a5c9-307">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="0a5c9-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="0a5c9-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="0a5c9-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="0a5c9-309">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="0a5c9-310">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="0a5c9-311">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="0a5c9-312">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-312">Environment variable key</span></span> | <span data-ttu-id="0a5c9-313">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="0a5c9-313">Converted configuration key</span></span> | <span data-ttu-id="0a5c9-314">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="0a5c9-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="0a5c9-315">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="0a5c9-316">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="0a5c9-317">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="0a5c9-318">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="0a5c9-319">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="0a5c9-320">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="0a5c9-321">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="0a5c9-322">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="0a5c9-322">JSON configuration provider</span></span>

<span data-ttu-id="0a5c9-323"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Načte konfiguraci z párů klíč-hodnota souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="0a5c9-324">Přetížení můžou specifikovat:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-324">Overloads can specify:</span></span>

* <span data-ttu-id="0a5c9-325">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-325">Whether the file is optional.</span></span>
* <span data-ttu-id="0a5c9-326">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="0a5c9-327">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="0a5c9-328">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-328">The preceding code:</span></span>

* <span data-ttu-id="0a5c9-329">Nakonfiguruje zprostředkovatele konfigurace JSON pro načtení souboru *MyConfig. JSON* s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="0a5c9-330">`optional: true`: Soubor je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="0a5c9-331">`reloadOnChange: true`: Soubor se znovu načte při uložení změn.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="0a5c9-332">Přečte [výchozí poskytovatele konfigurace](#default) před souborem *MyConfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="0a5c9-333">Nastavení v přepsání souboru *MyConfig. JSON* ve výchozích poskytovatelích konfigurace, včetně [poskytovatele konfigurace proměnných prostředí](#evcp) a [poskytovatele konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="0a5c9-334">***Obvykle nechcete, aby vlastní*** soubor JSON přepsal hodnoty nastavené ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a v [poskytovateli konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="0a5c9-335">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="0a5c9-336">V předchozím kódu se jedná o nastavení v *MyConfig. JSON* a *MyConfig*. `Environment`. soubory *JSON* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="0a5c9-337">Přepsat nastavení v souboru *appSettings. JSON* a *appSettings*. `Environment`. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="0a5c9-338">Jsou přepsány nastavením ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a [poskytovatelem konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="0a5c9-339">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyConfig. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="0a5c9-340">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="0a5c9-341">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="0a5c9-341">File configuration provider</span></span>

<span data-ttu-id="0a5c9-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="0a5c9-343">Následující poskytovatelé konfigurace jsou odvozeni z `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="0a5c9-344">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="0a5c9-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="0a5c9-345">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="0a5c9-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="0a5c9-346">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="0a5c9-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="0a5c9-347">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="0a5c9-347">INI configuration provider</span></span>

<span data-ttu-id="0a5c9-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="0a5c9-349">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="0a5c9-350">V předchozím kódu se jedná o nastavení v *souboru MyIniConfig. ini* a *MyIniConfig*. `Environment`. soubory *ini* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="0a5c9-351">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="0a5c9-352">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="0a5c9-353">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="0a5c9-354">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="0a5c9-355">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="0a5c9-355">XML configuration provider</span></span>

<span data-ttu-id="0a5c9-356"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="0a5c9-357">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="0a5c9-358">V předchozím kódu jsou nastavení v *souboru MyXMLFile. XML* a *MyXMLFile*. `Environment`. soubory *XML* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="0a5c9-359">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="0a5c9-360">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="0a5c9-361">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="0a5c9-362">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-363">Opakující se prvky, které používají stejný název elementu fungují, `name` Pokud je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="0a5c9-364">Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-365">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="0a5c9-366">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0a5c9-367">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="0a5c9-367">key:attribute</span></span>
* <span data-ttu-id="0a5c9-368">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="0a5c9-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="0a5c9-369">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="0a5c9-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="0a5c9-370"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="0a5c9-371">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-371">The key is the file name.</span></span> <span data-ttu-id="0a5c9-372">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-372">The value contains the file's contents.</span></span> <span data-ttu-id="0a5c9-373">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="0a5c9-374">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="0a5c9-375">`directoryPath` Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="0a5c9-376">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="0a5c9-377">`Action<KeyPerFileConfigurationSource>` Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="0a5c9-378">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="0a5c9-379">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="0a5c9-380">Například název `Logging__LogLevel__System` souboru vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="0a5c9-381">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="0a5c9-382">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="0a5c9-382">Memory configuration provider</span></span>

<span data-ttu-id="0a5c9-383"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="0a5c9-384">Následující kód přidá kolekci paměti do konfiguračního systému:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="0a5c9-385">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-386">V předchozím kódu `config.AddInMemoryCollection(Dict)` se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="0a5c9-387">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="0a5c9-388">Viz [vazba pole](#boa) pro jiný příklad pomocí `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-388">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="0a5c9-389">GetValue</span><span class="sxs-lookup"><span data-stu-id="0a5c9-389">GetValue</span></span>

<span data-ttu-id="0a5c9-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-391">Pokud `NumberKey` v předchozím kódu není v konfiguraci nalezeno, `99` je použita výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-391">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="0a5c9-392">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="0a5c9-392">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="0a5c9-393">V následujících příkladech zvažte následující soubor *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-393">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="0a5c9-394">Následující kód přidá *MySubsection. JSON* ke zprostředkovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-394">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="0a5c9-395">GetSection</span><span class="sxs-lookup"><span data-stu-id="0a5c9-395">GetSection</span></span>

<span data-ttu-id="0a5c9-396">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí dílčí část konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-396">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="0a5c9-397">Následující kód vrátí hodnoty pro `section1`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-397">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-398">Následující kód vrátí hodnoty pro `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-398">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-399">`GetSection`nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-399">`GetSection` never returns `null`.</span></span> <span data-ttu-id="0a5c9-400">Pokud se nenalezne shodný oddíl, vrátí `IConfigurationSection` se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-400">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="0a5c9-401">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-401">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="0a5c9-402"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-402">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="0a5c9-403">GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="0a5c9-403">GetChildren and Exists</span></span>

<span data-ttu-id="0a5c9-404">Následující kód volá [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a vrátí hodnoty pro `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-404">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-405">Předchozí kód volá [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pro ověření, zda existuje oddíl:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-405">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="0a5c9-406">Vazba pole</span><span class="sxs-lookup"><span data-stu-id="0a5c9-406">Bind an array</span></span>

<span data-ttu-id="0a5c9-407">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-407">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="0a5c9-408">Libovolný formát pole, který zveřejňuje numerický klíčový segment, je schopný vytvořit vazbu pole k poli třídy [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-408">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="0a5c9-409">V [ukázkovém stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)zvažte *myArray. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-409">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="0a5c9-410">Následující kód přidá *myArray. JSON* ke zprostředkovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-410">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="0a5c9-411">Následující kód přečte konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-411">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-412">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="0a5c9-413">V předchozím výstupu má index 3 hodnotu odpovídající hodnotě `value40` `"4": "value40",` v *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-413">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="0a5c9-414">Vazby vázaného pole jsou souvislé a nejsou vázané na index konfiguračního klíče.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-414">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="0a5c9-415">Pořadač konfigurace nemůže svázat hodnoty null ani vytvářet položky null ve vázaných objektech.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-415">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="0a5c9-416">Následující kód načte `array:entries` konfiguraci pomocí metody <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> rozšíření:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-416">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="0a5c9-417">Následující kód přečte konfiguraci v `arrayDict` `Dictionary` a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-417">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-418">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="0a5c9-419">Index &num;3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu. `value4`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-419">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="0a5c9-420">Když jsou data konfigurace obsahující pole svázané, používají se k iteraci konfiguračních dat při vytváření objektu pole indexy v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-420">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="0a5c9-421">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-421">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="0a5c9-422">Chybějící položka konfigurace pro index &num;3 se dá zadat předtím, než se vazba `ArrayExample` na instanci doplní jakýmkoli poskytovatelem konfigurace, &num;který načte dvojici klíč/hodnota indexu 3.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-422">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="0a5c9-423">V ukázkovém stažení zvažte následující soubor *hodnota3. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-423">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="0a5c9-424">Následující kód obsahuje konfiguraci pro *hodnota3. JSON* a `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-424">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="0a5c9-425">Následující kód přečte předchozí konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-425">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="0a5c9-426">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="0a5c9-427">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-427">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="0a5c9-428">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-428">Custom configuration provider</span></span>

<span data-ttu-id="0a5c9-429">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-429">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="0a5c9-430">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-430">The provider has the following characteristics:</span></span>

* <span data-ttu-id="0a5c9-431">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-431">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="0a5c9-432">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte `ConfigurationBuilder` sekundární pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-432">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="0a5c9-433">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-433">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="0a5c9-434">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-434">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="0a5c9-435">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-435">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="0a5c9-436">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-436">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="0a5c9-437">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-437">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="0a5c9-438">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-438">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="0a5c9-439">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="0a5c9-440">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-440">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="0a5c9-441">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="0a5c9-442">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-442">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="0a5c9-443">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-443">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="0a5c9-444">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-444">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="0a5c9-445">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="0a5c9-446">Metoda `AddEFConfiguration` rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-446">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="0a5c9-447">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-447">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="0a5c9-448">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-448">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="0a5c9-449">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="0a5c9-449">Access configuration in Startup</span></span>

<span data-ttu-id="0a5c9-450">Následující kód zobrazuje konfigurační data v `Startup` metodách:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-450">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="0a5c9-451">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-451">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="0a5c9-452">Konfigurace přístupu v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0a5c9-452">Access configuration in Razor Pages</span></span>

<span data-ttu-id="0a5c9-453">Následující kód zobrazuje konfigurační data na stránce Razor:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-453">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="0a5c9-454">Přístup ke konfiguraci v souboru zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="0a5c9-454">Access configuration in a MVC view file</span></span>

<span data-ttu-id="0a5c9-455">Následující kód zobrazuje konfigurační data v zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-455">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="0a5c9-456">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-456">Host versus app configuration</span></span>

<span data-ttu-id="0a5c9-457">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="0a5c9-458">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="0a5c9-459">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="0a5c9-460">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="0a5c9-461">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele <xref:fundamentals/index#host>, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="0a5c9-462">Výchozí konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="0a5c9-462">Default host configuration</span></span>

<span data-ttu-id="0a5c9-463">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="0a5c9-464">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="0a5c9-465">Proměnné prostředí s `DOTNET_` předponou (například `DOTNET_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="0a5c9-466">Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="0a5c9-467">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="0a5c9-468">Je navázána výchozí konfigurace webového`ConfigureWebHostDefaults`hostitele ():</span><span class="sxs-lookup"><span data-stu-id="0a5c9-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="0a5c9-469">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="0a5c9-470">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="0a5c9-471">Přidejte middleware předávaných hlaviček `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je proměnná prostředí `true`nastavena na.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="0a5c9-472">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="0a5c9-473">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-473">Other configuration</span></span>

<span data-ttu-id="0a5c9-474">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="0a5c9-475">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="0a5c9-476">*Launch. JSON*/*launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané tady:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="0a5c9-477">V <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="0a5c9-478">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="0a5c9-479">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="0a5c9-480">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-480">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="0a5c9-481">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="0a5c9-481">Add configuration from an external assembly</span></span>

<span data-ttu-id="0a5c9-482"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-482">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="0a5c9-483">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-483">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a5c9-484">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="0a5c9-484">Additional resources</span></span>

* [<span data-ttu-id="0a5c9-485">Zdrojový kód konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-485">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0a5c9-486">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-486">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="0a5c9-487">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-487">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="0a5c9-488">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0a5c9-488">Azure Key Vault</span></span>
* <span data-ttu-id="0a5c9-489">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="0a5c9-489">Azure App Configuration</span></span>
* <span data-ttu-id="0a5c9-490">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-490">Command-line arguments</span></span>
* <span data-ttu-id="0a5c9-491">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-491">Custom providers (installed or created)</span></span>
* <span data-ttu-id="0a5c9-492">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="0a5c9-492">Directory files</span></span>
* <span data-ttu-id="0a5c9-493">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-493">Environment variables</span></span>
* <span data-ttu-id="0a5c9-494">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="0a5c9-494">In-memory .NET objects</span></span>
* <span data-ttu-id="0a5c9-495">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="0a5c9-495">Settings files</span></span>

<span data-ttu-id="0a5c9-496">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-496">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="0a5c9-497">Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-497">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="0a5c9-498">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-498">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="0a5c9-499">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-499">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="0a5c9-500">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-500">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="0a5c9-501">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a5c9-501">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="0a5c9-502">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-502">Host versus app configuration</span></span>

<span data-ttu-id="0a5c9-503">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-503">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="0a5c9-504">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-504">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="0a5c9-505">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-505">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="0a5c9-506">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-506">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="0a5c9-507">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele <xref:fundamentals/index#host>, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-507">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="0a5c9-508">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-508">Other configuration</span></span>

<span data-ttu-id="0a5c9-509">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-509">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="0a5c9-510">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-510">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="0a5c9-511">*Launch. JSON*/*launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané tady:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-511">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="0a5c9-512">V <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-512">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="0a5c9-513">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-513">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="0a5c9-514">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-514">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="0a5c9-515">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-515">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="0a5c9-516">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-516">Default configuration</span></span>

<span data-ttu-id="0a5c9-517">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-517">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="0a5c9-518">`CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-518">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="0a5c9-519">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-519">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="0a5c9-520">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-520">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="0a5c9-521">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-521">Host configuration is provided from:</span></span>
  * <span data-ttu-id="0a5c9-522">Proměnné prostředí s `ASPNETCORE_` předponou (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-522">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="0a5c9-523">Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-523">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="0a5c9-524">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-524">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="0a5c9-525">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-525">App configuration is provided from:</span></span>
  * <span data-ttu-id="0a5c9-526">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-526">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="0a5c9-527">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-527">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="0a5c9-528">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží `Development` v prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="0a5c9-528">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="0a5c9-529">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-529">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="0a5c9-530">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-530">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="0a5c9-531">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="0a5c9-531">Security</span></span>

<span data-ttu-id="0a5c9-532">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-532">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="0a5c9-533">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-533">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="0a5c9-534">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-534">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="0a5c9-535">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-535">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="0a5c9-536">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-536">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="0a5c9-537"><xref:security/app-secrets>&ndash; Obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-537"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="0a5c9-538">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-538">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="0a5c9-539">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-539">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="0a5c9-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="0a5c9-541">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-541">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="0a5c9-542">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="0a5c9-542">Hierarchical configuration data</span></span>

<span data-ttu-id="0a5c9-543">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-543">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="0a5c9-544">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-544">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="0a5c9-545">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-545">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="0a5c9-546">Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) pro zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-546">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="0a5c9-547">section0:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-547">section0:key0</span></span>
* <span data-ttu-id="0a5c9-548">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-548">section0:key1</span></span>
* <span data-ttu-id="0a5c9-549">section1:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-549">section1:key0</span></span>
* <span data-ttu-id="0a5c9-550">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-550">section1:key1</span></span>

<span data-ttu-id="0a5c9-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>metody <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="0a5c9-552">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-552">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="0a5c9-553">Konvence</span><span class="sxs-lookup"><span data-stu-id="0a5c9-553">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="0a5c9-554">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="0a5c9-554">Sources and providers</span></span>

<span data-ttu-id="0a5c9-555">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-555">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="0a5c9-556">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-556">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="0a5c9-557">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-557">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="0a5c9-558"><xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a5c9-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>dá se vložit do Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC <xref:Microsoft.AspNetCore.Mvc.Controller> a získat tak konfiguraci pro třídu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="0a5c9-560">V následujících příkladech se `_config` pole používá pro přístup k hodnotám konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-560">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="0a5c9-561">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-561">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="0a5c9-562">Klíče</span><span class="sxs-lookup"><span data-stu-id="0a5c9-562">Keys</span></span>

<span data-ttu-id="0a5c9-563">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-563">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="0a5c9-564">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-564">Keys are case-insensitive.</span></span> <span data-ttu-id="0a5c9-565">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-565">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="0a5c9-566">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-566">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="0a5c9-567">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="0a5c9-567">Hierarchical keys</span></span>
  * <span data-ttu-id="0a5c9-568">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky`:`() na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-568">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="0a5c9-569">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-569">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="0a5c9-570">Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-570">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="0a5c9-571">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-571">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="0a5c9-572">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-572">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="0a5c9-573"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-573">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="0a5c9-574">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-574">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="0a5c9-575">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="0a5c9-575">Values</span></span>

<span data-ttu-id="0a5c9-576">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-576">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="0a5c9-577">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-577">Values are strings.</span></span>
* <span data-ttu-id="0a5c9-578">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-578">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="0a5c9-579">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="0a5c9-579">Providers</span></span>

<span data-ttu-id="0a5c9-580">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-580">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="0a5c9-581">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="0a5c9-581">Provider</span></span> | <span data-ttu-id="0a5c9-582">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="0a5c9-582">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="0a5c9-583">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="0a5c9-583">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="0a5c9-584">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0a5c9-584">Azure Key Vault</span></span> |
| <span data-ttu-id="0a5c9-585">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-585">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="0a5c9-586">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="0a5c9-586">Azure App Configuration</span></span> |
| [<span data-ttu-id="0a5c9-587">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-587">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="0a5c9-588">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-588">Command-line parameters</span></span> |
| [<span data-ttu-id="0a5c9-589">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-589">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="0a5c9-590">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="0a5c9-590">Custom source</span></span> |
| [<span data-ttu-id="0a5c9-591">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-591">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="0a5c9-592">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-592">Environment variables</span></span> |
| [<span data-ttu-id="0a5c9-593">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="0a5c9-593">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="0a5c9-594">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-594">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="0a5c9-595">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="0a5c9-595">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="0a5c9-596">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="0a5c9-596">Directory files</span></span> |
| [<span data-ttu-id="0a5c9-597">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="0a5c9-597">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="0a5c9-598">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="0a5c9-598">In-memory collections</span></span> |
| <span data-ttu-id="0a5c9-599">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-599">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="0a5c9-600">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="0a5c9-600">File in the user profile directory</span></span> |

<span data-ttu-id="0a5c9-601">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-601">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="0a5c9-602">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-602">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="0a5c9-603">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-603">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="0a5c9-604">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-604">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="0a5c9-605">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-605">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="0a5c9-606">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0a5c9-606">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="0a5c9-607">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-607">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="0a5c9-608">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-608">Environment variables</span></span>
1. <span data-ttu-id="0a5c9-609">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-609">Command-line arguments</span></span>

<span data-ttu-id="0a5c9-610">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-610">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="0a5c9-611">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v `CreateDefaultBuilder`nástroji.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-611">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0a5c9-612">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-612">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="0a5c9-613">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="0a5c9-613">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="0a5c9-614">Chcete-li nakonfigurovat tvůrce hostitele, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zavolejte na tvůrce hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-614">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="0a5c9-615">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="0a5c9-615">ConfigureAppConfiguration</span></span>

<span data-ttu-id="0a5c9-616">Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s automaticky přidanými uživateli `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-616">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="0a5c9-617">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-617">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="0a5c9-618">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku `AddCommandLine` , zavolejte jako poslední:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-618">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="0a5c9-619">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="0a5c9-619">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="0a5c9-620">Chcete-li odebrat poskytovatele přidaných nástrojem `CreateDefaultBuilder` [, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="0a5c9-620">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="0a5c9-621">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-621">Consume configuration during app startup</span></span>

<span data-ttu-id="0a5c9-622">Konfigurace dodaná do aplikace v `ConfigureAppConfiguration` nástroji je k dispozici během spouštění aplikace, `Startup.ConfigureServices`včetně.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-622">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a5c9-623">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-623">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="0a5c9-624">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="0a5c9-624">Command-line Configuration Provider</span></span>

<span data-ttu-id="0a5c9-625"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-625">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="0a5c9-626">Chcete-li aktivovat konfiguraci příkazového řádku <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> , je metoda rozšíření volána na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-626">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0a5c9-627">`AddCommandLine`je automaticky volána, `CreateDefaultBuilder(string [])` když je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-627">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="0a5c9-628">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-628">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="0a5c9-629">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-629">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="0a5c9-630">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-630">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="0a5c9-631">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-631">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="0a5c9-632">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-632">Environment variables.</span></span>

<span data-ttu-id="0a5c9-633">`CreateDefaultBuilder`přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-633">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="0a5c9-634">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-634">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="0a5c9-635">`CreateDefaultBuilder`funguje, když je hostitel vytvořen.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-635">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="0a5c9-636">Proto může konfigurace z příkazového řádku aktivované `CreateDefaultBuilder` nástrojem ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-636">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="0a5c9-637">Pro aplikace založené na šablonách `AddCommandLine` ASP.NET Core již byly volány pomocí. `CreateDefaultBuilder`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-637">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0a5c9-638">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` na poslední.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-638">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="0a5c9-639">**Případě**</span><span class="sxs-lookup"><span data-stu-id="0a5c9-639">**Example**</span></span>

<span data-ttu-id="0a5c9-640">Ukázková aplikace využívá metodu `CreateDefaultBuilder` statického usnadnění k sestavení hostitele, který obsahuje volání. <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*></span><span class="sxs-lookup"><span data-stu-id="0a5c9-640">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="0a5c9-641">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-641">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="0a5c9-642">Zadejte do `dotnet run` příkazu argument příkazového řádku, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-642">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="0a5c9-643">Po spuštění aplikace otevřete v `http://localhost:5000`aplikaci prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-643">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="0a5c9-644">Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k `dotnet run`dispozici pro příkaz.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-644">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="0a5c9-645">Argumenty</span><span class="sxs-lookup"><span data-stu-id="0a5c9-645">Arguments</span></span>

<span data-ttu-id="0a5c9-646">Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-646">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="0a5c9-647">Hodnota není povinná, `CommandLineKey=`Pokud se používá znaménko rovná se (například).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-647">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="0a5c9-648">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="0a5c9-648">Key prefix</span></span>               | <span data-ttu-id="0a5c9-649">Příklad</span><span class="sxs-lookup"><span data-stu-id="0a5c9-649">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="0a5c9-650">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="0a5c9-650">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="0a5c9-651">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-651">Two dashes (`--`)</span></span>        | <span data-ttu-id="0a5c9-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="0a5c9-653">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="0a5c9-653">Forward slash (`/`)</span></span>      | <span data-ttu-id="0a5c9-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="0a5c9-655">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-655">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="0a5c9-656">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-656">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="0a5c9-657">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="0a5c9-657">Switch mappings</span></span>

<span data-ttu-id="0a5c9-658">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-658">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="0a5c9-659">Při ručním sestavování <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>konfigurace pomocí nástroje zadejte slovník přepínačů pro vložení do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-659">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="0a5c9-660">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-660">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="0a5c9-661">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-661">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="0a5c9-662">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou (`-`).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-662">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="0a5c9-663">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-663">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="0a5c9-664">Přepínače musí začínat spojovníkem`-`() nebo dvojitou čárkou (`--`).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-664">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="0a5c9-665">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-665">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="0a5c9-666">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-666">Create a switch mappings dictionary.</span></span> <span data-ttu-id="0a5c9-667">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-667">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="0a5c9-668">Po sestavení hostitele zavolejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-668">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="0a5c9-669">Pro aplikace, které používají mapování přepínačů, by `CreateDefaultBuilder` volání nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-669">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="0a5c9-670">Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-670">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0a5c9-671">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` `AddCommandLine` metoda metody mohla zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-671">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="0a5c9-672">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-672">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="0a5c9-673">Key</span><span class="sxs-lookup"><span data-stu-id="0a5c9-673">Key</span></span>       | <span data-ttu-id="0a5c9-674">Hodnota</span><span class="sxs-lookup"><span data-stu-id="0a5c9-674">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="0a5c9-675">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-675">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="0a5c9-676">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-676">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="0a5c9-677">Key</span><span class="sxs-lookup"><span data-stu-id="0a5c9-677">Key</span></span>               | <span data-ttu-id="0a5c9-678">Hodnota</span><span class="sxs-lookup"><span data-stu-id="0a5c9-678">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="0a5c9-679">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-679">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="0a5c9-680"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-680">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="0a5c9-681">Chcete-li aktivovat konfiguraci proměnných prostředí, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> zavolejte metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-681">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="0a5c9-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="0a5c9-683">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-683">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="0a5c9-684">`AddEnvironmentVariables`slouží k načtení proměnných prostředí `ASPNETCORE_` s předponou pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-684">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="0a5c9-685">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-685">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="0a5c9-686">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-686">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="0a5c9-687">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-687">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="0a5c9-688">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-688">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="0a5c9-689">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-689">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="0a5c9-690">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-690">Command-line arguments.</span></span>

<span data-ttu-id="0a5c9-691">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-691">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="0a5c9-692">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-692">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="0a5c9-693">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-693">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="0a5c9-694">Zavolejte `AddEnvironmentVariables` jako poslední, pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-694">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="0a5c9-695">**Případě**</span><span class="sxs-lookup"><span data-stu-id="0a5c9-695">**Example**</span></span>

<span data-ttu-id="0a5c9-696">Ukázková aplikace využívá metodu `CreateDefaultBuilder` statického usnadnění k sestavení hostitele, který obsahuje volání. `AddEnvironmentVariables`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-696">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="0a5c9-697">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-697">Run the sample app.</span></span> <span data-ttu-id="0a5c9-698">Otevřete v `http://localhost:5000`aplikaci prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-698">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="0a5c9-699">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou `ENVIRONMENT`prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-699">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="0a5c9-700">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-700">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="0a5c9-701">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-701">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="0a5c9-702">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-702">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="0a5c9-703">K vystavení všech proměnných prostředí, které jsou k dispozici pro `FilteredConfiguration` aplikaci, změňte na *stránkách/index. cshtml. cs* následující:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-703">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="0a5c9-704">Předpony</span><span class="sxs-lookup"><span data-stu-id="0a5c9-704">Prefixes</span></span>

<span data-ttu-id="0a5c9-705">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-705">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="0a5c9-706">Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_`, zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-706">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="0a5c9-707">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-707">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="0a5c9-708">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-708">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="0a5c9-709">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-709">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="0a5c9-710">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="0a5c9-710">**Connection string prefixes**</span></span>

<span data-ttu-id="0a5c9-711">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-711">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="0a5c9-712">Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána žádná předpona `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-712">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="0a5c9-713">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="0a5c9-713">Connection string prefix</span></span> | <span data-ttu-id="0a5c9-714">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="0a5c9-714">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="0a5c9-715">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="0a5c9-715">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="0a5c9-716">MySQL</span><span class="sxs-lookup"><span data-stu-id="0a5c9-716">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="0a5c9-717">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="0a5c9-717">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="0a5c9-718">SQL Server</span><span class="sxs-lookup"><span data-stu-id="0a5c9-718">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="0a5c9-719">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-719">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="0a5c9-720">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-720">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="0a5c9-721">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-721">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="0a5c9-722">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0a5c9-722">Environment variable key</span></span> | <span data-ttu-id="0a5c9-723">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="0a5c9-723">Converted configuration key</span></span> | <span data-ttu-id="0a5c9-724">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="0a5c9-724">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="0a5c9-725">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-725">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="0a5c9-726">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="0a5c9-727">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-727">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="0a5c9-728">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="0a5c9-729">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-729">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="0a5c9-730">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="0a5c9-731">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-731">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="0a5c9-732">**Případě**</span><span class="sxs-lookup"><span data-stu-id="0a5c9-732">**Example**</span></span>

<span data-ttu-id="0a5c9-733">Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-733">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="0a5c9-734">Název &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-734">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="0a5c9-735">Hodnota &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-735">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="0a5c9-736">Pokud `IConfiguration` je vloženo a přiřazeno k poli s `_config`názvem, přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-736">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="0a5c9-737">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="0a5c9-737">File Configuration Provider</span></span>

<span data-ttu-id="0a5c9-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="0a5c9-739">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-739">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="0a5c9-740">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="0a5c9-740">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="0a5c9-741">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="0a5c9-741">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="0a5c9-742">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="0a5c9-742">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="0a5c9-743">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="0a5c9-743">INI Configuration Provider</span></span>

<span data-ttu-id="0a5c9-744"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-744">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="0a5c9-745">Chcete-li aktivovat konfiguraci souboru INI, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> zavolejte metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-745">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0a5c9-746">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-746">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="0a5c9-747">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-747">Overloads permit specifying:</span></span>

* <span data-ttu-id="0a5c9-748">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-748">Whether the file is optional.</span></span>
* <span data-ttu-id="0a5c9-749">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-749">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="0a5c9-750"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-750">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="0a5c9-751">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-751">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="0a5c9-752">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-752">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="0a5c9-753">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-753">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0a5c9-754">section0:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-754">section0:key0</span></span>
* <span data-ttu-id="0a5c9-755">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-755">section0:key1</span></span>
* <span data-ttu-id="0a5c9-756">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="0a5c9-756">section1:subsection:key</span></span>
* <span data-ttu-id="0a5c9-757">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="0a5c9-757">section2:subsection0:key</span></span>
* <span data-ttu-id="0a5c9-758">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="0a5c9-758">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="0a5c9-759">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="0a5c9-759">JSON Configuration Provider</span></span>

<span data-ttu-id="0a5c9-760"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-760">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="0a5c9-761">Chcete-li aktivovat konfiguraci souboru JSON, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> zavolejte metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-761">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0a5c9-762">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-762">Overloads permit specifying:</span></span>

* <span data-ttu-id="0a5c9-763">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-763">Whether the file is optional.</span></span>
* <span data-ttu-id="0a5c9-764">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-764">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="0a5c9-765"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-765">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="0a5c9-766">`AddJsonFile`se automaticky volá dvakrát při inicializaci nového hostitele v `CreateDefaultBuilder`nástroji.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-766">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0a5c9-767">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-767">The method is called to load configuration from:</span></span>

* <span data-ttu-id="0a5c9-768">*appSettings. JSON* &ndash; tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-768">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="0a5c9-769">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-769">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="0a5c9-770">*appSettings. {Environment}. JSON* &ndash; verze souboru je načtená na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-770">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="0a5c9-771">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-771">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="0a5c9-772">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-772">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="0a5c9-773">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-773">Environment variables.</span></span>
* <span data-ttu-id="0a5c9-774">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-774">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="0a5c9-775">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-775">Command-line arguments.</span></span>

<span data-ttu-id="0a5c9-776">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-776">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="0a5c9-777">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-777">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="0a5c9-778">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-778">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="0a5c9-779">**Případě**</span><span class="sxs-lookup"><span data-stu-id="0a5c9-779">**Example**</span></span>

<span data-ttu-id="0a5c9-780">Ukázková aplikace využívá `CreateDefaultBuilder` ke sestavení hostitele výhodu technologie statického usnadnění, která zahrnuje dvě volání `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-780">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="0a5c9-781">První volání `AddJsonFile` načtení konfigurace z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-781">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="0a5c9-782">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-782">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="0a5c9-783">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-783">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="0a5c9-784">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-784">Run the sample app.</span></span> <span data-ttu-id="0a5c9-785">Otevřete v `http://localhost:5000`aplikaci prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-785">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="0a5c9-786">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-786">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="0a5c9-787">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-787">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="0a5c9-788">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-788">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="0a5c9-789">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-789">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="0a5c9-790">V `ConfigurationSample` profilu změňte hodnotu proměnné `ASPNETCORE_ENVIRONMENT` prostředí na. `Production`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-790">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="0a5c9-791">Uložte soubor a spusťte aplikaci `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-791">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="0a5c9-792">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-792">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="0a5c9-793">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je. `Warning`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-793">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="0a5c9-794">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="0a5c9-794">XML Configuration Provider</span></span>

<span data-ttu-id="0a5c9-795"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-795">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="0a5c9-796">Chcete-li aktivovat konfiguraci souboru XML, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> zavolejte metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-796">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0a5c9-797">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-797">Overloads permit specifying:</span></span>

* <span data-ttu-id="0a5c9-798">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-798">Whether the file is optional.</span></span>
* <span data-ttu-id="0a5c9-799">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-799">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="0a5c9-800"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-800">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="0a5c9-801">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-801">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="0a5c9-802">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-802">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="0a5c9-803">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="0a5c9-804">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-804">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="0a5c9-805">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-805">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0a5c9-806">section0:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-806">section0:key0</span></span>
* <span data-ttu-id="0a5c9-807">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-807">section0:key1</span></span>
* <span data-ttu-id="0a5c9-808">section1:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-808">section1:key0</span></span>
* <span data-ttu-id="0a5c9-809">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-809">section1:key1</span></span>

<span data-ttu-id="0a5c9-810">Opakující se prvky, které používají stejný název elementu fungují, `name` Pokud je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-810">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="0a5c9-811">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-811">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0a5c9-812">oddíl: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-812">section:section0:key:key0</span></span>
* <span data-ttu-id="0a5c9-813">oddíl: section0: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-813">section:section0:key:key1</span></span>
* <span data-ttu-id="0a5c9-814">oddíl: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-814">section:section1:key:key0</span></span>
* <span data-ttu-id="0a5c9-815">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-815">section:section1:key:key1</span></span>

<span data-ttu-id="0a5c9-816">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-816">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="0a5c9-817">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-817">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0a5c9-818">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="0a5c9-818">key:attribute</span></span>
* <span data-ttu-id="0a5c9-819">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="0a5c9-819">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="0a5c9-820">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="0a5c9-820">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="0a5c9-821"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-821">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="0a5c9-822">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-822">The key is the file name.</span></span> <span data-ttu-id="0a5c9-823">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-823">The value contains the file's contents.</span></span> <span data-ttu-id="0a5c9-824">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-824">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="0a5c9-825">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-825">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="0a5c9-826">`directoryPath` Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-826">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="0a5c9-827">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-827">Overloads permit specifying:</span></span>

* <span data-ttu-id="0a5c9-828">`Action<KeyPerFileConfigurationSource>` Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-828">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="0a5c9-829">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-829">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="0a5c9-830">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-830">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="0a5c9-831">Například název `Logging__LogLevel__System` souboru vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-831">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="0a5c9-832">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-832">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="0a5c9-833">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="0a5c9-833">Memory Configuration Provider</span></span>

<span data-ttu-id="0a5c9-834"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-834">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="0a5c9-835">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-835">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0a5c9-836">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-836">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="0a5c9-837">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-837">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="0a5c9-838">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-838">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="0a5c9-839">Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-839">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="0a5c9-840">GetValue</span><span class="sxs-lookup"><span data-stu-id="0a5c9-840">GetValue</span></span>

<span data-ttu-id="0a5c9-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="0a5c9-842">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-842">An overload accepts a default value.</span></span>

<span data-ttu-id="0a5c9-843">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-843">The following example:</span></span>

* <span data-ttu-id="0a5c9-844">Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-844">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="0a5c9-845">Pokud `NumberKey` se v konfiguračních klíčích nenajde, použije se výchozí hodnota `99` .</span><span class="sxs-lookup"><span data-stu-id="0a5c9-845">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="0a5c9-846">Zadá hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-846">Types the value as an `int`.</span></span>
* <span data-ttu-id="0a5c9-847">Ukládá hodnotu `NumberConfig` vlastnosti pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-847">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="0a5c9-848">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="0a5c9-848">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="0a5c9-849">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-849">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="0a5c9-850">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-850">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="0a5c9-851">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-851">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="0a5c9-852">section0:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-852">section0:key0</span></span>
* <span data-ttu-id="0a5c9-853">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-853">section0:key1</span></span>
* <span data-ttu-id="0a5c9-854">section1:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-854">section1:key0</span></span>
* <span data-ttu-id="0a5c9-855">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-855">section1:key1</span></span>
* <span data-ttu-id="0a5c9-856">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-856">section2:subsection0:key0</span></span>
* <span data-ttu-id="0a5c9-857">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-857">section2:subsection0:key1</span></span>
* <span data-ttu-id="0a5c9-858">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-858">section2:subsection1:key0</span></span>
* <span data-ttu-id="0a5c9-859">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-859">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="0a5c9-860">GetSection</span><span class="sxs-lookup"><span data-stu-id="0a5c9-860">GetSection</span></span>

<span data-ttu-id="0a5c9-861">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="0a5c9-862">Chcete-li <xref:Microsoft.Extensions.Configuration.IConfigurationSection> vrátit obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-862">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="0a5c9-863">Hodnota `configSection` neobsahuje pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-863">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="0a5c9-864">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-864">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="0a5c9-865">`GetSection`nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-865">`GetSection` never returns `null`.</span></span> <span data-ttu-id="0a5c9-866">Pokud se nenalezne shodný oddíl, vrátí `IConfigurationSection` se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-866">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="0a5c9-867">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-867">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="0a5c9-868"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-868">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="0a5c9-869">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="0a5c9-869">GetChildren</span></span>

<span data-ttu-id="0a5c9-870">Volání [IConfiguration. Getchildrens](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-870">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="0a5c9-871">Existuje</span><span class="sxs-lookup"><span data-stu-id="0a5c9-871">Exists</span></span>

<span data-ttu-id="0a5c9-872">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-872">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="0a5c9-873">Vzhledem k ukázkovým datům `sectionExists` je `false` to `section2:subsection2` proto, že v konfiguračních datech není oddíl.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-873">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="0a5c9-874">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="0a5c9-874">Bind to an object graph</span></span>

<span data-ttu-id="0a5c9-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="0a5c9-876">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-876">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="0a5c9-877">Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="0a5c9-877">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="0a5c9-878">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-878">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="0a5c9-879">Konfigurace je svázána s celým `TvShow` grafem objektů `Bind` pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-879">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="0a5c9-880">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-880">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="0a5c9-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="0a5c9-882">`Get<T>`je pohodlnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-882">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="0a5c9-883">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-883">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="0a5c9-884">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="0a5c9-884">Bind an array to a class</span></span>

<span data-ttu-id="0a5c9-885">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="0a5c9-885">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="0a5c9-886"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-886">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="0a5c9-887">Libovolný formát pole, který zveřejňuje numerický klíčový segment`:0:`( `:1:`, &hellip; `:{n}:`,), je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-887">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="0a5c9-888">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-888">Binding is provided by convention.</span></span> <span data-ttu-id="0a5c9-889">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-889">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="0a5c9-890">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="0a5c9-890">**In-memory array processing**</span></span>

<span data-ttu-id="0a5c9-891">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-891">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="0a5c9-892">Key</span><span class="sxs-lookup"><span data-stu-id="0a5c9-892">Key</span></span>             | <span data-ttu-id="0a5c9-893">Hodnota</span><span class="sxs-lookup"><span data-stu-id="0a5c9-893">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="0a5c9-894">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-894">array:entries:0</span></span> | <span data-ttu-id="0a5c9-895">value0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-895">value0</span></span> |
| <span data-ttu-id="0a5c9-896">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-896">array:entries:1</span></span> | <span data-ttu-id="0a5c9-897">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-897">value1</span></span> |
| <span data-ttu-id="0a5c9-898">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="0a5c9-898">array:entries:2</span></span> | <span data-ttu-id="0a5c9-899">Argument</span><span class="sxs-lookup"><span data-stu-id="0a5c9-899">value2</span></span> |
| <span data-ttu-id="0a5c9-900">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="0a5c9-900">array:entries:4</span></span> | <span data-ttu-id="0a5c9-901">value4</span><span class="sxs-lookup"><span data-stu-id="0a5c9-901">value4</span></span> |
| <span data-ttu-id="0a5c9-902">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="0a5c9-902">array:entries:5</span></span> | <span data-ttu-id="0a5c9-903">value5</span><span class="sxs-lookup"><span data-stu-id="0a5c9-903">value5</span></span> |

<span data-ttu-id="0a5c9-904">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-904">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="0a5c9-905">Pole přeskočí hodnotu indexu &num;3.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-905">The array skips a value for index &num;3.</span></span> <span data-ttu-id="0a5c9-906">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-906">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="0a5c9-907">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-907">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="0a5c9-908">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-908">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="0a5c9-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lze také použít syntaxi, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="0a5c9-910">Vázaný objekt, instance `ArrayExample`, přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-910">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="0a5c9-911">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="0a5c9-911">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="0a5c9-912">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="0a5c9-912">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="0a5c9-913">0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-913">0</span></span>                            | <span data-ttu-id="0a5c9-914">value0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-914">value0</span></span>                       |
| <span data-ttu-id="0a5c9-915">1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-915">1</span></span>                            | <span data-ttu-id="0a5c9-916">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-916">value1</span></span>                       |
| <span data-ttu-id="0a5c9-917">2</span><span class="sxs-lookup"><span data-stu-id="0a5c9-917">2</span></span>                            | <span data-ttu-id="0a5c9-918">Argument</span><span class="sxs-lookup"><span data-stu-id="0a5c9-918">value2</span></span>                       |
| <span data-ttu-id="0a5c9-919">3</span><span class="sxs-lookup"><span data-stu-id="0a5c9-919">3</span></span>                            | <span data-ttu-id="0a5c9-920">value4</span><span class="sxs-lookup"><span data-stu-id="0a5c9-920">value4</span></span>                       |
| <span data-ttu-id="0a5c9-921">4</span><span class="sxs-lookup"><span data-stu-id="0a5c9-921">4</span></span>                            | <span data-ttu-id="0a5c9-922">value5</span><span class="sxs-lookup"><span data-stu-id="0a5c9-922">value5</span></span>                       |

<span data-ttu-id="0a5c9-923">Index &num;3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu. `value4`</span><span class="sxs-lookup"><span data-stu-id="0a5c9-923">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="0a5c9-924">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-924">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="0a5c9-925">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-925">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="0a5c9-926">Chybějící položka konfigurace pro index &num;3 se dá zadat předtím, než se vazba `ArrayExample` na instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-926">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="0a5c9-927">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-927">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="0a5c9-928">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-928">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="0a5c9-929">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-929">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="0a5c9-930">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-930">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="0a5c9-931">Key</span><span class="sxs-lookup"><span data-stu-id="0a5c9-931">Key</span></span>             | <span data-ttu-id="0a5c9-932">Hodnota</span><span class="sxs-lookup"><span data-stu-id="0a5c9-932">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="0a5c9-933">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="0a5c9-933">array:entries:3</span></span> | <span data-ttu-id="0a5c9-934">hodnota3</span><span class="sxs-lookup"><span data-stu-id="0a5c9-934">value3</span></span> |

<span data-ttu-id="0a5c9-935">Je- `ArrayExample` li instance třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro &num;index 3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-935">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="0a5c9-936">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="0a5c9-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="0a5c9-937">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="0a5c9-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="0a5c9-938">0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-938">0</span></span>                            | <span data-ttu-id="0a5c9-939">value0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-939">value0</span></span>                       |
| <span data-ttu-id="0a5c9-940">1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-940">1</span></span>                            | <span data-ttu-id="0a5c9-941">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-941">value1</span></span>                       |
| <span data-ttu-id="0a5c9-942">2</span><span class="sxs-lookup"><span data-stu-id="0a5c9-942">2</span></span>                            | <span data-ttu-id="0a5c9-943">Argument</span><span class="sxs-lookup"><span data-stu-id="0a5c9-943">value2</span></span>                       |
| <span data-ttu-id="0a5c9-944">3</span><span class="sxs-lookup"><span data-stu-id="0a5c9-944">3</span></span>                            | <span data-ttu-id="0a5c9-945">hodnota3</span><span class="sxs-lookup"><span data-stu-id="0a5c9-945">value3</span></span>                       |
| <span data-ttu-id="0a5c9-946">4</span><span class="sxs-lookup"><span data-stu-id="0a5c9-946">4</span></span>                            | <span data-ttu-id="0a5c9-947">value4</span><span class="sxs-lookup"><span data-stu-id="0a5c9-947">value4</span></span>                       |
| <span data-ttu-id="0a5c9-948">5</span><span class="sxs-lookup"><span data-stu-id="0a5c9-948">5</span></span>                            | <span data-ttu-id="0a5c9-949">value5</span><span class="sxs-lookup"><span data-stu-id="0a5c9-949">value5</span></span>                       |

<span data-ttu-id="0a5c9-950">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="0a5c9-950">**JSON array processing**</span></span>

<span data-ttu-id="0a5c9-951">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-951">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="0a5c9-952">V následujícím konfiguračním souboru `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-952">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="0a5c9-953">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-953">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="0a5c9-954">Key</span><span class="sxs-lookup"><span data-stu-id="0a5c9-954">Key</span></span>                     | <span data-ttu-id="0a5c9-955">Hodnota</span><span class="sxs-lookup"><span data-stu-id="0a5c9-955">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="0a5c9-956">json_array: klíč</span><span class="sxs-lookup"><span data-stu-id="0a5c9-956">json_array:key</span></span>          | <span data-ttu-id="0a5c9-957">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="0a5c9-957">valueA</span></span> |
| <span data-ttu-id="0a5c9-958">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-958">json_array:subsection:0</span></span> | <span data-ttu-id="0a5c9-959">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="0a5c9-959">valueB</span></span> |
| <span data-ttu-id="0a5c9-960">json_array: pododdíl: 1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-960">json_array:subsection:1</span></span> | <span data-ttu-id="0a5c9-961">valueC</span><span class="sxs-lookup"><span data-stu-id="0a5c9-961">valueC</span></span> |
| <span data-ttu-id="0a5c9-962">json_array: pododdíl: 2</span><span class="sxs-lookup"><span data-stu-id="0a5c9-962">json_array:subsection:2</span></span> | <span data-ttu-id="0a5c9-963">s</span><span class="sxs-lookup"><span data-stu-id="0a5c9-963">valueD</span></span> |

<span data-ttu-id="0a5c9-964">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-964">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="0a5c9-965">Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-965">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="0a5c9-966">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-966">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="0a5c9-967">`JsonArrayExample.Subsection`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="0a5c9-967">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="0a5c9-968">`JsonArrayExample.Subsection`Osa</span><span class="sxs-lookup"><span data-stu-id="0a5c9-968">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="0a5c9-969">0</span><span class="sxs-lookup"><span data-stu-id="0a5c9-969">0</span></span>                                   | <span data-ttu-id="0a5c9-970">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="0a5c9-970">valueB</span></span>                              |
| <span data-ttu-id="0a5c9-971">1</span><span class="sxs-lookup"><span data-stu-id="0a5c9-971">1</span></span>                                   | <span data-ttu-id="0a5c9-972">valueC</span><span class="sxs-lookup"><span data-stu-id="0a5c9-972">valueC</span></span>                              |
| <span data-ttu-id="0a5c9-973">2</span><span class="sxs-lookup"><span data-stu-id="0a5c9-973">2</span></span>                                   | <span data-ttu-id="0a5c9-974">s</span><span class="sxs-lookup"><span data-stu-id="0a5c9-974">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="0a5c9-975">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="0a5c9-975">Custom configuration provider</span></span>

<span data-ttu-id="0a5c9-976">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-976">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="0a5c9-977">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-977">The provider has the following characteristics:</span></span>

* <span data-ttu-id="0a5c9-978">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-978">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="0a5c9-979">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte `ConfigurationBuilder` sekundární pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-979">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="0a5c9-980">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-980">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="0a5c9-981">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-981">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="0a5c9-982">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-982">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="0a5c9-983">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-983">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="0a5c9-984">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-984">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="0a5c9-985">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-985">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="0a5c9-986">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="0a5c9-987">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-987">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="0a5c9-988">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="0a5c9-989">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-989">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="0a5c9-990">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-990">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="0a5c9-991">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="0a5c9-992">Metoda `AddEFConfiguration` rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-992">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="0a5c9-993">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-993">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="0a5c9-994">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-994">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="0a5c9-995">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="0a5c9-995">Access configuration during startup</span></span>

<span data-ttu-id="0a5c9-996">Vložení `IConfiguration` do `Startup` konstruktoru pro přístup k hodnotám konfigurace `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-996">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a5c9-997">Chcete-li získat `Startup.Configure`přístup ke konfiguraci `IConfiguration` v, buď přímo do metody, nebo použijte instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-997">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="0a5c9-998">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="0a5c9-998">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="0a5c9-999">Přístup ke konfiguraci na Razor stránce stránky nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="0a5c9-999">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="0a5c9-1000">Chcete-li získat přístup k Razor nastavení konfigurace na stránce stránky nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([Referenční dokumentace jazyka C#: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> ji do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-1000">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="0a5c9-1001">Na stránce Razor stránky:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-1001">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="0a5c9-1002">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="0a5c9-1002">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="0a5c9-1003">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="0a5c9-1003">Add configuration from an external assembly</span></span>

<span data-ttu-id="0a5c9-1004"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-1004">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="0a5c9-1005">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0a5c9-1005">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a5c9-1006">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="0a5c9-1006">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

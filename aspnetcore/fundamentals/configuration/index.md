---
title: Konfigurace v ASP.NET jádru
author: rick-anderson
description: Přečtěte si, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 506f01ace72d6e915c0f3ebdaae5b4a3328a79b9
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007155"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="4a7b5-103">Konfigurace v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="4a7b5-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="4a7b5-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4a7b5-105">Konfigurace v ASP.NET Core se provádí pomocí jednoho nebo více [poskytovatelů konfigurace](#cp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="4a7b5-106">Zprostředkovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="4a7b5-107">Soubory nastavení, například *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="4a7b5-108">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-108">Environment variables</span></span>
* <span data-ttu-id="4a7b5-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4a7b5-109">Azure Key Vault</span></span>
* <span data-ttu-id="4a7b5-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4a7b5-110">Azure App Configuration</span></span>
* <span data-ttu-id="4a7b5-111">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-111">Command-line arguments</span></span>
* <span data-ttu-id="4a7b5-112">Vlastní zprostředkovatelé, nainstalovaní nebo vytvoření</span><span class="sxs-lookup"><span data-stu-id="4a7b5-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="4a7b5-113">Soubory adresářů</span><span class="sxs-lookup"><span data-stu-id="4a7b5-113">Directory files</span></span>
* <span data-ttu-id="4a7b5-114">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="4a7b5-114">In-memory .NET objects</span></span>

<span data-ttu-id="4a7b5-115">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="4a7b5-116">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-116">Default configuration</span></span>

<span data-ttu-id="4a7b5-117">ASP.NET základní webové aplikace vytvořené pomocí [dotnet new](/dotnet/core/tools/dotnet-new) nebo Visual Studia generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="4a7b5-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>poskytuje výchozí konfiguraci aplikace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="4a7b5-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá `IConfiguration` existující jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="4a7b5-120">Ve výchozím případě konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj pro konfiguraci _aplikace._</span><span class="sxs-lookup"><span data-stu-id="4a7b5-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="4a7b5-121">[appsettings.json](#appsettingsjson) pomocí [zprostředkovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="4a7b5-122">*nastavení aplikace.* `Environment` *Json* pomocí [zprostředkovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="4a7b5-123">Například *nastavení aplikace*. ***Výroba***. *json* a *appsettings*. ***Vývoj***. *json*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="4a7b5-124">[Tajné kódy aplikací,](xref:security/app-secrets) `Development` když se aplikace spustí v prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="4a7b5-125">Proměnné prostředí pomocí [zprostředkovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="4a7b5-126">Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="4a7b5-127">Zprostředkovatelé konfigurace, kteří jsou přidáni později přepsat předchozí nastavení klíče.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="4a7b5-128">Například pokud `MyKey` je nastavena v *obou appsettings.json* a prostředí, hodnota prostředí se používá.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="4a7b5-129">Pomocí výchozích zprostředkovatelů konfigurace [přepíše poskytovatel konfigurace příkazového řádku](#command-line-configuration-provider) všechny ostatní zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="4a7b5-130">Další informace `CreateDefaultBuilder`naleznete v [tématu Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="4a7b5-131">Následující kód zobrazuje povolené zprostředkovatele konfigurace v pořadí, v jakém byli přidáni:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="4a7b5-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="4a7b5-132">appsettings.json</span></span>

<span data-ttu-id="4a7b5-133">Zvažte následující soubor *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="4a7b5-134">Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-135">Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> konfigurace načte v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="4a7b5-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-136">*appsettings.json*</span></span>
1. <span data-ttu-id="4a7b5-137">*nastavení aplikace.* `Environment` *.json* : Například *nastavení aplikace*. ***Výroba***. *json* a *appsettings*. ***Vývoj***. *json* soubory.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="4a7b5-138">Verze souboru prostředí je načtena na základě [iHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="4a7b5-139">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4a7b5-140">*appsettings*. `Environment`. *Hodnoty json* přepíší klíče v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="4a7b5-141">Ve výchozím nastavení by například:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-141">For example, by default:</span></span>

* <span data-ttu-id="4a7b5-142">Ve vývoji, *appsettings*. ***Vývoj***. *Konfigurace json* přepíše hodnoty nalezené v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="4a7b5-143">V produkčním prostředí *nastavení aplikací*. ***Výroba***. *Konfigurace json* přepíše hodnoty nalezené v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="4a7b5-144">Například při nasazování aplikace do Azure.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="4a7b5-145">Vazba hierarchických konfiguračních dat pomocí vzoru voleb</span><span class="sxs-lookup"><span data-stu-id="4a7b5-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="4a7b5-146">Upřednostňovaným způsobem čtení souvisejících hodnot konfigurace je použití [vzoru voleb](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="4a7b5-147">Chcete-li například číst následující hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="4a7b5-148">Vytvořte `PositionOptions` následující třídu:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="4a7b5-149">Všechny veřejné vlastnosti čtení a zápisu typu jsou vázány.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="4a7b5-150">Pole ***nejsou*** vázána.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="4a7b5-151">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-151">The following code:</span></span>

* <span data-ttu-id="4a7b5-152">Volá [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) svázat třídu `PositionOptions` do oddílu. `Position`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="4a7b5-153">Zobrazí `Position` konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)váže a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="4a7b5-155">`ConfigurationBinder.Get<T>`může být pohodlnější `ConfigurationBinder.Bind`než použití .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="4a7b5-156">Následující kód ukazuje, `ConfigurationBinder.Get<T>` jak `PositionOptions` používat s třídou:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-157">Alternativní přístup při použití ***vzoru možnosti*** je svázat `Position` oddíl a přidat jej do [kontejneru služby vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4a7b5-158">V následujícím kódu `PositionOptions` je přidán do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontejneru služby s konfigurací a vázán na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="4a7b5-159">Pomocí předchozího kódu přečte následující kód možnosti pozice:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-160">Pomocí [výchozí](#default) konfigurace *appsettings.json* a *appsettings.* `Environment` *.json* soubory jsou povoleny s [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="4a7b5-161">Změny provedené v *appsettings.json* a *appsettings.* `Environment` *Soubor JSON* ***po*** spuštění aplikace čte [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4a7b5-162">Informace o přidání dalších konfiguračních souborů JSON naleznete v tomto dokumentu na [tématu Zprostředkovatel konfigurace](#jcp) JSON.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="4a7b5-163">Bezpečnostní a tajný manažer</span><span class="sxs-lookup"><span data-stu-id="4a7b5-163">Security and secret manager</span></span>

<span data-ttu-id="4a7b5-164">Pokyny pro konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="4a7b5-165">Nikdy neuklápějte hesla nebo jiná citlivá data v kódu zprostředkovatele konfigurace nebo v konfiguračních souborech ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="4a7b5-166">Správce [tajných služeb](xref:security/app-secrets) lze použít k ukládání tajných kódů ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="4a7b5-167">Nepoužívejte produkční tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="4a7b5-168">Zadejte tajné kódy mimo projekt tak, aby nemohly být omylem potvrzeny do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="4a7b5-169">[Ve výchozím nastavení](#default)správce [tajných barev](xref:security/app-secrets) přečte nastavení konfigurace po nastavení *appsettings.json* a *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="4a7b5-170">Další informace o ukládání hesel nebo jiných citlivých dat:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="4a7b5-171"><xref:security/app-secrets>: Obsahuje rady ohledně používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="4a7b5-172">Správce tajných barev používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných kódů uživatelů v souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="4a7b5-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné kódy aplikací pro ASP.NET základní aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="4a7b5-174">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="4a7b5-175">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-175">Environment variables</span></span>

<span data-ttu-id="4a7b5-176">Pomocí [výchozí](#default) konfigurace <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z dvojice klíč-hodnota proměnné prostředí po přečtení *appsettings.json*, *appsettings.* `Environment` *Json*a [tajný manažer](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="4a7b5-177">Proto hodnoty klíče číst z prostředí přepsat hodnoty číst z *appsettings.json*, *appsettings.* `Environment` *Json*a tajný manažer.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4a7b5-178">Následující `set` příkazy:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-178">The following `set` commands:</span></span>

* <span data-ttu-id="4a7b5-179">Nastavte klíče prostředí a hodnoty [předchozího příkladu v](#appsettingsjson) systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="4a7b5-180">Otestujte nastavení při použití [ukázkového stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="4a7b5-181">Příkaz `dotnet run` musí být spuštěn v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="4a7b5-182">Předchozí nastavení prostředí:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-182">The preceding environment settings:</span></span>

* <span data-ttu-id="4a7b5-183">Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve které byly nastaveny.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="4a7b5-184">Prohlížeče spuštěné v sadě Visual Studio nebudou číst.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="4a7b5-185">Následující příkazy [setx](/windows-server/administration/windows-commands/setx) lze použít k nastavení klíčů a hodnot prostředí v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="4a7b5-186">Na `set` `setx` rozdíl od , nastavení jsou trvalé.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="4a7b5-187">`/M`nastaví proměnnou v systémovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="4a7b5-188">Pokud `/M` se přepínač nepoužívá, je nastavena proměnná uživatelského prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="4a7b5-189">Chcete-li otestovat, že předchozí příkazy přepsat *appsettings.json* a *appsettings.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="4a7b5-190">S Visual Studio: Ukončete a restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="4a7b5-191">Pomocí příkazového příkazového příkazu: `dotnet run`Spusťte nové příkazové okno a zadejte .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="4a7b5-192">Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem k určení předpony pro proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="4a7b5-193">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-193">In the preceding code:</span></span>

* <span data-ttu-id="4a7b5-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`je přidán a [výchozí zprostředkovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="4a7b5-195">Příklad objednání zprostředkovatelů konfigurace naleznete v tématu [Zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="4a7b5-196">Proměnné prostředí nastavené `MyCustomPrefix_` s předponou přepíší [výchozí zprostředkovatele konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="4a7b5-197">To zahrnuje proměnné prostředí bez předpony.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="4a7b5-198">Předpona je odstraněna při čtení párů hodnot klíči konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="4a7b5-199">Vlastní předponu testují následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="4a7b5-200">[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty `DOTNET_` `ASPNETCORE_`příkazového řádku s předponou a .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="4a7b5-201">A `DOTNET_` `ASPNETCORE_` předpony jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="4a7b5-202">Další informace o konfiguraci hostitele a aplikace naleznete v [tématu Obecný hostitel .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="4a7b5-203">Ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/)vyberte na stránce Nastavení > **konfigurace** nastavení aplikace nové **nastavení.**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="4a7b5-204">Nastavení aplikací služby Azure App Service jsou:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="4a7b5-205">Šifrované v klidovém stavu a přenášené přes šifrovaný kanál.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="4a7b5-206">Vystaveno jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-206">Exposed as environment variables.</span></span>

<span data-ttu-id="4a7b5-207">Další informace najdete v tématu [Azure Apps: Přepsat konfiguraci aplikací pomocí portálu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4a7b5-208">Informace o připojovacích řetězech Azure v databázi najdete v tématu [předpony připojovacího řetězce.](#constr)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="4a7b5-209">Příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="4a7b5-209">Command-line</span></span>

<span data-ttu-id="4a7b5-210">Při použití [výchozí](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> konfigurace načte konfiguraci z dvojice klíč-hodnota argumentu příkazového řádku za následující zdroje konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="4a7b5-211">*appsettings.json* a *appsettings*. `Environment`. *json* soubory.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="4a7b5-212">[Tajné klíče aplikací (Správce tajných kódů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4a7b5-213">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-213">Environment variables.</span></span>

<span data-ttu-id="4a7b5-214">[Ve výchozím nastavení](#default)přepíší hodnoty konfigurace nastavené na hodnotách konfigurace na stavicí příkazového řádku se všemi ostatními poskytovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="4a7b5-215">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-215">Command-line arguments</span></span>

<span data-ttu-id="4a7b5-216">Následující příkaz nastavuje `=`klíče a hodnoty pomocí :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="4a7b5-217">Následující příkaz nastavuje `/`klíče a hodnoty pomocí :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="4a7b5-218">Následující příkaz nastavuje `--`klíče a hodnoty pomocí :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="4a7b5-219">Hodnota klíče:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-219">The key value:</span></span>

* <span data-ttu-id="4a7b5-220">Musí `=`následovat , nebo klíč musí `--` mít `/` předponu nebo když hodnota následuje mezeru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="4a7b5-221">Není vyžadováno, `=` pokud se používá.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="4a7b5-222">Například, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="4a7b5-223">V rámci stejného příkazu nekombinujte dvojice klíčových hodnot `=` argumentů příkazového řádku, které se používají s dvojicemi klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="4a7b5-224">Přepnout mapování</span><span class="sxs-lookup"><span data-stu-id="4a7b5-224">Switch mappings</span></span>

<span data-ttu-id="4a7b5-225">Mapování přepínačů umožňuje logiku nahrazení názvu **klíče.**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="4a7b5-226">Zadejte slovník switch nahrazení <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="4a7b5-227">Při použití slovníku mapování přepínačů je slovník zkontrolován na klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="4a7b5-228">Pokud je klíč příkazového řádku nalezen ve slovníku, hodnota slovníku je předána zpět a nastavte dvojici klíč-hodnota do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="4a7b5-229">Mapování přepínače je vyžadováno pro všechny klávesy příkazového řádku s předponou s jednou pomlčkou (`-`).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="4a7b5-230">Přepnout pravidla klíčů slovníku mapování:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="4a7b5-231">Přepínače musí `-` `--`začínat písmenem nebo.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="4a7b5-232">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="4a7b5-233">Chcete-li použít slovník mapování přepínačů, přejděte jej do volání `AddCommandLine`na :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="4a7b5-234">Následující kód zobrazuje hodnoty klíčů pro nahrazené klíče:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-235">Chcete-li otestovat nahrazení klíče, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="4a7b5-236">Poznámka: V `=` současné době nelze použít k nastavení hodnot `-`nahrazení klíče s jednou pomlčkou .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="4a7b5-237">Podívejte se na [tento problém GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="4a7b5-238">Následující příkaz funguje k testování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="4a7b5-239">U aplikací, které používají mapování `CreateDefaultBuilder` přepínačů, by volání nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="4a7b5-240">Volání metody neobsahuje mapované přepínače a neexistuje žádný způsob, jak předat slovník mapování přepínačů . `CreateDefaultBuilder` `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4a7b5-241">Řešením není předat argumenty, `CreateDefaultBuilder` ale místo toho `ConfigurationBuilder` povolit `AddCommandLine` metodu metody ke zpracování argumentů a slovníku mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="4a7b5-242">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="4a7b5-242">Hierarchical configuration data</span></span>

<span data-ttu-id="4a7b5-243">Konfigurační rozhraní API čte hierarchická konfigurační data sloučením hierarchických dat pomocí oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="4a7b5-244">[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="4a7b5-245">Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-246">Upřednostňovaným způsobem čtení hierarchických konfiguračních dat je použití vzoru voleb.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="4a7b5-247">Další informace naleznete v [tématu Vazba hierarchických konfiguračních dat](#optpat) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="4a7b5-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody jsou k dispozici pro oddělení oddílů a podřízených částí v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="4a7b5-249">Tyto metody jsou popsány dále v [GetSection, GetChildren a Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="4a7b5-250">Konfigurační klíče a hodnoty</span><span class="sxs-lookup"><span data-stu-id="4a7b5-250">Configuration keys and values</span></span>

<span data-ttu-id="4a7b5-251">Konfigurační klíče:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-251">Configuration keys:</span></span>

* <span data-ttu-id="4a7b5-252">Nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-252">Are case-insensitive.</span></span> <span data-ttu-id="4a7b5-253">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="4a7b5-254">Pokud je klíč a hodnota nastavena ve více než jednom zprostředkovateli konfigurace, použije se hodnota z posledního přidaného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="4a7b5-255">Další informace naleznete [v tématu Výchozí konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="4a7b5-256">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="4a7b5-256">Hierarchical keys</span></span>
  * <span data-ttu-id="4a7b5-257">V rámci rozhraní API konfigurace`:`funguje oddělovač dvojtečky ( ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="4a7b5-258">V proměnných prostředí oddělovač dvojtečky nemusí fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="4a7b5-259">Dvojité podtržítko , `__`je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="4a7b5-260">V azure key vault hierarchické klíče použít `--` jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="4a7b5-261">[Zprostředkovatel konfigurace úložiště klíčů](xref:security/key-vault-configuration) Azure `--` vault `:` automaticky nahradí při načítání tajných kódů do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="4a7b5-262">Podporuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> vazby pole na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4a7b5-263">Vazba pole je popsána v části [Bind pole.](#boa)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="4a7b5-264">Hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-264">Configuration values:</span></span>

* <span data-ttu-id="4a7b5-265">Jsou struny.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-265">Are strings.</span></span>
* <span data-ttu-id="4a7b5-266">Hodnoty null nelze uložit v konfiguraci nebo vázány na objekty.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="4a7b5-267">Zprostředkovatelé konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-267">Configuration providers</span></span>

<span data-ttu-id="4a7b5-268">V následující tabulce jsou uvedeny zprostředkovatelé konfigurace, kteří jsou k dispozici pro ASP.NET aplikace Core.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="4a7b5-269">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="4a7b5-269">Provider</span></span> | <span data-ttu-id="4a7b5-270">Poskytuje konfiguraci od</span><span class="sxs-lookup"><span data-stu-id="4a7b5-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="4a7b5-271">Zprostředkovatel konfigurace služby Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4a7b5-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="4a7b5-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4a7b5-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="4a7b5-273">Zprostředkovatel konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="4a7b5-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="4a7b5-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4a7b5-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="4a7b5-275">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="4a7b5-276">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-276">Command-line parameters</span></span> |
| [<span data-ttu-id="4a7b5-277">Vlastní zprostředkovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="4a7b5-278">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="4a7b5-278">Custom source</span></span> |
| [<span data-ttu-id="4a7b5-279">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="4a7b5-280">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-280">Environment variables</span></span> |
| [<span data-ttu-id="4a7b5-281">Zprostředkovatel konfigurace souborů</span><span class="sxs-lookup"><span data-stu-id="4a7b5-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="4a7b5-282">Soubory INI, JSON a XML</span><span class="sxs-lookup"><span data-stu-id="4a7b5-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="4a7b5-283">Zprostředkovatel konfigurace pro jeden soubor</span><span class="sxs-lookup"><span data-stu-id="4a7b5-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="4a7b5-284">Soubory adresářů</span><span class="sxs-lookup"><span data-stu-id="4a7b5-284">Directory files</span></span> |
| [<span data-ttu-id="4a7b5-285">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="4a7b5-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="4a7b5-286">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="4a7b5-286">In-memory collections</span></span> |
| [<span data-ttu-id="4a7b5-287">Tajný správce</span><span class="sxs-lookup"><span data-stu-id="4a7b5-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="4a7b5-288">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="4a7b5-288">File in the user profile directory</span></span> |

<span data-ttu-id="4a7b5-289">Zdroje konfigurace jsou čteny v pořadí, ve které jsou zadáni jejich zprostředkovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="4a7b5-290">Objednejte zprostředkovatele konfigurace v kódu tak, aby vyhovovaly prioritám pro základní zdroje konfigurace, které aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="4a7b5-291">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="4a7b5-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-292">*appsettings.json*</span></span>
1. <span data-ttu-id="4a7b5-293">*appsettings*. `Environment`. *json řekl:*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="4a7b5-294">Tajný správce</span><span class="sxs-lookup"><span data-stu-id="4a7b5-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="4a7b5-295">Proměnné prostředí pomocí [zprostředkovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="4a7b5-296">Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="4a7b5-297">Běžnou praxí je přidat zprostředkovatele konfigurace příkazového řádku jako poslední ho v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou jinými zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="4a7b5-298">Předchozí posloupnost zprostředkovatelů se používá ve [výchozí konfiguraci](#default).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="4a7b5-299">Předpony připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="4a7b5-299">Connection string prefixes</span></span>

<span data-ttu-id="4a7b5-300">Konfigurační rozhraní API má zvláštní pravidla zpracování pro čtyři proměnné prostředí připojovacího řetězce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="4a7b5-301">Tyto připojovací řetězce se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="4a7b5-302">Proměnné prostředí s předčíslími zobrazenými v tabulce se načtou do aplikace `AddEnvironmentVariables`s [výchozí konfigurací](#default) nebo když není zadána žádná předpona .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="4a7b5-303">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="4a7b5-303">Connection string prefix</span></span> | <span data-ttu-id="4a7b5-304">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="4a7b5-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="4a7b5-305">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="4a7b5-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="4a7b5-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="4a7b5-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="4a7b5-307">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="4a7b5-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="4a7b5-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4a7b5-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="4a7b5-309">Když je proměnná prostředí zjištěna a načtena do konfigurace s některou ze čtyř předpon uvedených v tabulce:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="4a7b5-310">Konfigurační klíč je vytvořen odebráním předpony proměnné`ConnectionStrings`prostředí a přidáním oddílu konfiguračního klíče ( ).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="4a7b5-311">Je vytvořen nový konfigurační pár klíč-hodnota, `CUSTOMCONNSTR_`který představuje zprostředkovatele připojení k databázi (s výjimkou , který nemá žádného uvedeného zprostředkovatele).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="4a7b5-312">Klíč proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-312">Environment variable key</span></span> | <span data-ttu-id="4a7b5-313">Převedený konfigurační klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-313">Converted configuration key</span></span> | <span data-ttu-id="4a7b5-314">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="4a7b5-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4a7b5-315">Položka konfigurace nebyla vytvořena.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4a7b5-316">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4a7b5-317">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4a7b5-318">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4a7b5-319">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4a7b5-320">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4a7b5-321">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="4a7b5-322">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="4a7b5-322">JSON configuration provider</span></span>

<span data-ttu-id="4a7b5-323">Konfigurace <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> zatížení z párů klíč-hodnota souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="4a7b5-324">Přetížení může určit:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-324">Overloads can specify:</span></span>

* <span data-ttu-id="4a7b5-325">Určuje, zda je soubor volitelný.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-325">Whether the file is optional.</span></span>
* <span data-ttu-id="4a7b5-326">Určuje, zda je konfigurace znovu načtena, pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="4a7b5-327">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="4a7b5-328">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-328">The preceding code:</span></span>

* <span data-ttu-id="4a7b5-329">Nakonfiguruje zprostředkovatele konfigurace JSON tak, aby načetl soubor *MyConfig.json* následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="4a7b5-330">`optional: true`: Soubor je volitelný.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="4a7b5-331">`reloadOnChange: true`: Soubor se po uložení změn znovu načte.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="4a7b5-332">Přečte [výchozí zprostředkovatele konfigurace](#default) před souborem *MyConfig.json.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="4a7b5-333">Nastavení v nastavení přepsání souboru *MyConfig.json* ve výchozím zprostředkovateli konfigurace, včetně [zprostředkovatele konfigurace proměnných prostředí](#evcp) a [zprostředkovatele konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4a7b5-334">Obvykle ***nechcete,*** aby vlastní soubor JSON převažující hodnoty nastavené v [nastavení konfigurace proměnné prostředí a](#evcp) [zprostředkovatele konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4a7b5-335">Následující kód vymaže všechny zprostředkovatele konfigurace a přidá několik zprostředkovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="4a7b5-336">V předchozím kódu nastavení v *MyConfig.json* a *MyConfig*. `Environment`. *json* soubory:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="4a7b5-337">Přepište nastavení v *nastavení appsettings.json* a *appsettings*. `Environment`. *json* soubory.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="4a7b5-338">Jsou přepsány nastavením [ve zprostředkovateli konfigurace proměnných prostředí](#evcp) a [zprostředkovateli konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4a7b5-339">[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyConfig.json:*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="4a7b5-340">Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="4a7b5-341">Zprostředkovatel konfigurace souborů</span><span class="sxs-lookup"><span data-stu-id="4a7b5-341">File configuration provider</span></span>

<span data-ttu-id="4a7b5-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třída pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="4a7b5-343">Následující zprostředkovatelé konfigurace `FileConfigurationProvider`jsou odvozeni z :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="4a7b5-344">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="4a7b5-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="4a7b5-345">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="4a7b5-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="4a7b5-346">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="4a7b5-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="4a7b5-347">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="4a7b5-347">INI configuration provider</span></span>

<span data-ttu-id="4a7b5-348">Konfigurace <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte z ini souboru klíč-hodnota párů za běhu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="4a7b5-349">Následující kód vymaže všechny zprostředkovatele konfigurace a přidá několik zprostředkovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="4a7b5-350">V předchozím kódu nastavení v *MyIniConfig.ini* a *MyIniConfig*. `Environment`. *ini* soubory jsou přepsány nastavenív:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="4a7b5-351">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="4a7b5-352">[Zprostředkovatel konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4a7b5-353">[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig.ini:*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="4a7b5-354">Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="4a7b5-355">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="4a7b5-355">XML configuration provider</span></span>

<span data-ttu-id="4a7b5-356">Konfigurace <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načte z dvojice klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="4a7b5-357">Následující kód vymaže všechny zprostředkovatele konfigurace a přidá několik zprostředkovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="4a7b5-358">V předchozím kódu nastavení v *souborech MyXMLFile.xml* a *MyXMLFile*. `Environment`. soubory *XML* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="4a7b5-359">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="4a7b5-360">[Zprostředkovatel konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4a7b5-361">[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile.xml:*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="4a7b5-362">Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-363">Opakující se prvky, které používají `name` stejný název prvku práce, pokud atribut se používá k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="4a7b5-364">Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-365">Atributy lze použít k zadání hodnot:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="4a7b5-366">Předchozí konfigurační soubor `value`načte následující klíče pomocí :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4a7b5-367">klíč:atribut</span><span class="sxs-lookup"><span data-stu-id="4a7b5-367">key:attribute</span></span>
* <span data-ttu-id="4a7b5-368">oddíl:klíč:atribut</span><span class="sxs-lookup"><span data-stu-id="4a7b5-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="4a7b5-369">Zprostředkovatel konfigurace pro jeden soubor</span><span class="sxs-lookup"><span data-stu-id="4a7b5-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="4a7b5-370">Používá <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> soubory adresáře jako dvojice konfiguračníklíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="4a7b5-371">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-371">The key is the file name.</span></span> <span data-ttu-id="4a7b5-372">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-372">The value contains the file's contents.</span></span> <span data-ttu-id="4a7b5-373">Zprostředkovatel konfigurace key-per-file se používá ve scénářích hostování dockeru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="4a7b5-374">Chcete-li aktivovat konfiguraci klíče <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> za soubor, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zavolejte metodu rozšíření v instanci aplikace .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="4a7b5-375">Chcete-li `directoryPath` soubory musí být absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="4a7b5-376">Přetížení povolení s uvedením:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="4a7b5-377">Delegát, `Action<KeyPerFileConfigurationSource>` který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="4a7b5-378">Určuje, zda je adresář volitelný a zda je cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="4a7b5-379">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="4a7b5-380">Například název `Logging__LogLevel__System` souboru vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="4a7b5-381">Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="4a7b5-382">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="4a7b5-382">Memory configuration provider</span></span>

<span data-ttu-id="4a7b5-383">Používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> kolekci v paměti jako dvojice konfiguračníklíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="4a7b5-384">Následující kód přidá kolekci paměti do konfiguračního systému:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="4a7b5-385">Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-386">V předchozím kódu `config.AddInMemoryCollection(Dict)` je přidán za [výchozí zprostředkovatele konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="4a7b5-387">Příklad objednání zprostředkovatelů konfigurace naleznete v tématu [Zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4a7b5-388">Příklad objednání zprostředkovatelů konfigurace naleznete v tématu [Zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4a7b5-389">Viz [Vazba pole](#boa) pro `MemoryConfigurationProvider`jiný příklad pomocí .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="4a7b5-390">GetValue</span><span class="sxs-lookup"><span data-stu-id="4a7b5-390">GetValue</span></span>

<span data-ttu-id="4a7b5-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-392">V předchozím kódu, `NumberKey` pokud není nalezen v konfiguraci, `99` výchozí hodnota se používá.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-392">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="4a7b5-393">GetSection, GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="4a7b5-393">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="4a7b5-394">Příklady, které následují, zvažte následující soubor *MySubsection.json:*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-394">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="4a7b5-395">Následující kód přidá *MySubsection.json* k poskytovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-395">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="4a7b5-396">Getsection</span><span class="sxs-lookup"><span data-stu-id="4a7b5-396">GetSection</span></span>

<span data-ttu-id="4a7b5-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí pododdíl konfigurace se zadaným klíčem podsekce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="4a7b5-398">Následující kód vrátí `section1`hodnoty pro :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-398">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-399">Následující kód vrátí `section2:subsection0`hodnoty pro :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-399">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-400">`GetSection`nikdy `null`nevrátí .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-400">`GetSection` never returns `null`.</span></span> <span data-ttu-id="4a7b5-401">Pokud odpovídající oddíl nebyl nalezen, `IConfigurationSection` je vrácena prázdná.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-401">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="4a7b5-402">Pokud `GetSection` vrátí odpovídající <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> oddíl, není naplněn.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-402">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="4a7b5-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> a jsou vráceny, pokud oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="4a7b5-404">GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="4a7b5-404">GetChildren and Exists</span></span>

<span data-ttu-id="4a7b5-405">Následující kód volá [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a `section2:subsection0`vrátí hodnoty pro :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-405">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-406">Předchozí kód volá [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) ověřit oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-406">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="4a7b5-407">Svázat pole</span><span class="sxs-lookup"><span data-stu-id="4a7b5-407">Bind an array</span></span>

<span data-ttu-id="4a7b5-408">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-408">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4a7b5-409">Jakýkoli formát pole, který zveřejňuje segment číselného klíče, je schopen maticové vazby na pole třídy [POCO.](https://wikipedia.org/wiki/Plain_Old_CLR_Object)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-409">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="4a7b5-410">Vezměme si *MyArray.json* z [ukázky ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="4a7b5-410">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="4a7b5-411">Následující kód přidá *myarray.json* k poskytovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-411">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="4a7b5-412">Následující kód přečte konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-412">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-413">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="4a7b5-414">V předchozím výstupu index 3 `value40`má hodnotu , odpovídající `"4": "value40",` v *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-414">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="4a7b5-415">Indexy vázaného pole jsou souvislé a nejsou vázány na index konfiguračního klíče.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-415">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="4a7b5-416">Pořadač konfigurace není schopen spojit nulové hodnoty nebo vytvořit nulové položky ve vázaných objektech.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-416">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="4a7b5-417">Následující kód načte konfiguraci `array:entries` metodou <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-417">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="4a7b5-418">Následující kód přečte konfiguraci `arrayDict` `Dictionary` v a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-418">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-419">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-419">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="4a7b5-420">Index &num;3 v vázaném objektu `array:4` obsahuje konfigurační `value4`data pro konfigurační klíč a jeho hodnotu .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-420">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="4a7b5-421">Pokud jsou konfigurační data obsahující pole vázána, indexy pole v konfiguračních klíčích se při vytváření objektu používají k iteraci konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-421">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="4a7b5-422">Hodnotu null nelze zachovat v konfiguračních datech a položka s hodnotou null není vytvořena ve vázaném objektu, když pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-422">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="4a7b5-423">Chybějící položka konfigurace &num;pro index 3 může `ArrayExample` být dodána před vazbou &num;na instanci libovolným poskytovatelem konfigurace, který čte dvojici klíč/hodnota indexu 3.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-423">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="4a7b5-424">Zvažte následující soubor *Value3.json* z ukázkového stažení:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-424">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="4a7b5-425">Následující kód zahrnuje konfiguraci pro *Value3.json* a `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-425">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="4a7b5-426">Následující kód přečte předchozí konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-426">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4a7b5-427">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-427">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="4a7b5-428">Vlastní zprostředkovatelé konfigurace nejsou nutné k implementaci vazby pole.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-428">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="4a7b5-429">Vlastní zprostředkovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-429">Custom configuration provider</span></span>

<span data-ttu-id="4a7b5-430">Ukázková aplikace ukazuje, jak vytvořit základní ho poskytovatele konfigurace, který čte dvojice hodnot klíče konfigurace z databáze pomocí [entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-430">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="4a7b5-431">Poskytovatel má následující charakteristiky:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-431">The provider has the following characteristics:</span></span>

* <span data-ttu-id="4a7b5-432">Ef v paměti databáze se používá pro účely demonstrace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-432">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="4a7b5-433">Chcete-li použít databázi, která vyžaduje `ConfigurationBuilder` připojovací řetězec, implementujte sekundární pro zadání připojovacího řetězce od jiného zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-433">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="4a7b5-434">Zprostředkovatel přečte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-434">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="4a7b5-435">Zprostředkovatel není dotaz databáze na základě pro jeden klíč.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-435">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="4a7b5-436">Opětovné načtení při změně není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-436">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="4a7b5-437">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot v databázi.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-437">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="4a7b5-438">*Modely/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-438">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="4a7b5-439">Přidejte `EFConfigurationContext` a uložte a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-439">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="4a7b5-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="4a7b5-441">Vytvořte třídu, <xref:Microsoft.Extensions.Configuration.IConfigurationSource>která implementuje .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-441">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="4a7b5-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="4a7b5-443">Vytvořte vlastního zprostředkovatele konfigurace <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>děděním z aplikace .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-443">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="4a7b5-444">Zprostředkovatel konfigurace inicializuje databázi, když je prázdná.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-444">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="4a7b5-445">Vzhledem k tomu, [že konfigurační klíče nerozlišují malá a velká písmena](#keys), je slovník používaný k inicializaci databáze vytvořen pomocí porovnávání bez rozlišování velkých a malých písmen ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-445">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="4a7b5-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="4a7b5-447">Metoda `AddEFConfiguration` rozšíření umožňuje přidání zdroje `ConfigurationBuilder`konfigurace do .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-447">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="4a7b5-448">*Rozšíření/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-448">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="4a7b5-449">Následující kód ukazuje, jak `EFConfigurationProvider` používat vlastní v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-449">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="4a7b5-450">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="4a7b5-450">Access configuration in Startup</span></span>

<span data-ttu-id="4a7b5-451">Následující kód zobrazuje konfigurační data v `Startup` metodách:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-451">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="4a7b5-452">Příklad přístupu ke konfiguraci pomocí metod pohodlí při spuštění naleznete v [tématu Spuštění aplikace: Metody pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-452">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="4a7b5-453">Přístup ke konfiguraci na stránkách Razor</span><span class="sxs-lookup"><span data-stu-id="4a7b5-453">Access configuration in Razor Pages</span></span>

<span data-ttu-id="4a7b5-454">Následující kód zobrazuje konfigurační data na stránce Razor:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-454">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="4a7b5-455">Přístup ke konfiguraci v souboru zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="4a7b5-455">Access configuration in a MVC view file</span></span>

<span data-ttu-id="4a7b5-456">Následující kód zobrazuje konfigurační data v zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-456">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="4a7b5-457">Konfigurace hostitele versus aplikace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-457">Host versus app configuration</span></span>

<span data-ttu-id="4a7b5-458">Před konfigurací a spuštěním aplikace je nakonfigurován a spuštěn *hostitel.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="4a7b5-459">Hostitel je zodpovědný za spuštění aplikace a správu životnosti.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4a7b5-460">Aplikace i hostitel jsou konfigurovány pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="4a7b5-461">Dvojice klíč-hodnota konfigurace hostitele jsou také zahrnuty v konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="4a7b5-462">Další informace o tom, jak jsou poskytovatelé konfigurace používáni při stavění hostitele a jak zdroje konfigurace ovlivňují konfiguraci hostitele, naleznete v tématu <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="4a7b5-463">Výchozí konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="4a7b5-463">Default host configuration</span></span>

<span data-ttu-id="4a7b5-464">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET core verze 2.2 tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="4a7b5-465">Konfigurace hostitele je poskytována od:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="4a7b5-466">Proměnné prostředí s `DOTNET_` předponou `DOTNET_ENVIRONMENT`(například) pomocí [zprostředkovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="4a7b5-467">Předpona`DOTNET_`( ) je odstraněna při načtení dvojice konfiguračníklíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="4a7b5-468">Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="4a7b5-469">Výchozí konfigurace webového`ConfigureWebHostDefaults`hostitele je vytvořena ( ):</span><span class="sxs-lookup"><span data-stu-id="4a7b5-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="4a7b5-470">Kestrel se používá jako webový server a konfigurován pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="4a7b5-471">Přidat middleware filtrování hostitelů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="4a7b5-472">Přidat middleware předávaných záhlaví, pokud je proměnná `ASPNETCORE_FORWARDEDHEADERS_ENABLED` prostředí nastavena na . `true`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="4a7b5-473">Povolte integraci iis.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="4a7b5-474">Jiná konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-474">Other configuration</span></span>

<span data-ttu-id="4a7b5-475">Toto téma se bude vymkat *pouze konfiguraci aplikace*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="4a7b5-476">Další aspekty spouštění a hostování aplikací ASP.NET Core jsou konfigurovány pomocí konfiguračních souborů, které nejsou zahrnuty v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="4a7b5-477">*launch.json*/*launchSettings.json* jsou konfigurační soubory nástrojů pro vývojové prostředí, popsané:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="4a7b5-478">V <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="4a7b5-479">V celé sadě dokumentace, kde se soubory používají ke konfiguraci ASP.NET základních aplikací pro scénáře vývoje.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="4a7b5-480">*Web.config* je konfigurační soubor serveru popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4a7b5-481">Další informace o migraci konfigurace aplikace z předchozích <xref:migration/proper-to-2x/index#store-configurations>verzí ASP.NET naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="4a7b5-482">Přidání konfigurace z externí sestavy</span><span class="sxs-lookup"><span data-stu-id="4a7b5-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="4a7b5-483">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidávání vylepšení do aplikace při spuštění z externího `Startup` sestavení mimo třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4a7b5-484">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a7b5-485">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4a7b5-485">Additional resources</span></span>

* [<span data-ttu-id="4a7b5-486">Zdrojový kód konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4a7b5-487">Konfigurace aplikace v ASP.NET Core je založena na párech klíč-hodnota vytvořené *poskytovateli konfigurace*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="4a7b5-488">Zprostředkovatelé konfigurace čtou konfigurační data do dvojic klíč-hodnota z různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="4a7b5-489">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4a7b5-489">Azure Key Vault</span></span>
* <span data-ttu-id="4a7b5-490">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4a7b5-490">Azure App Configuration</span></span>
* <span data-ttu-id="4a7b5-491">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-491">Command-line arguments</span></span>
* <span data-ttu-id="4a7b5-492">Vlastní zprostředkovatelé (nainstalované nebo vytvořené)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="4a7b5-493">Soubory adresářů</span><span class="sxs-lookup"><span data-stu-id="4a7b5-493">Directory files</span></span>
* <span data-ttu-id="4a7b5-494">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-494">Environment variables</span></span>
* <span data-ttu-id="4a7b5-495">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="4a7b5-495">In-memory .NET objects</span></span>
* <span data-ttu-id="4a7b5-496">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="4a7b5-496">Settings files</span></span>

<span data-ttu-id="4a7b5-497">Balíčky konfigurace pro běžné scénáře zprostředkovatele konfigurace ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuty v [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4a7b5-498">Příklady kódu, které následují a <xref:Microsoft.Extensions.Configuration> v ukázkové aplikaci používají obor názvů:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="4a7b5-499">*Vzor možností* je rozšíření koncepty konfigurace popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="4a7b5-500">Možnosti používají třídy k reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="4a7b5-501">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="4a7b5-502">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="4a7b5-503">Konfigurace hostitele versus aplikace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-503">Host versus app configuration</span></span>

<span data-ttu-id="4a7b5-504">Před konfigurací a spuštěním aplikace je nakonfigurován a spuštěn *hostitel.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="4a7b5-505">Hostitel je zodpovědný za spuštění aplikace a správu životnosti.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4a7b5-506">Aplikace i hostitel jsou konfigurovány pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="4a7b5-507">Dvojice klíč-hodnota konfigurace hostitele jsou také zahrnuty v konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="4a7b5-508">Další informace o tom, jak jsou poskytovatelé konfigurace používáni při stavění hostitele a jak zdroje konfigurace ovlivňují konfiguraci hostitele, naleznete v tématu <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="4a7b5-509">Jiná konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-509">Other configuration</span></span>

<span data-ttu-id="4a7b5-510">Toto téma se bude vymkat *pouze konfiguraci aplikace*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="4a7b5-511">Další aspekty spouštění a hostování aplikací ASP.NET Core jsou konfigurovány pomocí konfiguračních souborů, které nejsou zahrnuty v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="4a7b5-512">*launch.json*/*launchSettings.json* jsou konfigurační soubory nástrojů pro vývojové prostředí, popsané:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="4a7b5-513">V <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="4a7b5-514">V celé sadě dokumentace, kde se soubory používají ke konfiguraci ASP.NET základních aplikací pro scénáře vývoje.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="4a7b5-515">*Web.config* je konfigurační soubor serveru popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4a7b5-516">Další informace o migraci konfigurace aplikace z předchozích <xref:migration/proper-to-2x/index#store-configurations>verzí ASP.NET naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="4a7b5-517">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-517">Default configuration</span></span>

<span data-ttu-id="4a7b5-518">Webové aplikace založené na ASP.NET nové šablony <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> [Core dotnet](/dotnet/core/tools/dotnet-new) volají při vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="4a7b5-519">`CreateDefaultBuilder`poskytuje výchozí konfiguraci aplikace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="4a7b5-520">Následující text platí pro aplikace používající [webový hostitel](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4a7b5-521">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)naleznete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="4a7b5-522">Konfigurace hostitele je poskytována od:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="4a7b5-523">Proměnné prostředí jsou `ASPNETCORE_` předponou `ASPNETCORE_ENVIRONMENT`(například) pomocí [zprostředkovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="4a7b5-524">Předpona`ASPNETCORE_`( ) je odstraněna při načtení dvojice konfiguračníklíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="4a7b5-525">Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="4a7b5-526">Konfigurace aplikace je poskytována od:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="4a7b5-527">*appsettings.json* pomocí [zprostředkovatele konfigurace souborů](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="4a7b5-528">*nastavení aplikace. {Environment}.json* pomocí [zprostředkovatele konfigurace souborů](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="4a7b5-529">[Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí pomocí sestavení položky.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="4a7b5-530">Proměnné prostředí pomocí [zprostředkovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="4a7b5-531">Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="4a7b5-532">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="4a7b5-532">Security</span></span>

<span data-ttu-id="4a7b5-533">K zabezpečení citlivých konfiguračních dat přijměte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="4a7b5-534">Nikdy neuklápějte hesla nebo jiná citlivá data v kódu zprostředkovatele konfigurace nebo v konfiguračních souborech ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="4a7b5-535">Nepoužívejte produkční tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="4a7b5-536">Zadejte tajné kódy mimo projekt tak, aby nemohly být omylem potvrzeny do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="4a7b5-537">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="4a7b5-538"><xref:security/app-secrets>&ndash; Obsahuje rady ohledně používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-538"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="4a7b5-539">Správce tajných barev používá zprostředkovatele konfigurace souborů k ukládání tajných kódů uživatelů v souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="4a7b5-540">Zprostředkovatel konfigurace souborů je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="4a7b5-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné kódy aplikací pro ASP.NET základní aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="4a7b5-542">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="4a7b5-543">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="4a7b5-543">Hierarchical configuration data</span></span>

<span data-ttu-id="4a7b5-544">Rozhraní CONFIGURATION API je schopno udržovat hierarchická konfigurační data sloučením hierarchických dat pomocí oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="4a7b5-545">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou částí:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="4a7b5-546">Při čtení souboru do konfigurace jsou vytvořeny jedinečné klíče pro zachování původní hierarchické datové struktury zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="4a7b5-547">Řezy a klíče jsou srovnány se`:`sloučením s použitím dvojtečky ( ) pro zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="4a7b5-548">oddíl0:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-548">section0:key0</span></span>
* <span data-ttu-id="4a7b5-549">oddíl0:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-549">section0:key1</span></span>
* <span data-ttu-id="4a7b5-550">oddíl1:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-550">section1:key0</span></span>
* <span data-ttu-id="4a7b5-551">oddíl1:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-551">section1:key1</span></span>

<span data-ttu-id="4a7b5-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody jsou k dispozici pro oddělení oddílů a podřízených částí v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="4a7b5-553">Tyto metody jsou popsány dále v [GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="4a7b5-554">Zásady</span><span class="sxs-lookup"><span data-stu-id="4a7b5-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="4a7b5-555">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="4a7b5-555">Sources and providers</span></span>

<span data-ttu-id="4a7b5-556">Při spuštění aplikace jsou zdroje konfigurace čteny v pořadí, v jakém jsou zadáni jejich zprostředkovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="4a7b5-557">Zprostředkovatelé konfigurace, kteří implementují zjišťování změn, mají možnost znovu načíst konfiguraci při změně základního nastavení.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="4a7b5-558">Například zprostředkovatel konfigurace souborů (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace trezoru klíčů Azure](xref:security/key-vault-configuration) implementují zjišťování změn.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="4a7b5-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí aplikace (DI).](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4a7b5-560"><xref:Microsoft.Extensions.Configuration.IConfiguration>lze vstřikovat <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> do Razor <xref:Microsoft.AspNetCore.Mvc.Controller> Pages nebo MVC získat konfiguraci pro třídu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="4a7b5-561">V následujících příkladech `_config` se toto pole používá pro přístup k hodnotám konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="4a7b5-562">Zprostředkovatelé konfigurace nemohou využívat DI, protože není k dispozici, když jsou nastaveny hostitelem.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="4a7b5-563">Klíče</span><span class="sxs-lookup"><span data-stu-id="4a7b5-563">Keys</span></span>

<span data-ttu-id="4a7b5-564">Konfigurační klíče přijmout následující konvence:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="4a7b5-565">Klíče nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-565">Keys are case-insensitive.</span></span> <span data-ttu-id="4a7b5-566">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="4a7b5-567">Pokud je hodnota pro stejný klíč nastavena stejnými nebo různými poskytovateli konfigurace, je poslední hodnotou nastavenou na klíč použitá hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="4a7b5-568">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="4a7b5-568">Hierarchical keys</span></span>
  * <span data-ttu-id="4a7b5-569">V rámci rozhraní API konfigurace`:`funguje oddělovač dvojtečky ( ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-569">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="4a7b5-570">V proměnných prostředí oddělovač dvojtečky nemusí fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-570">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="4a7b5-571">Dvojité podtržítko (`__`) je podporováno všemi platformami a automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-571">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="4a7b5-572">V azure key vault hierarchické klíče použít `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-572">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="4a7b5-573">Napište kód, který nahradí pomlčky dvojtečkou, když jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-573">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="4a7b5-574">Podporuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> vazby pole na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-574">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4a7b5-575">Vazba pole je popsána v části [Bind pole.](#bind-an-array-to-a-class)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-575">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="4a7b5-576">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="4a7b5-576">Values</span></span>

<span data-ttu-id="4a7b5-577">Hodnoty konfigurace přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-577">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="4a7b5-578">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-578">Values are strings.</span></span>
* <span data-ttu-id="4a7b5-579">Hodnoty null nelze uložit v konfiguraci nebo vázány na objekty.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-579">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="4a7b5-580">Poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="4a7b5-580">Providers</span></span>

<span data-ttu-id="4a7b5-581">V následující tabulce jsou uvedeny zprostředkovatelé konfigurace, kteří jsou k dispozici pro ASP.NET aplikace Core.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-581">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="4a7b5-582">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="4a7b5-582">Provider</span></span> | <span data-ttu-id="4a7b5-583">Poskytuje konfiguraci od&hellip;</span><span class="sxs-lookup"><span data-stu-id="4a7b5-583">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="4a7b5-584">[Zprostředkovatel konfigurace úložiště klíčů Azure](xref:security/key-vault-configuration) (témata*zabezpečení)*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-584">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="4a7b5-585">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4a7b5-585">Azure Key Vault</span></span> |
| <span data-ttu-id="4a7b5-586">[Zprostředkovatel konfigurace aplikací Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace k Azure)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-586">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="4a7b5-587">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4a7b5-587">Azure App Configuration</span></span> |
| [<span data-ttu-id="4a7b5-588">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-588">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="4a7b5-589">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-589">Command-line parameters</span></span> |
| [<span data-ttu-id="4a7b5-590">Vlastní zprostředkovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-590">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="4a7b5-591">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="4a7b5-591">Custom source</span></span> |
| [<span data-ttu-id="4a7b5-592">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-592">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="4a7b5-593">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-593">Environment variables</span></span> |
| [<span data-ttu-id="4a7b5-594">Zprostředkovatel konfigurace souborů</span><span class="sxs-lookup"><span data-stu-id="4a7b5-594">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="4a7b5-595">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-595">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="4a7b5-596">Zprostředkovatel konfigurace pro jeden soubor</span><span class="sxs-lookup"><span data-stu-id="4a7b5-596">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="4a7b5-597">Soubory adresářů</span><span class="sxs-lookup"><span data-stu-id="4a7b5-597">Directory files</span></span> |
| [<span data-ttu-id="4a7b5-598">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="4a7b5-598">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="4a7b5-599">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="4a7b5-599">In-memory collections</span></span> |
| <span data-ttu-id="4a7b5-600">[Tajné klíče uživatelů (Tajný správce)](xref:security/app-secrets) *(témata zabezpečení)*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-600">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="4a7b5-601">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="4a7b5-601">File in the user profile directory</span></span> |

<span data-ttu-id="4a7b5-602">Zdroje konfigurace jsou čteny v pořadí, ve které jsou při spuštění určeni jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-602">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="4a7b5-603">Zprostředkovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-603">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="4a7b5-604">Objednejte zprostředkovatele konfigurace v kódu tak, aby vyhovovaly prioritám pro základní zdroje konfigurace, které aplikace vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-604">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="4a7b5-605">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-605">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="4a7b5-606">Soubory (*appsettings.json*, *appsettings.{ Environment}.json*, `{Environment}` kde je aktuální hostitelské prostředí aplikace)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-606">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="4a7b5-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4a7b5-607">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="4a7b5-608">[Tajné klíče uživatelů (Správce tajných barev)](xref:security/app-secrets) (pouze vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-608">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="4a7b5-609">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-609">Environment variables</span></span>
1. <span data-ttu-id="4a7b5-610">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-610">Command-line arguments</span></span>

<span data-ttu-id="4a7b5-611">Běžnou praxí je umístit zprostředkovatele konfigurace příkazového řádku jako posledního v řadě zprostředkovatelů, aby argumenty příkazového řádku povolily přepsání konfigurace nastavené jinými zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-611">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="4a7b5-612">Předchozí posloupnost zprostředkovatelů se používá při inicializace nového tvůrce hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-612">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4a7b5-613">Další informace naleznete v části [Výchozí konfigurace.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-613">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="4a7b5-614">Konfigurace tvůrce hostitelů pomocí funkce UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="4a7b5-614">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="4a7b5-615">Chcete-li nakonfigurovat <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> tvůrce hostitelů, zavolejte tvůrce hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-615">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="4a7b5-616">Konfigurace konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-616">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4a7b5-617">Volání `ConfigureAppConfiguration` při vytváření hostitele určit poskytovatele konfigurace aplikace kromě těch, `CreateDefaultBuilder`které automaticky přidá :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-617">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="4a7b5-618">Přepsat předchozí konfiguraci argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-618">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="4a7b5-619">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat `AddCommandLine` argumenty příkazového řádku, zavolejte poslední:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-619">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="4a7b5-620">Odebrat zprostředkovatele přidané createdefaultbuilderem</span><span class="sxs-lookup"><span data-stu-id="4a7b5-620">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="4a7b5-621">Chcete-li odebrat `CreateDefaultBuilder`zprostředkovatele přidané uživatelem , nejprve volejte [clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [iConfigurationBuilder.Sources:](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-621">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="4a7b5-622">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-622">Consume configuration during app startup</span></span>

<span data-ttu-id="4a7b5-623">Konfigurace dodávaná `ConfigureAppConfiguration` do aplikace je k dispozici `Startup.ConfigureServices`během spuštění aplikace, včetně .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-623">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4a7b5-624">Další informace naleznete v [části Konfigurace aplikace Access při spuštění.](#access-configuration-during-startup)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-624">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="4a7b5-625">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="4a7b5-625">Command-line Configuration Provider</span></span>

<span data-ttu-id="4a7b5-626">Načte <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> konfiguraci z dvojice klíč-hodnota argumentu příkazového řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-626">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="4a7b5-627">Chcete-li aktivovat konfiguraci příkazového řádku, je metoda <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> rozšíření volána na instanci aplikace <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-627">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4a7b5-628">`AddCommandLine`je automaticky `CreateDefaultBuilder(string [])` volána, když je volána.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-628">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="4a7b5-629">Další informace naleznete v části [Výchozí konfigurace.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-629">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4a7b5-630">`CreateDefaultBuilder`také zatížení:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-630">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4a7b5-631">Volitelná konfigurace z *appsettings.json* a *appsettings.{ Prostředí}.json.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-631">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="4a7b5-632">[Tajné klíče uživatelů (Správce tajných kódů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-632">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4a7b5-633">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-633">Environment variables.</span></span>

<span data-ttu-id="4a7b5-634">`CreateDefaultBuilder`přidá zprostředkovatele konfigurace příkazového řádku jako poslední.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-634">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="4a7b5-635">Argumenty příkazového řádku předané při přepsání runtime konfigurace nastavené jinými zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-635">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="4a7b5-636">`CreateDefaultBuilder`funguje, když je hostitel konstruován.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-636">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="4a7b5-637">Konfigurace příkazového řádku aktivovaná proto `CreateDefaultBuilder` může ovlivnit konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-637">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="4a7b5-638">Pro aplikace založené na šablonách `AddCommandLine` ASP.NET Core `CreateDefaultBuilder`již byla volána aplikací .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-638">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4a7b5-639">Chcete-li přidat další zprostředkovatele konfigurace a zachovat možnost přepsat konfiguraci od těchto zprostředkovatelů `ConfigureAppConfiguration` s `AddCommandLine` argumenty příkazového řádku, zavolejte další zprostředkovatele aplikace a volání poslední.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-639">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="4a7b5-640">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-640">**Example**</span></span>

<span data-ttu-id="4a7b5-641">Ukázková aplikace využívá statickou `CreateDefaultBuilder` metodu pohodlí k sestavení hostitele, která zahrnuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-641">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="4a7b5-642">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-642">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="4a7b5-643">Zařazuje příkaz `dotnet run` argument `dotnet run CommandLineKey=CommandLineValue`příkazu .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-643">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="4a7b5-644">Po spuštění aplikace otevřete prohlížeč v `http://localhost:5000`aplikaci na adrese .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-644">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4a7b5-645">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro `dotnet run`argument příkazového řádku konfigurace k dispozici .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-645">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="4a7b5-646">Argumenty</span><span class="sxs-lookup"><span data-stu-id="4a7b5-646">Arguments</span></span>

<span data-ttu-id="4a7b5-647">Hodnota musí následovat znaménko rovná se (`=`),`--` `/`nebo klíč musí mít předponu ( nebo ) pokud hodnota následuje za mezerou.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-647">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="4a7b5-648">Hodnota není vyžadována, pokud se používá znaménko rovná se `CommandLineKey=`(například).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-648">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="4a7b5-649">Předpona klíče</span><span class="sxs-lookup"><span data-stu-id="4a7b5-649">Key prefix</span></span>               | <span data-ttu-id="4a7b5-650">Příklad</span><span class="sxs-lookup"><span data-stu-id="4a7b5-650">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="4a7b5-651">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="4a7b5-651">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="4a7b5-652">Dvě pomlčky`--`( )</span><span class="sxs-lookup"><span data-stu-id="4a7b5-652">Two dashes (`--`)</span></span>        | <span data-ttu-id="4a7b5-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="4a7b5-654">Lomítko`/`- )</span><span class="sxs-lookup"><span data-stu-id="4a7b5-654">Forward slash (`/`)</span></span>      | <span data-ttu-id="4a7b5-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="4a7b5-656">V rámci stejného příkazu nekombinujte dvojice klíčových hodnot argumentů příkazového řádku, které používají znaménko rovná se párům klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-656">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="4a7b5-657">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-657">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="4a7b5-658">Přepnout mapování</span><span class="sxs-lookup"><span data-stu-id="4a7b5-658">Switch mappings</span></span>

<span data-ttu-id="4a7b5-659">Mapování přepínačů umožňuje logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-659">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="4a7b5-660">Při ručním vytváření <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>konfigurace s , zadejte slovník <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> switch nahrazení metody.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-660">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="4a7b5-661">Při použití slovníku mapování přepínačů je slovník zkontrolován na klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-661">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="4a7b5-662">Pokud je klíč příkazového řádku nalezen ve slovníku, hodnota slovníku (nahrazení klíče) je předána zpět a nastavte dvojici klíč-hodnota do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-662">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="4a7b5-663">Mapování přepínače je vyžadováno pro všechny klávesy příkazového řádku s předponou s jednou pomlčkou (`-`).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-663">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="4a7b5-664">Přepnout pravidla klíčů slovníku mapování:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-664">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="4a7b5-665">Přepínače musí začínat pomlčkou`-`(`--`) nebo dvojitou pomlčkou ( ).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-665">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="4a7b5-666">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-666">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="4a7b5-667">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-667">Create a switch mappings dictionary.</span></span> <span data-ttu-id="4a7b5-668">V následujícím příkladu jsou vytvořeny dvě mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-668">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="4a7b5-669">Když je hostitel vytvořen, volejte pomocí `AddCommandLine` slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-669">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="4a7b5-670">U aplikací, které používají mapování `CreateDefaultBuilder` přepínačů, by volání nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-670">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="4a7b5-671">Volání metody neobsahuje mapované přepínače a neexistuje žádný způsob, jak předat slovník mapování přepínačů . `CreateDefaultBuilder` `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-671">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4a7b5-672">Řešením není předat argumenty, `CreateDefaultBuilder` ale místo toho `ConfigurationBuilder` povolit `AddCommandLine` metodu metody pro zpracování argumentů i slovníku mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-672">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="4a7b5-673">Po vytvoření slovníku mapování přepínačů obsahuje data zobrazená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-673">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="4a7b5-674">Klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-674">Key</span></span>       | <span data-ttu-id="4a7b5-675">Hodnota</span><span class="sxs-lookup"><span data-stu-id="4a7b5-675">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="4a7b5-676">Pokud se při spuštění aplikace používají klávesy mapované přepínačem, konfigurace obdrží hodnotu konfigurace na klíči dodaném slovníkem:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-676">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="4a7b5-677">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-677">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="4a7b5-678">Klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-678">Key</span></span>               | <span data-ttu-id="4a7b5-679">Hodnota</span><span class="sxs-lookup"><span data-stu-id="4a7b5-679">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="4a7b5-680">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-680">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="4a7b5-681">Konfigurace <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> zatížení z dvojice proměnné klíč-hodnota prostředí za běhu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-681">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="4a7b5-682">Chcete-li aktivovat konfiguraci <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> proměnných prostředí, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zavolejte metodu rozšíření na instanci aplikace .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-682">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4a7b5-683">[Služba Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavení proměnných prostředí na webu Azure Portal, které můžou přepsat konfiguraci aplikace pomocí zprostředkovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="4a7b5-684">Další informace najdete v tématu [Azure Apps: Přepsat konfiguraci aplikací pomocí portálu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-684">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4a7b5-685">`AddEnvironmentVariables`se používá k načtení `ASPNETCORE_` proměnných prostředí s předponou pro [konfiguraci hostitele,](#host-versus-app-configuration) když je inicializován nový tvůrce hostitele s [webovým hostitelem](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je volán.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-685">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="4a7b5-686">Další informace naleznete v části [Výchozí konfigurace.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-686">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4a7b5-687">`CreateDefaultBuilder`také zatížení:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-687">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4a7b5-688">Konfigurace aplikace z nepředponožených proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-688">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="4a7b5-689">Volitelná konfigurace z *appsettings.json* a *appsettings.{ Prostředí}.json.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-689">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="4a7b5-690">[Tajné klíče uživatelů (Správce tajných kódů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-690">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4a7b5-691">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-691">Command-line arguments.</span></span>

<span data-ttu-id="4a7b5-692">Zprostředkovatel konfigurace proměnných prostředí se nazývá po konfiguraci je vytvořen z tajných kódů uživatelů a soubory *nastavení aplikací.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-692">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="4a7b5-693">Volání zprostředkovatele v této pozici umožňuje proměnné prostředí číst za běhu přepsat konfiguraci nastavenou tajnými kódy uživatelů a soubory *nastavení aplikací.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-693">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="4a7b5-694">Chcete-li zajistit konfiguraci aplikace z dalších proměnných `ConfigureAppConfiguration` prostředí, zavolejte další zprostředkovatele aplikace a zavolejte `AddEnvironmentVariables` s předponou:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-694">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="4a7b5-695">Volání `AddEnvironmentVariables` poslední povolit proměnné prostředí s danou předponou přepsat hodnoty od jiných poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-695">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="4a7b5-696">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-696">**Example**</span></span>

<span data-ttu-id="4a7b5-697">Ukázková aplikace využívá statickou `CreateDefaultBuilder` metodu pohodlí k sestavení hostitele, která zahrnuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-697">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="4a7b5-698">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-698">Run the sample app.</span></span> <span data-ttu-id="4a7b5-699">Otevřete prohlížeč do `http://localhost:5000`aplikace na adrese .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-699">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4a7b5-700">Všimněte si, že výstup obsahuje dvojici `ENVIRONMENT`klíč-hodnota pro proměnnou prostředí .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-700">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="4a7b5-701">Hodnota odráží prostředí, ve kterém je aplikace `Development` spuštěna, obvykle při spuštění místně.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-701">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="4a7b5-702">Chcete-li zachovat seznam proměnných prostředí vykreslených aplikací krátký, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-702">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="4a7b5-703">Podívejte se na ukázkový soubor *Pages/Index.cshtml.cs.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-703">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="4a7b5-704">Chcete-li zpřístupnit všechny proměnné prostředí, které `FilteredConfiguration` jsou v aplikaci k dispozici, změňte v *stránce/index.cshtml.cs* následující:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-704">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="4a7b5-705">Předpony</span><span class="sxs-lookup"><span data-stu-id="4a7b5-705">Prefixes</span></span>

<span data-ttu-id="4a7b5-706">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při poskytování `AddEnvironmentVariables` předpony metody.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-706">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="4a7b5-707">Chcete-li například filtrovat proměnné `CUSTOM_`prostředí na předponě , zajistěte předponu zprostředkovateli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-707">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="4a7b5-708">Předpona je odstraněna při vytvoření dvojice konfigurační klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-708">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="4a7b5-709">Při vytvoření tvůrce hostitele je konfigurace hostitele poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-709">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="4a7b5-710">Další informace o předponě použité pro tyto proměnné prostředí naleznete v části [Výchozí konfigurace.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-710">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4a7b5-711">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-711">**Connection string prefixes**</span></span>

<span data-ttu-id="4a7b5-712">Konfigurační rozhraní API má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacího řetězce, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-712">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="4a7b5-713">Proměnné prostředí s předčíslími zobrazenými v tabulce se načtou do aplikace, pokud není zadána `AddEnvironmentVariables`žádná předpona .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-713">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="4a7b5-714">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="4a7b5-714">Connection string prefix</span></span> | <span data-ttu-id="4a7b5-715">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="4a7b5-715">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="4a7b5-716">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="4a7b5-716">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="4a7b5-717">MySQL</span><span class="sxs-lookup"><span data-stu-id="4a7b5-717">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="4a7b5-718">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="4a7b5-718">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="4a7b5-719">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4a7b5-719">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="4a7b5-720">Když je proměnná prostředí zjištěna a načtena do konfigurace s některou ze čtyř předpon uvedených v tabulce:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-720">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="4a7b5-721">Konfigurační klíč je vytvořen odebráním předpony proměnné`ConnectionStrings`prostředí a přidáním oddílu konfiguračního klíče ( ).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-721">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="4a7b5-722">Je vytvořen nový konfigurační pár klíč-hodnota, `CUSTOMCONNSTR_`který představuje zprostředkovatele připojení k databázi (s výjimkou , který nemá žádného uvedeného zprostředkovatele).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-722">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="4a7b5-723">Klíč proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="4a7b5-723">Environment variable key</span></span> | <span data-ttu-id="4a7b5-724">Převedený konfigurační klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-724">Converted configuration key</span></span> | <span data-ttu-id="4a7b5-725">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="4a7b5-725">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4a7b5-726">Položka konfigurace nebyla vytvořena.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-726">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4a7b5-727">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4a7b5-728">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-728">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4a7b5-729">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4a7b5-730">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-730">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4a7b5-731">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4a7b5-732">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-732">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="4a7b5-733">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-733">**Example**</span></span>

<span data-ttu-id="4a7b5-734">Na serveru je vytvořena vlastní proměnná prostředí připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-734">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="4a7b5-735">Název &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-735">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="4a7b5-736">Hodnota &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="4a7b5-736">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="4a7b5-737">Pokud `IConfiguration` je vložen a přiřazen `_config`k poli s názvem , přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-737">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="4a7b5-738">Zprostředkovatel konfigurace souborů</span><span class="sxs-lookup"><span data-stu-id="4a7b5-738">File Configuration Provider</span></span>

<span data-ttu-id="4a7b5-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třída pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="4a7b5-740">Pro konkrétní typy souborů jsou vyhrazeni následující poskytovatelé konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-740">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="4a7b5-741">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="4a7b5-741">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="4a7b5-742">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="4a7b5-742">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="4a7b5-743">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="4a7b5-743">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="4a7b5-744">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="4a7b5-744">INI Configuration Provider</span></span>

<span data-ttu-id="4a7b5-745">Konfigurace <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte z ini souboru klíč-hodnota párů za běhu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-745">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="4a7b5-746">Chcete-li aktivovat konfiguraci <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> souboru INI, zavolejte metodu přípony v instanci aplikace <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-746">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4a7b5-747">Dvojtečka může být použita jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-747">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="4a7b5-748">Přetížení povolení s uvedením:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-748">Overloads permit specifying:</span></span>

* <span data-ttu-id="4a7b5-749">Určuje, zda je soubor volitelný.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-749">Whether the file is optional.</span></span>
* <span data-ttu-id="4a7b5-750">Určuje, zda je konfigurace znovu načtena, pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-750">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4a7b5-751">Slouží <xref:Microsoft.Extensions.FileProviders.IFileProvider> k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-751">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4a7b5-752">Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-752">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4a7b5-753">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-753">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="4a7b5-754">Předchozí konfigurační soubor `value`načte následující klíče pomocí :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-754">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4a7b5-755">oddíl0:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-755">section0:key0</span></span>
* <span data-ttu-id="4a7b5-756">oddíl0:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-756">section0:key1</span></span>
* <span data-ttu-id="4a7b5-757">section1:podsekce:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-757">section1:subsection:key</span></span>
* <span data-ttu-id="4a7b5-758">section2:podsekce0:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-758">section2:subsection0:key</span></span>
* <span data-ttu-id="4a7b5-759">section2:podsekce1:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-759">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="4a7b5-760">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="4a7b5-760">JSON Configuration Provider</span></span>

<span data-ttu-id="4a7b5-761">Konfigurace <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> zatížení z dvojice klíč-hodnota souboru JSON za běhu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-761">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="4a7b5-762">Chcete-li aktivovat konfiguraci <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> souboru JSON, zavolejte metodu rozšíření na instanci aplikace <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-762">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4a7b5-763">Přetížení povolení s uvedením:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-763">Overloads permit specifying:</span></span>

* <span data-ttu-id="4a7b5-764">Určuje, zda je soubor volitelný.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-764">Whether the file is optional.</span></span>
* <span data-ttu-id="4a7b5-765">Určuje, zda je konfigurace znovu načtena, pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-765">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4a7b5-766">Slouží <xref:Microsoft.Extensions.FileProviders.IFileProvider> k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-766">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4a7b5-767">`AddJsonFile`je automaticky volána dvakrát při inicializování nového tvůrce hostitele pomocí `CreateDefaultBuilder`aplikace .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-767">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4a7b5-768">Metoda je volána k načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-768">The method is called to load configuration from:</span></span>

* <span data-ttu-id="4a7b5-769">*appsettings.json* &ndash; Tento soubor se čte jako první.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-769">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="4a7b5-770">Verze souboru prostředí může přepsat hodnoty poskytované souborem *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-770">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="4a7b5-771">*nastavení aplikace. {Environment}.json* &ndash; Verze souboru prostředí je načtena na základě [iHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-771">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="4a7b5-772">Další informace naleznete v části [Výchozí konfigurace.](#default-configuration)</span><span class="sxs-lookup"><span data-stu-id="4a7b5-772">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4a7b5-773">`CreateDefaultBuilder`také zatížení:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-773">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4a7b5-774">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-774">Environment variables.</span></span>
* <span data-ttu-id="4a7b5-775">[Tajné klíče uživatelů (Správce tajných kódů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-775">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4a7b5-776">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-776">Command-line arguments.</span></span>

<span data-ttu-id="4a7b5-777">Jako první je zřízen zprostředkovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-777">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="4a7b5-778">Proto tajné klíče uživatelů, proměnné prostředí a argumenty příkazového řádku přepsat konfiguraci nastavenou soubory *nastavení aplikace.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-778">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="4a7b5-779">Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace pro soubory jiné než *appsettings.json* a *appsettings.{ Životní prostředí}.json*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-779">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4a7b5-780">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-780">**Example**</span></span>

<span data-ttu-id="4a7b5-781">Ukázková aplikace využívá statickou `CreateDefaultBuilder` metodu pohodlí k sestavení hostitele, která zahrnuje dvě volání `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-781">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="4a7b5-782">První volání `AddJsonFile` načte konfiguraci z *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-782">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="4a7b5-783">Druhé volání `AddJsonFile` načte konfiguraci z *nastavení aplikace.{ Prostředí}.json*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-783">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="4a7b5-784">Pro *nastavení aplikace. Development.json* v ukázkové aplikaci, načte se následující soubor:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-784">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="4a7b5-785">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-785">Run the sample app.</span></span> <span data-ttu-id="4a7b5-786">Otevřete prohlížeč do `http://localhost:5000`aplikace na adrese .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-786">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4a7b5-787">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-787">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="4a7b5-788">Úroveň protokolu pro `Logging:LogLevel:Default` klíč `Debug` je při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-788">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="4a7b5-789">Spusťte ukázkovou aplikaci znovu v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-789">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="4a7b5-790">Otevřete soubor *Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-790">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="4a7b5-791">V `ConfigurationSample` profilu změňte hodnotu `ASPNETCORE_ENVIRONMENT` proměnné `Production`prostředí na .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-791">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="4a7b5-792">Uložte soubor a spusťte aplikaci v `dotnet run` příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-792">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="4a7b5-793">Nastavení v *nastavení aplikace. Soubor Development.json* již nepřepisuje nastavení v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-793">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="4a7b5-794">Úroveň protokolu pro `Logging:LogLevel:Default` klíč `Warning`je .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-794">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="4a7b5-795">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="4a7b5-795">XML Configuration Provider</span></span>

<span data-ttu-id="4a7b5-796">Konfigurace <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načte z dvojice klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-796">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="4a7b5-797">Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> přípony v instanci aplikace <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-797">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4a7b5-798">Přetížení povolení s uvedením:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-798">Overloads permit specifying:</span></span>

* <span data-ttu-id="4a7b5-799">Určuje, zda je soubor volitelný.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-799">Whether the file is optional.</span></span>
* <span data-ttu-id="4a7b5-800">Určuje, zda je konfigurace znovu načtena, pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-800">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4a7b5-801">Slouží <xref:Microsoft.Extensions.FileProviders.IFileProvider> k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-801">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4a7b5-802">Kořenový uzel konfiguračního souboru je při vytvoření dvojice konfiguračního klíče a hodnoty ignorován.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-802">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="4a7b5-803">Nezadávejte v souboru definici typu dokumentu (DTD) ani obor názvů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-803">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="4a7b5-804">Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-804">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4a7b5-805">Konfigurační soubory XML mohou pro oddíly s opakováním používat odlišné názvy prvků:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-805">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="4a7b5-806">Předchozí konfigurační soubor `value`načte následující klíče pomocí :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-806">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4a7b5-807">oddíl0:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-807">section0:key0</span></span>
* <span data-ttu-id="4a7b5-808">oddíl0:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-808">section0:key1</span></span>
* <span data-ttu-id="4a7b5-809">oddíl1:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-809">section1:key0</span></span>
* <span data-ttu-id="4a7b5-810">oddíl1:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-810">section1:key1</span></span>

<span data-ttu-id="4a7b5-811">Opakující se prvky, které používají `name` stejný název prvku práce, pokud atribut se používá k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-811">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="4a7b5-812">Předchozí konfigurační soubor `value`načte následující klíče pomocí :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-812">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4a7b5-813">oddíl:oddíl0:klíč:klíč0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-813">section:section0:key:key0</span></span>
* <span data-ttu-id="4a7b5-814">oddíl:oddíl0:klíč:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-814">section:section0:key:key1</span></span>
* <span data-ttu-id="4a7b5-815">oddíl:section1:klíč:klíč0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-815">section:section1:key:key0</span></span>
* <span data-ttu-id="4a7b5-816">oddíl:section1:klíč:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-816">section:section1:key:key1</span></span>

<span data-ttu-id="4a7b5-817">Atributy lze použít k zadání hodnot:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-817">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="4a7b5-818">Předchozí konfigurační soubor `value`načte následující klíče pomocí :</span><span class="sxs-lookup"><span data-stu-id="4a7b5-818">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4a7b5-819">klíč:atribut</span><span class="sxs-lookup"><span data-stu-id="4a7b5-819">key:attribute</span></span>
* <span data-ttu-id="4a7b5-820">oddíl:klíč:atribut</span><span class="sxs-lookup"><span data-stu-id="4a7b5-820">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="4a7b5-821">Zprostředkovatel konfigurace pro jeden soubor</span><span class="sxs-lookup"><span data-stu-id="4a7b5-821">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="4a7b5-822">Používá <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> soubory adresáře jako dvojice konfiguračníklíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-822">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="4a7b5-823">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-823">The key is the file name.</span></span> <span data-ttu-id="4a7b5-824">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-824">The value contains the file's contents.</span></span> <span data-ttu-id="4a7b5-825">Zprostředkovatel konfigurace key-per-file se používá ve scénářích hostování dockeru.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-825">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="4a7b5-826">Chcete-li aktivovat konfiguraci klíče <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> za soubor, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zavolejte metodu rozšíření v instanci aplikace .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-826">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="4a7b5-827">Chcete-li `directoryPath` soubory musí být absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-827">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="4a7b5-828">Přetížení povolení s uvedením:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-828">Overloads permit specifying:</span></span>

* <span data-ttu-id="4a7b5-829">Delegát, `Action<KeyPerFileConfigurationSource>` který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-829">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="4a7b5-830">Určuje, zda je adresář volitelný a zda je cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-830">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="4a7b5-831">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-831">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="4a7b5-832">Například název `Logging__LogLevel__System` souboru vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-832">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="4a7b5-833">Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="4a7b5-834">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="4a7b5-834">Memory Configuration Provider</span></span>

<span data-ttu-id="4a7b5-835">Používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> kolekci v paměti jako dvojice konfiguračníklíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-835">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="4a7b5-836">Chcete-li aktivovat konfiguraci <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> kolekce v paměti, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zavolejte metodu rozšíření na instanci aplikace .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-836">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4a7b5-837">Zprostředkovatel konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-837">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="4a7b5-838">Volání `ConfigureAppConfiguration` při vytváření hostitele určit konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-838">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="4a7b5-839">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-839">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="4a7b5-840">Slovník se používá s voláním k `AddInMemoryCollection` zajištění konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-840">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="4a7b5-841">GetValue</span><span class="sxs-lookup"><span data-stu-id="4a7b5-841">GetValue</span></span>

<span data-ttu-id="4a7b5-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="4a7b5-843">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-843">An overload accepts a default value.</span></span>

<span data-ttu-id="4a7b5-844">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-844">The following example:</span></span>

* <span data-ttu-id="4a7b5-845">Extrahuje hodnotu řetězce z `NumberKey`konfigurace pomocí klíče .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-845">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="4a7b5-846">Pokud `NumberKey` není nalezen v konfiguračních `99` klíčích, použije se výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-846">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="4a7b5-847">Zadá hodnotu `int`jako .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-847">Types the value as an `int`.</span></span>
* <span data-ttu-id="4a7b5-848">Uloží hodnotu `NumberConfig` ve vlastnosti pro použití na stránce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-848">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="4a7b5-849">GetSection, GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="4a7b5-849">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="4a7b5-850">Příklady, které následují, zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-850">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="4a7b5-851">Čtyři klíče se nacházejí ve dvou částech, z nichž jedna obsahuje dvojici podsekcí:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-851">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="4a7b5-852">Při čtení souboru do konfigurace jsou vytvořeny následující jedinečné hierarchické klíče pro uložení hodnot konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-852">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="4a7b5-853">oddíl0:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-853">section0:key0</span></span>
* <span data-ttu-id="4a7b5-854">oddíl0:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-854">section0:key1</span></span>
* <span data-ttu-id="4a7b5-855">oddíl1:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-855">section1:key0</span></span>
* <span data-ttu-id="4a7b5-856">oddíl1:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-856">section1:key1</span></span>
* <span data-ttu-id="4a7b5-857">section2:podsekce0:key0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-857">section2:subsection0:key0</span></span>
* <span data-ttu-id="4a7b5-858">section2:podsekce0:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-858">section2:subsection0:key1</span></span>
* <span data-ttu-id="4a7b5-859">section2:podsekce1:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-859">section2:subsection1:key0</span></span>
* <span data-ttu-id="4a7b5-860">section2:podsekce1:klíč1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-860">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="4a7b5-861">Getsection</span><span class="sxs-lookup"><span data-stu-id="4a7b5-861">GetSection</span></span>

<span data-ttu-id="4a7b5-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje podsekci konfigurace se zadaným klíčem podsekce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="4a7b5-863">Chcete-li <xref:Microsoft.Extensions.Configuration.IConfigurationSection> vrátit obsahující pouze dvojice klíč `section1`hodnota `GetSection` v aplikaci , volání a zadání názvu oddílu:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-863">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="4a7b5-864">Nemá `configSection` hodnotu, pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-864">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="4a7b5-865">Podobně chcete-li získat hodnoty `section2:subsection0`pro `GetSection` klíče v , volání a zadání cesty oddílu:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-865">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="4a7b5-866">`GetSection`nikdy `null`nevrátí .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-866">`GetSection` never returns `null`.</span></span> <span data-ttu-id="4a7b5-867">Pokud odpovídající oddíl nebyl nalezen, `IConfigurationSection` je vrácena prázdná.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-867">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="4a7b5-868">Pokud `GetSection` vrátí odpovídající <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> oddíl, není naplněn.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-868">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="4a7b5-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> a jsou vráceny, pokud oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="4a7b5-870">GetChildren</span><span class="sxs-lookup"><span data-stu-id="4a7b5-870">GetChildren</span></span>

<span data-ttu-id="4a7b5-871">Volání [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) na `section2` `IEnumerable<IConfigurationSection>` získá, který zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-871">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="4a7b5-872">Existuje</span><span class="sxs-lookup"><span data-stu-id="4a7b5-872">Exists</span></span>

<span data-ttu-id="4a7b5-873">Pomocí [configurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) zjistěte, zda existuje oddíl konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="4a7b5-874">Vzhledem k `sectionExists` příkladu dat `false` je, `section2:subsection2` protože v konfiguračních datech není oddíl.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-874">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="4a7b5-875">Vazba na objektový graf</span><span class="sxs-lookup"><span data-stu-id="4a7b5-875">Bind to an object graph</span></span>

<span data-ttu-id="4a7b5-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopen spoje celého grafu objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="4a7b5-877">Stejně jako u vazby jednoduchý objekt, jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-877">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="4a7b5-878">Ukázka obsahuje `TvShow` model, jehož objektový graf obsahuje `Metadata` a `Actors` třídy *(Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="4a7b5-878">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="4a7b5-879">Ukázková aplikace obsahuje soubor *tvshow.xml* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-879">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="4a7b5-880">Konfigurace je vázána `TvShow` na celý `Bind` objekt grafu s metodou.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-880">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="4a7b5-881">Vázaná instance je přiřazena vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-881">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="4a7b5-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)váže a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="4a7b5-883">`Get<T>`je pohodlnější než `Bind`použití .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-883">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="4a7b5-884">Následující kód ukazuje, `Get<T>` jak používat s předchozím příkladem:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-884">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="4a7b5-885">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="4a7b5-885">Bind an array to a class</span></span>

<span data-ttu-id="4a7b5-886">*Ukázková aplikace ukazuje koncepty vysvětlené v této části.*</span><span class="sxs-lookup"><span data-stu-id="4a7b5-886">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="4a7b5-887">Podporuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> vazby pole na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-887">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4a7b5-888">Jakýkoli formát pole, který zveřejňuje`:0:`číselný &hellip; `:{n}:`segment klíče ( , `:1:`, ) je schopen matice vazby na pole třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-888">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="4a7b5-889">Vazba je poskytována úmluvou.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-889">Binding is provided by convention.</span></span> <span data-ttu-id="4a7b5-890">Vlastní zprostředkovatelé konfigurace nejsou nutné k implementaci vazby pole.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-890">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="4a7b5-891">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-891">**In-memory array processing**</span></span>

<span data-ttu-id="4a7b5-892">Zvažte konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-892">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="4a7b5-893">Klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-893">Key</span></span>             | <span data-ttu-id="4a7b5-894">Hodnota</span><span class="sxs-lookup"><span data-stu-id="4a7b5-894">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="4a7b5-895">pole:položky:0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-895">array:entries:0</span></span> | <span data-ttu-id="4a7b5-896">hodnota0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-896">value0</span></span> |
| <span data-ttu-id="4a7b5-897">pole:položky:1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-897">array:entries:1</span></span> | <span data-ttu-id="4a7b5-898">hodnota1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-898">value1</span></span> |
| <span data-ttu-id="4a7b5-899">pole:položky:2</span><span class="sxs-lookup"><span data-stu-id="4a7b5-899">array:entries:2</span></span> | <span data-ttu-id="4a7b5-900">hodnota2</span><span class="sxs-lookup"><span data-stu-id="4a7b5-900">value2</span></span> |
| <span data-ttu-id="4a7b5-901">pole:položky:4</span><span class="sxs-lookup"><span data-stu-id="4a7b5-901">array:entries:4</span></span> | <span data-ttu-id="4a7b5-902">hodnota4</span><span class="sxs-lookup"><span data-stu-id="4a7b5-902">value4</span></span> |
| <span data-ttu-id="4a7b5-903">pole:položky:5</span><span class="sxs-lookup"><span data-stu-id="4a7b5-903">array:entries:5</span></span> | <span data-ttu-id="4a7b5-904">hodnota5</span><span class="sxs-lookup"><span data-stu-id="4a7b5-904">value5</span></span> |

<span data-ttu-id="4a7b5-905">Tyto klíče a hodnoty se načítají v ukázkové aplikaci pomocí zprostředkovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-905">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="4a7b5-906">Pole přeskočí hodnotu &num;indexu 3.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-906">The array skips a value for index &num;3.</span></span> <span data-ttu-id="4a7b5-907">Konfigurační pořadač není schopen vazby nula hodnoty nebo vytváření nulových položek v vázané objekty, což se stane jasné v okamžiku, kdy je prokázánvýsledek vazby tohoto pole na objekt.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-907">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="4a7b5-908">V ukázkové aplikaci je k dispozici třída POCO pro uložení vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-908">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="4a7b5-909">Konfigurační data jsou vázána na objekt:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-909">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="4a7b5-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)syntaxe lze také použít, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="4a7b5-911">Vázaný objekt, instance `ArrayExample`aplikace , přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-911">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="4a7b5-912">`ArrayExample.Entries`Index</span><span class="sxs-lookup"><span data-stu-id="4a7b5-912">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="4a7b5-913">`ArrayExample.Entries`Hodnotu</span><span class="sxs-lookup"><span data-stu-id="4a7b5-913">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="4a7b5-914">0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-914">0</span></span>                            | <span data-ttu-id="4a7b5-915">hodnota0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-915">value0</span></span>                       |
| <span data-ttu-id="4a7b5-916">1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-916">1</span></span>                            | <span data-ttu-id="4a7b5-917">hodnota1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-917">value1</span></span>                       |
| <span data-ttu-id="4a7b5-918">2</span><span class="sxs-lookup"><span data-stu-id="4a7b5-918">2</span></span>                            | <span data-ttu-id="4a7b5-919">hodnota2</span><span class="sxs-lookup"><span data-stu-id="4a7b5-919">value2</span></span>                       |
| <span data-ttu-id="4a7b5-920">3</span><span class="sxs-lookup"><span data-stu-id="4a7b5-920">3</span></span>                            | <span data-ttu-id="4a7b5-921">hodnota4</span><span class="sxs-lookup"><span data-stu-id="4a7b5-921">value4</span></span>                       |
| <span data-ttu-id="4a7b5-922">4</span><span class="sxs-lookup"><span data-stu-id="4a7b5-922">4</span></span>                            | <span data-ttu-id="4a7b5-923">hodnota5</span><span class="sxs-lookup"><span data-stu-id="4a7b5-923">value5</span></span>                       |

<span data-ttu-id="4a7b5-924">Index &num;3 v vázaném objektu `array:4` obsahuje konfigurační `value4`data pro konfigurační klíč a jeho hodnotu .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-924">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="4a7b5-925">Pokud jsou konfigurační data obsahující pole vázána, indexy pole v konfiguračních klíčích se při vytváření objektu používají pouze k iteraci konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-925">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="4a7b5-926">Hodnotu null nelze zachovat v konfiguračních datech a položka s hodnotou null není vytvořena ve vázaném objektu, když pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-926">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="4a7b5-927">Chybějící položka konfigurace &num;pro index 3 může `ArrayExample` být dodána před vazbou na instanci libovolným poskytovatelem konfigurace, který vytvoří správný pár klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-927">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="4a7b5-928">Pokud ukázka zahrnovala dalšího zprostředkovatele konfigurace JSON s `ArrayExample.Entries` chybějícím párem klíč-hodnota, odpovídá kompletnímu konfiguračnímu poli:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-928">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="4a7b5-929">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-929">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="4a7b5-930">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-930">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="4a7b5-931">Dvojice klíč-hodnota zobrazená v tabulce je načtena do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-931">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="4a7b5-932">Klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-932">Key</span></span>             | <span data-ttu-id="4a7b5-933">Hodnota</span><span class="sxs-lookup"><span data-stu-id="4a7b5-933">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="4a7b5-934">pole:položky:3</span><span class="sxs-lookup"><span data-stu-id="4a7b5-934">array:entries:3</span></span> | <span data-ttu-id="4a7b5-935">hodnota3</span><span class="sxs-lookup"><span data-stu-id="4a7b5-935">value3</span></span> |

<span data-ttu-id="4a7b5-936">Pokud `ArrayExample` je instance třídy vázána poté, co zprostředkovatel &num;konfigurace `ArrayExample.Entries` JSON zahrnuje položku pro index 3, pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-936">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="4a7b5-937">`ArrayExample.Entries`Index</span><span class="sxs-lookup"><span data-stu-id="4a7b5-937">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="4a7b5-938">`ArrayExample.Entries`Hodnotu</span><span class="sxs-lookup"><span data-stu-id="4a7b5-938">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="4a7b5-939">0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-939">0</span></span>                            | <span data-ttu-id="4a7b5-940">hodnota0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-940">value0</span></span>                       |
| <span data-ttu-id="4a7b5-941">1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-941">1</span></span>                            | <span data-ttu-id="4a7b5-942">hodnota1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-942">value1</span></span>                       |
| <span data-ttu-id="4a7b5-943">2</span><span class="sxs-lookup"><span data-stu-id="4a7b5-943">2</span></span>                            | <span data-ttu-id="4a7b5-944">hodnota2</span><span class="sxs-lookup"><span data-stu-id="4a7b5-944">value2</span></span>                       |
| <span data-ttu-id="4a7b5-945">3</span><span class="sxs-lookup"><span data-stu-id="4a7b5-945">3</span></span>                            | <span data-ttu-id="4a7b5-946">hodnota3</span><span class="sxs-lookup"><span data-stu-id="4a7b5-946">value3</span></span>                       |
| <span data-ttu-id="4a7b5-947">4</span><span class="sxs-lookup"><span data-stu-id="4a7b5-947">4</span></span>                            | <span data-ttu-id="4a7b5-948">hodnota4</span><span class="sxs-lookup"><span data-stu-id="4a7b5-948">value4</span></span>                       |
| <span data-ttu-id="4a7b5-949">5</span><span class="sxs-lookup"><span data-stu-id="4a7b5-949">5</span></span>                            | <span data-ttu-id="4a7b5-950">hodnota5</span><span class="sxs-lookup"><span data-stu-id="4a7b5-950">value5</span></span>                       |

<span data-ttu-id="4a7b5-951">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="4a7b5-951">**JSON array processing**</span></span>

<span data-ttu-id="4a7b5-952">Pokud soubor JSON obsahuje pole, konfigurační klíče jsou vytvořeny pro prvky pole s indexem oddílu založeném na nule.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-952">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="4a7b5-953">V následujícím konfiguračním souboru `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-953">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="4a7b5-954">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-954">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="4a7b5-955">Klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-955">Key</span></span>                     | <span data-ttu-id="4a7b5-956">Hodnota</span><span class="sxs-lookup"><span data-stu-id="4a7b5-956">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="4a7b5-957">json_array:klíč</span><span class="sxs-lookup"><span data-stu-id="4a7b5-957">json_array:key</span></span>          | <span data-ttu-id="4a7b5-958">valueA</span><span class="sxs-lookup"><span data-stu-id="4a7b5-958">valueA</span></span> |
| <span data-ttu-id="4a7b5-959">json_array:podsekce:0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-959">json_array:subsection:0</span></span> | <span data-ttu-id="4a7b5-960">valueB</span><span class="sxs-lookup"><span data-stu-id="4a7b5-960">valueB</span></span> |
| <span data-ttu-id="4a7b5-961">json_array:podsekce:1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-961">json_array:subsection:1</span></span> | <span data-ttu-id="4a7b5-962">valueC</span><span class="sxs-lookup"><span data-stu-id="4a7b5-962">valueC</span></span> |
| <span data-ttu-id="4a7b5-963">json_array:podsekce:2</span><span class="sxs-lookup"><span data-stu-id="4a7b5-963">json_array:subsection:2</span></span> | <span data-ttu-id="4a7b5-964">Hodnotami</span><span class="sxs-lookup"><span data-stu-id="4a7b5-964">valueD</span></span> |

<span data-ttu-id="4a7b5-965">V ukázkové aplikaci je k dispozici následující třída POCO, která sváže dvojice hodnot konfiguračního klíče a hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-965">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="4a7b5-966">Po vazbě podrží `JsonArrayExample.Key` hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-966">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="4a7b5-967">Hodnoty podsekce jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-967">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="4a7b5-968">`JsonArrayExample.Subsection`Index</span><span class="sxs-lookup"><span data-stu-id="4a7b5-968">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="4a7b5-969">`JsonArrayExample.Subsection`Hodnotu</span><span class="sxs-lookup"><span data-stu-id="4a7b5-969">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="4a7b5-970">0</span><span class="sxs-lookup"><span data-stu-id="4a7b5-970">0</span></span>                                   | <span data-ttu-id="4a7b5-971">valueB</span><span class="sxs-lookup"><span data-stu-id="4a7b5-971">valueB</span></span>                              |
| <span data-ttu-id="4a7b5-972">1</span><span class="sxs-lookup"><span data-stu-id="4a7b5-972">1</span></span>                                   | <span data-ttu-id="4a7b5-973">valueC</span><span class="sxs-lookup"><span data-stu-id="4a7b5-973">valueC</span></span>                              |
| <span data-ttu-id="4a7b5-974">2</span><span class="sxs-lookup"><span data-stu-id="4a7b5-974">2</span></span>                                   | <span data-ttu-id="4a7b5-975">Hodnotami</span><span class="sxs-lookup"><span data-stu-id="4a7b5-975">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="4a7b5-976">Vlastní zprostředkovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="4a7b5-976">Custom configuration provider</span></span>

<span data-ttu-id="4a7b5-977">Ukázková aplikace ukazuje, jak vytvořit základní ho poskytovatele konfigurace, který čte dvojice hodnot klíče konfigurace z databáze pomocí [entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-977">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="4a7b5-978">Poskytovatel má následující charakteristiky:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-978">The provider has the following characteristics:</span></span>

* <span data-ttu-id="4a7b5-979">Ef v paměti databáze se používá pro účely demonstrace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-979">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="4a7b5-980">Chcete-li použít databázi, která vyžaduje `ConfigurationBuilder` připojovací řetězec, implementujte sekundární pro zadání připojovacího řetězce od jiného zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-980">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="4a7b5-981">Zprostředkovatel přečte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-981">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="4a7b5-982">Zprostředkovatel není dotaz databáze na základě pro jeden klíč.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-982">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="4a7b5-983">Opětovné načtení při změně není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-983">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="4a7b5-984">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot v databázi.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-984">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="4a7b5-985">*Modely/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-985">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="4a7b5-986">Přidejte `EFConfigurationContext` a uložte a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-986">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="4a7b5-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="4a7b5-988">Vytvořte třídu, <xref:Microsoft.Extensions.Configuration.IConfigurationSource>která implementuje .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-988">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="4a7b5-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="4a7b5-990">Vytvořte vlastního zprostředkovatele konfigurace <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>děděním z aplikace .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-990">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="4a7b5-991">Zprostředkovatel konfigurace inicializuje databázi, když je prázdná.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-991">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="4a7b5-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="4a7b5-993">Metoda `AddEFConfiguration` rozšíření umožňuje přidání zdroje `ConfigurationBuilder`konfigurace do .</span><span class="sxs-lookup"><span data-stu-id="4a7b5-993">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="4a7b5-994">*Rozšíření/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-994">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="4a7b5-995">Následující kód ukazuje, jak `EFConfigurationProvider` používat vlastní v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-995">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="4a7b5-996">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="4a7b5-996">Access configuration during startup</span></span>

<span data-ttu-id="4a7b5-997">Vstříkněte `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v aplikaci `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-997">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4a7b5-998">Chcete-li `Startup.Configure`získat přístup `IConfiguration` ke konfiguraci v aplikaci , buď vložte přímo do metody nebo použijte instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-998">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="4a7b5-999">Příklad přístupu ke konfiguraci pomocí metod pohodlí při spuštění naleznete v [tématu Spuštění aplikace: Metody pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="4a7b5-999">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="4a7b5-1000">Přístup ke konfiguraci na stránce Razor Pages nebo mvc zobrazení</span><span class="sxs-lookup"><span data-stu-id="4a7b5-1000">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="4a7b5-1001">Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo v zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([C# reference: using directive)](/dotnet/csharp/language-reference/keywords/using-directive)pro [obor názvů Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> je do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-1001">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="4a7b5-1002">Na stránce Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-1002">In a Razor Pages page:</span></span>

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

<span data-ttu-id="4a7b5-1003">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="4a7b5-1003">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="4a7b5-1004">Přidání konfigurace z externí sestavy</span><span class="sxs-lookup"><span data-stu-id="4a7b5-1004">Add configuration from an external assembly</span></span>

<span data-ttu-id="4a7b5-1005">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidávání vylepšení do aplikace při spuštění z externího `Startup` sestavení mimo třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-1005">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4a7b5-1006">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4a7b5-1006">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a7b5-1007">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4a7b5-1007">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

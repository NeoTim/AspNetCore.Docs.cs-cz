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
ms.openlocfilehash: ead9cb8c852ac768c8fe1f9066eb73ac1203c4ce
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454698"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="291c1-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="291c1-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="291c1-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="291c1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="291c1-105">Konfigurace v ASP.NET Core provádí použití jednoho nebo více [poskytovatelů konfigurace](#cp).</span><span class="sxs-lookup"><span data-stu-id="291c1-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="291c1-106">Poskytovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="291c1-107">Soubory nastavení, například *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="291c1-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="291c1-108">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-108">Environment variables</span></span>
* <span data-ttu-id="291c1-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="291c1-109">Azure Key Vault</span></span>
* <span data-ttu-id="291c1-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="291c1-110">Azure App Configuration</span></span>
* <span data-ttu-id="291c1-111">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-111">Command-line arguments</span></span>
* <span data-ttu-id="291c1-112">Vlastní zprostředkovatelé, nainstalované nebo vytvořené</span><span class="sxs-lookup"><span data-stu-id="291c1-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="291c1-113">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="291c1-113">Directory files</span></span>
* <span data-ttu-id="291c1-114">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="291c1-114">In-memory .NET objects</span></span>

<span data-ttu-id="291c1-115">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="291c1-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="291c1-116">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-116">Default configuration</span></span>

<span data-ttu-id="291c1-117">ASP.NET Core webové aplikace vytvořené pomocí [dotnet New](/dotnet/core/tools/dotnet-new) nebo sady Visual Studio generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="291c1-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="291c1-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="291c1-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="291c1-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá existující `IConfiguration` jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="291c1-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="291c1-120">Ve výchozím případu konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj konfigurace _aplikace_ .</span><span class="sxs-lookup"><span data-stu-id="291c1-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="291c1-121">[appSettings. JSON](#appsettingsjson) s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="291c1-122">*appSettings.* `Environment` *. JSON* s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="291c1-123">Například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="291c1-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="291c1-124">[Tajné kódy aplikace](xref:security/app-secrets) v případě, že aplikace běží v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="291c1-125">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="291c1-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="291c1-126">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line).</span><span class="sxs-lookup"><span data-stu-id="291c1-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="291c1-127">Poskytovatelé konfigurace, kteří jsou přidáni později, přepíší předchozí nastavení klíče.</span><span class="sxs-lookup"><span data-stu-id="291c1-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="291c1-128">Například pokud `MyKey` je nastavena v souboru *appSettings. JSON* i v prostředí, je použita hodnota prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="291c1-129">Pomocí výchozích zprostředkovatelů konfigurace přepíše [Poskytovatel konfigurace příkazového řádku](#command-line-configuration-provider) všechny ostatní poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="291c1-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="291c1-130">Další informace o najdete v `CreateDefaultBuilder` tématu [výchozí nastavení tvůrce](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="291c1-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="291c1-131">Následující kód zobrazuje povolené poskytovatele konfigurace v pořadí, v jakém byly přidány:</span><span class="sxs-lookup"><span data-stu-id="291c1-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="291c1-132">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="291c1-132">appsettings.json</span></span>

<span data-ttu-id="291c1-133">Vezměte v úvahu následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="291c1-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="291c1-134">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-135">Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načtení konfigurace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="291c1-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="291c1-136">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="291c1-136">*appsettings.json*</span></span>
1. <span data-ttu-id="291c1-137">*appSettings.* `Environment` *. JSON* : například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="291c1-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="291c1-138">Verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="291c1-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="291c1-139">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="291c1-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="291c1-140">*appSettings*. `Environment` .. hodnoty *JSON* přepíší klíče v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="291c1-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="291c1-141">Ve výchozím nastavení by například:</span><span class="sxs-lookup"><span data-stu-id="291c1-141">For example, by default:</span></span>

* <span data-ttu-id="291c1-142">Ve vývoji, *appSettings*. ***Vývoj***. konfigurace *JSON* Přepisuje hodnoty nalezené v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="291c1-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="291c1-143">V produkčním prostředí, *appSettings*. ***Provozní***prostředí. konfigurace *JSON* Přepisuje hodnoty nalezené v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="291c1-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="291c1-144">Například při nasazení aplikace do Azure.</span><span class="sxs-lookup"><span data-stu-id="291c1-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="291c1-145">Vytvoření vazby hierarchických konfiguračních dat pomocí vzoru možností</span><span class="sxs-lookup"><span data-stu-id="291c1-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="291c1-146">Pomocí [výchozí](#default) konfigurace, *appSettings. JSON* a *appSettings.* `Environment` soubory *. JSON* jsou povolené pomocí [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="291c1-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="291c1-147">Změny provedené v souboru *appSettings. JSON* a *appSettings.* `Environment` soubor *. JSON* ***po*** spuštění aplikace si přečte [Poskytovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="291c1-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="291c1-148">Informace o přidání dalších konfiguračních souborů JSON najdete v části [Poskytovatel konfigurace JSON](#jcp) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="291c1-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="291c1-149">Správce zabezpečení a tajného klíče</span><span class="sxs-lookup"><span data-stu-id="291c1-149">Security and secret manager</span></span>

<span data-ttu-id="291c1-150">Pokyny pro konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="291c1-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="291c1-151">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="291c1-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="291c1-152">[Správce tajného klíče](xref:security/app-secrets) se dá použít k ukládání tajných kódů do vývoje.</span><span class="sxs-lookup"><span data-stu-id="291c1-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="291c1-153">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="291c1-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="291c1-154">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="291c1-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="291c1-155">Ve [výchozím nastavení](#default)přečte [správce tajných klíčů](xref:security/app-secrets) nastavení konfigurace po souboru *appSettings. JSON* a *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="291c1-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="291c1-156">Další informace o ukládání hesel nebo jiných citlivých dat:</span><span class="sxs-lookup"><span data-stu-id="291c1-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="291c1-157"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="291c1-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="291c1-158">Správce tajného kódu používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="291c1-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="291c1-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="291c1-160">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="291c1-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="291c1-161">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-161">Environment variables</span></span>

<span data-ttu-id="291c1-162">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z proměnných prostředí klíč-hodnota po čtení *appSettings. JSON*, *appSettings.* `Environment` *. JSON*a [správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="291c1-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="291c1-163">Proto hodnoty klíčů načtené z prostředí přepisují hodnoty načtené z hodnot *appSettings. JSON*, *appSettings.* `Environment` *. JSON*a správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="291c1-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="291c1-164">Následující `set` příkazy:</span><span class="sxs-lookup"><span data-stu-id="291c1-164">The following `set` commands:</span></span>

* <span data-ttu-id="291c1-165">Nastavte klíče prostředí a hodnoty v [předchozím příkladu](#appsettingsjson) ve Windows.</span><span class="sxs-lookup"><span data-stu-id="291c1-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="291c1-166">Při použití [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)otestujte nastavení.</span><span class="sxs-lookup"><span data-stu-id="291c1-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="291c1-167">`dotnet run`Příkaz musí být spuštěn v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="291c1-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="291c1-168">Předchozí nastavení prostředí:</span><span class="sxs-lookup"><span data-stu-id="291c1-168">The preceding environment settings:</span></span>

* <span data-ttu-id="291c1-169">Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.</span><span class="sxs-lookup"><span data-stu-id="291c1-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="291c1-170">Nebudou přečteny pomocí prohlížečů spuštěných v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="291c1-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="291c1-171">K nastavení klíčů a hodnot prostředí ve Windows se dají použít následující příkazy [setx](/windows-server/administration/windows-commands/setx) .</span><span class="sxs-lookup"><span data-stu-id="291c1-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="291c1-172">Na rozdíl `set` od `setx` platí, že nastavení jsou trvalá.</span><span class="sxs-lookup"><span data-stu-id="291c1-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="291c1-173">`/M`nastaví proměnnou v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="291c1-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="291c1-174">Pokud se `/M` přepínač nepoužívá, je nastavena proměnná prostředí uživatele.</span><span class="sxs-lookup"><span data-stu-id="291c1-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="291c1-175">Chcete-li otestovat předchozí příkazy, přepište *appSettings. JSON* a *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="291c1-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="291c1-176">Se sadou Visual Studio: Ukončete a restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="291c1-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="291c1-177">Pomocí rozhraní příkazového řádku: spusťte nové příkazové okno a zadejte `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="291c1-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="291c1-178">Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem pro určení předpony pro proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="291c1-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="291c1-179">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="291c1-179">In the preceding code:</span></span>

* <span data-ttu-id="291c1-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="291c1-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="291c1-181">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="291c1-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="291c1-182">Proměnné prostředí nastavené s `MyCustomPrefix_` předponou přepíšou [výchozí poskytovatele konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="291c1-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="291c1-183">To zahrnuje proměnné prostředí bez předpony.</span><span class="sxs-lookup"><span data-stu-id="291c1-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="291c1-184">Pokud jsou načteny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="291c1-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="291c1-185">Vlastní předponu otestujete následujícími příkazy:</span><span class="sxs-lookup"><span data-stu-id="291c1-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="291c1-186">[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty příkazového řádku s předponou `DOTNET_` a `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="291c1-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="291c1-187">`DOTNET_` `ASPNETCORE_` Předpony a jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele.</span><span class="sxs-lookup"><span data-stu-id="291c1-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="291c1-188">Další informace o konfiguraci hostitele a aplikace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="291c1-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="291c1-189">V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="291c1-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="291c1-190">Azure App Service nastavení aplikace:</span><span class="sxs-lookup"><span data-stu-id="291c1-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="291c1-191">Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.</span><span class="sxs-lookup"><span data-stu-id="291c1-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="291c1-192">Vystaveno jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-192">Exposed as environment variables.</span></span>

<span data-ttu-id="291c1-193">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="291c1-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="291c1-194">Informace o připojovacích řetězcích Azure Database najdete v tématu [předpony připojovacího řetězce](#constr) .</span><span class="sxs-lookup"><span data-stu-id="291c1-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="291c1-195">Příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="291c1-195">Command-line</span></span>

<span data-ttu-id="291c1-196">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načte konfiguraci z dvojice klíč-hodnota argumentu klíč-hodnota po následujících zdrojích konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-196">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="291c1-197">*appSettings. JSON* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="291c1-197">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="291c1-198">[Tajné kódy aplikací (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-198">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="291c1-199">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-199">Environment variables.</span></span>

<span data-ttu-id="291c1-200">Ve [výchozím nastavení](#default)jsou konfigurační hodnoty nastavené pro přepisy hodnot konfigurace na příkazovém řádku nastavené se všemi ostatními zprostředkovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-200">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="291c1-201">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-201">Command-line arguments</span></span>

<span data-ttu-id="291c1-202">Následující příkaz nastaví klíče a hodnoty pomocí `=` :</span><span class="sxs-lookup"><span data-stu-id="291c1-202">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="291c1-203">Následující příkaz nastaví klíče a hodnoty pomocí `/` :</span><span class="sxs-lookup"><span data-stu-id="291c1-203">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="291c1-204">Následující příkaz nastaví klíče a hodnoty pomocí `--` :</span><span class="sxs-lookup"><span data-stu-id="291c1-204">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="291c1-205">Hodnota klíče:</span><span class="sxs-lookup"><span data-stu-id="291c1-205">The key value:</span></span>

* <span data-ttu-id="291c1-206">Musí následovat po `=` , nebo klíč musí mít předponu `--` nebo, `/` Pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="291c1-206">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="291c1-207">Není vyžadováno `=` , pokud je použit.</span><span class="sxs-lookup"><span data-stu-id="291c1-207">Isn't required if `=` is used.</span></span> <span data-ttu-id="291c1-208">Například, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="291c1-208">For example, `MySetting=`.</span></span>

<span data-ttu-id="291c1-209">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které `=` se používají s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="291c1-209">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="291c1-210">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="291c1-210">Switch mappings</span></span>

<span data-ttu-id="291c1-211">Mapování přepínačů povolují logiku nahrazení názvu **klíče** .</span><span class="sxs-lookup"><span data-stu-id="291c1-211">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="291c1-212">Poskytněte slovník pro nahrazení přepínačů v <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodě.</span><span class="sxs-lookup"><span data-stu-id="291c1-212">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="291c1-213">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="291c1-213">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="291c1-214">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota Dictionary zpátky, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-214">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="291c1-215">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="291c1-215">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="291c1-216">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="291c1-216">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="291c1-217">Přepínače musí začínat na `-` nebo `--` .</span><span class="sxs-lookup"><span data-stu-id="291c1-217">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="291c1-218">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="291c1-218">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="291c1-219">Chcete-li použít slovník mapování přepínačů, předejte jej do volání `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="291c1-219">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="291c1-220">Následující kód ukazuje klíčové hodnoty pro nahrazené klíče:</span><span class="sxs-lookup"><span data-stu-id="291c1-220">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-221">Spusťte následující příkaz pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="291c1-221">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="291c1-222">Poznámka: v současné době `=` nelze použít k nastavení hodnot nahrazení klíče jedinou pomlčkou `-` .</span><span class="sxs-lookup"><span data-stu-id="291c1-222">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="291c1-223">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="291c1-223">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="291c1-224">Následující příkaz funguje pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="291c1-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="291c1-225">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="291c1-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="291c1-226">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="291c1-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="291c1-227">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty i slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="291c1-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="291c1-228">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="291c1-228">Hierarchical configuration data</span></span>

<span data-ttu-id="291c1-229">Rozhraní API pro konfiguraci čte hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="291c1-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="291c1-230">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="291c1-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="291c1-231">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-232">Upřednostňovaným způsobem, jak číst hierarchická konfigurační data, je použít vzor možností.</span><span class="sxs-lookup"><span data-stu-id="291c1-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="291c1-233">Další informace najdete v tématu [vázání hierarchických konfiguračních dat](#optpat) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="291c1-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="291c1-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="291c1-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="291c1-235">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="291c1-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="291c1-236">Konfigurační klíče a hodnoty</span><span class="sxs-lookup"><span data-stu-id="291c1-236">Configuration keys and values</span></span>

<span data-ttu-id="291c1-237">Konfigurační klíče:</span><span class="sxs-lookup"><span data-stu-id="291c1-237">Configuration keys:</span></span>

* <span data-ttu-id="291c1-238">Rozlišují se malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="291c1-238">Are case-insensitive.</span></span> <span data-ttu-id="291c1-239">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="291c1-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="291c1-240">Pokud je klíč a hodnota nastavená ve více poskytovatelích konfigurace, použije se hodnota z posledního přidaného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="291c1-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="291c1-241">Další informace najdete v tématu [výchozí konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="291c1-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="291c1-242">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="291c1-242">Hierarchical keys</span></span>
  * <span data-ttu-id="291c1-243">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="291c1-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="291c1-244">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="291c1-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="291c1-245">Dvojité podtržítko, `__` , je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:` .</span><span class="sxs-lookup"><span data-stu-id="291c1-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="291c1-246">V Azure Key Vault hierarchické klíče používají `--` jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="291c1-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="291c1-247">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) se `--` `:` při načtení tajných klíčů do konfigurace aplikace automaticky nahradí.</span><span class="sxs-lookup"><span data-stu-id="291c1-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="291c1-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="291c1-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="291c1-249">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#boa) .</span><span class="sxs-lookup"><span data-stu-id="291c1-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="291c1-250">Konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="291c1-250">Configuration values:</span></span>

* <span data-ttu-id="291c1-251">Jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="291c1-251">Are strings.</span></span>
* <span data-ttu-id="291c1-252">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="291c1-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="291c1-253">Poskytovatelé konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-253">Configuration providers</span></span>

<span data-ttu-id="291c1-254">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="291c1-255">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="291c1-255">Provider</span></span> | <span data-ttu-id="291c1-256">Poskytuje konfiguraci z</span><span class="sxs-lookup"><span data-stu-id="291c1-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="291c1-257">Poskytovatel konfigurace Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="291c1-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="291c1-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="291c1-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="291c1-259">Poskytovatel konfigurace Azure App</span><span class="sxs-lookup"><span data-stu-id="291c1-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="291c1-260">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="291c1-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="291c1-261">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="291c1-262">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-262">Command-line parameters</span></span> |
| [<span data-ttu-id="291c1-263">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="291c1-264">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="291c1-264">Custom source</span></span> |
| [<span data-ttu-id="291c1-265">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="291c1-266">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-266">Environment variables</span></span> |
| [<span data-ttu-id="291c1-267">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="291c1-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="291c1-268">Soubory INI, JSON a XML</span><span class="sxs-lookup"><span data-stu-id="291c1-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="291c1-269">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="291c1-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="291c1-270">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="291c1-270">Directory files</span></span> |
| [<span data-ttu-id="291c1-271">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="291c1-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="291c1-272">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="291c1-272">In-memory collections</span></span> |
| [<span data-ttu-id="291c1-273">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="291c1-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="291c1-274">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="291c1-274">File in the user profile directory</span></span> |

<span data-ttu-id="291c1-275">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou zadáni poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="291c1-276">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="291c1-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="291c1-277">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="291c1-277">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="291c1-278">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="291c1-278">*appsettings.json*</span></span>
1. <span data-ttu-id="291c1-279">*appSettings*. `Environment` .. formát *JSON*</span><span class="sxs-lookup"><span data-stu-id="291c1-279">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="291c1-280">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="291c1-280">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="291c1-281">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="291c1-281">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="291c1-282">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-282">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="291c1-283">Běžný postup je přidat poskytovatele konfigurace příkazového řádku na poslední v řadě poskytovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="291c1-283">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="291c1-284">Předchozí sekvence zprostředkovatelů se používá ve [výchozí konfiguraci](#default).</span><span class="sxs-lookup"><span data-stu-id="291c1-284">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="291c1-285">Předpony připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="291c1-285">Connection string prefixes</span></span>

<span data-ttu-id="291c1-286">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro proměnné prostředí pro čtyři připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="291c1-286">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="291c1-287">Tyto připojovací řetězce jsou součástí konfigurace připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-287">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="291c1-288">Proměnné prostředí s předponami, které jsou uvedené v tabulce, se načtou do aplikace s [výchozí konfigurací](#default) nebo když není dodána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="291c1-288">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="291c1-289">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="291c1-289">Connection string prefix</span></span> | <span data-ttu-id="291c1-290">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="291c1-290">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="291c1-291">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="291c1-291">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="291c1-292">MySQL</span><span class="sxs-lookup"><span data-stu-id="291c1-292">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="291c1-293">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="291c1-293">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="291c1-294">SQL Server</span><span class="sxs-lookup"><span data-stu-id="291c1-294">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="291c1-295">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="291c1-295">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="291c1-296">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="291c1-296">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="291c1-297">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="291c1-297">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="291c1-298">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-298">Environment variable key</span></span> | <span data-ttu-id="291c1-299">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="291c1-299">Converted configuration key</span></span> | <span data-ttu-id="291c1-300">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="291c1-300">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="291c1-301">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="291c1-301">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="291c1-302">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="291c1-302">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="291c1-303">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="291c1-303">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="291c1-304">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="291c1-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="291c1-305">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="291c1-305">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="291c1-306">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="291c1-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="291c1-307">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="291c1-307">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="291c1-308">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="291c1-308">JSON configuration provider</span></span>

<span data-ttu-id="291c1-309"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načte konfiguraci z párů klíč-hodnota souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="291c1-309">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="291c1-310">Přetížení můžou specifikovat:</span><span class="sxs-lookup"><span data-stu-id="291c1-310">Overloads can specify:</span></span>

* <span data-ttu-id="291c1-311">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="291c1-311">Whether the file is optional.</span></span>
* <span data-ttu-id="291c1-312">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-312">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="291c1-313">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="291c1-313">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="291c1-314">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="291c1-314">The preceding code:</span></span>

* <span data-ttu-id="291c1-315">Nakonfiguruje zprostředkovatele konfigurace JSON pro načtení souboru *MyConfig. JSON* s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="291c1-315">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="291c1-316">`optional: true`: Soubor je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="291c1-316">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="291c1-317">`reloadOnChange: true`: Soubor se znovu načte při uložení změn.</span><span class="sxs-lookup"><span data-stu-id="291c1-317">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="291c1-318">Přečte [výchozí poskytovatele konfigurace](#default) před souborem *MyConfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="291c1-318">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="291c1-319">Nastavení v přepsání souboru *MyConfig. JSON* ve výchozích poskytovatelích konfigurace, včetně [poskytovatele konfigurace proměnných prostředí](#evcp) a [poskytovatele konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="291c1-319">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="291c1-320">***Obvykle nechcete, aby vlastní*** soubor JSON přepsal hodnoty nastavené ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a v [poskytovateli konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="291c1-320">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="291c1-321">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-321">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="291c1-322">V předchozím kódu se jedná o nastavení v *MyConfig. JSON* a *MyConfig*. `Environment` . soubory *JSON* :</span><span class="sxs-lookup"><span data-stu-id="291c1-322">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="291c1-323">Přepsat nastavení v souboru *appSettings. JSON* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="291c1-323">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="291c1-324">Jsou přepsány nastavením ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a [poskytovatelem konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="291c1-324">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="291c1-325">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyConfig. JSON* :</span><span class="sxs-lookup"><span data-stu-id="291c1-325">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="291c1-326">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-326">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="291c1-327">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="291c1-327">File configuration provider</span></span>

<span data-ttu-id="291c1-328"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="291c1-328"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="291c1-329">Následující poskytovatelé konfigurace jsou odvozeni z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="291c1-329">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="291c1-330">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="291c1-330">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="291c1-331">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="291c1-331">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="291c1-332">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="291c1-332">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="291c1-333">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="291c1-333">INI configuration provider</span></span>

<span data-ttu-id="291c1-334"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="291c1-334">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="291c1-335">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="291c1-336">V předchozím kódu jsou nastavení v *souboru MyIniConfig. ini* a *MyIniConfig*. `Environment` . soubory *ini* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="291c1-336">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="291c1-337">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-337">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="291c1-338">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="291c1-338">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="291c1-339">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="291c1-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="291c1-340">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="291c1-341">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="291c1-341">XML configuration provider</span></span>

<span data-ttu-id="291c1-342"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="291c1-342">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="291c1-343">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-343">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="291c1-344">V předchozím kódu, nastavení v *souboru MyXMLFile. XML* a *MyXMLFile*. `Environment` . soubory *XML* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="291c1-344">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="291c1-345">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-345">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="291c1-346">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="291c1-346">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="291c1-347">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="291c1-347">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="291c1-348">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-348">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-349">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="291c1-349">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="291c1-350">Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:</span><span class="sxs-lookup"><span data-stu-id="291c1-350">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-351">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="291c1-351">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="291c1-352">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="291c1-352">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="291c1-353">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="291c1-353">key:attribute</span></span>
* <span data-ttu-id="291c1-354">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="291c1-354">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="291c1-355">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="291c1-355">Key-per-file configuration provider</span></span>

<span data-ttu-id="291c1-356"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-356">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="291c1-357">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-357">The key is the file name.</span></span> <span data-ttu-id="291c1-358">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-358">The value contains the file's contents.</span></span> <span data-ttu-id="291c1-359">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="291c1-359">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="291c1-360">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="291c1-360">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="291c1-361">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="291c1-361">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="291c1-362">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="291c1-362">Overloads permit specifying:</span></span>

* <span data-ttu-id="291c1-363">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="291c1-363">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="291c1-364">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="291c1-364">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="291c1-365">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="291c1-365">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="291c1-366">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="291c1-366">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="291c1-367">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="291c1-367">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="291c1-368">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="291c1-368">Memory configuration provider</span></span>

<span data-ttu-id="291c1-369"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-369">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="291c1-370">Následující kód přidá kolekci paměti do konfiguračního systému:</span><span class="sxs-lookup"><span data-stu-id="291c1-370">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="291c1-371">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-371">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-372">V předchozím kódu `config.AddInMemoryCollection(Dict)` se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="291c1-372">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="291c1-373">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="291c1-373">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="291c1-374">Viz [vazba pole](#boa) pro jiný příklad pomocí `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="291c1-374">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="291c1-375">GetValue</span><span class="sxs-lookup"><span data-stu-id="291c1-375">GetValue</span></span>

<span data-ttu-id="291c1-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:</span><span class="sxs-lookup"><span data-stu-id="291c1-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-377">Pokud v předchozím kódu `NumberKey` není v konfiguraci nalezeno, `99` je použita výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-377">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="291c1-378">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="291c1-378">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="291c1-379">V následujících příkladech zvažte následující soubor *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="291c1-379">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="291c1-380">Následující kód přidá *MySubsection. JSON* ke zprostředkovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-380">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="291c1-381">GetSection</span><span class="sxs-lookup"><span data-stu-id="291c1-381">GetSection</span></span>

<span data-ttu-id="291c1-382">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí dílčí část konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="291c1-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="291c1-383">Následující kód vrátí hodnoty pro `section1` :</span><span class="sxs-lookup"><span data-stu-id="291c1-383">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-384">Následující kód vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="291c1-384">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-385">`GetSection`nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="291c1-385">`GetSection` never returns `null`.</span></span> <span data-ttu-id="291c1-386">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="291c1-386">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="291c1-387">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-387">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="291c1-388"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="291c1-388">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="291c1-389">GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="291c1-389">GetChildren and Exists</span></span>

<span data-ttu-id="291c1-390">Následující kód volá [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="291c1-390">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-391">Předchozí kód volá [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pro ověření, zda existuje oddíl:</span><span class="sxs-lookup"><span data-stu-id="291c1-391">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="291c1-392">Vazba pole</span><span class="sxs-lookup"><span data-stu-id="291c1-392">Bind an array</span></span>

<span data-ttu-id="291c1-393">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="291c1-393">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="291c1-394">Libovolný formát pole, který zveřejňuje numerický klíčový segment, je schopný vytvořit vazbu pole k poli třídy [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="291c1-394">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="291c1-395">V [ukázkovém stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)zvažte *myArray. JSON* :</span><span class="sxs-lookup"><span data-stu-id="291c1-395">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="291c1-396">Následující kód přidá *myArray. JSON* ke zprostředkovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-396">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="291c1-397">Následující kód přečte konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="291c1-397">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-398">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="291c1-398">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="291c1-399">V předchozím výstupu má index 3 hodnotu odpovídající hodnotě `value40` `"4": "value40",` v *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="291c1-399">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="291c1-400">Vazby vázaného pole jsou souvislé a nejsou vázané na index konfiguračního klíče.</span><span class="sxs-lookup"><span data-stu-id="291c1-400">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="291c1-401">Pořadač konfigurace nemůže svázat hodnoty null ani vytvářet položky null ve vázaných objektech.</span><span class="sxs-lookup"><span data-stu-id="291c1-401">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="291c1-402">Následující kód načte `array:entries` konfiguraci pomocí <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="291c1-402">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="291c1-403">Následující kód přečte konfiguraci v `arrayDict` `Dictionary` a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="291c1-403">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-404">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="291c1-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="291c1-405">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="291c1-405">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="291c1-406">Když jsou data konfigurace obsahující pole svázané, používají se k iteraci konfiguračních dat při vytváření objektu pole indexy v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="291c1-406">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="291c1-407">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="291c1-407">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="291c1-408">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na instanci doplní `ArrayExample` jakýmkoli poskytovatelem konfigurace, který načte &num; dvojici klíč/hodnota indexu 3.</span><span class="sxs-lookup"><span data-stu-id="291c1-408">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="291c1-409">V ukázkovém stažení zvažte následující soubor *hodnota3. JSON* :</span><span class="sxs-lookup"><span data-stu-id="291c1-409">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="291c1-410">Následující kód obsahuje konfiguraci pro *hodnota3. JSON* a `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="291c1-410">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="291c1-411">Následující kód přečte předchozí konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="291c1-411">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-412">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="291c1-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="291c1-413">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="291c1-413">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="291c1-414">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-414">Custom configuration provider</span></span>

<span data-ttu-id="291c1-415">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="291c1-415">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="291c1-416">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="291c1-416">The provider has the following characteristics:</span></span>

* <span data-ttu-id="291c1-417">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="291c1-417">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="291c1-418">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-418">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="291c1-419">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="291c1-419">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="291c1-420">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="291c1-420">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="291c1-421">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-421">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="291c1-422">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="291c1-422">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="291c1-423">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-423">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="291c1-424">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="291c1-424">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="291c1-425">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="291c1-426">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="291c1-426">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="291c1-427">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="291c1-428">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="291c1-428">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="291c1-429">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="291c1-429">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="291c1-430">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="291c1-430">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="291c1-431">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="291c1-432">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="291c1-432">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="291c1-433">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-433">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="291c1-434">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-434">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="291c1-435">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="291c1-435">Access configuration in Startup</span></span>

<span data-ttu-id="291c1-436">Následující kód zobrazuje konfigurační data v `Startup` metodách:</span><span class="sxs-lookup"><span data-stu-id="291c1-436">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="291c1-437">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="291c1-437">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="291c1-438">Přístup ke konfiguraci na Razor stránkách</span><span class="sxs-lookup"><span data-stu-id="291c1-438">Access configuration in Razor Pages</span></span>

<span data-ttu-id="291c1-439">Následující kód zobrazuje konfigurační data na Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="291c1-439">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="291c1-440">V následujícím kódu `MyOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="291c1-440">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="291c1-441">Následující kód používá [`@inject`](xref:mvc/views/razor#inject) Razor direktivu k vyřešení a zobrazení hodnot možností:</span><span class="sxs-lookup"><span data-stu-id="291c1-441">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="291c1-442">Přístup ke konfiguraci v souboru zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="291c1-442">Access configuration in a MVC view file</span></span>

<span data-ttu-id="291c1-443">Následující kód zobrazuje konfigurační data v zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="291c1-443">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="291c1-444">Konfigurace možností s delegátem</span><span class="sxs-lookup"><span data-stu-id="291c1-444">Configure options with a delegate</span></span>

<span data-ttu-id="291c1-445">Možnosti nakonfigurované v delegátech přepíší hodnoty nastavené ve zprostředkovatelích konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-445">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="291c1-446">Konfigurace možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="291c1-446">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="291c1-447">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je služba přidána do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="291c1-447">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="291c1-448">Pomocí delegáta konfiguruje hodnoty pro `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="291c1-448">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="291c1-449">Následující kód zobrazí hodnoty možností:</span><span class="sxs-lookup"><span data-stu-id="291c1-449">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="291c1-450">V předchozím příkladu `Option1` jsou hodnoty a `Option2` zadány v souboru *appSettings. JSON* a následně přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="291c1-450">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="291c1-451">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="291c1-451">Host versus app configuration</span></span>

<span data-ttu-id="291c1-452">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="291c1-452">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="291c1-453">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="291c1-453">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="291c1-454">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="291c1-454">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="291c1-455">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="291c1-455">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="291c1-456">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="291c1-456">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="291c1-457">Výchozí konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="291c1-457">Default host configuration</span></span>

<span data-ttu-id="291c1-458">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="291c1-458">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="291c1-459">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="291c1-459">Host configuration is provided from:</span></span>
  * <span data-ttu-id="291c1-460">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-460">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="291c1-461">Předpona ( `DOTNET_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-461">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="291c1-462">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-462">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="291c1-463">Je navázána výchozí konfigurace webového hostitele ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="291c1-463">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="291c1-464">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-464">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="291c1-465">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="291c1-465">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="291c1-466">Přidejte middleware předávaných hlaviček, pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="291c1-466">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="291c1-467">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="291c1-467">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="291c1-468">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-468">Other configuration</span></span>

<span data-ttu-id="291c1-469">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="291c1-469">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="291c1-470">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="291c1-470">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="291c1-471">*Launch. JSON* / *launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané:</span><span class="sxs-lookup"><span data-stu-id="291c1-471">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="291c1-472">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="291c1-472">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="291c1-473">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="291c1-473">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="291c1-474">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="291c1-474">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="291c1-475">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="291c1-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="291c1-476">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="291c1-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="291c1-477"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="291c1-478">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="291c1-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="291c1-479">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="291c1-479">Additional resources</span></span>

* [<span data-ttu-id="291c1-480">Zdrojový kód konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="291c1-481">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="291c1-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="291c1-482">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="291c1-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="291c1-483">Azure Key Vault</span></span>
* <span data-ttu-id="291c1-484">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="291c1-484">Azure App Configuration</span></span>
* <span data-ttu-id="291c1-485">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-485">Command-line arguments</span></span>
* <span data-ttu-id="291c1-486">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="291c1-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="291c1-487">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="291c1-487">Directory files</span></span>
* <span data-ttu-id="291c1-488">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-488">Environment variables</span></span>
* <span data-ttu-id="291c1-489">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="291c1-489">In-memory .NET objects</span></span>
* <span data-ttu-id="291c1-490">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="291c1-490">Settings files</span></span>

<span data-ttu-id="291c1-491">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="291c1-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="291c1-492">Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="291c1-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="291c1-493">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="291c1-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="291c1-494">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="291c1-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="291c1-495">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="291c1-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="291c1-496">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="291c1-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="291c1-497">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="291c1-497">Host versus app configuration</span></span>

<span data-ttu-id="291c1-498">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="291c1-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="291c1-499">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="291c1-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="291c1-500">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="291c1-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="291c1-501">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="291c1-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="291c1-502">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="291c1-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="291c1-503">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-503">Other configuration</span></span>

<span data-ttu-id="291c1-504">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="291c1-504">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="291c1-505">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="291c1-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="291c1-506">*Launch. JSON* / *launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané:</span><span class="sxs-lookup"><span data-stu-id="291c1-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="291c1-507">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="291c1-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="291c1-508">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="291c1-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="291c1-509">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="291c1-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="291c1-510">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="291c1-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="291c1-511">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-511">Default configuration</span></span>

<span data-ttu-id="291c1-512">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="291c1-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="291c1-513">`CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="291c1-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="291c1-514">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="291c1-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="291c1-515">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="291c1-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="291c1-516">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="291c1-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="291c1-517">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="291c1-518">Předpona ( `ASPNETCORE_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="291c1-519">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="291c1-520">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="291c1-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="291c1-521">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-521">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="291c1-522">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="291c1-523">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v `Development` prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="291c1-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="291c1-524">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="291c1-525">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="291c1-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="291c1-526">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="291c1-526">Security</span></span>

<span data-ttu-id="291c1-527">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="291c1-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="291c1-528">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="291c1-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="291c1-529">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="291c1-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="291c1-530">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="291c1-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="291c1-531">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="291c1-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="291c1-532"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="291c1-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="291c1-533">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="291c1-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="291c1-534">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="291c1-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="291c1-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="291c1-536">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="291c1-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="291c1-537">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="291c1-537">Hierarchical configuration data</span></span>

<span data-ttu-id="291c1-538">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="291c1-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="291c1-539">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="291c1-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="291c1-540">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="291c1-541">Oddíly a klíče jsou shrnuty s použitím dvojtečky ( `:` ) pro zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="291c1-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="291c1-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-542">section0:key0</span></span>
* <span data-ttu-id="291c1-543">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-543">section0:key1</span></span>
* <span data-ttu-id="291c1-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-544">section1:key0</span></span>
* <span data-ttu-id="291c1-545">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-545">section1:key1</span></span>

<span data-ttu-id="291c1-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="291c1-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="291c1-547">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="291c1-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="291c1-548">Konvence</span><span class="sxs-lookup"><span data-stu-id="291c1-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="291c1-549">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="291c1-549">Sources and providers</span></span>

<span data-ttu-id="291c1-550">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="291c1-551">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="291c1-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="291c1-552">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="291c1-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="291c1-553"><xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="291c1-554"><xref:Microsoft.Extensions.Configuration.IConfiguration>lze vložit do Razor stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pro získání konfigurace pro třídu.</span><span class="sxs-lookup"><span data-stu-id="291c1-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="291c1-555">V následujících příkladech se `_config` pole používá pro přístup k hodnotám konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="291c1-556">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="291c1-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="291c1-557">Klíče</span><span class="sxs-lookup"><span data-stu-id="291c1-557">Keys</span></span>

<span data-ttu-id="291c1-558">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="291c1-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="291c1-559">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="291c1-559">Keys are case-insensitive.</span></span> <span data-ttu-id="291c1-560">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="291c1-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="291c1-561">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="291c1-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="291c1-562">Další informace o duplicitních klíčích JSON najdete v [tomto problému GitHubu](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="291c1-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="291c1-563">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="291c1-563">Hierarchical keys</span></span>
  * <span data-ttu-id="291c1-564">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="291c1-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="291c1-565">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="291c1-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="291c1-566">Dvojité podtržítko ( `__` ) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="291c1-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="291c1-567">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="291c1-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="291c1-568">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="291c1-569"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="291c1-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="291c1-570">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="291c1-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="291c1-571">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="291c1-571">Values</span></span>

<span data-ttu-id="291c1-572">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="291c1-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="291c1-573">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="291c1-573">Values are strings.</span></span>
* <span data-ttu-id="291c1-574">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="291c1-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="291c1-575">Poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="291c1-575">Providers</span></span>

<span data-ttu-id="291c1-576">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="291c1-577">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="291c1-577">Provider</span></span> | <span data-ttu-id="291c1-578">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="291c1-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="291c1-579">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="291c1-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="291c1-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="291c1-580">Azure Key Vault</span></span> |
| <span data-ttu-id="291c1-581">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="291c1-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="291c1-582">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="291c1-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="291c1-583">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="291c1-584">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-584">Command-line parameters</span></span> |
| [<span data-ttu-id="291c1-585">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="291c1-586">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="291c1-586">Custom source</span></span> |
| [<span data-ttu-id="291c1-587">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="291c1-588">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-588">Environment variables</span></span> |
| [<span data-ttu-id="291c1-589">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="291c1-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="291c1-590">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="291c1-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="291c1-591">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="291c1-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="291c1-592">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="291c1-592">Directory files</span></span> |
| [<span data-ttu-id="291c1-593">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="291c1-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="291c1-594">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="291c1-594">In-memory collections</span></span> |
| <span data-ttu-id="291c1-595">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="291c1-595">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="291c1-596">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="291c1-596">File in the user profile directory</span></span> |

<span data-ttu-id="291c1-597">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="291c1-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="291c1-598">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="291c1-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="291c1-599">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="291c1-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="291c1-600">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="291c1-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="291c1-601">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace</span><span class="sxs-lookup"><span data-stu-id="291c1-601">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="291c1-602">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="291c1-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="291c1-603">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="291c1-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="291c1-604">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-604">Environment variables</span></span>
1. <span data-ttu-id="291c1-605">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-605">Command-line arguments</span></span>

<span data-ttu-id="291c1-606">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="291c1-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="291c1-607">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="291c1-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="291c1-608">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="291c1-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="291c1-609">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="291c1-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="291c1-610">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> na tvůrce hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="291c1-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="291c1-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="291c1-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="291c1-612">Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s automaticky přidanými uživateli `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="291c1-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="291c1-613">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="291c1-614">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte jako `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="291c1-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="291c1-615">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="291c1-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="291c1-616">Chcete-li odebrat poskytovatele přidaných nástrojem `CreateDefaultBuilder` , zavolejte nejprve [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="291c1-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="291c1-617">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="291c1-617">Consume configuration during app startup</span></span>

<span data-ttu-id="291c1-618">Konfigurace dodaná do aplikace v nástroji `ConfigureAppConfiguration` je k dispozici během spouštění aplikace, včetně `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="291c1-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="291c1-619">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="291c1-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="291c1-620">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="291c1-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="291c1-621"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="291c1-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="291c1-622">Chcete-li aktivovat konfiguraci příkazového řádku, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> je metoda rozšíření volána na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="291c1-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="291c1-623">`AddCommandLine`je automaticky volána `CreateDefaultBuilder(string [])` , když je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="291c1-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="291c1-624">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="291c1-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="291c1-625">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="291c1-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="291c1-626">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="291c1-626">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="291c1-627">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="291c1-628">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-628">Environment variables.</span></span>

<span data-ttu-id="291c1-629">`CreateDefaultBuilder`přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="291c1-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="291c1-630">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="291c1-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="291c1-631">`CreateDefaultBuilder`funguje, když je hostitel vytvořen.</span><span class="sxs-lookup"><span data-stu-id="291c1-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="291c1-632">Proto může konfigurace z příkazového řádku aktivované nástrojem `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="291c1-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="291c1-633">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány pomocí `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="291c1-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="291c1-634">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte na `AddCommandLine` Poslední.</span><span class="sxs-lookup"><span data-stu-id="291c1-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="291c1-635">**Případě**</span><span class="sxs-lookup"><span data-stu-id="291c1-635">**Example**</span></span>

<span data-ttu-id="291c1-636">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="291c1-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="291c1-637">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="291c1-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="291c1-638">Zadejte do příkazu argument příkazového řádku `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="291c1-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="291c1-639">Po spuštění aplikace otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="291c1-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="291c1-640">Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k dispozici pro příkaz `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="291c1-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="291c1-641">Arguments</span><span class="sxs-lookup"><span data-stu-id="291c1-641">Arguments</span></span>

<span data-ttu-id="291c1-642">Hodnota musí následovat po znaménku rovná se ( `=` ), nebo klíč musí obsahovat předponu ( `--` nebo `/` ), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="291c1-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="291c1-643">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="291c1-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="291c1-644">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="291c1-644">Key prefix</span></span>               | <span data-ttu-id="291c1-645">Příklad</span><span class="sxs-lookup"><span data-stu-id="291c1-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="291c1-646">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="291c1-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="291c1-647">Dvě pomlčky ( `--` )</span><span class="sxs-lookup"><span data-stu-id="291c1-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="291c1-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="291c1-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="291c1-649">Lomítko ( `/` )</span><span class="sxs-lookup"><span data-stu-id="291c1-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="291c1-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="291c1-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="291c1-651">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="291c1-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="291c1-652">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="291c1-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="291c1-653">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="291c1-653">Switch mappings</span></span>

<span data-ttu-id="291c1-654">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="291c1-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="291c1-655">Při ručním sestavování konfigurace pomocí nástroje <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> Zadejte slovník přepínačů pro vložení do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="291c1-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="291c1-656">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="291c1-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="291c1-657">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="291c1-658">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="291c1-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="291c1-659">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="291c1-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="291c1-660">Přepínače musí začínat spojovníkem ( `-` ) nebo dvojitou čárkou ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="291c1-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="291c1-661">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="291c1-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="291c1-662">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="291c1-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="291c1-663">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="291c1-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="291c1-664">Po sestavení hostitele zavolejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="291c1-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="291c1-665">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="291c1-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="291c1-666">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="291c1-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="291c1-667">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="291c1-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="291c1-668">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="291c1-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="291c1-669">Klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-669">Key</span></span>       | <span data-ttu-id="291c1-670">Hodnota</span><span class="sxs-lookup"><span data-stu-id="291c1-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="291c1-671">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="291c1-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="291c1-672">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="291c1-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="291c1-673">Klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-673">Key</span></span>               | <span data-ttu-id="291c1-674">Hodnota</span><span class="sxs-lookup"><span data-stu-id="291c1-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="291c1-675">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="291c1-676"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="291c1-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="291c1-677">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="291c1-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="291c1-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="291c1-679">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="291c1-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="291c1-680">`AddEnvironmentVariables`slouží k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán.</span><span class="sxs-lookup"><span data-stu-id="291c1-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="291c1-681">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="291c1-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="291c1-682">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="291c1-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="291c1-683">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="291c1-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="291c1-684">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="291c1-684">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="291c1-685">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="291c1-686">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="291c1-686">Command-line arguments.</span></span>

<span data-ttu-id="291c1-687">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="291c1-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="291c1-688">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="291c1-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="291c1-689">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu:</span><span class="sxs-lookup"><span data-stu-id="291c1-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="291c1-690">Zavolejte `AddEnvironmentVariables` jako poslední, pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="291c1-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="291c1-691">**Případě**</span><span class="sxs-lookup"><span data-stu-id="291c1-691">**Example**</span></span>

<span data-ttu-id="291c1-692">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="291c1-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="291c1-693">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="291c1-693">Run the sample app.</span></span> <span data-ttu-id="291c1-694">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="291c1-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="291c1-695">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="291c1-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="291c1-696">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.</span><span class="sxs-lookup"><span data-stu-id="291c1-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="291c1-697">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="291c1-698">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="291c1-699">K vystavení všech proměnných prostředí, které jsou k dispozici pro aplikaci, změňte na `FilteredConfiguration` *stránkách/index. cshtml. cs* následující:</span><span class="sxs-lookup"><span data-stu-id="291c1-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="291c1-700">Předpony</span><span class="sxs-lookup"><span data-stu-id="291c1-700">Prefixes</span></span>

<span data-ttu-id="291c1-701">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="291c1-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="291c1-702">Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_` , zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="291c1-703">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="291c1-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="291c1-704">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="291c1-705">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="291c1-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="291c1-706">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="291c1-706">**Connection string prefixes**</span></span>

<span data-ttu-id="291c1-707">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="291c1-708">Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="291c1-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="291c1-709">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="291c1-709">Connection string prefix</span></span> | <span data-ttu-id="291c1-710">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="291c1-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="291c1-711">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="291c1-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="291c1-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="291c1-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="291c1-713">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="291c1-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="291c1-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="291c1-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="291c1-715">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="291c1-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="291c1-716">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="291c1-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="291c1-717">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="291c1-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="291c1-718">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="291c1-718">Environment variable key</span></span> | <span data-ttu-id="291c1-719">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="291c1-719">Converted configuration key</span></span> | <span data-ttu-id="291c1-720">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="291c1-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="291c1-721">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="291c1-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="291c1-722">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="291c1-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="291c1-723">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="291c1-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="291c1-724">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="291c1-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="291c1-725">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="291c1-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="291c1-726">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="291c1-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="291c1-727">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="291c1-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="291c1-728">**Případě**</span><span class="sxs-lookup"><span data-stu-id="291c1-728">**Example**</span></span>

<span data-ttu-id="291c1-729">Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="291c1-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="291c1-730">Jméno:`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="291c1-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="291c1-731">Hodnota: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="291c1-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="291c1-732">Pokud `IConfiguration` je vloženo a přiřazeno k poli s názvem `_config` , přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="291c1-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="291c1-733">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="291c1-733">File Configuration Provider</span></span>

<span data-ttu-id="291c1-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="291c1-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="291c1-735">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="291c1-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="291c1-736">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="291c1-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="291c1-737">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="291c1-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="291c1-738">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="291c1-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="291c1-739">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="291c1-739">INI Configuration Provider</span></span>

<span data-ttu-id="291c1-740"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="291c1-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="291c1-741">Chcete-li aktivovat konfiguraci souboru INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="291c1-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="291c1-742">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="291c1-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="291c1-743">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="291c1-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="291c1-744">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="291c1-744">Whether the file is optional.</span></span>
* <span data-ttu-id="291c1-745">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="291c1-746"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="291c1-747">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="291c1-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="291c1-748">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="291c1-748">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="291c1-749">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="291c1-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="291c1-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-750">section0:key0</span></span>
* <span data-ttu-id="291c1-751">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-751">section0:key1</span></span>
* <span data-ttu-id="291c1-752">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-752">section1:subsection:key</span></span>
* <span data-ttu-id="291c1-753">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-753">section2:subsection0:key</span></span>
* <span data-ttu-id="291c1-754">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="291c1-755">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="291c1-755">JSON Configuration Provider</span></span>

<span data-ttu-id="291c1-756"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="291c1-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="291c1-757">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="291c1-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="291c1-758">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="291c1-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="291c1-759">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="291c1-759">Whether the file is optional.</span></span>
* <span data-ttu-id="291c1-760">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="291c1-761"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="291c1-762">`AddJsonFile`se automaticky volá dvakrát při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="291c1-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="291c1-763">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="291c1-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="291c1-764">*appSettings. JSON*: Tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="291c1-764">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="291c1-765">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="291c1-765">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="291c1-766">*appSettings. {Environment}. JSON*: verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="291c1-766">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="291c1-767">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="291c1-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="291c1-768">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="291c1-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="291c1-769">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-769">Environment variables.</span></span>
* <span data-ttu-id="291c1-770">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="291c1-771">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="291c1-771">Command-line arguments.</span></span>

<span data-ttu-id="291c1-772">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="291c1-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="291c1-773">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="291c1-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="291c1-774">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="291c1-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="291c1-775">**Případě**</span><span class="sxs-lookup"><span data-stu-id="291c1-775">**Example**</span></span>

<span data-ttu-id="291c1-776">Ukázková aplikace využívá ke sestavení hostitele výhodu technologie statického usnadnění `CreateDefaultBuilder` , která zahrnuje dvě volání `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="291c1-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="291c1-777">První volání `AddJsonFile` načtení konfigurace z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="291c1-777">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="291c1-778">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="291c1-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="291c1-779">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="291c1-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="291c1-780">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="291c1-780">Run the sample app.</span></span> <span data-ttu-id="291c1-781">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="291c1-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="291c1-782">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="291c1-783">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="291c1-784">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="291c1-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="291c1-785">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="291c1-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="291c1-786">V `ConfigurationSample` profilu změňte hodnotu `ASPNETCORE_ENVIRONMENT` proměnné prostředí na `Production` .</span><span class="sxs-lookup"><span data-stu-id="291c1-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="291c1-787">Uložte soubor a spusťte aplikaci `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="291c1-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="291c1-788">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="291c1-788">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="291c1-789">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Warning` .</span><span class="sxs-lookup"><span data-stu-id="291c1-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="291c1-790">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="291c1-790">XML Configuration Provider</span></span>

<span data-ttu-id="291c1-791"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="291c1-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="291c1-792">Chcete-li aktivovat konfiguraci souboru XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="291c1-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="291c1-793">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="291c1-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="291c1-794">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="291c1-794">Whether the file is optional.</span></span>
* <span data-ttu-id="291c1-795">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="291c1-796"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="291c1-797">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="291c1-798">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="291c1-799">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="291c1-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="291c1-800">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="291c1-800">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="291c1-801">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="291c1-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="291c1-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-802">section0:key0</span></span>
* <span data-ttu-id="291c1-803">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-803">section0:key1</span></span>
* <span data-ttu-id="291c1-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-804">section1:key0</span></span>
* <span data-ttu-id="291c1-805">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-805">section1:key1</span></span>

<span data-ttu-id="291c1-806">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="291c1-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="291c1-807">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="291c1-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="291c1-808">oddíl: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="291c1-808">section:section0:key:key0</span></span>
* <span data-ttu-id="291c1-809">oddíl: section0: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-809">section:section0:key:key1</span></span>
* <span data-ttu-id="291c1-810">oddíl: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="291c1-810">section:section1:key:key0</span></span>
* <span data-ttu-id="291c1-811">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-811">section:section1:key:key1</span></span>

<span data-ttu-id="291c1-812">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="291c1-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="291c1-813">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="291c1-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="291c1-814">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="291c1-814">key:attribute</span></span>
* <span data-ttu-id="291c1-815">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="291c1-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="291c1-816">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="291c1-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="291c1-817"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="291c1-818">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-818">The key is the file name.</span></span> <span data-ttu-id="291c1-819">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="291c1-819">The value contains the file's contents.</span></span> <span data-ttu-id="291c1-820">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="291c1-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="291c1-821">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="291c1-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="291c1-822">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="291c1-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="291c1-823">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="291c1-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="291c1-824">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="291c1-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="291c1-825">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="291c1-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="291c1-826">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="291c1-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="291c1-827">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="291c1-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="291c1-828">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="291c1-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="291c1-829">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="291c1-829">Memory Configuration Provider</span></span>

<span data-ttu-id="291c1-830"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="291c1-831">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="291c1-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="291c1-832">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="291c1-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="291c1-833">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="291c1-834">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="291c1-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="291c1-835">Slovník se používá s voláním k `AddInMemoryCollection` poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="291c1-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="291c1-836">GetValue</span></span>

<span data-ttu-id="291c1-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="291c1-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="291c1-838">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="291c1-838">An overload accepts a default value.</span></span>

<span data-ttu-id="291c1-839">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="291c1-839">The following example:</span></span>

* <span data-ttu-id="291c1-840">Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="291c1-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="291c1-841">Pokud se `NumberKey` v konfiguračních klíčích nenajde, použije se výchozí hodnota `99` .</span><span class="sxs-lookup"><span data-stu-id="291c1-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="291c1-842">Zadá hodnotu jako `int` .</span><span class="sxs-lookup"><span data-stu-id="291c1-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="291c1-843">Ukládá hodnotu `NumberConfig` vlastnosti pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="291c1-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="291c1-844">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="291c1-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="291c1-845">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="291c1-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="291c1-846">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="291c1-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="291c1-847">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="291c1-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-848">section0:key0</span></span>
* <span data-ttu-id="291c1-849">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-849">section0:key1</span></span>
* <span data-ttu-id="291c1-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-850">section1:key0</span></span>
* <span data-ttu-id="291c1-851">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-851">section1:key1</span></span>
* <span data-ttu-id="291c1-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="291c1-853">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="291c1-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="291c1-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="291c1-855">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="291c1-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="291c1-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="291c1-856">GetSection</span></span>

<span data-ttu-id="291c1-857">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="291c1-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="291c1-858">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1` , zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="291c1-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="291c1-859">`configSection`Hodnota neobsahuje pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="291c1-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="291c1-860">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0` , zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="291c1-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="291c1-861">`GetSection`nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="291c1-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="291c1-862">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="291c1-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="291c1-863">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="291c1-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="291c1-864"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="291c1-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="291c1-865">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="291c1-865">GetChildren</span></span>

<span data-ttu-id="291c1-866">Volání [IConfiguration. Getchildrens](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="291c1-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="291c1-867">Existuje</span><span class="sxs-lookup"><span data-stu-id="291c1-867">Exists</span></span>

<span data-ttu-id="291c1-868">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="291c1-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="291c1-869">Vzhledem k ukázkovým datům `sectionExists` je `false` to proto, že `section2:subsection2` v konfiguračních datech není oddíl.</span><span class="sxs-lookup"><span data-stu-id="291c1-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="291c1-870">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="291c1-870">Bind to an object graph</span></span>

<span data-ttu-id="291c1-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="291c1-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="291c1-872">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="291c1-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="291c1-873">Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="291c1-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="291c1-874">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="291c1-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="291c1-875">Konfigurace je svázána s celým `TvShow` grafem objektů pomocí `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="291c1-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="291c1-876">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="291c1-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="291c1-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="291c1-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="291c1-878">`Get<T>`je pohodlnější než použití `Bind` .</span><span class="sxs-lookup"><span data-stu-id="291c1-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="291c1-879">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem:</span><span class="sxs-lookup"><span data-stu-id="291c1-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="291c1-880">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="291c1-880">Bind an array to a class</span></span>

<span data-ttu-id="291c1-881">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="291c1-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="291c1-882"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="291c1-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="291c1-883">Libovolný formát pole, který zveřejňuje numerický klíčový segment ( `:0:` , `:1:` ,), &hellip; `:{n}:` je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="291c1-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="291c1-884">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="291c1-884">Binding is provided by convention.</span></span> <span data-ttu-id="291c1-885">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="291c1-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="291c1-886">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="291c1-886">**In-memory array processing**</span></span>

<span data-ttu-id="291c1-887">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="291c1-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="291c1-888">Klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-888">Key</span></span>             | <span data-ttu-id="291c1-889">Hodnota</span><span class="sxs-lookup"><span data-stu-id="291c1-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="291c1-890">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="291c1-890">array:entries:0</span></span> | <span data-ttu-id="291c1-891">value0</span><span class="sxs-lookup"><span data-stu-id="291c1-891">value0</span></span> |
| <span data-ttu-id="291c1-892">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="291c1-892">array:entries:1</span></span> | <span data-ttu-id="291c1-893">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="291c1-893">value1</span></span> |
| <span data-ttu-id="291c1-894">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="291c1-894">array:entries:2</span></span> | <span data-ttu-id="291c1-895">Argument</span><span class="sxs-lookup"><span data-stu-id="291c1-895">value2</span></span> |
| <span data-ttu-id="291c1-896">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="291c1-896">array:entries:4</span></span> | <span data-ttu-id="291c1-897">value4</span><span class="sxs-lookup"><span data-stu-id="291c1-897">value4</span></span> |
| <span data-ttu-id="291c1-898">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="291c1-898">array:entries:5</span></span> | <span data-ttu-id="291c1-899">value5</span><span class="sxs-lookup"><span data-stu-id="291c1-899">value5</span></span> |

<span data-ttu-id="291c1-900">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="291c1-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="291c1-901">Pole přeskočí hodnotu indexu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="291c1-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="291c1-902">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="291c1-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="291c1-903">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="291c1-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="291c1-904">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="291c1-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="291c1-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lze také použít syntaxi, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="291c1-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="291c1-906">Vázaný objekt, instance `ArrayExample` , přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="291c1-907">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="291c1-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="291c1-908">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="291c1-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="291c1-909">0</span><span class="sxs-lookup"><span data-stu-id="291c1-909">0</span></span>                            | <span data-ttu-id="291c1-910">value0</span><span class="sxs-lookup"><span data-stu-id="291c1-910">value0</span></span>                       |
| <span data-ttu-id="291c1-911">1</span><span class="sxs-lookup"><span data-stu-id="291c1-911">1</span></span>                            | <span data-ttu-id="291c1-912">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="291c1-912">value1</span></span>                       |
| <span data-ttu-id="291c1-913">2</span><span class="sxs-lookup"><span data-stu-id="291c1-913">2</span></span>                            | <span data-ttu-id="291c1-914">Argument</span><span class="sxs-lookup"><span data-stu-id="291c1-914">value2</span></span>                       |
| <span data-ttu-id="291c1-915">3</span><span class="sxs-lookup"><span data-stu-id="291c1-915">3</span></span>                            | <span data-ttu-id="291c1-916">value4</span><span class="sxs-lookup"><span data-stu-id="291c1-916">value4</span></span>                       |
| <span data-ttu-id="291c1-917">4</span><span class="sxs-lookup"><span data-stu-id="291c1-917">4</span></span>                            | <span data-ttu-id="291c1-918">value5</span><span class="sxs-lookup"><span data-stu-id="291c1-918">value5</span></span>                       |

<span data-ttu-id="291c1-919">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="291c1-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="291c1-920">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="291c1-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="291c1-921">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="291c1-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="291c1-922">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na `ArrayExample` instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="291c1-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="291c1-923">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="291c1-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="291c1-924">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="291c1-924">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="291c1-925">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="291c1-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="291c1-926">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="291c1-927">Klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-927">Key</span></span>             | <span data-ttu-id="291c1-928">Hodnota</span><span class="sxs-lookup"><span data-stu-id="291c1-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="291c1-929">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="291c1-929">array:entries:3</span></span> | <span data-ttu-id="291c1-930">hodnota3</span><span class="sxs-lookup"><span data-stu-id="291c1-930">value3</span></span> |

<span data-ttu-id="291c1-931">Je-li `ArrayExample` instance třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro index &num; 3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="291c1-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="291c1-932">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="291c1-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="291c1-933">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="291c1-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="291c1-934">0</span><span class="sxs-lookup"><span data-stu-id="291c1-934">0</span></span>                            | <span data-ttu-id="291c1-935">value0</span><span class="sxs-lookup"><span data-stu-id="291c1-935">value0</span></span>                       |
| <span data-ttu-id="291c1-936">1</span><span class="sxs-lookup"><span data-stu-id="291c1-936">1</span></span>                            | <span data-ttu-id="291c1-937">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="291c1-937">value1</span></span>                       |
| <span data-ttu-id="291c1-938">2</span><span class="sxs-lookup"><span data-stu-id="291c1-938">2</span></span>                            | <span data-ttu-id="291c1-939">Argument</span><span class="sxs-lookup"><span data-stu-id="291c1-939">value2</span></span>                       |
| <span data-ttu-id="291c1-940">3</span><span class="sxs-lookup"><span data-stu-id="291c1-940">3</span></span>                            | <span data-ttu-id="291c1-941">hodnota3</span><span class="sxs-lookup"><span data-stu-id="291c1-941">value3</span></span>                       |
| <span data-ttu-id="291c1-942">4</span><span class="sxs-lookup"><span data-stu-id="291c1-942">4</span></span>                            | <span data-ttu-id="291c1-943">value4</span><span class="sxs-lookup"><span data-stu-id="291c1-943">value4</span></span>                       |
| <span data-ttu-id="291c1-944">5</span><span class="sxs-lookup"><span data-stu-id="291c1-944">5</span></span>                            | <span data-ttu-id="291c1-945">value5</span><span class="sxs-lookup"><span data-stu-id="291c1-945">value5</span></span>                       |

<span data-ttu-id="291c1-946">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="291c1-946">**JSON array processing**</span></span>

<span data-ttu-id="291c1-947">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="291c1-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="291c1-948">V následujícím konfiguračním souboru `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="291c1-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="291c1-949">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="291c1-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="291c1-950">Klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-950">Key</span></span>                     | <span data-ttu-id="291c1-951">Hodnota</span><span class="sxs-lookup"><span data-stu-id="291c1-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="291c1-952">json_array: klíč</span><span class="sxs-lookup"><span data-stu-id="291c1-952">json_array:key</span></span>          | <span data-ttu-id="291c1-953">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="291c1-953">valueA</span></span> |
| <span data-ttu-id="291c1-954">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="291c1-954">json_array:subsection:0</span></span> | <span data-ttu-id="291c1-955">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="291c1-955">valueB</span></span> |
| <span data-ttu-id="291c1-956">json_array: pododdíl: 1</span><span class="sxs-lookup"><span data-stu-id="291c1-956">json_array:subsection:1</span></span> | <span data-ttu-id="291c1-957">valueC</span><span class="sxs-lookup"><span data-stu-id="291c1-957">valueC</span></span> |
| <span data-ttu-id="291c1-958">json_array: pododdíl: 2</span><span class="sxs-lookup"><span data-stu-id="291c1-958">json_array:subsection:2</span></span> | <span data-ttu-id="291c1-959">s</span><span class="sxs-lookup"><span data-stu-id="291c1-959">valueD</span></span> |

<span data-ttu-id="291c1-960">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="291c1-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="291c1-961">Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu `valueA` .</span><span class="sxs-lookup"><span data-stu-id="291c1-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="291c1-962">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="291c1-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="291c1-963">`JsonArrayExample.Subsection`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="291c1-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="291c1-964">`JsonArrayExample.Subsection`Osa</span><span class="sxs-lookup"><span data-stu-id="291c1-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="291c1-965">0</span><span class="sxs-lookup"><span data-stu-id="291c1-965">0</span></span>                                   | <span data-ttu-id="291c1-966">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="291c1-966">valueB</span></span>                              |
| <span data-ttu-id="291c1-967">1</span><span class="sxs-lookup"><span data-stu-id="291c1-967">1</span></span>                                   | <span data-ttu-id="291c1-968">valueC</span><span class="sxs-lookup"><span data-stu-id="291c1-968">valueC</span></span>                              |
| <span data-ttu-id="291c1-969">2</span><span class="sxs-lookup"><span data-stu-id="291c1-969">2</span></span>                                   | <span data-ttu-id="291c1-970">s</span><span class="sxs-lookup"><span data-stu-id="291c1-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="291c1-971">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="291c1-971">Custom configuration provider</span></span>

<span data-ttu-id="291c1-972">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="291c1-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="291c1-973">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="291c1-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="291c1-974">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="291c1-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="291c1-975">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="291c1-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="291c1-976">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="291c1-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="291c1-977">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="291c1-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="291c1-978">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="291c1-979">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="291c1-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="291c1-980">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-980">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="291c1-981">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="291c1-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="291c1-982">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-982">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="291c1-983">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="291c1-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="291c1-984">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-984">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="291c1-985">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="291c1-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="291c1-986">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="291c1-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="291c1-987">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-987">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="291c1-988">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="291c1-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="291c1-989">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-989">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="291c1-990">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="291c1-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="291c1-991">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="291c1-991">Access configuration during startup</span></span>

<span data-ttu-id="291c1-992">Vložení `IConfiguration` do `Startup` konstruktoru pro přístup k hodnotám konfigurace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="291c1-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="291c1-993">Chcete-li získat přístup ke konfiguraci v `Startup.Configure` , buď `IConfiguration` přímo do metody, nebo použijte instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="291c1-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="291c1-994">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="291c1-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="291c1-995">Přístup ke konfiguraci na Razor stránce stránky nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="291c1-995">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="291c1-996">Chcete-li získat přístup k nastavení konfigurace na Razor stránce stránky nebo zobrazení MVC, [přidejte direktivu using](xref:mvc/views/razor#using) ([Referenční dokumentace jazyka C#: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> ji do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="291c1-996">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="291c1-997">Na Razor stránce stránky:</span><span class="sxs-lookup"><span data-stu-id="291c1-997">In a Razor Pages page:</span></span>

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

<span data-ttu-id="291c1-998">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="291c1-998">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="291c1-999">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="291c1-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="291c1-1000"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="291c1-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="291c1-1001">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="291c1-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="291c1-1002">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="291c1-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

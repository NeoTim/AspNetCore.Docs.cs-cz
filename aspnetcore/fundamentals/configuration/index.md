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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: 6e47e627915bd8988d161f7d5af4a89f3671c0a7
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445447"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="b5843-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5843-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="b5843-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b5843-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5843-105">Konfigurace v ASP.NET Core provádí použití jednoho nebo více [poskytovatelů konfigurace](#cp).</span><span class="sxs-lookup"><span data-stu-id="b5843-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="b5843-106">Poskytovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="b5843-107">Soubory nastavení, například *appsettings.jszapnuto*</span><span class="sxs-lookup"><span data-stu-id="b5843-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="b5843-108">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-108">Environment variables</span></span>
* <span data-ttu-id="b5843-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5843-109">Azure Key Vault</span></span>
* <span data-ttu-id="b5843-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="b5843-110">Azure App Configuration</span></span>
* <span data-ttu-id="b5843-111">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-111">Command-line arguments</span></span>
* <span data-ttu-id="b5843-112">Vlastní zprostředkovatelé, nainstalované nebo vytvořené</span><span class="sxs-lookup"><span data-stu-id="b5843-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="b5843-113">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="b5843-113">Directory files</span></span>
* <span data-ttu-id="b5843-114">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="b5843-114">In-memory .NET objects</span></span>

<span data-ttu-id="b5843-115">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5843-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="b5843-116">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-116">Default configuration</span></span>

<span data-ttu-id="b5843-117">ASP.NET Core webové aplikace vytvořené pomocí [dotnet New](/dotnet/core/tools/dotnet-new) nebo sady Visual Studio generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="b5843-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="b5843-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="b5843-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="b5843-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá existující `IConfiguration` jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="b5843-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="b5843-120">Ve výchozím případu konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj konfigurace _aplikace_ .</span><span class="sxs-lookup"><span data-stu-id="b5843-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="b5843-121">[appsettings.js](#appsettingsjson) používání [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="b5843-122">*appSettings.* `Environment` *. JSON* s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="b5843-123">Například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="b5843-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="b5843-124">[Tajné kódy aplikace](xref:security/app-secrets) v případě, že aplikace běží v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="b5843-125">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="b5843-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="b5843-126">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line).</span><span class="sxs-lookup"><span data-stu-id="b5843-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="b5843-127">Poskytovatelé konfigurace, kteří jsou přidáni později, přepíší předchozí nastavení klíče.</span><span class="sxs-lookup"><span data-stu-id="b5843-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="b5843-128">Například pokud `MyKey` je nastavena v *appsettings.jsv* a prostředí, je použita hodnota prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="b5843-129">Pomocí výchozích zprostředkovatelů konfigurace přepíše [Poskytovatel konfigurace příkazového řádku](#clcp) všechny ostatní poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b5843-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="b5843-130">Další informace o najdete v `CreateDefaultBuilder` tématu [výchozí nastavení tvůrce](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="b5843-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="b5843-131">Následující kód zobrazuje povolené poskytovatele konfigurace v pořadí, v jakém byly přidány:</span><span class="sxs-lookup"><span data-stu-id="b5843-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="b5843-132">appsettings.jsna</span><span class="sxs-lookup"><span data-stu-id="b5843-132">appsettings.json</span></span>

<span data-ttu-id="b5843-133">V souboru zvažte následující *appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="b5843-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="b5843-134">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-135">Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načtení konfigurace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="b5843-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="b5843-136">*appsettings.jsna*</span><span class="sxs-lookup"><span data-stu-id="b5843-136">*appsettings.json*</span></span>
1. <span data-ttu-id="b5843-137">*appSettings.* `Environment` *. JSON* : například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="b5843-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="b5843-138">Verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b5843-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="b5843-139">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b5843-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b5843-140">*appSettings*. `Environment` .. hodnoty *JSON* přepisují klíče v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="b5843-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="b5843-141">Ve výchozím nastavení by například:</span><span class="sxs-lookup"><span data-stu-id="b5843-141">For example, by default:</span></span>

* <span data-ttu-id="b5843-142">Ve vývoji, *appSettings*. ***Vývoj***. konfigurace *JSON* Přepisuje hodnoty nalezené v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="b5843-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="b5843-143">V produkčním prostředí, *appSettings*. ***Provozní***prostředí. konfigurace *JSON* Přepisuje hodnoty nalezené v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="b5843-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="b5843-144">Například při nasazení aplikace do Azure.</span><span class="sxs-lookup"><span data-stu-id="b5843-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="b5843-145">Vytvoření vazby hierarchických konfiguračních dat pomocí vzoru možností</span><span class="sxs-lookup"><span data-stu-id="b5843-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="b5843-146">Pomocí [výchozí](#default) konfigurace *appsettings.jsna* a *appSettings.* `Environment` soubory *. JSON* jsou povolené pomocí [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="b5843-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="b5843-147">Změny provedené v *appsettings.jsv* a *appSettings.* `Environment` soubor *. JSON* ***po*** spuštění aplikace si přečte [Poskytovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b5843-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="b5843-148">Informace o přidání dalších konfiguračních souborů JSON najdete v části [Poskytovatel konfigurace JSON](#jcp) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b5843-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="b5843-149">Správce zabezpečení a tajného klíče</span><span class="sxs-lookup"><span data-stu-id="b5843-149">Security and secret manager</span></span>

<span data-ttu-id="b5843-150">Pokyny pro konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="b5843-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="b5843-151">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="b5843-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="b5843-152">[Správce tajného klíče](xref:security/app-secrets) se dá použít k ukládání tajných kódů do vývoje.</span><span class="sxs-lookup"><span data-stu-id="b5843-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="b5843-153">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="b5843-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b5843-154">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="b5843-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b5843-155">Ve [výchozím nastavení](#default)přečte [správce tajných klíčů](xref:security/app-secrets) nastavení konfigurace po *appsettings.jsv* a *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b5843-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="b5843-156">Další informace o ukládání hesel nebo jiných citlivých dat:</span><span class="sxs-lookup"><span data-stu-id="b5843-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="b5843-157"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="b5843-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="b5843-158">Správce tajného kódu používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="b5843-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="b5843-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="b5843-160">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b5843-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="b5843-161">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-161">Environment variables</span></span>

<span data-ttu-id="b5843-162">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z proměnných prostředí s proměnnou klíč-hodnota po přečtení *appsettings.jsna*, *appSettings.* `Environment` *. JSON*a [správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b5843-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="b5843-163">Proto hodnoty klíčů načtené z prostředí přepisují hodnoty načtené z *appsettings.jsna*, *appSettings.* `Environment` *. JSON*a správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="b5843-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b5843-164">Následující `set` příkazy:</span><span class="sxs-lookup"><span data-stu-id="b5843-164">The following `set` commands:</span></span>

* <span data-ttu-id="b5843-165">Nastavte klíče prostředí a hodnoty v [předchozím příkladu](#appsettingsjson) ve Windows.</span><span class="sxs-lookup"><span data-stu-id="b5843-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="b5843-166">Při použití [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)otestujte nastavení.</span><span class="sxs-lookup"><span data-stu-id="b5843-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="b5843-167">`dotnet run`Příkaz musí být spuštěn v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="b5843-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="b5843-168">Předchozí nastavení prostředí:</span><span class="sxs-lookup"><span data-stu-id="b5843-168">The preceding environment settings:</span></span>

* <span data-ttu-id="b5843-169">Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.</span><span class="sxs-lookup"><span data-stu-id="b5843-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="b5843-170">Nebudou přečteny pomocí prohlížečů spuštěných v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b5843-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="b5843-171">K nastavení klíčů a hodnot prostředí ve Windows se dají použít následující příkazy [setx](/windows-server/administration/windows-commands/setx) .</span><span class="sxs-lookup"><span data-stu-id="b5843-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="b5843-172">Na rozdíl `set` od `setx` platí, že nastavení jsou trvalá.</span><span class="sxs-lookup"><span data-stu-id="b5843-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="b5843-173">`/M`nastaví proměnnou v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="b5843-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="b5843-174">Pokud se `/M` přepínač nepoužívá, je nastavena proměnná prostředí uživatele.</span><span class="sxs-lookup"><span data-stu-id="b5843-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="b5843-175">Chcete-li otestovat, zda předchozí příkazy přepisují *appsettings.jsv* a *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b5843-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="b5843-176">Se sadou Visual Studio: Ukončete a restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b5843-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="b5843-177">Pomocí rozhraní příkazového řádku: spusťte nové příkazové okno a zadejte `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="b5843-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="b5843-178">Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem pro určení předpony pro proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="b5843-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="b5843-179">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="b5843-179">In the preceding code:</span></span>

* <span data-ttu-id="b5843-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="b5843-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="b5843-181">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b5843-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="b5843-182">Proměnné prostředí nastavené s `MyCustomPrefix_` předponou přepíšou [výchozí poskytovatele konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="b5843-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="b5843-183">To zahrnuje proměnné prostředí bez předpony.</span><span class="sxs-lookup"><span data-stu-id="b5843-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="b5843-184">Pokud jsou načteny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="b5843-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="b5843-185">Vlastní předponu otestujete následujícími příkazy:</span><span class="sxs-lookup"><span data-stu-id="b5843-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="b5843-186">[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty příkazového řádku s předponou `DOTNET_` a `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="b5843-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="b5843-187">`DOTNET_` `ASPNETCORE_` Předpony a jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele.</span><span class="sxs-lookup"><span data-stu-id="b5843-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="b5843-188">Další informace o konfiguraci hostitele a aplikace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b5843-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="b5843-189">V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="b5843-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="b5843-190">Azure App Service nastavení aplikace:</span><span class="sxs-lookup"><span data-stu-id="b5843-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="b5843-191">Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.</span><span class="sxs-lookup"><span data-stu-id="b5843-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="b5843-192">Vystaveno jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-192">Exposed as environment variables.</span></span>

<span data-ttu-id="b5843-193">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b5843-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="b5843-194">Informace o připojovacích řetězcích Azure Database najdete v tématu [předpony připojovacího řetězce](#constr) .</span><span class="sxs-lookup"><span data-stu-id="b5843-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="b5843-195">Proměnné prostředí nastavené v launchSettings.jszapnuté</span><span class="sxs-lookup"><span data-stu-id="b5843-195">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="b5843-196">Proměnné prostředí nastavené v *launchSettings.jspři* přepisování nastavení v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="b5843-196">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="b5843-197">Příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="b5843-197">Command-line</span></span>

<span data-ttu-id="b5843-198">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načte konfiguraci z dvojice klíč-hodnota argumentu klíč-hodnota po následujících zdrojích konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-198">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="b5843-199">*appsettings.jsna* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="b5843-199">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="b5843-200">[Tajné kódy aplikací (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-200">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b5843-201">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-201">Environment variables.</span></span>

<span data-ttu-id="b5843-202">Ve [výchozím nastavení](#default)jsou konfigurační hodnoty nastavené pro přepisy hodnot konfigurace na příkazovém řádku nastavené se všemi ostatními zprostředkovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-202">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="b5843-203">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-203">Command-line arguments</span></span>

<span data-ttu-id="b5843-204">Následující příkaz nastaví klíče a hodnoty pomocí `=` :</span><span class="sxs-lookup"><span data-stu-id="b5843-204">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="b5843-205">Následující příkaz nastaví klíče a hodnoty pomocí `/` :</span><span class="sxs-lookup"><span data-stu-id="b5843-205">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="b5843-206">Následující příkaz nastaví klíče a hodnoty pomocí `--` :</span><span class="sxs-lookup"><span data-stu-id="b5843-206">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="b5843-207">Hodnota klíče:</span><span class="sxs-lookup"><span data-stu-id="b5843-207">The key value:</span></span>

* <span data-ttu-id="b5843-208">Musí následovat po `=` , nebo klíč musí mít předponu `--` nebo, `/` Pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="b5843-208">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="b5843-209">Není vyžadováno `=` , pokud je použit.</span><span class="sxs-lookup"><span data-stu-id="b5843-209">Isn't required if `=` is used.</span></span> <span data-ttu-id="b5843-210">Například `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="b5843-210">For example, `MySetting=`.</span></span>

<span data-ttu-id="b5843-211">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které `=` se používají s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="b5843-211">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="b5843-212">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="b5843-212">Switch mappings</span></span>

<span data-ttu-id="b5843-213">Mapování přepínačů povolují logiku nahrazení názvu **klíče** .</span><span class="sxs-lookup"><span data-stu-id="b5843-213">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="b5843-214">Poskytněte slovník pro nahrazení přepínačů v <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodě.</span><span class="sxs-lookup"><span data-stu-id="b5843-214">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b5843-215">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b5843-215">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b5843-216">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota Dictionary zpátky, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-216">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b5843-217">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="b5843-217">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b5843-218">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="b5843-218">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b5843-219">Přepínače musí začínat na `-` nebo `--` .</span><span class="sxs-lookup"><span data-stu-id="b5843-219">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="b5843-220">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="b5843-220">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="b5843-221">Chcete-li použít slovník mapování přepínačů, předejte jej do volání `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="b5843-221">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="b5843-222">Následující kód ukazuje klíčové hodnoty pro nahrazené klíče:</span><span class="sxs-lookup"><span data-stu-id="b5843-222">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-223">Následující příkaz funguje pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="b5843-223">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<!-- Run the following command to test the key replacement: -->

<span data-ttu-id="b5843-224">Poznámka: v současné době `=` nelze použít k nastavení hodnot nahrazení klíče jedinou pomlčkou `-` .</span><span class="sxs-lookup"><span data-stu-id="b5843-224">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="b5843-225">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="b5843-225">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="b5843-226">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="b5843-226">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="b5843-227">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5843-227">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b5843-228">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty i slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="b5843-228">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b5843-229">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="b5843-229">Hierarchical configuration data</span></span>

<span data-ttu-id="b5843-230">Rozhraní API pro konfiguraci čte hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="b5843-230">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b5843-231">[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="b5843-231">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="b5843-232">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-232">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-233">Upřednostňovaným způsobem, jak číst hierarchická konfigurační data, je použít vzor možností.</span><span class="sxs-lookup"><span data-stu-id="b5843-233">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="b5843-234">Další informace najdete v tématu [vázání hierarchických konfiguračních dat](#optpat) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b5843-234">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="b5843-235"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="b5843-235"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b5843-236">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="b5843-236">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="b5843-237">Konfigurační klíče a hodnoty</span><span class="sxs-lookup"><span data-stu-id="b5843-237">Configuration keys and values</span></span>

<span data-ttu-id="b5843-238">Konfigurační klíče:</span><span class="sxs-lookup"><span data-stu-id="b5843-238">Configuration keys:</span></span>

* <span data-ttu-id="b5843-239">Rozlišují se malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b5843-239">Are case-insensitive.</span></span> <span data-ttu-id="b5843-240">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="b5843-240">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b5843-241">Pokud je klíč a hodnota nastavená ve více poskytovatelích konfigurace, použije se hodnota z posledního přidaného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="b5843-241">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="b5843-242">Další informace najdete v tématu [výchozí konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="b5843-242">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="b5843-243">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="b5843-243">Hierarchical keys</span></span>
  * <span data-ttu-id="b5843-244">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b5843-244">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b5843-245">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b5843-245">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b5843-246">Dvojité podtržítko, `__` , je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:` .</span><span class="sxs-lookup"><span data-stu-id="b5843-246">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="b5843-247">V Azure Key Vault hierarchické klíče používají `--` jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="b5843-247">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="b5843-248">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) se `--` `:` při načtení tajných klíčů do konfigurace aplikace automaticky nahradí.</span><span class="sxs-lookup"><span data-stu-id="b5843-248">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b5843-249"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="b5843-249">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b5843-250">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#boa) .</span><span class="sxs-lookup"><span data-stu-id="b5843-250">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="b5843-251">Konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b5843-251">Configuration values:</span></span>

* <span data-ttu-id="b5843-252">Jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="b5843-252">Are strings.</span></span>
* <span data-ttu-id="b5843-253">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="b5843-253">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="b5843-254">Poskytovatelé konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-254">Configuration providers</span></span>

<span data-ttu-id="b5843-255">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-255">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="b5843-256">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="b5843-256">Provider</span></span> | <span data-ttu-id="b5843-257">Poskytuje konfiguraci z</span><span class="sxs-lookup"><span data-stu-id="b5843-257">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="b5843-258">Poskytovatel konfigurace Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5843-258">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="b5843-259">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5843-259">Azure Key Vault</span></span> |
| [<span data-ttu-id="b5843-260">Poskytovatel konfigurace Azure App</span><span class="sxs-lookup"><span data-stu-id="b5843-260">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="b5843-261">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="b5843-261">Azure App Configuration</span></span> |
| [<span data-ttu-id="b5843-262">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-262">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="b5843-263">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-263">Command-line parameters</span></span> |
| [<span data-ttu-id="b5843-264">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-264">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b5843-265">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="b5843-265">Custom source</span></span> |
| [<span data-ttu-id="b5843-266">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-266">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="b5843-267">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-267">Environment variables</span></span> |
| [<span data-ttu-id="b5843-268">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b5843-268">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b5843-269">Soubory INI, JSON a XML</span><span class="sxs-lookup"><span data-stu-id="b5843-269">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="b5843-270">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="b5843-270">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b5843-271">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="b5843-271">Directory files</span></span> |
| [<span data-ttu-id="b5843-272">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b5843-272">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b5843-273">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="b5843-273">In-memory collections</span></span> |
| [<span data-ttu-id="b5843-274">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="b5843-274">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="b5843-275">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="b5843-275">File in the user profile directory</span></span> |

<span data-ttu-id="b5843-276">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou zadáni poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-276">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="b5843-277">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="b5843-277">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="b5843-278">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="b5843-278">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="b5843-279">*appsettings.jsna*</span><span class="sxs-lookup"><span data-stu-id="b5843-279">*appsettings.json*</span></span>
1. <span data-ttu-id="b5843-280">*appSettings*. `Environment` .. formát *JSON*</span><span class="sxs-lookup"><span data-stu-id="b5843-280">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="b5843-281">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="b5843-281">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="b5843-282">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="b5843-282">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="b5843-283">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-283">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="b5843-284">Běžný postup je přidat poskytovatele konfigurace příkazového řádku na poslední v řadě poskytovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="b5843-284">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="b5843-285">Předchozí sekvence zprostředkovatelů se používá ve [výchozí konfiguraci](#default).</span><span class="sxs-lookup"><span data-stu-id="b5843-285">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="b5843-286">Předpony připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="b5843-286">Connection string prefixes</span></span>

<span data-ttu-id="b5843-287">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro proměnné prostředí pro čtyři připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="b5843-287">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="b5843-288">Tyto připojovací řetězce jsou součástí konfigurace připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-288">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b5843-289">Proměnné prostředí s předponami, které jsou uvedené v tabulce, se načtou do aplikace s [výchozí konfigurací](#default) nebo když není dodána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="b5843-289">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b5843-290">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="b5843-290">Connection string prefix</span></span> | <span data-ttu-id="b5843-291">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="b5843-291">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b5843-292">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b5843-292">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b5843-293">MySQL</span><span class="sxs-lookup"><span data-stu-id="b5843-293">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b5843-294">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="b5843-294">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b5843-295">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b5843-295">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b5843-296">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="b5843-296">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b5843-297">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="b5843-297">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b5843-298">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="b5843-298">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b5843-299">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-299">Environment variable key</span></span> | <span data-ttu-id="b5843-300">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="b5843-300">Converted configuration key</span></span> | <span data-ttu-id="b5843-301">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="b5843-301">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b5843-302">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="b5843-302">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b5843-303">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b5843-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b5843-304">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b5843-304">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b5843-305">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b5843-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b5843-306">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b5843-306">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b5843-307">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b5843-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b5843-308">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b5843-308">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="b5843-309">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="b5843-309">JSON configuration provider</span></span>

<span data-ttu-id="b5843-310"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načte konfiguraci z párů klíč-hodnota souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="b5843-310">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="b5843-311">Přetížení můžou specifikovat:</span><span class="sxs-lookup"><span data-stu-id="b5843-311">Overloads can specify:</span></span>

* <span data-ttu-id="b5843-312">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="b5843-312">Whether the file is optional.</span></span>
* <span data-ttu-id="b5843-313">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-313">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="b5843-314">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="b5843-314">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="b5843-315">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="b5843-315">The preceding code:</span></span>

* <span data-ttu-id="b5843-316">Nakonfiguruje zprostředkovatele konfigurace JSON, aby načetl *MyConfig.js* do souboru s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="b5843-316">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="b5843-317">`optional: true`: Soubor je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="b5843-317">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="b5843-318">`reloadOnChange: true`: Soubor se znovu načte při uložení změn.</span><span class="sxs-lookup"><span data-stu-id="b5843-318">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="b5843-319">Přečte [výchozí zprostředkovatele konfigurace](#default) před *MyConfig.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-319">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="b5843-320">Nastavení v nastavení *MyConfig.jspři* přepsání souborů ve výchozích poskytovatelích konfigurace, včetně [poskytovatele konfigurace proměnných prostředí](#evcp) a [poskytovatele konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b5843-320">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b5843-321">***Obvykle nechcete, aby vlastní*** soubor JSON přepsal hodnoty nastavené ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a v [poskytovateli konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b5843-321">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b5843-322">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="b5843-323">V předchozím kódu nastavení *MyConfig.js* v a *MyConfig*. `Environment` . soubory *JSON* :</span><span class="sxs-lookup"><span data-stu-id="b5843-323">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="b5843-324">Přepsat nastavení v *appsettings.jsna* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="b5843-324">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="b5843-325">Jsou přepsány nastavením ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a [poskytovatelem konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="b5843-325">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b5843-326">[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující *MyConfig.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="b5843-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="b5843-327">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="b5843-328">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b5843-328">File configuration provider</span></span>

<span data-ttu-id="b5843-329"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="b5843-329"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b5843-330">Následující poskytovatelé konfigurace jsou odvozeni z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="b5843-330">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="b5843-331">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="b5843-331">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b5843-332">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="b5843-332">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="b5843-333">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="b5843-333">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b5843-334">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="b5843-334">INI configuration provider</span></span>

<span data-ttu-id="b5843-335"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="b5843-335">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b5843-336">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-336">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="b5843-337">V předchozím kódu nastavení *MyIniConfig.ini* a *MyIniConfig*. `Environment` . soubory *ini* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="b5843-337">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="b5843-338">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-338">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="b5843-339">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="b5843-339">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b5843-340">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="b5843-340">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="b5843-341">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-341">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="b5843-342">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="b5843-342">XML configuration provider</span></span>

<span data-ttu-id="b5843-343"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="b5843-343">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b5843-344">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-344">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="b5843-345">V předchozím kódu nastavení *MyXMLFile.xml* a *MyXMLFile*. `Environment` . soubory *XML* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="b5843-345">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="b5843-346">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-346">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="b5843-347">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="b5843-347">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="b5843-348">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="b5843-348">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="b5843-349">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-349">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-350">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="b5843-350">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="b5843-351">Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b5843-351">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-352">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="b5843-352">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b5843-353">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="b5843-353">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b5843-354">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="b5843-354">key:attribute</span></span>
* <span data-ttu-id="b5843-355">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="b5843-355">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b5843-356">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="b5843-356">Key-per-file configuration provider</span></span>

<span data-ttu-id="b5843-357"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-357">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b5843-358">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-358">The key is the file name.</span></span> <span data-ttu-id="b5843-359">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-359">The value contains the file's contents.</span></span> <span data-ttu-id="b5843-360">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="b5843-360">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b5843-361">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b5843-361">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b5843-362">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="b5843-362">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b5843-363">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="b5843-363">Overloads permit specifying:</span></span>

* <span data-ttu-id="b5843-364">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="b5843-364">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b5843-365">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="b5843-365">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b5843-366">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="b5843-366">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b5843-367">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="b5843-367">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b5843-368">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="b5843-368">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="b5843-369">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b5843-369">Memory configuration provider</span></span>

<span data-ttu-id="b5843-370"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-370">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b5843-371">Následující kód přidá kolekci paměti do konfiguračního systému:</span><span class="sxs-lookup"><span data-stu-id="b5843-371">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="b5843-372">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-372">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-373">V předchozím kódu `config.AddInMemoryCollection(Dict)` se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="b5843-373">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="b5843-374">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="b5843-374">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="b5843-375">Viz [vazba pole](#boa) pro jiný příklad pomocí `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="b5843-375">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="b5843-376">GetValue</span><span class="sxs-lookup"><span data-stu-id="b5843-376">GetValue</span></span>

<span data-ttu-id="b5843-377">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:</span><span class="sxs-lookup"><span data-stu-id="b5843-377">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-378">Pokud v předchozím kódu `NumberKey` není v konfiguraci nalezeno, `99` je použita výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-378">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b5843-379">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="b5843-379">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b5843-380">V následujících příkladech zvažte následující *MySubsection.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="b5843-380">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="b5843-381">Následující kód přidá *MySubsection.js* do zprostředkovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-381">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="b5843-382">GetSection</span><span class="sxs-lookup"><span data-stu-id="b5843-382">GetSection</span></span>

<span data-ttu-id="b5843-383">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí dílčí část konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="b5843-383">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="b5843-384">Následující kód vrátí hodnoty pro `section1` :</span><span class="sxs-lookup"><span data-stu-id="b5843-384">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-385">Následující kód vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="b5843-385">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-386">`GetSection`nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="b5843-386">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b5843-387">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="b5843-387">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="b5843-388">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-388">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="b5843-389"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="b5843-389">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="b5843-390">GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="b5843-390">GetChildren and Exists</span></span>

<span data-ttu-id="b5843-391">Následující kód volá [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="b5843-391">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-392">Předchozí kód volá [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pro ověření, zda existuje oddíl:</span><span class="sxs-lookup"><span data-stu-id="b5843-392">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="b5843-393">Vazba pole</span><span class="sxs-lookup"><span data-stu-id="b5843-393">Bind an array</span></span>

<span data-ttu-id="b5843-394">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="b5843-394">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b5843-395">Libovolný formát pole, který zveřejňuje numerický klíčový segment, je schopný vytvořit vazbu pole k poli třídy [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="b5843-395">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="b5843-396">Zvažte *MyArray.jsna* [webu ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="b5843-396">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="b5843-397">Následující kód přidá *MyArray.js* do zprostředkovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-397">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="b5843-398">Následující kód přečte konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b5843-398">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-399">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="b5843-399">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="b5843-400">V předchozím výstupu má index 3 hodnotu odpovídající hodnotě `value40` `"4": "value40",` v *MyArray.js*.</span><span class="sxs-lookup"><span data-stu-id="b5843-400">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="b5843-401">Vazby vázaného pole jsou souvislé a nejsou vázané na index konfiguračního klíče.</span><span class="sxs-lookup"><span data-stu-id="b5843-401">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="b5843-402">Pořadač konfigurace nemůže svázat hodnoty null ani vytvářet položky null ve vázaných objektech.</span><span class="sxs-lookup"><span data-stu-id="b5843-402">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="b5843-403">Následující kód načte `array:entries` konfiguraci pomocí <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b5843-403">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="b5843-404">Následující kód přečte konfiguraci v `arrayDict` `Dictionary` a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b5843-404">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-405">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="b5843-405">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="b5843-406">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="b5843-406">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b5843-407">Když jsou data konfigurace obsahující pole svázané, používají se k iteraci konfiguračních dat při vytváření objektu pole indexy v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="b5843-407">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b5843-408">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="b5843-408">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b5843-409">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na instanci doplní `ArrayExample` jakýmkoli poskytovatelem konfigurace, který načte &num; dvojici klíč/hodnota indexu 3.</span><span class="sxs-lookup"><span data-stu-id="b5843-409">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="b5843-410">V souboru ukázka stažení zvažte následující *Value3.js* :</span><span class="sxs-lookup"><span data-stu-id="b5843-410">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="b5843-411">Následující kód obsahuje konfiguraci pro *Value3.jsv* a `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="b5843-411">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="b5843-412">Následující kód přečte předchozí konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b5843-412">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-413">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="b5843-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="b5843-414">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="b5843-414">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="b5843-415">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-415">Custom configuration provider</span></span>

<span data-ttu-id="b5843-416">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b5843-416">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b5843-417">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b5843-417">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b5843-418">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="b5843-418">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b5843-419">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-419">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b5843-420">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5843-420">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b5843-421">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="b5843-421">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b5843-422">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-422">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b5843-423">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="b5843-423">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b5843-424">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-424">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="b5843-425">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="b5843-425">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b5843-426">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-426">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="b5843-427">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="b5843-427">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b5843-428">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-428">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="b5843-429">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="b5843-429">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b5843-430">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="b5843-430">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="b5843-431">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="b5843-431">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="b5843-432">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-432">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="b5843-433">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5843-433">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b5843-434">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-434">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="b5843-435">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-435">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="b5843-436">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="b5843-436">Access configuration in Startup</span></span>

<span data-ttu-id="b5843-437">Následující kód zobrazuje konfigurační data v `Startup` metodách:</span><span class="sxs-lookup"><span data-stu-id="b5843-437">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="b5843-438">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b5843-438">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="b5843-439">Přístup ke konfiguraci na Razor stránkách</span><span class="sxs-lookup"><span data-stu-id="b5843-439">Access configuration in Razor Pages</span></span>

<span data-ttu-id="b5843-440">Následující kód zobrazuje konfigurační data na Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="b5843-440">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="b5843-441">V následujícím kódu `MyOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="b5843-441">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="b5843-442">Následující kód používá [`@inject`](xref:mvc/views/razor#inject) Razor direktivu k vyřešení a zobrazení hodnot možností:</span><span class="sxs-lookup"><span data-stu-id="b5843-442">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="b5843-443">Přístup ke konfiguraci v souboru zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="b5843-443">Access configuration in a MVC view file</span></span>

<span data-ttu-id="b5843-444">Následující kód zobrazuje konfigurační data v zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="b5843-444">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="b5843-445">Konfigurace možností s delegátem</span><span class="sxs-lookup"><span data-stu-id="b5843-445">Configure options with a delegate</span></span>

<span data-ttu-id="b5843-446">Možnosti nakonfigurované v delegátech přepíší hodnoty nastavené ve zprostředkovatelích konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-446">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="b5843-447">Konfigurace možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b5843-447">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="b5843-448">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je služba přidána do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="b5843-448">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="b5843-449">Pomocí delegáta konfiguruje hodnoty pro `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="b5843-449">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="b5843-450">Následující kód zobrazí hodnoty možností:</span><span class="sxs-lookup"><span data-stu-id="b5843-450">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="b5843-451">V předchozím příkladu `Option1` jsou hodnoty a `Option2` zadány v *appsettings.js* a poté přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="b5843-451">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="b5843-452">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="b5843-452">Host versus app configuration</span></span>

<span data-ttu-id="b5843-453">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="b5843-453">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b5843-454">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="b5843-454">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b5843-455">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b5843-455">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b5843-456">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="b5843-456">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="b5843-457">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="b5843-457">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="b5843-458">Výchozí konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="b5843-458">Default host configuration</span></span>

<span data-ttu-id="b5843-459">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="b5843-459">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="b5843-460">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="b5843-460">Host configuration is provided from:</span></span>
  * <span data-ttu-id="b5843-461">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b5843-461">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="b5843-462">Předpona ( `DOTNET_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-462">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="b5843-463">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-463">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="b5843-464">Je navázána výchozí konfigurace webového hostitele ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="b5843-464">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="b5843-465">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-465">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="b5843-466">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="b5843-466">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="b5843-467">Přidejte middleware předávaných hlaviček, pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="b5843-467">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="b5843-468">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="b5843-468">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="b5843-469">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-469">Other configuration</span></span>

<span data-ttu-id="b5843-470">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="b5843-470">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="b5843-471">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="b5843-471">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="b5843-472">*launch.jsna* / *launchSettings.jsv* nástroji jsou konfigurační soubory nástroje pro vývojové prostředí popsané níže:</span><span class="sxs-lookup"><span data-stu-id="b5843-472">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="b5843-473">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="b5843-473">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="b5843-474">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="b5843-474">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="b5843-475">*web.config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b5843-475">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="b5843-476">Proměnné prostředí nastavené v *launchSettings.jspři* přepisování nastavení v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="b5843-476">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="b5843-477">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="b5843-477">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b5843-478">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="b5843-478">Add configuration from an external assembly</span></span>

<span data-ttu-id="b5843-479"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-479">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b5843-480">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b5843-480">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5843-481">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b5843-481">Additional resources</span></span>

* [<span data-ttu-id="b5843-482">Zdrojový kód konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-482">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b5843-483">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="b5843-483">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="b5843-484">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-484">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="b5843-485">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5843-485">Azure Key Vault</span></span>
* <span data-ttu-id="b5843-486">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="b5843-486">Azure App Configuration</span></span>
* <span data-ttu-id="b5843-487">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-487">Command-line arguments</span></span>
* <span data-ttu-id="b5843-488">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="b5843-488">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b5843-489">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="b5843-489">Directory files</span></span>
* <span data-ttu-id="b5843-490">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-490">Environment variables</span></span>
* <span data-ttu-id="b5843-491">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="b5843-491">In-memory .NET objects</span></span>
* <span data-ttu-id="b5843-492">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="b5843-492">Settings files</span></span>

<span data-ttu-id="b5843-493">Do [Microsoft. AspNetCore. app metapackageu](xref:fundamentals/metapackage-app)patří konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)).</span><span class="sxs-lookup"><span data-stu-id="b5843-493">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b5843-494">Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="b5843-494">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="b5843-495">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b5843-495">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="b5843-496">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="b5843-496">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="b5843-497">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b5843-497">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b5843-498">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5843-498">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="b5843-499">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="b5843-499">Host versus app configuration</span></span>

<span data-ttu-id="b5843-500">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="b5843-500">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b5843-501">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="b5843-501">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b5843-502">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b5843-502">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b5843-503">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="b5843-503">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="b5843-504">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="b5843-504">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="b5843-505">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-505">Other configuration</span></span>

<span data-ttu-id="b5843-506">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="b5843-506">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="b5843-507">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="b5843-507">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="b5843-508">*launch.jsna* / *launchSettings.jsv* nástroji jsou konfigurační soubory nástroje pro vývojové prostředí popsané níže:</span><span class="sxs-lookup"><span data-stu-id="b5843-508">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="b5843-509">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="b5843-509">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="b5843-510">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="b5843-510">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="b5843-511">*web.config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b5843-511">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="b5843-512">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="b5843-512">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="b5843-513">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-513">Default configuration</span></span>

<span data-ttu-id="b5843-514">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="b5843-514">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="b5843-515">`CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="b5843-515">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="b5843-516">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="b5843-516">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="b5843-517">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b5843-517">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="b5843-518">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="b5843-518">Host configuration is provided from:</span></span>
  * <span data-ttu-id="b5843-519">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-519">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="b5843-520">Předpona ( `ASPNETCORE_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-520">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="b5843-521">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-521">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="b5843-522">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="b5843-522">App configuration is provided from:</span></span>
  * <span data-ttu-id="b5843-523">*appsettings.js* používání [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-523">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b5843-524">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-524">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b5843-525">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v `Development` prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="b5843-525">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="b5843-526">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-526">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="b5843-527">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b5843-527">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="b5843-528">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="b5843-528">Security</span></span>

<span data-ttu-id="b5843-529">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="b5843-529">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="b5843-530">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="b5843-530">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="b5843-531">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="b5843-531">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b5843-532">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="b5843-532">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b5843-533">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b5843-533">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="b5843-534"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="b5843-534"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="b5843-535">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="b5843-535">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="b5843-536">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b5843-536">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b5843-537">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-537">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="b5843-538">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b5843-538">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b5843-539">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="b5843-539">Hierarchical configuration data</span></span>

<span data-ttu-id="b5843-540">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="b5843-540">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b5843-541">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="b5843-541">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="b5843-542">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-542">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="b5843-543">Oddíly a klíče jsou shrnuty s použitím dvojtečky ( `:` ) pro zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="b5843-543">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="b5843-544">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-544">section0:key0</span></span>
* <span data-ttu-id="b5843-545">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-545">section0:key1</span></span>
* <span data-ttu-id="b5843-546">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-546">section1:key0</span></span>
* <span data-ttu-id="b5843-547">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-547">section1:key1</span></span>

<span data-ttu-id="b5843-548"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="b5843-548"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b5843-549">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="b5843-549">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="b5843-550">Konvence</span><span class="sxs-lookup"><span data-stu-id="b5843-550">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="b5843-551">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="b5843-551">Sources and providers</span></span>

<span data-ttu-id="b5843-552">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-552">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="b5843-553">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="b5843-553">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="b5843-554">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="b5843-554">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="b5843-555"><xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b5843-556"><xref:Microsoft.Extensions.Configuration.IConfiguration>lze vložit do Razor stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pro získání konfigurace pro třídu.</span><span class="sxs-lookup"><span data-stu-id="b5843-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="b5843-557">V následujících příkladech se `_config` pole používá pro přístup k hodnotám konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-557">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="b5843-558">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="b5843-558">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="b5843-559">Klíče</span><span class="sxs-lookup"><span data-stu-id="b5843-559">Keys</span></span>

<span data-ttu-id="b5843-560">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="b5843-560">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="b5843-561">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b5843-561">Keys are case-insensitive.</span></span> <span data-ttu-id="b5843-562">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="b5843-562">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b5843-563">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="b5843-563">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="b5843-564">Další informace o duplicitních klíčích JSON najdete v [tomto problému GitHubu](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="b5843-564">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="b5843-565">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="b5843-565">Hierarchical keys</span></span>
  * <span data-ttu-id="b5843-566">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b5843-566">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b5843-567">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b5843-567">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b5843-568">Dvojité podtržítko ( `__` ) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="b5843-568">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="b5843-569">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="b5843-569">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b5843-570">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-570">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b5843-571"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="b5843-571">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b5843-572">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="b5843-572">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="b5843-573">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="b5843-573">Values</span></span>

<span data-ttu-id="b5843-574">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="b5843-574">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="b5843-575">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="b5843-575">Values are strings.</span></span>
* <span data-ttu-id="b5843-576">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="b5843-576">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="b5843-577">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="b5843-577">Providers</span></span>

<span data-ttu-id="b5843-578">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-578">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="b5843-579">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="b5843-579">Provider</span></span> | <span data-ttu-id="b5843-580">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="b5843-580">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b5843-581">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="b5843-581">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b5843-582">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5843-582">Azure Key Vault</span></span> |
| <span data-ttu-id="b5843-583">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="b5843-583">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="b5843-584">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="b5843-584">Azure App Configuration</span></span> |
| [<span data-ttu-id="b5843-585">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-585">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b5843-586">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-586">Command-line parameters</span></span> |
| [<span data-ttu-id="b5843-587">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-587">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b5843-588">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="b5843-588">Custom source</span></span> |
| [<span data-ttu-id="b5843-589">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-589">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b5843-590">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-590">Environment variables</span></span> |
| [<span data-ttu-id="b5843-591">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b5843-591">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b5843-592">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b5843-592">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b5843-593">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="b5843-593">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b5843-594">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="b5843-594">Directory files</span></span> |
| [<span data-ttu-id="b5843-595">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b5843-595">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b5843-596">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="b5843-596">In-memory collections</span></span> |
| <span data-ttu-id="b5843-597">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="b5843-597">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b5843-598">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="b5843-598">File in the user profile directory</span></span> |

<span data-ttu-id="b5843-599">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5843-599">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="b5843-600">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="b5843-600">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="b5843-601">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="b5843-601">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="b5843-602">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="b5843-602">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="b5843-603">Soubory (*appsettings.jsna*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace</span><span class="sxs-lookup"><span data-stu-id="b5843-603">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="b5843-604">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5843-604">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="b5843-605">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="b5843-605">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="b5843-606">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-606">Environment variables</span></span>
1. <span data-ttu-id="b5843-607">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-607">Command-line arguments</span></span>

<span data-ttu-id="b5843-608">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="b5843-608">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="b5843-609">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5843-609">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b5843-610">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="b5843-610">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="b5843-611">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="b5843-611">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="b5843-612">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> na tvůrce hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="b5843-612">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="b5843-613">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b5843-613">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b5843-614">Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s automaticky přidanými uživateli `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="b5843-614">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="b5843-615">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-615">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="b5843-616">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte jako `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="b5843-616">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="b5843-617">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="b5843-617">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="b5843-618">Chcete-li odebrat poskytovatele přidaných nástrojem `CreateDefaultBuilder` , zavolejte nejprve [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="b5843-618">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="b5843-619">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="b5843-619">Consume configuration during app startup</span></span>

<span data-ttu-id="b5843-620">Konfigurace dodaná do aplikace v nástroji `ConfigureAppConfiguration` je k dispozici během spouštění aplikace, včetně `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b5843-620">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b5843-621">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="b5843-621">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="b5843-622">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b5843-622">Command-line Configuration Provider</span></span>

<span data-ttu-id="b5843-623"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="b5843-623">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="b5843-624">Chcete-li aktivovat konfiguraci příkazového řádku, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> je metoda rozšíření volána na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b5843-624">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b5843-625">`AddCommandLine`je automaticky volána `CreateDefaultBuilder(string [])` , když je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="b5843-625">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="b5843-626">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="b5843-626">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b5843-627">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="b5843-627">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b5843-628">Volitelná konfigurace z *appsettings.jsna* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b5843-628">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="b5843-629">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-629">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b5843-630">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-630">Environment variables.</span></span>

<span data-ttu-id="b5843-631">`CreateDefaultBuilder`přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="b5843-631">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="b5843-632">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="b5843-632">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="b5843-633">`CreateDefaultBuilder`funguje, když je hostitel vytvořen.</span><span class="sxs-lookup"><span data-stu-id="b5843-633">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="b5843-634">Proto může konfigurace z příkazového řádku aktivované nástrojem `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="b5843-634">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="b5843-635">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány pomocí `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5843-635">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b5843-636">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte na `AddCommandLine` Poslední.</span><span class="sxs-lookup"><span data-stu-id="b5843-636">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="b5843-637">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b5843-637">**Example**</span></span>

<span data-ttu-id="b5843-638">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="b5843-638">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="b5843-639">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="b5843-639">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="b5843-640">Zadejte do příkazu argument příkazového řádku `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="b5843-640">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="b5843-641">Po spuštění aplikace otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="b5843-641">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b5843-642">Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k dispozici pro příkaz `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="b5843-642">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="b5843-643">Arguments</span><span class="sxs-lookup"><span data-stu-id="b5843-643">Arguments</span></span>

<span data-ttu-id="b5843-644">Hodnota musí následovat po znaménku rovná se ( `=` ), nebo klíč musí obsahovat předponu ( `--` nebo `/` ), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="b5843-644">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="b5843-645">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="b5843-645">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="b5843-646">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="b5843-646">Key prefix</span></span>               | <span data-ttu-id="b5843-647">Příklad</span><span class="sxs-lookup"><span data-stu-id="b5843-647">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="b5843-648">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="b5843-648">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="b5843-649">Dvě pomlčky ( `--` )</span><span class="sxs-lookup"><span data-stu-id="b5843-649">Two dashes (`--`)</span></span>        | <span data-ttu-id="b5843-650">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="b5843-650">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="b5843-651">Lomítko ( `/` )</span><span class="sxs-lookup"><span data-stu-id="b5843-651">Forward slash (`/`)</span></span>      | <span data-ttu-id="b5843-652">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="b5843-652">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="b5843-653">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="b5843-653">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="b5843-654">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="b5843-654">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="b5843-655">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="b5843-655">Switch mappings</span></span>

<span data-ttu-id="b5843-656">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="b5843-656">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="b5843-657">Při ručním sestavování konfigurace pomocí nástroje <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> Zadejte slovník přepínačů pro vložení do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="b5843-657">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b5843-658">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b5843-658">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b5843-659">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-659">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b5843-660">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="b5843-660">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b5843-661">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="b5843-661">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b5843-662">Přepínače musí začínat spojovníkem ( `-` ) nebo dvojitou čárkou ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="b5843-662">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="b5843-663">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="b5843-663">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="b5843-664">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="b5843-664">Create a switch mappings dictionary.</span></span> <span data-ttu-id="b5843-665">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="b5843-665">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="b5843-666">Po sestavení hostitele zavolejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="b5843-666">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="b5843-667">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="b5843-667">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="b5843-668">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5843-668">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b5843-669">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="b5843-669">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="b5843-670">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b5843-670">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="b5843-671">Klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-671">Key</span></span>       | <span data-ttu-id="b5843-672">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b5843-672">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="b5843-673">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="b5843-673">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="b5843-674">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b5843-674">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="b5843-675">Klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-675">Key</span></span>               | <span data-ttu-id="b5843-676">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b5843-676">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="b5843-677">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-677">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="b5843-678"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="b5843-678">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="b5843-679">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b5843-679">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b5843-680">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-680">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="b5843-681">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b5843-681">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="b5843-682">`AddEnvironmentVariables`slouží k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán.</span><span class="sxs-lookup"><span data-stu-id="b5843-682">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="b5843-683">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="b5843-683">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b5843-684">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="b5843-684">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b5843-685">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="b5843-685">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="b5843-686">Volitelná konfigurace z *appsettings.jsna* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="b5843-686">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="b5843-687">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-687">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b5843-688">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b5843-688">Command-line arguments.</span></span>

<span data-ttu-id="b5843-689">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="b5843-689">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="b5843-690">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="b5843-690">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="b5843-691">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu:</span><span class="sxs-lookup"><span data-stu-id="b5843-691">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="b5843-692">Zavolejte `AddEnvironmentVariables` jako poslední, pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="b5843-692">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="b5843-693">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b5843-693">**Example**</span></span>

<span data-ttu-id="b5843-694">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="b5843-694">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="b5843-695">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b5843-695">Run the sample app.</span></span> <span data-ttu-id="b5843-696">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="b5843-696">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b5843-697">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="b5843-697">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="b5843-698">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.</span><span class="sxs-lookup"><span data-stu-id="b5843-698">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="b5843-699">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-699">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="b5843-700">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-700">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="b5843-701">K vystavení všech proměnných prostředí, které jsou k dispozici pro aplikaci, změňte na `FilteredConfiguration` *stránkách/index. cshtml. cs* následující:</span><span class="sxs-lookup"><span data-stu-id="b5843-701">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="b5843-702">Předpony</span><span class="sxs-lookup"><span data-stu-id="b5843-702">Prefixes</span></span>

<span data-ttu-id="b5843-703">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="b5843-703">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="b5843-704">Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_` , zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-704">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="b5843-705">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="b5843-705">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="b5843-706">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-706">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="b5843-707">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="b5843-707">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b5843-708">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="b5843-708">**Connection string prefixes**</span></span>

<span data-ttu-id="b5843-709">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-709">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b5843-710">Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="b5843-710">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b5843-711">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="b5843-711">Connection string prefix</span></span> | <span data-ttu-id="b5843-712">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="b5843-712">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b5843-713">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b5843-713">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b5843-714">MySQL</span><span class="sxs-lookup"><span data-stu-id="b5843-714">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b5843-715">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="b5843-715">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b5843-716">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b5843-716">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b5843-717">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="b5843-717">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b5843-718">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="b5843-718">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b5843-719">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="b5843-719">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b5843-720">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b5843-720">Environment variable key</span></span> | <span data-ttu-id="b5843-721">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="b5843-721">Converted configuration key</span></span> | <span data-ttu-id="b5843-722">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="b5843-722">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b5843-723">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="b5843-723">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b5843-724">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b5843-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b5843-725">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b5843-725">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b5843-726">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b5843-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b5843-727">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b5843-727">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="b5843-728">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="b5843-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="b5843-729">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b5843-729">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="b5843-730">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b5843-730">**Example**</span></span>

<span data-ttu-id="b5843-731">Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="b5843-731">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="b5843-732">Název: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="b5843-732">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="b5843-733">Hodnota: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="b5843-733">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="b5843-734">Pokud `IConfiguration` je vloženo a přiřazeno k poli s názvem `_config` , přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="b5843-734">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="b5843-735">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b5843-735">File Configuration Provider</span></span>

<span data-ttu-id="b5843-736"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="b5843-736"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b5843-737">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="b5843-737">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="b5843-738">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="b5843-738">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b5843-739">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="b5843-739">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="b5843-740">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="b5843-740">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b5843-741">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="b5843-741">INI Configuration Provider</span></span>

<span data-ttu-id="b5843-742"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="b5843-742">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b5843-743">Chcete-li aktivovat konfiguraci souboru INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b5843-743">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b5843-744">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="b5843-744">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="b5843-745">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="b5843-745">Overloads permit specifying:</span></span>

* <span data-ttu-id="b5843-746">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="b5843-746">Whether the file is optional.</span></span>
* <span data-ttu-id="b5843-747">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-747">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b5843-748"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-748">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b5843-749">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="b5843-749">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b5843-750">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="b5843-750">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="b5843-751">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="b5843-751">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b5843-752">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-752">section0:key0</span></span>
* <span data-ttu-id="b5843-753">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-753">section0:key1</span></span>
* <span data-ttu-id="b5843-754">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-754">section1:subsection:key</span></span>
* <span data-ttu-id="b5843-755">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-755">section2:subsection0:key</span></span>
* <span data-ttu-id="b5843-756">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-756">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="b5843-757">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="b5843-757">JSON Configuration Provider</span></span>

<span data-ttu-id="b5843-758"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="b5843-758">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="b5843-759">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b5843-759">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b5843-760">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="b5843-760">Overloads permit specifying:</span></span>

* <span data-ttu-id="b5843-761">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="b5843-761">Whether the file is optional.</span></span>
* <span data-ttu-id="b5843-762">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-762">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b5843-763"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-763">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b5843-764">`AddJsonFile`se automaticky volá dvakrát při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5843-764">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b5843-765">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="b5843-765">The method is called to load configuration from:</span></span>

* <span data-ttu-id="b5843-766">*appsettings.js*: Tento soubor je nejprve přečten.</span><span class="sxs-lookup"><span data-stu-id="b5843-766">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="b5843-767">Verze prostředí souboru může přepsat hodnoty poskytnuté *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-767">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="b5843-768">*appSettings. {Environment}. JSON*: verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b5843-768">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="b5843-769">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="b5843-769">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="b5843-770">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="b5843-770">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b5843-771">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-771">Environment variables.</span></span>
* <span data-ttu-id="b5843-772">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-772">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="b5843-773">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b5843-773">Command-line arguments.</span></span>

<span data-ttu-id="b5843-774">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="b5843-774">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="b5843-775">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="b5843-775">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="b5843-776">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appsettings.jsv* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b5843-776">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b5843-777">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b5843-777">**Example**</span></span>

<span data-ttu-id="b5843-778">Ukázková aplikace využívá ke sestavení hostitele výhodu technologie statického usnadnění `CreateDefaultBuilder` , která zahrnuje dvě volání `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="b5843-778">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="b5843-779">První volání `AddJsonFile` načtení konfigurace z *appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="b5843-779">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="b5843-780">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b5843-780">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="b5843-781">Pro *appsettings.Development.js* v ukázkové aplikaci se načte následující soubor:</span><span class="sxs-lookup"><span data-stu-id="b5843-781">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="b5843-782">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b5843-782">Run the sample app.</span></span> <span data-ttu-id="b5843-783">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="b5843-783">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b5843-784">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-784">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="b5843-785">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-785">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="b5843-786">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="b5843-786">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="b5843-787">Otevřete *vlastnosti/launchSettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-787">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="b5843-788">V `ConfigurationSample` profilu změňte hodnotu `ASPNETCORE_ENVIRONMENT` proměnné prostředí na `Production` .</span><span class="sxs-lookup"><span data-stu-id="b5843-788">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="b5843-789">Uložte soubor a spusťte aplikaci `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b5843-789">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="b5843-790">Nastavení v *appsettings.Development.js* už nepřepisují nastavení v *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="b5843-790">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="b5843-791">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Warning` .</span><span class="sxs-lookup"><span data-stu-id="b5843-791">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="b5843-792">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="b5843-792">XML Configuration Provider</span></span>

<span data-ttu-id="b5843-793"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="b5843-793">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b5843-794">Chcete-li aktivovat konfiguraci souboru XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b5843-794">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b5843-795">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="b5843-795">Overloads permit specifying:</span></span>

* <span data-ttu-id="b5843-796">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="b5843-796">Whether the file is optional.</span></span>
* <span data-ttu-id="b5843-797">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-797">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b5843-798"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-798">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b5843-799">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-799">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="b5843-800">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-800">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="b5843-801">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="b5843-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="b5843-802">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="b5843-802">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="b5843-803">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="b5843-803">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b5843-804">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-804">section0:key0</span></span>
* <span data-ttu-id="b5843-805">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-805">section0:key1</span></span>
* <span data-ttu-id="b5843-806">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-806">section1:key0</span></span>
* <span data-ttu-id="b5843-807">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-807">section1:key1</span></span>

<span data-ttu-id="b5843-808">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="b5843-808">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="b5843-809">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="b5843-809">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b5843-810">oddíl: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="b5843-810">section:section0:key:key0</span></span>
* <span data-ttu-id="b5843-811">oddíl: section0: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-811">section:section0:key:key1</span></span>
* <span data-ttu-id="b5843-812">oddíl: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="b5843-812">section:section1:key:key0</span></span>
* <span data-ttu-id="b5843-813">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-813">section:section1:key:key1</span></span>

<span data-ttu-id="b5843-814">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="b5843-814">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b5843-815">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="b5843-815">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b5843-816">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="b5843-816">key:attribute</span></span>
* <span data-ttu-id="b5843-817">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="b5843-817">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b5843-818">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="b5843-818">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="b5843-819"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-819">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b5843-820">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-820">The key is the file name.</span></span> <span data-ttu-id="b5843-821">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="b5843-821">The value contains the file's contents.</span></span> <span data-ttu-id="b5843-822">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="b5843-822">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b5843-823">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b5843-823">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b5843-824">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="b5843-824">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b5843-825">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="b5843-825">Overloads permit specifying:</span></span>

* <span data-ttu-id="b5843-826">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="b5843-826">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b5843-827">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="b5843-827">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b5843-828">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="b5843-828">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b5843-829">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="b5843-829">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b5843-830">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="b5843-830">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="b5843-831">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b5843-831">Memory Configuration Provider</span></span>

<span data-ttu-id="b5843-832"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-832">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b5843-833">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b5843-833">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b5843-834">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="b5843-834">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="b5843-835">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b5843-836">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="b5843-836">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="b5843-837">Slovník se používá s voláním k `AddInMemoryCollection` poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-837">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="b5843-838">GetValue</span><span class="sxs-lookup"><span data-stu-id="b5843-838">GetValue</span></span>

<span data-ttu-id="b5843-839">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="b5843-839">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="b5843-840">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b5843-840">An overload accepts a default value.</span></span>

<span data-ttu-id="b5843-841">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="b5843-841">The following example:</span></span>

* <span data-ttu-id="b5843-842">Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="b5843-842">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="b5843-843">Pokud se `NumberKey` v konfiguračních klíčích nenajde, použije se výchozí hodnota `99` .</span><span class="sxs-lookup"><span data-stu-id="b5843-843">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="b5843-844">Zadá hodnotu jako `int` .</span><span class="sxs-lookup"><span data-stu-id="b5843-844">Types the value as an `int`.</span></span>
* <span data-ttu-id="b5843-845">Ukládá hodnotu `NumberConfig` vlastnosti pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="b5843-845">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b5843-846">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="b5843-846">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b5843-847">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="b5843-847">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="b5843-848">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="b5843-848">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="b5843-849">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-849">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="b5843-850">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-850">section0:key0</span></span>
* <span data-ttu-id="b5843-851">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-851">section0:key1</span></span>
* <span data-ttu-id="b5843-852">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-852">section1:key0</span></span>
* <span data-ttu-id="b5843-853">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-853">section1:key1</span></span>
* <span data-ttu-id="b5843-854">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-854">section2:subsection0:key0</span></span>
* <span data-ttu-id="b5843-855">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-855">section2:subsection0:key1</span></span>
* <span data-ttu-id="b5843-856">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="b5843-856">section2:subsection1:key0</span></span>
* <span data-ttu-id="b5843-857">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="b5843-857">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="b5843-858">GetSection</span><span class="sxs-lookup"><span data-stu-id="b5843-858">GetSection</span></span>

<span data-ttu-id="b5843-859">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="b5843-859">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="b5843-860">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1` , zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="b5843-860">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="b5843-861">`configSection`Hodnota neobsahuje pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="b5843-861">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="b5843-862">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0` , zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="b5843-862">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="b5843-863">`GetSection`nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="b5843-863">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b5843-864">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="b5843-864">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="b5843-865">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="b5843-865">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="b5843-866"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="b5843-866">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="b5843-867">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="b5843-867">GetChildren</span></span>

<span data-ttu-id="b5843-868">Volání [IConfiguration. Getchildrens](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="b5843-868">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="b5843-869">Existuje</span><span class="sxs-lookup"><span data-stu-id="b5843-869">Exists</span></span>

<span data-ttu-id="b5843-870">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="b5843-870">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="b5843-871">Vzhledem k ukázkovým datům `sectionExists` je `false` to proto, že `section2:subsection2` v konfiguračních datech není oddíl.</span><span class="sxs-lookup"><span data-stu-id="b5843-871">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="b5843-872">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="b5843-872">Bind to an object graph</span></span>

<span data-ttu-id="b5843-873"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="b5843-873"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="b5843-874">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="b5843-874">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="b5843-875">Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="b5843-875">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="b5843-876">Ukázková aplikace má *tvshow.xml* soubor obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="b5843-876">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="b5843-877">Konfigurace je svázána s celým `TvShow` grafem objektů pomocí `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="b5843-877">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="b5843-878">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="b5843-878">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="b5843-879">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="b5843-879">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="b5843-880">`Get<T>`je pohodlnější než použití `Bind` .</span><span class="sxs-lookup"><span data-stu-id="b5843-880">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="b5843-881">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem:</span><span class="sxs-lookup"><span data-stu-id="b5843-881">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b5843-882">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="b5843-882">Bind an array to a class</span></span>

<span data-ttu-id="b5843-883">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="b5843-883">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="b5843-884"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="b5843-884">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b5843-885">Libovolný formát pole, který zveřejňuje numerický klíčový segment ( `:0:` , `:1:` ,), &hellip; `:{n}:` je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="b5843-885">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="b5843-886">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="b5843-886">Binding is provided by convention.</span></span> <span data-ttu-id="b5843-887">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="b5843-887">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="b5843-888">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="b5843-888">**In-memory array processing**</span></span>

<span data-ttu-id="b5843-889">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b5843-889">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="b5843-890">Klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-890">Key</span></span>             | <span data-ttu-id="b5843-891">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b5843-891">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b5843-892">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="b5843-892">array:entries:0</span></span> | <span data-ttu-id="b5843-893">value0</span><span class="sxs-lookup"><span data-stu-id="b5843-893">value0</span></span> |
| <span data-ttu-id="b5843-894">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="b5843-894">array:entries:1</span></span> | <span data-ttu-id="b5843-895">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="b5843-895">value1</span></span> |
| <span data-ttu-id="b5843-896">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="b5843-896">array:entries:2</span></span> | <span data-ttu-id="b5843-897">Argument</span><span class="sxs-lookup"><span data-stu-id="b5843-897">value2</span></span> |
| <span data-ttu-id="b5843-898">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="b5843-898">array:entries:4</span></span> | <span data-ttu-id="b5843-899">value4</span><span class="sxs-lookup"><span data-stu-id="b5843-899">value4</span></span> |
| <span data-ttu-id="b5843-900">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="b5843-900">array:entries:5</span></span> | <span data-ttu-id="b5843-901">value5</span><span class="sxs-lookup"><span data-stu-id="b5843-901">value5</span></span> |

<span data-ttu-id="b5843-902">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="b5843-902">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="b5843-903">Pole přeskočí hodnotu indexu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="b5843-903">The array skips a value for index &num;3.</span></span> <span data-ttu-id="b5843-904">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="b5843-904">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="b5843-905">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="b5843-905">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="b5843-906">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="b5843-906">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="b5843-907">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lze také použít syntaxi, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="b5843-907">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="b5843-908">Vázaný objekt, instance `ArrayExample` , přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-908">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="b5843-909">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="b5843-909">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b5843-910">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="b5843-910">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b5843-911">0</span><span class="sxs-lookup"><span data-stu-id="b5843-911">0</span></span>                            | <span data-ttu-id="b5843-912">value0</span><span class="sxs-lookup"><span data-stu-id="b5843-912">value0</span></span>                       |
| <span data-ttu-id="b5843-913">1</span><span class="sxs-lookup"><span data-stu-id="b5843-913">1</span></span>                            | <span data-ttu-id="b5843-914">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="b5843-914">value1</span></span>                       |
| <span data-ttu-id="b5843-915">2</span><span class="sxs-lookup"><span data-stu-id="b5843-915">2</span></span>                            | <span data-ttu-id="b5843-916">Argument</span><span class="sxs-lookup"><span data-stu-id="b5843-916">value2</span></span>                       |
| <span data-ttu-id="b5843-917">3</span><span class="sxs-lookup"><span data-stu-id="b5843-917">3</span></span>                            | <span data-ttu-id="b5843-918">value4</span><span class="sxs-lookup"><span data-stu-id="b5843-918">value4</span></span>                       |
| <span data-ttu-id="b5843-919">4</span><span class="sxs-lookup"><span data-stu-id="b5843-919">4</span></span>                            | <span data-ttu-id="b5843-920">value5</span><span class="sxs-lookup"><span data-stu-id="b5843-920">value5</span></span>                       |

<span data-ttu-id="b5843-921">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="b5843-921">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b5843-922">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="b5843-922">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b5843-923">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="b5843-923">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b5843-924">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na `ArrayExample` instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="b5843-924">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="b5843-925">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b5843-925">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="b5843-926">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="b5843-926">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="b5843-927">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="b5843-927">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="b5843-928">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-928">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="b5843-929">Klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-929">Key</span></span>             | <span data-ttu-id="b5843-930">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b5843-930">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b5843-931">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="b5843-931">array:entries:3</span></span> | <span data-ttu-id="b5843-932">hodnota3</span><span class="sxs-lookup"><span data-stu-id="b5843-932">value3</span></span> |

<span data-ttu-id="b5843-933">Je-li `ArrayExample` instance třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro index &num; 3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b5843-933">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="b5843-934">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="b5843-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b5843-935">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="b5843-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b5843-936">0</span><span class="sxs-lookup"><span data-stu-id="b5843-936">0</span></span>                            | <span data-ttu-id="b5843-937">value0</span><span class="sxs-lookup"><span data-stu-id="b5843-937">value0</span></span>                       |
| <span data-ttu-id="b5843-938">1</span><span class="sxs-lookup"><span data-stu-id="b5843-938">1</span></span>                            | <span data-ttu-id="b5843-939">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="b5843-939">value1</span></span>                       |
| <span data-ttu-id="b5843-940">2</span><span class="sxs-lookup"><span data-stu-id="b5843-940">2</span></span>                            | <span data-ttu-id="b5843-941">Argument</span><span class="sxs-lookup"><span data-stu-id="b5843-941">value2</span></span>                       |
| <span data-ttu-id="b5843-942">3</span><span class="sxs-lookup"><span data-stu-id="b5843-942">3</span></span>                            | <span data-ttu-id="b5843-943">hodnota3</span><span class="sxs-lookup"><span data-stu-id="b5843-943">value3</span></span>                       |
| <span data-ttu-id="b5843-944">4</span><span class="sxs-lookup"><span data-stu-id="b5843-944">4</span></span>                            | <span data-ttu-id="b5843-945">value4</span><span class="sxs-lookup"><span data-stu-id="b5843-945">value4</span></span>                       |
| <span data-ttu-id="b5843-946">5</span><span class="sxs-lookup"><span data-stu-id="b5843-946">5</span></span>                            | <span data-ttu-id="b5843-947">value5</span><span class="sxs-lookup"><span data-stu-id="b5843-947">value5</span></span>                       |

<span data-ttu-id="b5843-948">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="b5843-948">**JSON array processing**</span></span>

<span data-ttu-id="b5843-949">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="b5843-949">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="b5843-950">V následujícím konfiguračním souboru `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="b5843-950">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="b5843-951">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="b5843-951">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="b5843-952">Klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-952">Key</span></span>                     | <span data-ttu-id="b5843-953">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b5843-953">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="b5843-954">json_array: klíč</span><span class="sxs-lookup"><span data-stu-id="b5843-954">json_array:key</span></span>          | <span data-ttu-id="b5843-955">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="b5843-955">valueA</span></span> |
| <span data-ttu-id="b5843-956">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="b5843-956">json_array:subsection:0</span></span> | <span data-ttu-id="b5843-957">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="b5843-957">valueB</span></span> |
| <span data-ttu-id="b5843-958">json_array: pododdíl: 1</span><span class="sxs-lookup"><span data-stu-id="b5843-958">json_array:subsection:1</span></span> | <span data-ttu-id="b5843-959">valueC</span><span class="sxs-lookup"><span data-stu-id="b5843-959">valueC</span></span> |
| <span data-ttu-id="b5843-960">json_array: pododdíl: 2</span><span class="sxs-lookup"><span data-stu-id="b5843-960">json_array:subsection:2</span></span> | <span data-ttu-id="b5843-961">s</span><span class="sxs-lookup"><span data-stu-id="b5843-961">valueD</span></span> |

<span data-ttu-id="b5843-962">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="b5843-962">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="b5843-963">Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu `valueA` .</span><span class="sxs-lookup"><span data-stu-id="b5843-963">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="b5843-964">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="b5843-964">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="b5843-965">`JsonArrayExample.Subsection`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="b5843-965">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="b5843-966">`JsonArrayExample.Subsection`Osa</span><span class="sxs-lookup"><span data-stu-id="b5843-966">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="b5843-967">0</span><span class="sxs-lookup"><span data-stu-id="b5843-967">0</span></span>                                   | <span data-ttu-id="b5843-968">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="b5843-968">valueB</span></span>                              |
| <span data-ttu-id="b5843-969">1</span><span class="sxs-lookup"><span data-stu-id="b5843-969">1</span></span>                                   | <span data-ttu-id="b5843-970">valueC</span><span class="sxs-lookup"><span data-stu-id="b5843-970">valueC</span></span>                              |
| <span data-ttu-id="b5843-971">2</span><span class="sxs-lookup"><span data-stu-id="b5843-971">2</span></span>                                   | <span data-ttu-id="b5843-972">s</span><span class="sxs-lookup"><span data-stu-id="b5843-972">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="b5843-973">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="b5843-973">Custom configuration provider</span></span>

<span data-ttu-id="b5843-974">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b5843-974">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b5843-975">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b5843-975">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b5843-976">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="b5843-976">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b5843-977">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b5843-977">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b5843-978">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b5843-978">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b5843-979">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="b5843-979">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b5843-980">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-980">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b5843-981">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="b5843-981">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b5843-982">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-982">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="b5843-983">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="b5843-983">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b5843-984">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-984">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="b5843-985">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="b5843-985">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b5843-986">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-986">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="b5843-987">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="b5843-987">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b5843-988">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="b5843-988">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="b5843-989">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-989">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="b5843-990">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b5843-990">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b5843-991">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-991">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="b5843-992">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5843-992">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="b5843-993">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="b5843-993">Access configuration during startup</span></span>

<span data-ttu-id="b5843-994">Vložení `IConfiguration` do `Startup` konstruktoru pro přístup k hodnotám konfigurace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b5843-994">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b5843-995">Chcete-li získat přístup ke konfiguraci v `Startup.Configure` , buď `IConfiguration` přímo do metody, nebo použijte instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="b5843-995">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="b5843-996">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b5843-996">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="b5843-997">Přístup ke konfiguraci na Razor stránce stránky nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="b5843-997">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="b5843-998">Chcete-li získat přístup k nastavení konfigurace na Razor stránce stránky nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([Referenční dokumentace jazyka C#: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvůMicrosoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) a vložte ho <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b5843-998">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="b5843-999">Na Razor stránce stránky:</span><span class="sxs-lookup"><span data-stu-id="b5843-999">In a Razor Pages page:</span></span>

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

<span data-ttu-id="b5843-1000">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="b5843-1000">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b5843-1001">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="b5843-1001">Add configuration from an external assembly</span></span>

<span data-ttu-id="b5843-1002"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b5843-1002">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b5843-1003">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b5843-1003">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5843-1004">Další materiály</span><span class="sxs-lookup"><span data-stu-id="b5843-1004">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

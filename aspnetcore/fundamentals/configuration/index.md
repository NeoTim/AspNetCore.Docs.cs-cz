---
title: Konfigurace v ASP.NET Core
author: rick-anderson
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: fe0a0d3dbb87455be602234825d702fab02df936
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634589"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="aef05-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aef05-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="aef05-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="aef05-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aef05-105">Konfigurace v ASP.NET Core provádí použití jednoho nebo více [poskytovatelů konfigurace](#cp).</span><span class="sxs-lookup"><span data-stu-id="aef05-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="aef05-106">Poskytovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="aef05-107">Soubory nastavení, například *appsettings.jszapnuto*</span><span class="sxs-lookup"><span data-stu-id="aef05-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="aef05-108">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-108">Environment variables</span></span>
* <span data-ttu-id="aef05-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="aef05-109">Azure Key Vault</span></span>
* <span data-ttu-id="aef05-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="aef05-110">Azure App Configuration</span></span>
* <span data-ttu-id="aef05-111">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-111">Command-line arguments</span></span>
* <span data-ttu-id="aef05-112">Vlastní zprostředkovatelé, nainstalované nebo vytvořené</span><span class="sxs-lookup"><span data-stu-id="aef05-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="aef05-113">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="aef05-113">Directory files</span></span>
* <span data-ttu-id="aef05-114">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="aef05-114">In-memory .NET objects</span></span>

<span data-ttu-id="aef05-115">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aef05-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="aef05-116">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-116">Default configuration</span></span>

<span data-ttu-id="aef05-117">ASP.NET Core webové aplikace vytvořené pomocí [dotnet New](/dotnet/core/tools/dotnet-new) nebo sady Visual Studio generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="aef05-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="aef05-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="aef05-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="aef05-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá existující `IConfiguration` jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="aef05-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="aef05-120">Ve výchozím případu konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj konfigurace _aplikace_ .</span><span class="sxs-lookup"><span data-stu-id="aef05-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="aef05-121">[appsettings.js](#appsettingsjson) používání [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="aef05-122">*appSettings.* `Environment` *. JSON* s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="aef05-123">Například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="aef05-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="aef05-124">[Tajné kódy aplikace](xref:security/app-secrets) v případě, že aplikace běží v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="aef05-125">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="aef05-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="aef05-126">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line).</span><span class="sxs-lookup"><span data-stu-id="aef05-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="aef05-127">Poskytovatelé konfigurace, kteří jsou přidáni později, přepíší předchozí nastavení klíče.</span><span class="sxs-lookup"><span data-stu-id="aef05-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="aef05-128">Například pokud `MyKey` je nastavena v *appsettings.jsv* a prostředí, je použita hodnota prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="aef05-129">Pomocí výchozích zprostředkovatelů konfigurace přepíše  [Poskytovatel konfigurace příkazového řádku](#clcp) všechny ostatní poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="aef05-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="aef05-130">Další informace o najdete v `CreateDefaultBuilder` tématu [výchozí nastavení tvůrce](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="aef05-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="aef05-131">Následující kód zobrazuje povolené poskytovatele konfigurace v pořadí, v jakém byly přidány:</span><span class="sxs-lookup"><span data-stu-id="aef05-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="aef05-132">appsettings.jsna</span><span class="sxs-lookup"><span data-stu-id="aef05-132">appsettings.json</span></span>

<span data-ttu-id="aef05-133">V souboru zvažte následující *appsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="aef05-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="aef05-134">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-135">Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načtení konfigurace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="aef05-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="aef05-136">*appsettings.jsna*</span><span class="sxs-lookup"><span data-stu-id="aef05-136">*appsettings.json*</span></span>
1. <span data-ttu-id="aef05-137">*appSettings.* `Environment` *. JSON* : například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="aef05-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="aef05-138">Verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="aef05-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="aef05-139">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="aef05-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="aef05-140">*appSettings*. `Environment` .. hodnoty *JSON* přepisují klíče v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="aef05-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="aef05-141">Ve výchozím nastavení by například:</span><span class="sxs-lookup"><span data-stu-id="aef05-141">For example, by default:</span></span>

* <span data-ttu-id="aef05-142">Ve vývoji, *appSettings*. ***Vývoj***. konfigurace *JSON* Přepisuje hodnoty nalezené v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="aef05-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="aef05-143">V produkčním prostředí, *appSettings*. ***Provozní***prostředí. konfigurace *JSON* Přepisuje hodnoty nalezené v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="aef05-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="aef05-144">Například při nasazení aplikace do Azure.</span><span class="sxs-lookup"><span data-stu-id="aef05-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="aef05-145">Vytvoření vazby hierarchických konfiguračních dat pomocí vzoru možností</span><span class="sxs-lookup"><span data-stu-id="aef05-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="aef05-146">Pomocí [výchozí](#default) konfigurace *appsettings.jsna* a *appSettings.* `Environment` soubory *. JSON* jsou povolené pomocí [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="aef05-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="aef05-147">Změny provedené v *appsettings.jsv* a *appSettings.* `Environment` soubor *. JSON* ***po*** spuštění aplikace si přečte [Poskytovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="aef05-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="aef05-148">Informace o přidání dalších konfiguračních souborů JSON najdete v části [Poskytovatel konfigurace JSON](#jcp) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="aef05-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="aef05-149">Správce zabezpečení a tajného klíče</span><span class="sxs-lookup"><span data-stu-id="aef05-149">Security and secret manager</span></span>

<span data-ttu-id="aef05-150">Pokyny pro konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="aef05-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="aef05-151">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="aef05-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="aef05-152">[Správce tajného klíče](xref:security/app-secrets) se dá použít k ukládání tajných kódů do vývoje.</span><span class="sxs-lookup"><span data-stu-id="aef05-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="aef05-153">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="aef05-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="aef05-154">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="aef05-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="aef05-155">Ve [výchozím nastavení](#default)přečte [správce tajných klíčů](xref:security/app-secrets) nastavení konfigurace po *appsettings.jsv* a *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="aef05-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="aef05-156">Další informace o ukládání hesel nebo jiných citlivých dat:</span><span class="sxs-lookup"><span data-stu-id="aef05-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="aef05-157"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="aef05-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="aef05-158">Správce tajného kódu používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="aef05-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="aef05-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="aef05-160">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="aef05-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="aef05-161">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-161">Environment variables</span></span>

<span data-ttu-id="aef05-162">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z proměnných prostředí s proměnnou klíč-hodnota po přečtení *appsettings.jsna*, *appSettings.* `Environment` *. JSON*a [správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="aef05-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="aef05-163">Proto hodnoty klíčů načtené z prostředí přepisují hodnoty načtené z *appsettings.jsna*, *appSettings.* `Environment` *. JSON*a správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="aef05-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="aef05-164">Následující `set` příkazy:</span><span class="sxs-lookup"><span data-stu-id="aef05-164">The following `set` commands:</span></span>

* <span data-ttu-id="aef05-165">Nastavte klíče prostředí a hodnoty v [předchozím příkladu](#appsettingsjson) ve Windows.</span><span class="sxs-lookup"><span data-stu-id="aef05-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="aef05-166">Při použití [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)otestujte nastavení.</span><span class="sxs-lookup"><span data-stu-id="aef05-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="aef05-167">`dotnet run`Příkaz musí být spuštěn v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="aef05-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="aef05-168">Předchozí nastavení prostředí:</span><span class="sxs-lookup"><span data-stu-id="aef05-168">The preceding environment settings:</span></span>

* <span data-ttu-id="aef05-169">Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.</span><span class="sxs-lookup"><span data-stu-id="aef05-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="aef05-170">Nebudou přečteny pomocí prohlížečů spuštěných v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="aef05-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="aef05-171">K nastavení klíčů a hodnot prostředí ve Windows se dají použít následující příkazy [setx](/windows-server/administration/windows-commands/setx) .</span><span class="sxs-lookup"><span data-stu-id="aef05-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="aef05-172">Na rozdíl `set` od `setx` platí, že nastavení jsou trvalá.</span><span class="sxs-lookup"><span data-stu-id="aef05-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="aef05-173">`/M` nastaví proměnnou v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="aef05-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="aef05-174">Pokud se `/M` přepínač nepoužívá, je nastavena proměnná prostředí uživatele.</span><span class="sxs-lookup"><span data-stu-id="aef05-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="aef05-175">Chcete-li otestovat, zda předchozí příkazy přepisují *appsettings.jsv* a *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="aef05-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="aef05-176">Se sadou Visual Studio: Ukončete a restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="aef05-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="aef05-177">Pomocí rozhraní příkazového řádku: spusťte nové příkazové okno a zadejte `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="aef05-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="aef05-178">Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem pro určení předpony pro proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="aef05-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="aef05-179">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="aef05-179">In the preceding code:</span></span>

* <span data-ttu-id="aef05-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="aef05-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="aef05-181">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="aef05-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="aef05-182">Proměnné prostředí nastavené s `MyCustomPrefix_` předponou přepíšou [výchozí poskytovatele konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="aef05-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="aef05-183">To zahrnuje proměnné prostředí bez předpony.</span><span class="sxs-lookup"><span data-stu-id="aef05-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="aef05-184">Pokud jsou načteny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="aef05-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="aef05-185">Vlastní předponu otestujete následujícími příkazy:</span><span class="sxs-lookup"><span data-stu-id="aef05-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="aef05-186">[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty příkazového řádku s předponou `DOTNET_` a `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="aef05-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="aef05-187">`DOTNET_` `ASPNETCORE_` Předpony a jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele.</span><span class="sxs-lookup"><span data-stu-id="aef05-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="aef05-188">Další informace o konfiguraci hostitele a aplikace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="aef05-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="aef05-189">V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="aef05-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="aef05-190">Azure App Service nastavení aplikace:</span><span class="sxs-lookup"><span data-stu-id="aef05-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="aef05-191">Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.</span><span class="sxs-lookup"><span data-stu-id="aef05-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="aef05-192">Vystaveno jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-192">Exposed as environment variables.</span></span>

<span data-ttu-id="aef05-193">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="aef05-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="aef05-194">Informace o připojovacích řetězcích Azure Database najdete v tématu [předpony připojovacího řetězce](#constr) .</span><span class="sxs-lookup"><span data-stu-id="aef05-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="aef05-195">Proměnné prostředí nastavené v launchSettings.jszapnuté</span><span class="sxs-lookup"><span data-stu-id="aef05-195">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="aef05-196">Proměnné prostředí nastavené v *launchSettings.jspři* přepisování nastavení v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="aef05-196">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="aef05-197">Příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="aef05-197">Command-line</span></span>

<span data-ttu-id="aef05-198">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načte konfiguraci z dvojice klíč-hodnota argumentu klíč-hodnota po následujících zdrojích konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-198">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="aef05-199">*appsettings.jsna* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="aef05-199">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="aef05-200">[Tajné kódy aplikací (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-200">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="aef05-201">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-201">Environment variables.</span></span>

<span data-ttu-id="aef05-202">Ve [výchozím nastavení](#default)jsou konfigurační hodnoty nastavené pro přepisy hodnot konfigurace na příkazovém řádku nastavené se všemi ostatními zprostředkovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-202">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="aef05-203">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-203">Command-line arguments</span></span>

<span data-ttu-id="aef05-204">Následující příkaz nastaví klíče a hodnoty pomocí `=` :</span><span class="sxs-lookup"><span data-stu-id="aef05-204">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="aef05-205">Následující příkaz nastaví klíče a hodnoty pomocí `/` :</span><span class="sxs-lookup"><span data-stu-id="aef05-205">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="aef05-206">Následující příkaz nastaví klíče a hodnoty pomocí `--` :</span><span class="sxs-lookup"><span data-stu-id="aef05-206">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="aef05-207">Hodnota klíče:</span><span class="sxs-lookup"><span data-stu-id="aef05-207">The key value:</span></span>

* <span data-ttu-id="aef05-208">Musí následovat po `=` , nebo klíč musí mít předponu `--` nebo, `/` Pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="aef05-208">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="aef05-209">Není vyžadováno `=` , pokud je použit.</span><span class="sxs-lookup"><span data-stu-id="aef05-209">Isn't required if `=` is used.</span></span> <span data-ttu-id="aef05-210">Například, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="aef05-210">For example, `MySetting=`.</span></span>

<span data-ttu-id="aef05-211">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které `=` se používají s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="aef05-211">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="aef05-212">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="aef05-212">Switch mappings</span></span>

<span data-ttu-id="aef05-213">Mapování přepínačů povolují logiku nahrazení názvu **klíče** .</span><span class="sxs-lookup"><span data-stu-id="aef05-213">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="aef05-214">Poskytněte slovník pro nahrazení přepínačů v <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodě.</span><span class="sxs-lookup"><span data-stu-id="aef05-214">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="aef05-215">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="aef05-215">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="aef05-216">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota Dictionary zpátky, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-216">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="aef05-217">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="aef05-217">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="aef05-218">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="aef05-218">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="aef05-219">Přepínače musí začínat na `-` nebo `--` .</span><span class="sxs-lookup"><span data-stu-id="aef05-219">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="aef05-220">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="aef05-220">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="aef05-221">Chcete-li použít slovník mapování přepínačů, předejte jej do volání `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="aef05-221">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="aef05-222">Následující kód ukazuje klíčové hodnoty pro nahrazené klíče:</span><span class="sxs-lookup"><span data-stu-id="aef05-222">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-223">Následující příkaz funguje pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="aef05-223">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="aef05-224">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="aef05-224">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="aef05-225">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="aef05-225">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="aef05-226">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty i slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="aef05-226">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="aef05-227">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="aef05-227">Hierarchical configuration data</span></span>

<span data-ttu-id="aef05-228">Rozhraní API pro konfiguraci čte hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="aef05-228">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="aef05-229">[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="aef05-229">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="aef05-230">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-230">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-231">Upřednostňovaným způsobem, jak číst hierarchická konfigurační data, je použít vzor možností.</span><span class="sxs-lookup"><span data-stu-id="aef05-231">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="aef05-232">Další informace najdete v tématu [vázání hierarchických konfiguračních dat](#optpat) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="aef05-232">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="aef05-233"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="aef05-233"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="aef05-234">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="aef05-234">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="aef05-235">Konfigurační klíče a hodnoty</span><span class="sxs-lookup"><span data-stu-id="aef05-235">Configuration keys and values</span></span>

<span data-ttu-id="aef05-236">Konfigurační klíče:</span><span class="sxs-lookup"><span data-stu-id="aef05-236">Configuration keys:</span></span>

* <span data-ttu-id="aef05-237">Rozlišují se malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="aef05-237">Are case-insensitive.</span></span> <span data-ttu-id="aef05-238">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="aef05-238">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="aef05-239">Pokud je klíč a hodnota nastavená ve více poskytovatelích konfigurace, použije se hodnota z posledního přidaného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="aef05-239">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="aef05-240">Další informace najdete v tématu [výchozí konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="aef05-240">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="aef05-241">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="aef05-241">Hierarchical keys</span></span>
  * <span data-ttu-id="aef05-242">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="aef05-242">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="aef05-243">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="aef05-243">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="aef05-244">Dvojité podtržítko, `__` , je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:` .</span><span class="sxs-lookup"><span data-stu-id="aef05-244">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="aef05-245">V Azure Key Vault hierarchické klíče používají `--` jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="aef05-245">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="aef05-246">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) se `--` `:` při načtení tajných klíčů do konfigurace aplikace automaticky nahradí.</span><span class="sxs-lookup"><span data-stu-id="aef05-246">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="aef05-247"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="aef05-247">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="aef05-248">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#boa) .</span><span class="sxs-lookup"><span data-stu-id="aef05-248">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="aef05-249">Konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="aef05-249">Configuration values:</span></span>

* <span data-ttu-id="aef05-250">Jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="aef05-250">Are strings.</span></span>
* <span data-ttu-id="aef05-251">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="aef05-251">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="aef05-252">Poskytovatelé konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-252">Configuration providers</span></span>

<span data-ttu-id="aef05-253">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-253">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="aef05-254">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="aef05-254">Provider</span></span> | <span data-ttu-id="aef05-255">Poskytuje konfiguraci z</span><span class="sxs-lookup"><span data-stu-id="aef05-255">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="aef05-256">Poskytovatel konfigurace Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="aef05-256">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="aef05-257">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="aef05-257">Azure Key Vault</span></span> |
| [<span data-ttu-id="aef05-258">Poskytovatel konfigurace Azure App</span><span class="sxs-lookup"><span data-stu-id="aef05-258">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="aef05-259">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="aef05-259">Azure App Configuration</span></span> |
| [<span data-ttu-id="aef05-260">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-260">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="aef05-261">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-261">Command-line parameters</span></span> |
| [<span data-ttu-id="aef05-262">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-262">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="aef05-263">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="aef05-263">Custom source</span></span> |
| [<span data-ttu-id="aef05-264">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-264">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="aef05-265">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-265">Environment variables</span></span> |
| [<span data-ttu-id="aef05-266">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="aef05-266">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="aef05-267">Soubory INI, JSON a XML</span><span class="sxs-lookup"><span data-stu-id="aef05-267">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="aef05-268">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="aef05-268">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="aef05-269">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="aef05-269">Directory files</span></span> |
| [<span data-ttu-id="aef05-270">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="aef05-270">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="aef05-271">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="aef05-271">In-memory collections</span></span> |
| [<span data-ttu-id="aef05-272">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="aef05-272">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="aef05-273">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="aef05-273">File in the user profile directory</span></span> |

<span data-ttu-id="aef05-274">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou zadáni poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-274">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="aef05-275">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="aef05-275">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="aef05-276">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="aef05-276">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="aef05-277">*appsettings.jsna*</span><span class="sxs-lookup"><span data-stu-id="aef05-277">*appsettings.json*</span></span>
1. <span data-ttu-id="aef05-278">*appSettings*. `Environment` .. formát *JSON*</span><span class="sxs-lookup"><span data-stu-id="aef05-278">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="aef05-279">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="aef05-279">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="aef05-280">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="aef05-280">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="aef05-281">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-281">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="aef05-282">Běžný postup je přidat poskytovatele konfigurace příkazového řádku na poslední v řadě poskytovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="aef05-282">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="aef05-283">Předchozí sekvence zprostředkovatelů se používá ve [výchozí konfiguraci](#default).</span><span class="sxs-lookup"><span data-stu-id="aef05-283">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="aef05-284">Předpony připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="aef05-284">Connection string prefixes</span></span>

<span data-ttu-id="aef05-285">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro proměnné prostředí pro čtyři připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="aef05-285">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="aef05-286">Tyto připojovací řetězce jsou součástí konfigurace připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-286">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="aef05-287">Proměnné prostředí s předponami, které jsou uvedené v tabulce, se načtou do aplikace s [výchozí konfigurací](#default) nebo když není dodána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="aef05-287">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="aef05-288">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="aef05-288">Connection string prefix</span></span> | <span data-ttu-id="aef05-289">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="aef05-289">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="aef05-290">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="aef05-290">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="aef05-291">MySQL</span><span class="sxs-lookup"><span data-stu-id="aef05-291">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="aef05-292">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="aef05-292">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="aef05-293">SQL Server</span><span class="sxs-lookup"><span data-stu-id="aef05-293">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="aef05-294">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="aef05-294">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="aef05-295">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="aef05-295">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="aef05-296">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="aef05-296">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="aef05-297">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-297">Environment variable key</span></span> | <span data-ttu-id="aef05-298">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="aef05-298">Converted configuration key</span></span> | <span data-ttu-id="aef05-299">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="aef05-299">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="aef05-300">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="aef05-300">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="aef05-301">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="aef05-301">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="aef05-302">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="aef05-302">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="aef05-303">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="aef05-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="aef05-304">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="aef05-304">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="aef05-305">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="aef05-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="aef05-306">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="aef05-306">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="aef05-307">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="aef05-307">File configuration provider</span></span>

<span data-ttu-id="aef05-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="aef05-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="aef05-309">Následující poskytovatelé konfigurace jsou odvozeni z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="aef05-309">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="aef05-310">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="aef05-310">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="aef05-311">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="aef05-311">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="aef05-312">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="aef05-312">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="aef05-313">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="aef05-313">INI configuration provider</span></span>

<span data-ttu-id="aef05-314"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="aef05-314">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="aef05-315">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-315">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="aef05-316">V předchozím kódu nastavení *MyIniConfig.ini* a  *MyIniConfig*. `Environment` . soubory *ini* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="aef05-316">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="aef05-317">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-317">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="aef05-318">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="aef05-318">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="aef05-319">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="aef05-319">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="aef05-320">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-320">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="aef05-321">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="aef05-321">JSON configuration provider</span></span>

<span data-ttu-id="aef05-322"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načte konfiguraci z párů klíč-hodnota souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="aef05-322">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="aef05-323">Přetížení můžou specifikovat:</span><span class="sxs-lookup"><span data-stu-id="aef05-323">Overloads can specify:</span></span>

* <span data-ttu-id="aef05-324">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="aef05-324">Whether the file is optional.</span></span>
* <span data-ttu-id="aef05-325">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-325">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="aef05-326">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="aef05-326">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="aef05-327">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="aef05-327">The preceding code:</span></span>

* <span data-ttu-id="aef05-328">Nakonfiguruje zprostředkovatele konfigurace JSON, aby načetl *MyConfig.js* do souboru s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="aef05-328">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="aef05-329">`optional: true`: Soubor je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="aef05-329">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="aef05-330">`reloadOnChange: true` : Soubor se znovu načte při uložení změn.</span><span class="sxs-lookup"><span data-stu-id="aef05-330">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="aef05-331">Přečte [výchozí zprostředkovatele konfigurace](#default) před *MyConfig.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-331">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="aef05-332">Nastavení v nastavení *MyConfig.jspři* přepsání souborů ve výchozích poskytovatelích konfigurace, včetně [poskytovatele konfigurace proměnných prostředí](#evcp) a [poskytovatele konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="aef05-332">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="aef05-333">***Obvykle nechcete, aby vlastní*** soubor JSON přepsal hodnoty nastavené ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a v [poskytovateli konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="aef05-333">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="aef05-334">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-334">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="aef05-335">V předchozím kódu nastavení *MyConfig.js* v a  *MyConfig*. `Environment` . soubory *JSON* :</span><span class="sxs-lookup"><span data-stu-id="aef05-335">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="aef05-336">Přepsat nastavení v *appsettings.jsna* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="aef05-336">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="aef05-337">Jsou přepsány nastavením ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a [poskytovatelem konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="aef05-337">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="aef05-338">[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující *MyConfig.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="aef05-338">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="aef05-339">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-339">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="aef05-340">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="aef05-340">XML configuration provider</span></span>

<span data-ttu-id="aef05-341"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="aef05-341">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="aef05-342">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-342">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="aef05-343">V předchozím kódu nastavení *MyXMLFile.xml* a  *MyXMLFile*. `Environment` . soubory *XML* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="aef05-343">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="aef05-344">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-344">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="aef05-345">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="aef05-345">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="aef05-346">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="aef05-346">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="aef05-347">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-347">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-348">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="aef05-348">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="aef05-349">Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:</span><span class="sxs-lookup"><span data-stu-id="aef05-349">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-350">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="aef05-350">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="aef05-351">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="aef05-351">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="aef05-352">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="aef05-352">key:attribute</span></span>
* <span data-ttu-id="aef05-353">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="aef05-353">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="aef05-354">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="aef05-354">Key-per-file configuration provider</span></span>

<span data-ttu-id="aef05-355"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-355">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="aef05-356">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-356">The key is the file name.</span></span> <span data-ttu-id="aef05-357">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-357">The value contains the file's contents.</span></span> <span data-ttu-id="aef05-358">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="aef05-358">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="aef05-359">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aef05-359">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="aef05-360">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="aef05-360">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="aef05-361">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="aef05-361">Overloads permit specifying:</span></span>

* <span data-ttu-id="aef05-362">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="aef05-362">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="aef05-363">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="aef05-363">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="aef05-364">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="aef05-364">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="aef05-365">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="aef05-365">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="aef05-366">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="aef05-366">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="aef05-367">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="aef05-367">Memory configuration provider</span></span>

<span data-ttu-id="aef05-368"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-368">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="aef05-369">Následující kód přidá kolekci paměti do konfiguračního systému:</span><span class="sxs-lookup"><span data-stu-id="aef05-369">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="aef05-370">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-370">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-371">V předchozím kódu `config.AddInMemoryCollection(Dict)` se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="aef05-371">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="aef05-372">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="aef05-372">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="aef05-373">Viz [vazba pole](#boa) pro jiný příklad pomocí `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="aef05-373">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="aef05-374">GetValue</span><span class="sxs-lookup"><span data-stu-id="aef05-374">GetValue</span></span>

<span data-ttu-id="aef05-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:</span><span class="sxs-lookup"><span data-stu-id="aef05-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-376">Pokud v předchozím kódu `NumberKey` není v konfiguraci nalezeno, `99` je použita výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-376">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="aef05-377">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="aef05-377">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="aef05-378">V následujících příkladech zvažte následující *MySubsection.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="aef05-378">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="aef05-379">Následující kód přidá *MySubsection.js* do zprostředkovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-379">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="aef05-380">GetSection</span><span class="sxs-lookup"><span data-stu-id="aef05-380">GetSection</span></span>

<span data-ttu-id="aef05-381">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí dílčí část konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="aef05-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="aef05-382">Následující kód vrátí hodnoty pro `section1` :</span><span class="sxs-lookup"><span data-stu-id="aef05-382">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-383">Následující kód vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="aef05-383">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-384">`GetSection` nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="aef05-384">`GetSection` never returns `null`.</span></span> <span data-ttu-id="aef05-385">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="aef05-385">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="aef05-386">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-386">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="aef05-387"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="aef05-387">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="aef05-388">GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="aef05-388">GetChildren and Exists</span></span>

<span data-ttu-id="aef05-389">Následující kód volá [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="aef05-389">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-390">Předchozí kód volá [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pro ověření, zda existuje oddíl:</span><span class="sxs-lookup"><span data-stu-id="aef05-390">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="aef05-391">Vazba pole</span><span class="sxs-lookup"><span data-stu-id="aef05-391">Bind an array</span></span>

<span data-ttu-id="aef05-392">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="aef05-392">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="aef05-393">Libovolný formát pole, který zveřejňuje numerický klíčový segment, je schopný vytvořit vazbu pole k poli třídy [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="aef05-393">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="aef05-394">Zvažte *MyArray.jsna* [webu ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="aef05-394">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="aef05-395">Následující kód přidá *MyArray.js* do zprostředkovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-395">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="aef05-396">Následující kód přečte konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="aef05-396">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-397">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="aef05-397">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="aef05-398">V předchozím výstupu má index 3 hodnotu odpovídající hodnotě `value40` `"4": "value40",` v *MyArray.js*.</span><span class="sxs-lookup"><span data-stu-id="aef05-398">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="aef05-399">Vazby vázaného pole jsou souvislé a nejsou vázané na index konfiguračního klíče.</span><span class="sxs-lookup"><span data-stu-id="aef05-399">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="aef05-400">Pořadač konfigurace nemůže svázat hodnoty null ani vytvářet položky null ve vázaných objektech.</span><span class="sxs-lookup"><span data-stu-id="aef05-400">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="aef05-401">Následující kód načte `array:entries` konfiguraci pomocí <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="aef05-401">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="aef05-402">Následující kód přečte konfiguraci v `arrayDict` `Dictionary` a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="aef05-402">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-403">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="aef05-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="aef05-404">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="aef05-404">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="aef05-405">Když jsou data konfigurace obsahující pole svázané, používají se k iteraci konfiguračních dat při vytváření objektu pole indexy v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="aef05-405">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="aef05-406">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="aef05-406">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="aef05-407">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na instanci doplní `ArrayExample` jakýmkoli poskytovatelem konfigurace, který načte &num; dvojici klíč/hodnota indexu 3.</span><span class="sxs-lookup"><span data-stu-id="aef05-407">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="aef05-408">V souboru ukázka stažení zvažte následující *Value3.js* :</span><span class="sxs-lookup"><span data-stu-id="aef05-408">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="aef05-409">Následující kód obsahuje konfiguraci pro *Value3.jsv* a `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="aef05-409">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="aef05-410">Následující kód přečte předchozí konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="aef05-410">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-411">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="aef05-411">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="aef05-412">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="aef05-412">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="aef05-413">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-413">Custom configuration provider</span></span>

<span data-ttu-id="aef05-414">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="aef05-414">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="aef05-415">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="aef05-415">The provider has the following characteristics:</span></span>

* <span data-ttu-id="aef05-416">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="aef05-416">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="aef05-417">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-417">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="aef05-418">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="aef05-418">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="aef05-419">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="aef05-419">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="aef05-420">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-420">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="aef05-421">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="aef05-421">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="aef05-422">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-422">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="aef05-423">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="aef05-423">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="aef05-424">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-424">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="aef05-425">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="aef05-425">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="aef05-426">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-426">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="aef05-427">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="aef05-427">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="aef05-428">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="aef05-428">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="aef05-429">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="aef05-429">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="aef05-430">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-430">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="aef05-431">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="aef05-431">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="aef05-432">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-432">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="aef05-433">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-433">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="aef05-434">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="aef05-434">Access configuration in Startup</span></span>

<span data-ttu-id="aef05-435">Následující kód zobrazuje konfigurační data v `Startup` metodách:</span><span class="sxs-lookup"><span data-stu-id="aef05-435">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="aef05-436">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="aef05-436">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="aef05-437">Přístup ke konfiguraci na Razor stránkách</span><span class="sxs-lookup"><span data-stu-id="aef05-437">Access configuration in Razor Pages</span></span>

<span data-ttu-id="aef05-438">Následující kód zobrazuje konfigurační data na Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="aef05-438">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="aef05-439">V následujícím kódu `MyOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="aef05-439">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="aef05-440">Následující kód používá [`@inject`](xref:mvc/views/razor#inject) Razor direktivu k vyřešení a zobrazení hodnot možností:</span><span class="sxs-lookup"><span data-stu-id="aef05-440">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="aef05-441">Přístup ke konfiguraci v souboru zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="aef05-441">Access configuration in a MVC view file</span></span>

<span data-ttu-id="aef05-442">Následující kód zobrazuje konfigurační data v zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="aef05-442">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="aef05-443">Konfigurace možností s delegátem</span><span class="sxs-lookup"><span data-stu-id="aef05-443">Configure options with a delegate</span></span>

<span data-ttu-id="aef05-444">Možnosti nakonfigurované v delegátech přepíší hodnoty nastavené ve zprostředkovatelích konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-444">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="aef05-445">Konfigurace možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="aef05-445">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="aef05-446">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je služba přidána do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="aef05-446">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="aef05-447">Pomocí delegáta konfiguruje hodnoty pro `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="aef05-447">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="aef05-448">Následující kód zobrazí hodnoty možností:</span><span class="sxs-lookup"><span data-stu-id="aef05-448">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="aef05-449">V předchozím příkladu `Option1` jsou hodnoty a `Option2` zadány v *appsettings.js* a poté přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="aef05-449">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="aef05-450">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="aef05-450">Host versus app configuration</span></span>

<span data-ttu-id="aef05-451">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="aef05-451">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="aef05-452">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="aef05-452">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="aef05-453">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="aef05-453">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="aef05-454">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="aef05-454">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="aef05-455">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="aef05-455">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="aef05-456">Výchozí konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="aef05-456">Default host configuration</span></span>

<span data-ttu-id="aef05-457">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="aef05-457">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="aef05-458">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="aef05-458">Host configuration is provided from:</span></span>
  * <span data-ttu-id="aef05-459">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="aef05-459">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="aef05-460">Předpona ( `DOTNET_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-460">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="aef05-461">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-461">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="aef05-462">Je navázána výchozí konfigurace webového hostitele ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="aef05-462">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="aef05-463">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-463">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="aef05-464">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="aef05-464">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="aef05-465">Přidejte middleware předávaných hlaviček, pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="aef05-465">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="aef05-466">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="aef05-466">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="aef05-467">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-467">Other configuration</span></span>

<span data-ttu-id="aef05-468">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="aef05-468">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="aef05-469">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="aef05-469">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="aef05-470">*launch.jsna* / *launchSettings.jsv* nástroji jsou konfigurační soubory nástroje pro vývojové prostředí popsané níže:</span><span class="sxs-lookup"><span data-stu-id="aef05-470">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="aef05-471">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="aef05-471">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="aef05-472">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="aef05-472">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="aef05-473">*web.config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="aef05-473">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="aef05-474">Proměnné prostředí nastavené v *launchSettings.jspři* přepisování nastavení v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="aef05-474">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="aef05-475">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="aef05-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="aef05-476">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="aef05-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="aef05-477"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="aef05-478">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="aef05-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aef05-479">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="aef05-479">Additional resources</span></span>

* [<span data-ttu-id="aef05-480">Zdrojový kód konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aef05-481">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="aef05-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="aef05-482">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="aef05-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="aef05-483">Azure Key Vault</span></span>
* <span data-ttu-id="aef05-484">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="aef05-484">Azure App Configuration</span></span>
* <span data-ttu-id="aef05-485">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-485">Command-line arguments</span></span>
* <span data-ttu-id="aef05-486">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="aef05-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="aef05-487">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="aef05-487">Directory files</span></span>
* <span data-ttu-id="aef05-488">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-488">Environment variables</span></span>
* <span data-ttu-id="aef05-489">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="aef05-489">In-memory .NET objects</span></span>
* <span data-ttu-id="aef05-490">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="aef05-490">Settings files</span></span>

<span data-ttu-id="aef05-491">Do [Microsoft. AspNetCore. app metapackageu](xref:fundamentals/metapackage-app)patří konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)).</span><span class="sxs-lookup"><span data-stu-id="aef05-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="aef05-492">Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="aef05-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="aef05-493">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="aef05-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="aef05-494">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="aef05-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="aef05-495">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="aef05-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="aef05-496">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aef05-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="aef05-497">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="aef05-497">Host versus app configuration</span></span>

<span data-ttu-id="aef05-498">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="aef05-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="aef05-499">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="aef05-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="aef05-500">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="aef05-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="aef05-501">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="aef05-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="aef05-502">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="aef05-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="aef05-503">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-503">Other configuration</span></span>

<span data-ttu-id="aef05-504">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="aef05-504">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="aef05-505">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="aef05-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="aef05-506">*launch.jsna* / *launchSettings.jsv* nástroji jsou konfigurační soubory nástroje pro vývojové prostředí popsané níže:</span><span class="sxs-lookup"><span data-stu-id="aef05-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="aef05-507">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="aef05-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="aef05-508">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="aef05-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="aef05-509">*web.config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="aef05-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="aef05-510">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="aef05-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="aef05-511">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-511">Default configuration</span></span>

<span data-ttu-id="aef05-512">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="aef05-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="aef05-513">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="aef05-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="aef05-514">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="aef05-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="aef05-515">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aef05-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="aef05-516">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="aef05-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="aef05-517">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="aef05-518">Předpona ( `ASPNETCORE_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="aef05-519">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="aef05-520">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="aef05-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="aef05-521">*appsettings.js* používání [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-521">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="aef05-522">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="aef05-523">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v `Development` prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="aef05-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="aef05-524">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="aef05-525">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="aef05-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="aef05-526">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="aef05-526">Security</span></span>

<span data-ttu-id="aef05-527">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="aef05-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="aef05-528">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="aef05-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="aef05-529">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="aef05-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="aef05-530">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="aef05-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="aef05-531">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="aef05-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="aef05-532"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="aef05-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="aef05-533">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="aef05-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="aef05-534">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="aef05-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="aef05-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="aef05-536">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="aef05-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="aef05-537">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="aef05-537">Hierarchical configuration data</span></span>

<span data-ttu-id="aef05-538">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="aef05-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="aef05-539">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="aef05-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="aef05-540">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="aef05-541">Oddíly a klíče jsou shrnuty s použitím dvojtečky ( `:` ) pro zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="aef05-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="aef05-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-542">section0:key0</span></span>
* <span data-ttu-id="aef05-543">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-543">section0:key1</span></span>
* <span data-ttu-id="aef05-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-544">section1:key0</span></span>
* <span data-ttu-id="aef05-545">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-545">section1:key1</span></span>

<span data-ttu-id="aef05-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="aef05-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="aef05-547">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="aef05-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="aef05-548">Konvence</span><span class="sxs-lookup"><span data-stu-id="aef05-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="aef05-549">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="aef05-549">Sources and providers</span></span>

<span data-ttu-id="aef05-550">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="aef05-551">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="aef05-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="aef05-552">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="aef05-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="aef05-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="aef05-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do Razor stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pro získání konfigurace pro třídu.</span><span class="sxs-lookup"><span data-stu-id="aef05-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="aef05-555">V následujících příkladech se `_config` pole používá pro přístup k hodnotám konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="aef05-556">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="aef05-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="aef05-557">Klíče</span><span class="sxs-lookup"><span data-stu-id="aef05-557">Keys</span></span>

<span data-ttu-id="aef05-558">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="aef05-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="aef05-559">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="aef05-559">Keys are case-insensitive.</span></span> <span data-ttu-id="aef05-560">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="aef05-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="aef05-561">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="aef05-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="aef05-562">Další informace o duplicitních klíčích JSON najdete v [tomto problému GitHubu](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="aef05-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="aef05-563">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="aef05-563">Hierarchical keys</span></span>
  * <span data-ttu-id="aef05-564">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="aef05-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="aef05-565">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="aef05-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="aef05-566">Dvojité podtržítko ( `__` ) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="aef05-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="aef05-567">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="aef05-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="aef05-568">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="aef05-569"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="aef05-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="aef05-570">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="aef05-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="aef05-571">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="aef05-571">Values</span></span>

<span data-ttu-id="aef05-572">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="aef05-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="aef05-573">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="aef05-573">Values are strings.</span></span>
* <span data-ttu-id="aef05-574">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="aef05-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="aef05-575">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="aef05-575">Providers</span></span>

<span data-ttu-id="aef05-576">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="aef05-577">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="aef05-577">Provider</span></span> | <span data-ttu-id="aef05-578">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="aef05-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="aef05-579">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="aef05-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="aef05-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="aef05-580">Azure Key Vault</span></span> |
| <span data-ttu-id="aef05-581">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="aef05-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="aef05-582">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="aef05-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="aef05-583">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="aef05-584">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-584">Command-line parameters</span></span> |
| [<span data-ttu-id="aef05-585">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="aef05-586">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="aef05-586">Custom source</span></span> |
| [<span data-ttu-id="aef05-587">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="aef05-588">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-588">Environment variables</span></span> |
| [<span data-ttu-id="aef05-589">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="aef05-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="aef05-590">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="aef05-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="aef05-591">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="aef05-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="aef05-592">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="aef05-592">Directory files</span></span> |
| [<span data-ttu-id="aef05-593">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="aef05-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="aef05-594">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="aef05-594">In-memory collections</span></span> |
| <span data-ttu-id="aef05-595">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="aef05-595">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="aef05-596">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="aef05-596">File in the user profile directory</span></span> |

<span data-ttu-id="aef05-597">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="aef05-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="aef05-598">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="aef05-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="aef05-599">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="aef05-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="aef05-600">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="aef05-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="aef05-601">Soubory (*appsettings.jsna*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace</span><span class="sxs-lookup"><span data-stu-id="aef05-601">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="aef05-602">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="aef05-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="aef05-603">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="aef05-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="aef05-604">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-604">Environment variables</span></span>
1. <span data-ttu-id="aef05-605">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-605">Command-line arguments</span></span>

<span data-ttu-id="aef05-606">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="aef05-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="aef05-607">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="aef05-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="aef05-608">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="aef05-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="aef05-609">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="aef05-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="aef05-610">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> na tvůrce hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="aef05-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="aef05-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="aef05-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="aef05-612">Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s automaticky přidanými uživateli `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="aef05-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="aef05-613">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="aef05-614">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte jako `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="aef05-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="aef05-615">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="aef05-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="aef05-616">Chcete-li odebrat poskytovatele přidaných nástrojem `CreateDefaultBuilder` , zavolejte nejprve [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="aef05-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="aef05-617">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="aef05-617">Consume configuration during app startup</span></span>

<span data-ttu-id="aef05-618">Konfigurace dodaná do aplikace v nástroji `ConfigureAppConfiguration` je k dispozici během spouštění aplikace, včetně `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="aef05-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aef05-619">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="aef05-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="aef05-620">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="aef05-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="aef05-621"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="aef05-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="aef05-622">Chcete-li aktivovat konfiguraci příkazového řádku, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> je metoda rozšíření volána na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aef05-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="aef05-623">`AddCommandLine` je automaticky volána `CreateDefaultBuilder(string [])` , když je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="aef05-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="aef05-624">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="aef05-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="aef05-625">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="aef05-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="aef05-626">Volitelná konfigurace z *appsettings.jsna* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="aef05-626">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="aef05-627">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="aef05-628">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-628">Environment variables.</span></span>

<span data-ttu-id="aef05-629">`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="aef05-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="aef05-630">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="aef05-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="aef05-631">`CreateDefaultBuilder` funguje, když je hostitel vytvořen.</span><span class="sxs-lookup"><span data-stu-id="aef05-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="aef05-632">Proto může konfigurace z příkazového řádku aktivované nástrojem `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="aef05-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="aef05-633">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány pomocí `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="aef05-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="aef05-634">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte na `AddCommandLine` Poslední.</span><span class="sxs-lookup"><span data-stu-id="aef05-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="aef05-635">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="aef05-635">**Example**</span></span>

<span data-ttu-id="aef05-636">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="aef05-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="aef05-637">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="aef05-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="aef05-638">Zadejte do příkazu argument příkazového řádku `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="aef05-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="aef05-639">Po spuštění aplikace otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="aef05-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="aef05-640">Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k dispozici pro příkaz `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="aef05-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="aef05-641">Arguments</span><span class="sxs-lookup"><span data-stu-id="aef05-641">Arguments</span></span>

<span data-ttu-id="aef05-642">Hodnota musí následovat po znaménku rovná se ( `=` ), nebo klíč musí obsahovat předponu ( `--` nebo `/` ), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="aef05-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="aef05-643">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="aef05-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="aef05-644">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="aef05-644">Key prefix</span></span>               | <span data-ttu-id="aef05-645">Příklad</span><span class="sxs-lookup"><span data-stu-id="aef05-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="aef05-646">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="aef05-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="aef05-647">Dvě pomlčky ( `--` )</span><span class="sxs-lookup"><span data-stu-id="aef05-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="aef05-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="aef05-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="aef05-649">Lomítko ( `/` )</span><span class="sxs-lookup"><span data-stu-id="aef05-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="aef05-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="aef05-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="aef05-651">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="aef05-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="aef05-652">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="aef05-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="aef05-653">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="aef05-653">Switch mappings</span></span>

<span data-ttu-id="aef05-654">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="aef05-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="aef05-655">Při ručním sestavování konfigurace pomocí nástroje <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> Zadejte slovník přepínačů pro vložení do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="aef05-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="aef05-656">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="aef05-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="aef05-657">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="aef05-658">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="aef05-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="aef05-659">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="aef05-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="aef05-660">Přepínače musí začínat spojovníkem ( `-` ) nebo dvojitou čárkou ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="aef05-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="aef05-661">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="aef05-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="aef05-662">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="aef05-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="aef05-663">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="aef05-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="aef05-664">Po sestavení hostitele zavolejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="aef05-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="aef05-665">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="aef05-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="aef05-666">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="aef05-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="aef05-667">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="aef05-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="aef05-668">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="aef05-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="aef05-669">Klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-669">Key</span></span>       | <span data-ttu-id="aef05-670">Hodnota</span><span class="sxs-lookup"><span data-stu-id="aef05-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="aef05-671">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="aef05-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="aef05-672">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="aef05-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="aef05-673">Klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-673">Key</span></span>               | <span data-ttu-id="aef05-674">Hodnota</span><span class="sxs-lookup"><span data-stu-id="aef05-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="aef05-675">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="aef05-676"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="aef05-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="aef05-677">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aef05-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="aef05-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="aef05-679">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="aef05-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="aef05-680">`AddEnvironmentVariables` slouží k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán.</span><span class="sxs-lookup"><span data-stu-id="aef05-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="aef05-681">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="aef05-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="aef05-682">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="aef05-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="aef05-683">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="aef05-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="aef05-684">Volitelná konfigurace z *appsettings.jsna* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="aef05-684">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="aef05-685">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="aef05-686">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="aef05-686">Command-line arguments.</span></span>

<span data-ttu-id="aef05-687">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="aef05-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="aef05-688">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="aef05-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="aef05-689">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu:</span><span class="sxs-lookup"><span data-stu-id="aef05-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="aef05-690">Zavolejte `AddEnvironmentVariables` jako poslední, pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="aef05-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="aef05-691">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="aef05-691">**Example**</span></span>

<span data-ttu-id="aef05-692">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="aef05-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="aef05-693">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="aef05-693">Run the sample app.</span></span> <span data-ttu-id="aef05-694">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="aef05-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="aef05-695">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="aef05-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="aef05-696">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.</span><span class="sxs-lookup"><span data-stu-id="aef05-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="aef05-697">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="aef05-698">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="aef05-699">K vystavení všech proměnných prostředí, které jsou k dispozici pro aplikaci, změňte na `FilteredConfiguration` *stránkách/index. cshtml. cs* následující:</span><span class="sxs-lookup"><span data-stu-id="aef05-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="aef05-700">Předpony</span><span class="sxs-lookup"><span data-stu-id="aef05-700">Prefixes</span></span>

<span data-ttu-id="aef05-701">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="aef05-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="aef05-702">Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_` , zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="aef05-703">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="aef05-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="aef05-704">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="aef05-705">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="aef05-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="aef05-706">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="aef05-706">**Connection string prefixes**</span></span>

<span data-ttu-id="aef05-707">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="aef05-708">Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="aef05-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="aef05-709">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="aef05-709">Connection string prefix</span></span> | <span data-ttu-id="aef05-710">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="aef05-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="aef05-711">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="aef05-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="aef05-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="aef05-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="aef05-713">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="aef05-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="aef05-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="aef05-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="aef05-715">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="aef05-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="aef05-716">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="aef05-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="aef05-717">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="aef05-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="aef05-718">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="aef05-718">Environment variable key</span></span> | <span data-ttu-id="aef05-719">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="aef05-719">Converted configuration key</span></span> | <span data-ttu-id="aef05-720">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="aef05-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="aef05-721">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="aef05-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="aef05-722">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="aef05-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="aef05-723">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="aef05-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="aef05-724">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="aef05-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="aef05-725">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="aef05-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="aef05-726">Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="aef05-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="aef05-727">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="aef05-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="aef05-728">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="aef05-728">**Example**</span></span>

<span data-ttu-id="aef05-729">Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="aef05-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="aef05-730">Název: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="aef05-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="aef05-731">Hodnota: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="aef05-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="aef05-732">Pokud `IConfiguration` je vloženo a přiřazeno k poli s názvem `_config` , přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="aef05-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="aef05-733">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="aef05-733">File Configuration Provider</span></span>

<span data-ttu-id="aef05-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="aef05-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="aef05-735">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="aef05-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="aef05-736">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="aef05-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="aef05-737">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="aef05-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="aef05-738">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="aef05-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="aef05-739">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="aef05-739">INI Configuration Provider</span></span>

<span data-ttu-id="aef05-740"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="aef05-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="aef05-741">Chcete-li aktivovat konfiguraci souboru INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aef05-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="aef05-742">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="aef05-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="aef05-743">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="aef05-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="aef05-744">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="aef05-744">Whether the file is optional.</span></span>
* <span data-ttu-id="aef05-745">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="aef05-746"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="aef05-747">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="aef05-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="aef05-748">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="aef05-748">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="aef05-749">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="aef05-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="aef05-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-750">section0:key0</span></span>
* <span data-ttu-id="aef05-751">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-751">section0:key1</span></span>
* <span data-ttu-id="aef05-752">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-752">section1:subsection:key</span></span>
* <span data-ttu-id="aef05-753">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-753">section2:subsection0:key</span></span>
* <span data-ttu-id="aef05-754">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="aef05-755">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="aef05-755">JSON Configuration Provider</span></span>

<span data-ttu-id="aef05-756"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="aef05-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="aef05-757">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aef05-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="aef05-758">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="aef05-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="aef05-759">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="aef05-759">Whether the file is optional.</span></span>
* <span data-ttu-id="aef05-760">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="aef05-761"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="aef05-762">`AddJsonFile` se automaticky volá dvakrát při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="aef05-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="aef05-763">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="aef05-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="aef05-764">*appsettings.js*: Tento soubor je nejprve přečten.</span><span class="sxs-lookup"><span data-stu-id="aef05-764">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="aef05-765">Verze prostředí souboru může přepsat hodnoty poskytnuté *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-765">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="aef05-766">*appSettings. {Environment}. JSON*: verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="aef05-766">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="aef05-767">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="aef05-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="aef05-768">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="aef05-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="aef05-769">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-769">Environment variables.</span></span>
* <span data-ttu-id="aef05-770">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="aef05-771">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="aef05-771">Command-line arguments.</span></span>

<span data-ttu-id="aef05-772">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="aef05-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="aef05-773">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="aef05-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="aef05-774">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appsettings.jsv* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="aef05-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="aef05-775">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="aef05-775">**Example**</span></span>

<span data-ttu-id="aef05-776">Ukázková aplikace využívá ke sestavení hostitele výhodu technologie statického usnadnění `CreateDefaultBuilder` , která zahrnuje dvě volání `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="aef05-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="aef05-777">První volání `AddJsonFile` načtení konfigurace z *appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="aef05-777">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="aef05-778">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="aef05-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="aef05-779">Pro *appsettings.Development.js* v ukázkové aplikaci se načte následující soubor:</span><span class="sxs-lookup"><span data-stu-id="aef05-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="aef05-780">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="aef05-780">Run the sample app.</span></span> <span data-ttu-id="aef05-781">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="aef05-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="aef05-782">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="aef05-783">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="aef05-784">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="aef05-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="aef05-785">Otevřete *vlastnosti/launchSettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="aef05-786">V `ConfigurationSample` profilu změňte hodnotu `ASPNETCORE_ENVIRONMENT` proměnné prostředí na `Production` .</span><span class="sxs-lookup"><span data-stu-id="aef05-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="aef05-787">Uložte soubor a spusťte aplikaci `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="aef05-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="aef05-788">Nastavení v *appsettings.Development.js* už nepřepisují nastavení v *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="aef05-788">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="aef05-789">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Warning` .</span><span class="sxs-lookup"><span data-stu-id="aef05-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="aef05-790">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="aef05-790">XML Configuration Provider</span></span>

<span data-ttu-id="aef05-791"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="aef05-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="aef05-792">Chcete-li aktivovat konfiguraci souboru XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aef05-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="aef05-793">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="aef05-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="aef05-794">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="aef05-794">Whether the file is optional.</span></span>
* <span data-ttu-id="aef05-795">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="aef05-796"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="aef05-797">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="aef05-798">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="aef05-799">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="aef05-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="aef05-800">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="aef05-800">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="aef05-801">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="aef05-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="aef05-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-802">section0:key0</span></span>
* <span data-ttu-id="aef05-803">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-803">section0:key1</span></span>
* <span data-ttu-id="aef05-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-804">section1:key0</span></span>
* <span data-ttu-id="aef05-805">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-805">section1:key1</span></span>

<span data-ttu-id="aef05-806">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="aef05-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="aef05-807">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="aef05-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="aef05-808">oddíl: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="aef05-808">section:section0:key:key0</span></span>
* <span data-ttu-id="aef05-809">oddíl: section0: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-809">section:section0:key:key1</span></span>
* <span data-ttu-id="aef05-810">oddíl: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="aef05-810">section:section1:key:key0</span></span>
* <span data-ttu-id="aef05-811">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-811">section:section1:key:key1</span></span>

<span data-ttu-id="aef05-812">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="aef05-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="aef05-813">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="aef05-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="aef05-814">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="aef05-814">key:attribute</span></span>
* <span data-ttu-id="aef05-815">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="aef05-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="aef05-816">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="aef05-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="aef05-817"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="aef05-818">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-818">The key is the file name.</span></span> <span data-ttu-id="aef05-819">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="aef05-819">The value contains the file's contents.</span></span> <span data-ttu-id="aef05-820">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="aef05-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="aef05-821">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aef05-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="aef05-822">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="aef05-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="aef05-823">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="aef05-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="aef05-824">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="aef05-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="aef05-825">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="aef05-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="aef05-826">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="aef05-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="aef05-827">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="aef05-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="aef05-828">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="aef05-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="aef05-829">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="aef05-829">Memory Configuration Provider</span></span>

<span data-ttu-id="aef05-830"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="aef05-831">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="aef05-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="aef05-832">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="aef05-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="aef05-833">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="aef05-834">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="aef05-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="aef05-835">Slovník se používá s voláním k `AddInMemoryCollection` poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="aef05-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="aef05-836">GetValue</span></span>

<span data-ttu-id="aef05-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="aef05-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="aef05-838">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="aef05-838">An overload accepts a default value.</span></span>

<span data-ttu-id="aef05-839">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="aef05-839">The following example:</span></span>

* <span data-ttu-id="aef05-840">Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="aef05-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="aef05-841">Pokud se `NumberKey` v konfiguračních klíčích nenajde, použije se výchozí hodnota `99` .</span><span class="sxs-lookup"><span data-stu-id="aef05-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="aef05-842">Zadá hodnotu jako `int` .</span><span class="sxs-lookup"><span data-stu-id="aef05-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="aef05-843">Ukládá hodnotu `NumberConfig` vlastnosti pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="aef05-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="aef05-844">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="aef05-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="aef05-845">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="aef05-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="aef05-846">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="aef05-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="aef05-847">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="aef05-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-848">section0:key0</span></span>
* <span data-ttu-id="aef05-849">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-849">section0:key1</span></span>
* <span data-ttu-id="aef05-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-850">section1:key0</span></span>
* <span data-ttu-id="aef05-851">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-851">section1:key1</span></span>
* <span data-ttu-id="aef05-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="aef05-853">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="aef05-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="aef05-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="aef05-855">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="aef05-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="aef05-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="aef05-856">GetSection</span></span>

<span data-ttu-id="aef05-857">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="aef05-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="aef05-858">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1` , zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="aef05-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="aef05-859">`configSection`Hodnota neobsahuje pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="aef05-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="aef05-860">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0` , zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="aef05-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="aef05-861">`GetSection` nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="aef05-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="aef05-862">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="aef05-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="aef05-863">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="aef05-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="aef05-864"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="aef05-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="aef05-865">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="aef05-865">GetChildren</span></span>

<span data-ttu-id="aef05-866">Volání [IConfiguration. Getchildrens](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="aef05-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="aef05-867">Existuje</span><span class="sxs-lookup"><span data-stu-id="aef05-867">Exists</span></span>

<span data-ttu-id="aef05-868">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="aef05-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="aef05-869">Vzhledem k ukázkovým datům `sectionExists` je `false` to proto, že `section2:subsection2` v konfiguračních datech není oddíl.</span><span class="sxs-lookup"><span data-stu-id="aef05-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="aef05-870">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="aef05-870">Bind to an object graph</span></span>

<span data-ttu-id="aef05-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="aef05-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="aef05-872">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="aef05-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="aef05-873">Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="aef05-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="aef05-874">Ukázková aplikace má *tvshow.xml* soubor obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="aef05-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="aef05-875">Konfigurace je svázána s celým `TvShow` grafem objektů pomocí `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="aef05-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="aef05-876">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="aef05-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="aef05-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="aef05-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="aef05-878">`Get<T>` je pohodlnější než použití `Bind` .</span><span class="sxs-lookup"><span data-stu-id="aef05-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="aef05-879">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem:</span><span class="sxs-lookup"><span data-stu-id="aef05-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="aef05-880">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="aef05-880">Bind an array to a class</span></span>

<span data-ttu-id="aef05-881">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="aef05-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="aef05-882"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="aef05-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="aef05-883">Libovolný formát pole, který zveřejňuje numerický klíčový segment ( `:0:` , `:1:` ,), &hellip; `:{n}:` je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="aef05-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="aef05-884">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="aef05-884">Binding is provided by convention.</span></span> <span data-ttu-id="aef05-885">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="aef05-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="aef05-886">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="aef05-886">**In-memory array processing**</span></span>

<span data-ttu-id="aef05-887">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="aef05-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="aef05-888">Klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-888">Key</span></span>             | <span data-ttu-id="aef05-889">Hodnota</span><span class="sxs-lookup"><span data-stu-id="aef05-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="aef05-890">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="aef05-890">array:entries:0</span></span> | <span data-ttu-id="aef05-891">value0</span><span class="sxs-lookup"><span data-stu-id="aef05-891">value0</span></span> |
| <span data-ttu-id="aef05-892">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="aef05-892">array:entries:1</span></span> | <span data-ttu-id="aef05-893">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="aef05-893">value1</span></span> |
| <span data-ttu-id="aef05-894">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="aef05-894">array:entries:2</span></span> | <span data-ttu-id="aef05-895">Argument</span><span class="sxs-lookup"><span data-stu-id="aef05-895">value2</span></span> |
| <span data-ttu-id="aef05-896">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="aef05-896">array:entries:4</span></span> | <span data-ttu-id="aef05-897">value4</span><span class="sxs-lookup"><span data-stu-id="aef05-897">value4</span></span> |
| <span data-ttu-id="aef05-898">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="aef05-898">array:entries:5</span></span> | <span data-ttu-id="aef05-899">value5</span><span class="sxs-lookup"><span data-stu-id="aef05-899">value5</span></span> |

<span data-ttu-id="aef05-900">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="aef05-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="aef05-901">Pole přeskočí hodnotu indexu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="aef05-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="aef05-902">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="aef05-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="aef05-903">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="aef05-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="aef05-904">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="aef05-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="aef05-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lze také použít syntaxi, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="aef05-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="aef05-906">Vázaný objekt, instance `ArrayExample` , přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="aef05-907">`ArrayExample.Entries` Indexovacím</span><span class="sxs-lookup"><span data-stu-id="aef05-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="aef05-908">`ArrayExample.Entries` Osa</span><span class="sxs-lookup"><span data-stu-id="aef05-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="aef05-909">0</span><span class="sxs-lookup"><span data-stu-id="aef05-909">0</span></span>                            | <span data-ttu-id="aef05-910">value0</span><span class="sxs-lookup"><span data-stu-id="aef05-910">value0</span></span>                       |
| <span data-ttu-id="aef05-911">1</span><span class="sxs-lookup"><span data-stu-id="aef05-911">1</span></span>                            | <span data-ttu-id="aef05-912">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="aef05-912">value1</span></span>                       |
| <span data-ttu-id="aef05-913">2</span><span class="sxs-lookup"><span data-stu-id="aef05-913">2</span></span>                            | <span data-ttu-id="aef05-914">Argument</span><span class="sxs-lookup"><span data-stu-id="aef05-914">value2</span></span>                       |
| <span data-ttu-id="aef05-915">3</span><span class="sxs-lookup"><span data-stu-id="aef05-915">3</span></span>                            | <span data-ttu-id="aef05-916">value4</span><span class="sxs-lookup"><span data-stu-id="aef05-916">value4</span></span>                       |
| <span data-ttu-id="aef05-917">4</span><span class="sxs-lookup"><span data-stu-id="aef05-917">4</span></span>                            | <span data-ttu-id="aef05-918">value5</span><span class="sxs-lookup"><span data-stu-id="aef05-918">value5</span></span>                       |

<span data-ttu-id="aef05-919">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="aef05-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="aef05-920">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="aef05-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="aef05-921">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="aef05-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="aef05-922">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na `ArrayExample` instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="aef05-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="aef05-923">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="aef05-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="aef05-924">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="aef05-924">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="aef05-925">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="aef05-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="aef05-926">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="aef05-927">Klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-927">Key</span></span>             | <span data-ttu-id="aef05-928">Hodnota</span><span class="sxs-lookup"><span data-stu-id="aef05-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="aef05-929">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="aef05-929">array:entries:3</span></span> | <span data-ttu-id="aef05-930">hodnota3</span><span class="sxs-lookup"><span data-stu-id="aef05-930">value3</span></span> |

<span data-ttu-id="aef05-931">Je-li `ArrayExample` instance třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro index &num; 3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="aef05-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="aef05-932">`ArrayExample.Entries` Indexovacím</span><span class="sxs-lookup"><span data-stu-id="aef05-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="aef05-933">`ArrayExample.Entries` Osa</span><span class="sxs-lookup"><span data-stu-id="aef05-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="aef05-934">0</span><span class="sxs-lookup"><span data-stu-id="aef05-934">0</span></span>                            | <span data-ttu-id="aef05-935">value0</span><span class="sxs-lookup"><span data-stu-id="aef05-935">value0</span></span>                       |
| <span data-ttu-id="aef05-936">1</span><span class="sxs-lookup"><span data-stu-id="aef05-936">1</span></span>                            | <span data-ttu-id="aef05-937">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="aef05-937">value1</span></span>                       |
| <span data-ttu-id="aef05-938">2</span><span class="sxs-lookup"><span data-stu-id="aef05-938">2</span></span>                            | <span data-ttu-id="aef05-939">Argument</span><span class="sxs-lookup"><span data-stu-id="aef05-939">value2</span></span>                       |
| <span data-ttu-id="aef05-940">3</span><span class="sxs-lookup"><span data-stu-id="aef05-940">3</span></span>                            | <span data-ttu-id="aef05-941">hodnota3</span><span class="sxs-lookup"><span data-stu-id="aef05-941">value3</span></span>                       |
| <span data-ttu-id="aef05-942">4</span><span class="sxs-lookup"><span data-stu-id="aef05-942">4</span></span>                            | <span data-ttu-id="aef05-943">value4</span><span class="sxs-lookup"><span data-stu-id="aef05-943">value4</span></span>                       |
| <span data-ttu-id="aef05-944">5</span><span class="sxs-lookup"><span data-stu-id="aef05-944">5</span></span>                            | <span data-ttu-id="aef05-945">value5</span><span class="sxs-lookup"><span data-stu-id="aef05-945">value5</span></span>                       |

<span data-ttu-id="aef05-946">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="aef05-946">**JSON array processing**</span></span>

<span data-ttu-id="aef05-947">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="aef05-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="aef05-948">V následujícím konfiguračním souboru `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="aef05-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="aef05-949">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="aef05-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="aef05-950">Klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-950">Key</span></span>                     | <span data-ttu-id="aef05-951">Hodnota</span><span class="sxs-lookup"><span data-stu-id="aef05-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="aef05-952">json_array: klíč</span><span class="sxs-lookup"><span data-stu-id="aef05-952">json_array:key</span></span>          | <span data-ttu-id="aef05-953">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="aef05-953">valueA</span></span> |
| <span data-ttu-id="aef05-954">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="aef05-954">json_array:subsection:0</span></span> | <span data-ttu-id="aef05-955">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="aef05-955">valueB</span></span> |
| <span data-ttu-id="aef05-956">json_array: pododdíl: 1</span><span class="sxs-lookup"><span data-stu-id="aef05-956">json_array:subsection:1</span></span> | <span data-ttu-id="aef05-957">valueC</span><span class="sxs-lookup"><span data-stu-id="aef05-957">valueC</span></span> |
| <span data-ttu-id="aef05-958">json_array: pododdíl: 2</span><span class="sxs-lookup"><span data-stu-id="aef05-958">json_array:subsection:2</span></span> | <span data-ttu-id="aef05-959">s</span><span class="sxs-lookup"><span data-stu-id="aef05-959">valueD</span></span> |

<span data-ttu-id="aef05-960">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="aef05-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="aef05-961">Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu `valueA` .</span><span class="sxs-lookup"><span data-stu-id="aef05-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="aef05-962">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="aef05-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="aef05-963">`JsonArrayExample.Subsection` Indexovacím</span><span class="sxs-lookup"><span data-stu-id="aef05-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="aef05-964">`JsonArrayExample.Subsection` Osa</span><span class="sxs-lookup"><span data-stu-id="aef05-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="aef05-965">0</span><span class="sxs-lookup"><span data-stu-id="aef05-965">0</span></span>                                   | <span data-ttu-id="aef05-966">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="aef05-966">valueB</span></span>                              |
| <span data-ttu-id="aef05-967">1</span><span class="sxs-lookup"><span data-stu-id="aef05-967">1</span></span>                                   | <span data-ttu-id="aef05-968">valueC</span><span class="sxs-lookup"><span data-stu-id="aef05-968">valueC</span></span>                              |
| <span data-ttu-id="aef05-969">2</span><span class="sxs-lookup"><span data-stu-id="aef05-969">2</span></span>                                   | <span data-ttu-id="aef05-970">s</span><span class="sxs-lookup"><span data-stu-id="aef05-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="aef05-971">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="aef05-971">Custom configuration provider</span></span>

<span data-ttu-id="aef05-972">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="aef05-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="aef05-973">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="aef05-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="aef05-974">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="aef05-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="aef05-975">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="aef05-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="aef05-976">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="aef05-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="aef05-977">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="aef05-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="aef05-978">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="aef05-979">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="aef05-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="aef05-980">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-980">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="aef05-981">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="aef05-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="aef05-982">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-982">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="aef05-983">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="aef05-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="aef05-984">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-984">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="aef05-985">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="aef05-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="aef05-986">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="aef05-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="aef05-987">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-987">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="aef05-988">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="aef05-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="aef05-989">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-989">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="aef05-990">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="aef05-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="aef05-991">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="aef05-991">Access configuration during startup</span></span>

<span data-ttu-id="aef05-992">Vložení `IConfiguration` do `Startup` konstruktoru pro přístup k hodnotám konfigurace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="aef05-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aef05-993">Chcete-li získat přístup ke konfiguraci v `Startup.Configure` , buď `IConfiguration` přímo do metody, nebo použijte instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="aef05-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="aef05-994">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="aef05-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="aef05-995">Přístup ke konfiguraci na Razor stránce stránky nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="aef05-995">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="aef05-996">Chcete-li získat přístup k nastavení konfigurace na Razor stránce stránky nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([Referenční dokumentace jazyka C#: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [ obor názvůMicrosoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) a vložte ho <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="aef05-996">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="aef05-997">Na Razor stránce stránky:</span><span class="sxs-lookup"><span data-stu-id="aef05-997">In a Razor Pages page:</span></span>

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

<span data-ttu-id="aef05-998">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="aef05-998">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="aef05-999">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="aef05-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="aef05-1000"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="aef05-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="aef05-1001">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="aef05-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aef05-1002">Další materiály</span><span class="sxs-lookup"><span data-stu-id="aef05-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

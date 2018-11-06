---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Zjistěte, jak použít rozhraní API pro konfiguraci ke konfiguraci aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2018
uid: fundamentals/configuration/index
ms.openlocfilehash: 6dd478770d4eae4d497da576c17fbe7d2c133b89
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021739"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="b4e27-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4e27-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="b4e27-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b4e27-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b4e27-105">Konfigurace aplikace v ASP.NET Core je založená na páry klíč hodnota stanovené *poskytovatelé konfigurace*.</span><span class="sxs-lookup"><span data-stu-id="b4e27-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="b4e27-106">Poskytovatelé konfigurace čtení konfiguračních dat do párů hodnot klíčů z různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b4e27-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="b4e27-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b4e27-107">Azure Key Vault</span></span>
* <span data-ttu-id="b4e27-108">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-108">Command-line arguments</span></span>
* <span data-ttu-id="b4e27-109">Vlastní zprostředkovatelé (nainstalované nebo vytváření)</span><span class="sxs-lookup"><span data-stu-id="b4e27-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b4e27-110">Adresář souborů</span><span class="sxs-lookup"><span data-stu-id="b4e27-110">Directory files</span></span>
* <span data-ttu-id="b4e27-111">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-111">Environment variables</span></span>
* <span data-ttu-id="b4e27-112">Objekty v paměti .NET</span><span class="sxs-lookup"><span data-stu-id="b4e27-112">In-memory .NET objects</span></span>
* <span data-ttu-id="b4e27-113">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="b4e27-113">Settings files</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

* <span data-ttu-id="b4e27-114">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b4e27-114">Azure Key Vault</span></span>
* <span data-ttu-id="b4e27-115">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-115">Command-line arguments</span></span>
* <span data-ttu-id="b4e27-116">Vlastní zprostředkovatelé (nainstalované nebo vytváření)</span><span class="sxs-lookup"><span data-stu-id="b4e27-116">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b4e27-117">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-117">Environment variables</span></span>
* <span data-ttu-id="b4e27-118">Objekty v paměti .NET</span><span class="sxs-lookup"><span data-stu-id="b4e27-118">In-memory .NET objects</span></span>
* <span data-ttu-id="b4e27-119">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="b4e27-119">Settings files</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.0"

* <span data-ttu-id="b4e27-120">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-120">Command-line arguments</span></span>
* <span data-ttu-id="b4e27-121">Vlastní zprostředkovatelé (nainstalované nebo vytváření)</span><span class="sxs-lookup"><span data-stu-id="b4e27-121">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b4e27-122">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-122">Environment variables</span></span>
* <span data-ttu-id="b4e27-123">Objekty v paměti .NET</span><span class="sxs-lookup"><span data-stu-id="b4e27-123">In-memory .NET objects</span></span>
* <span data-ttu-id="b4e27-124">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="b4e27-124">Settings files</span></span>

::: moniker-end

<span data-ttu-id="b4e27-125">*Možnosti vzor* je rozšířením konfigurace koncepty popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-125">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="b4e27-126">Možnosti třídy používá k reprezentování skupiny související nastavení.</span><span class="sxs-lookup"><span data-stu-id="b4e27-126">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b4e27-127">Další informace o použití vzoru možnosti najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-127">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b4e27-128">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4e27-128">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b4e27-129">Příklady uvedené v tomto tématu využívají:</span><span class="sxs-lookup"><span data-stu-id="b4e27-129">The examples provided in this topic rely upon:</span></span>

* <span data-ttu-id="b4e27-130">Nastavení základní cesty aplikace s využitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-130">Setting the base path of the app with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b4e27-131">`SetBasePath` je k dispozici pro aplikaci odkazováním [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="b4e27-131">`SetBasePath` is made available to an app by referencing the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package.</span></span>
* <span data-ttu-id="b4e27-132">Řešení oddíly konfiguračních souborů s <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-132">Resolving sections of configuration files with <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>.</span></span> <span data-ttu-id="b4e27-133">`GetSection` je k dispozici pro aplikaci odkazováním [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="b4e27-133">`GetSection` is made available to an app by referencing the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package.</span></span>
* <span data-ttu-id="b4e27-134">Konfigurace vazby na .NET třídy s <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> a [získat&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*).</span><span class="sxs-lookup"><span data-stu-id="b4e27-134">Binding configuration to .NET classes with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> and [Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*).</span></span> <span data-ttu-id="b4e27-135">`Bind` a `Get<T>` jsou k dispozici pro aplikaci odkazováním [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="b4e27-135">`Bind` and `Get<T>` are made available to an app by referencing the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package.</span></span> <span data-ttu-id="b4e27-136">`Get<T>` je k dispozici v ASP.NET Core 1.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="b4e27-136">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b4e27-137">Tyto tři balíčky jsou součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b4e27-137">These three packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b4e27-138">Tyto tři balíčky jsou součástí [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="b4e27-138">These three packages are included in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

## <a name="host-vs-app-configuration"></a><span data-ttu-id="b4e27-139">Hostování a konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="b4e27-139">Host vs. app configuration</span></span>

<span data-ttu-id="b4e27-140">Předtím, než aplikace je nakonfigurovaná a spuštěna, *hostitele* nakonfigurovaný a spustit.</span><span class="sxs-lookup"><span data-stu-id="b4e27-140">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b4e27-141">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="b4e27-141">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b4e27-142">Aplikace a hostitel jsou nakonfigurováni pomocí zprostředkovatele konfigurace popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-142">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b4e27-143">Páry klíč hodnota konfigurace hostitele se stanou součástí globální konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-143">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="b4e27-144">Další informace o jak konfiguraci poskytovatele se používají při vytváření hostitele a vliv zdroje konfigurace hostitele konfigurace najdete v tématu <xref:fundamentals/host/index>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-144">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/host/index>.</span></span>

## <a name="security"></a><span data-ttu-id="b4e27-145">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="b4e27-145">Security</span></span>

<span data-ttu-id="b4e27-146">Použijte následující osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="b4e27-146">Adopt the following best practices:</span></span>

* <span data-ttu-id="b4e27-147">Nikdy ukládat hesla a jiná citlivá data v konfiguraci zprostředkovatele kódu nebo v konfiguračních souborech na prostý text.</span><span class="sxs-lookup"><span data-stu-id="b4e27-147">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="b4e27-148">Nechcete používat produkční tajných kódů při vývoji nebo testovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="b4e27-148">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b4e27-149">Zadejte tajných kódů mimo projekt tak, že nemohou být omylem zaměřuje na úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-149">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b4e27-150">Další informace o [jak používat více prostředí](xref:fundamentals/environments) a správu [bezpečné ukládání tajných kódů aplikace při vývoji pomocí manažera tajných](xref:security/app-secrets) (včetně poradit se správným určením použití proměnných k ukládání citlivá data).</span><span class="sxs-lookup"><span data-stu-id="b4e27-150">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="b4e27-151">Správce tajný klíč používá zprostředkovatel konfigurace souboru ukládat tajné klíče uživatelů v souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="b4e27-151">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="b4e27-152">Zprostředkovatel konfigurace souboru je popsána dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-152">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b4e27-153">[Služba Azure Key Vault](https://azure.microsoft.com/services/key-vault/) je jednou z možností pro bezpečné ukládání tajných klíčů aplikací.</span><span class="sxs-lookup"><span data-stu-id="b4e27-153">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="b4e27-154">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-154">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b4e27-155">Hierarchické konfigurační data</span><span class="sxs-lookup"><span data-stu-id="b4e27-155">Hierarchical configuration data</span></span>

<span data-ttu-id="b4e27-156">Konfigurační rozhraní API je schopni zachovat hierarchické konfigurační data linearizovat hierarchických dat s použitím oddělovače v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b4e27-156">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b4e27-157">V následujícím souboru JSON existují čtyři kódy v hierarchii strukturovaných ze dvou částí:</span><span class="sxs-lookup"><span data-stu-id="b4e27-157">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="b4e27-158">Pokud je soubor pro čtení do konfigurace, jedinečné klíče se vytvoří zachovat původní struktury hierarchická data zdroj konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-158">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="b4e27-159">Oddíly a klíče se sloučí s použitím dvojtečkou (`:`) Chcete-li zachovat původní struktury:</span><span class="sxs-lookup"><span data-stu-id="b4e27-159">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="b4e27-160">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-160">section0:key0</span></span>
* <span data-ttu-id="b4e27-161">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-161">section0:key1</span></span>
* <span data-ttu-id="b4e27-162">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-162">section1:key0</span></span>
* <span data-ttu-id="b4e27-163">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-163">section1:key1</span></span>

<span data-ttu-id="b4e27-164"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody jsou k dispozici k izolaci oddíly a podřízené objekty daného oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="b4e27-164"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b4e27-165">Tyto metody jsou popsané dále v [GetSection GetChildren – a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="b4e27-165">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="b4e27-166">Konvence</span><span class="sxs-lookup"><span data-stu-id="b4e27-166">Conventions</span></span>

<span data-ttu-id="b4e27-167">Konfigurace zdroje jsou při spuštění aplikace pro čtení v pořadí, že jsou uvedeny příslušné poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-167">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="b4e27-168">Poskytovatelé konfigurace souboru mají možnost znovu načíst konfiguraci je podkladový soubor nastavení se při změně po spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-168">File Configuration Providers have the ability to reload configuration when an underlying settings file is changed after app startup.</span></span> <span data-ttu-id="b4e27-169">Zprostředkovatel konfigurace souboru je popsána dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-169">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b4e27-170"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v aplikaci prvku [Dependency Injection (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b4e27-170"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [Dependency Injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b4e27-171">Poskytovatelé konfigurace nemůže využít DI, protože není k dispozici při jejich nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="b4e27-171">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="b4e27-172">Konfigurační klíče použijte následující konvence:</span><span class="sxs-lookup"><span data-stu-id="b4e27-172">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="b4e27-173">Klíče jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b4e27-173">Keys are case-insensitive.</span></span> <span data-ttu-id="b4e27-174">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="b4e27-174">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b4e27-175">Pokud poskytovateli stejné nebo různé konfigurace je nastavena hodnota pro stejný klíč, poslední hodnotu nastavit na klíč je hodnota.</span><span class="sxs-lookup"><span data-stu-id="b4e27-175">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="b4e27-176">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="b4e27-176">Hierarchical keys</span></span>
  * <span data-ttu-id="b4e27-177">V rámci rozhraní API pro konfiguraci, oddělovač dvojtečka (`:`) funguje na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b4e27-177">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b4e27-178">V seznamu proměnných prostředí oddělovač dvojtečka nemusí fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b4e27-178">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b4e27-179">Dvojitým podtržítkem (`__`) podporuje všechny platformy a je převedena na dvojtečkou.</span><span class="sxs-lookup"><span data-stu-id="b4e27-179">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="b4e27-180">Ve službě Azure Key Vault, hierarchické klíče, použijte `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="b4e27-180">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b4e27-181">Je nutné zadat kód pomlček nahraďte dvojtečkou tajné klíče jsou načtena do konfigurace vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-181">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b4e27-182"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Podporuje pole vazby na objekty pomocí indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b4e27-182">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b4e27-183">Pole vazby je popsána v [svázat pole třídy](#bind-an-array-to-a-class) oddílu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-183">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="b4e27-184">Hodnoty konfigurace přijmout následující konvence:</span><span class="sxs-lookup"><span data-stu-id="b4e27-184">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="b4e27-185">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="b4e27-185">Values are strings.</span></span>
* <span data-ttu-id="b4e27-186">Hodnoty Null nelze uložit v konfiguraci nebo vázány s objekty.</span><span class="sxs-lookup"><span data-stu-id="b4e27-186">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="b4e27-187">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="b4e27-187">Providers</span></span>

<span data-ttu-id="b4e27-188">V následující tabulce jsou uvedeny poskytovatelé konfigurace k dispozici pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4e27-188">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

::: moniker range=">= aspnetcore-2.1"

| <span data-ttu-id="b4e27-189">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b4e27-189">Provider</span></span> | <span data-ttu-id="b4e27-190">Obsahuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="b4e27-190">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b4e27-191">[Azure zprostředkovatel konfigurace trezoru klíčů](xref:security/key-vault-configuration) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="b4e27-191">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b4e27-192">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b4e27-192">Azure Key Vault</span></span> |
| [<span data-ttu-id="b4e27-193">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-193">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b4e27-194">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-194">Command-line parameters</span></span> |
| [<span data-ttu-id="b4e27-195">Vlastního poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="b4e27-195">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b4e27-196">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="b4e27-196">Custom source</span></span> |
| [<span data-ttu-id="b4e27-197">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-197">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b4e27-198">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-198">Environment variables</span></span> |
| [<span data-ttu-id="b4e27-199">Zprostředkovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b4e27-199">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b4e27-200">Soubory INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b4e27-200">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b4e27-201">Poskytovatel konfigurace pro každý soubor klíče</span><span class="sxs-lookup"><span data-stu-id="b4e27-201">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b4e27-202">Adresář souborů</span><span class="sxs-lookup"><span data-stu-id="b4e27-202">Directory files</span></span> |
| [<span data-ttu-id="b4e27-203">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b4e27-203">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b4e27-204">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="b4e27-204">In-memory collections</span></span> |
| <span data-ttu-id="b4e27-205">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="b4e27-205">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b4e27-206">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="b4e27-206">File in the user profile directory</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

| <span data-ttu-id="b4e27-207">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b4e27-207">Provider</span></span> | <span data-ttu-id="b4e27-208">Obsahuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="b4e27-208">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b4e27-209">[Azure zprostředkovatel konfigurace trezoru klíčů](xref:security/key-vault-configuration) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="b4e27-209">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b4e27-210">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b4e27-210">Azure Key Vault</span></span> |
| [<span data-ttu-id="b4e27-211">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-211">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b4e27-212">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-212">Command-line parameters</span></span> |
| [<span data-ttu-id="b4e27-213">Vlastního poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="b4e27-213">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b4e27-214">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="b4e27-214">Custom source</span></span> |
| [<span data-ttu-id="b4e27-215">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-215">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b4e27-216">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-216">Environment variables</span></span> |
| [<span data-ttu-id="b4e27-217">Zprostředkovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b4e27-217">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b4e27-218">Soubory INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b4e27-218">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b4e27-219">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b4e27-219">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b4e27-220">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="b4e27-220">In-memory collections</span></span> |
| <span data-ttu-id="b4e27-221">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="b4e27-221">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b4e27-222">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="b4e27-222">File in the user profile directory</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-1.0"

| <span data-ttu-id="b4e27-223">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b4e27-223">Provider</span></span> | <span data-ttu-id="b4e27-224">Obsahuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="b4e27-224">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="b4e27-225">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-225">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b4e27-226">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-226">Command-line parameters</span></span> |
| [<span data-ttu-id="b4e27-227">Vlastního poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="b4e27-227">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b4e27-228">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="b4e27-228">Custom source</span></span> |
| [<span data-ttu-id="b4e27-229">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-229">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b4e27-230">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-230">Environment variables</span></span> |
| [<span data-ttu-id="b4e27-231">Zprostředkovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b4e27-231">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b4e27-232">Soubory INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b4e27-232">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b4e27-233">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b4e27-233">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b4e27-234">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="b4e27-234">In-memory collections</span></span> |
| <span data-ttu-id="b4e27-235">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="b4e27-235">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b4e27-236">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="b4e27-236">File in the user profile directory</span></span> |

::: moniker-end

<span data-ttu-id="b4e27-237">Konfigurace zdrojů se čtení v pořadí, že jejich poskytovatelé konfigurace jsou zadány při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b4e27-237">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="b4e27-238">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, ne v pořadí, že váš kód může uspořádat je.</span><span class="sxs-lookup"><span data-stu-id="b4e27-238">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="b4e27-239">Pořadí poskytovatelé konfigurace ve vašem kódu tak, aby vyhovoval vašim prioritám pro podkladové zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-239">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="b4e27-240">Typická posloupnost poskytovatelé konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="b4e27-240">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="b4e27-241">Soubory (*appsettings.json*, *appsettings. { Prostředí} .json*, kde `{Environment}` je aktuálním prostředí hostování aplikace)</span><span class="sxs-lookup"><span data-stu-id="b4e27-241">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="b4e27-242">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b4e27-242">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="b4e27-243">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí jenom)</span><span class="sxs-lookup"><span data-stu-id="b4e27-243">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="b4e27-244">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-244">Environment variables</span></span>
1. <span data-ttu-id="b4e27-245">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-245">Command-line arguments</span></span>

<span data-ttu-id="b4e27-246">Je obvyklé na poslední pozici poskytovatele konfigurace příkazového řádku v sérii poskytovatele, jak povolit argumenty příkazového řádku k přepsání konfigurace nastavená poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="b4e27-246">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-247">Toto pořadí poskytovatelů přejde do místa při inicializaci nové <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-247">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b4e27-248">Další informace najdete v tématu [webového hostitele: nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b4e27-248">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-249">Toto pořadí poskytovatelů lze vytvořit pro aplikaci (ne hostiteli) s <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> volání a jeho <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder.Build*> metoda ve `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-249">This sequence of providers can be created for the app (not the host) with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> and a call to its <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder.Build*> method in `Startup`:</span></span>

```csharp
public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
            reloadOnChange: true);

    var appAssembly = Assembly.Load(new AssemblyName(env.ApplicationName));

    if (appAssembly != null)
    {
        builder.AddUserSecrets(appAssembly, optional: true);
    }

    builder.AddEnvironmentVariables();

    Configuration = builder.Build();
}

public IConfiguration Configuration { get; }

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IConfiguration>(Configuration);
}
```

<span data-ttu-id="b4e27-250">V předchozím příkladu název prostředí (`env.EnvironmentName`) a název aplikace sestavení (`env.ApplicationName`) jsou poskytovány <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-250">In the preceding example, the environment name (`env.EnvironmentName`) and app assembly name (`env.ApplicationName`) are provided by the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>.</span></span> <span data-ttu-id="b4e27-251">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-251">For more information, see <xref:fundamentals/environments>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="configureappconfiguration"></a><span data-ttu-id="b4e27-252">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b4e27-252">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b4e27-253">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření webového hostitele k určení aplikace poskytovatelé konfigurace kromě těch, přidání automaticky <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="b4e27-253">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the Web Host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

::: moniker-end

## <a name="command-line-configuration-provider"></a><span data-ttu-id="b4e27-254">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b4e27-254">Command-line Configuration Provider</span></span>

<span data-ttu-id="b4e27-255"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Načte konfiguraci z páry klíč hodnota pro argument příkazového řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-255">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="b4e27-256">K aktivaci příkazového řádku konfigurace <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> rozšiřující metoda je volána v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-256">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-257">`AddCommandLine` je automaticky volána, když inicializujete novou <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-257">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b4e27-258">Další informace najdete v tématu [webového hostitele: nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b4e27-258">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b4e27-259">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="b4e27-259">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b4e27-260">Volitelná konfigurace z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="b4e27-260">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="b4e27-261">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="b4e27-261">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b4e27-262">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b4e27-262">Environment variables.</span></span>

<span data-ttu-id="b4e27-263">`CreateDefaultBuilder` poslední přidá poskytovatele konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b4e27-263">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="b4e27-264">Argumenty příkazového řádku předané za běhu přepsat nastavení ostatní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-264">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="b4e27-265">`CreateDefaultBuilder` funguje, když je vytvořen hostiteli.</span><span class="sxs-lookup"><span data-stu-id="b4e27-265">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="b4e27-266">Proto příkazového řádku konfigurace aktivoval `CreateDefaultBuilder` může mít vliv na konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="b4e27-266">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b4e27-267">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-267">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b4e27-268">`AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-268">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b4e27-269">Pokud je potřeba zadat konfiguraci aplikace a stále mít možnost přepsat tuto konfiguraci s argumenty příkazového řádku, volání další poskytovatele aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> a volat `AddCommandLine` poslední.</span><span class="sxs-lookup"><span data-stu-id="b4e27-269">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call other providers here and call AddCommandLine last.
                config.AddCommandLine(args);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b4e27-270">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-270">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b4e27-271">Použít konfiguraci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> metody.</span><span class="sxs-lookup"><span data-stu-id="b4e27-271">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method.</span></span>

<span data-ttu-id="b4e27-272">`AddCommandLine` již byly volány `CreateDefaultBuilder` při `UseConfiguration` je volána.</span><span class="sxs-lookup"><span data-stu-id="b4e27-272">`AddCommandLine` has already been called by `CreateDefaultBuilder` when `UseConfiguration` is called.</span></span> <span data-ttu-id="b4e27-273">Pokud je potřeba zadat konfiguraci aplikace a stále mít možnost přepsat tuto konfiguraci s argumenty příkazového řádku, volání na další poskytovatele aplikaci `ConfigurationBuilder` a volat `AddCommandLine` poslední.</span><span class="sxs-lookup"><span data-stu-id="b4e27-273">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers on a `ConfigurationBuilder` and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            // Call other providers here and call AddCommandLine last.
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b4e27-274">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-274">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-275">Chcete-li aktivovat příkazového řádku konfigurace, zavolejte <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-275">To activate command-line configuration, call the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b4e27-276">Volání zprostředkovatele poslední Chcete-li povolit argumenty příkazového řádku předané za běhu přepsat konfiguraci nastavením jiných poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-276">Call the provider last to allow the command-line arguments passed at runtime to override configuration set by other configuration providers.</span></span>

<span data-ttu-id="b4e27-277">Použít konfiguraci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> metody:</span><span class="sxs-lookup"><span data-stu-id="b4e27-277">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    // Call additional providers here as needed.
    // Call AddCommandLine last to allow arguments to override other configuration.
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b4e27-278">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b4e27-278">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-279">2.x ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-279">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-280">Volání 1.x ukázkové aplikace <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-280">The 1.x sample app calls <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> on a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

::: moniker-end

1. <span data-ttu-id="b4e27-281">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-281">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="b4e27-282">Zadat argument příkazového řádku k `dotnet run` příkazu `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-282">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="b4e27-283">Jakmile je aplikace spuštěná, otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-283">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b4e27-284">Podívejte se, že výstup obsahuje pár klíč hodnota pro argument příkazového řádku konfigurace poskytnuté `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-284">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="b4e27-285">Arguments</span><span class="sxs-lookup"><span data-stu-id="b4e27-285">Arguments</span></span>

<span data-ttu-id="b4e27-286">Hodnota musí následovat znak rovná se (`=`), nebo klíč musí mít předponu (`--` nebo `/`) když hodnota má následující formát mezerou.</span><span class="sxs-lookup"><span data-stu-id="b4e27-286">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="b4e27-287">Hodnota může mít hodnotu null, pokud se používá znak rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="b4e27-287">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="b4e27-288">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="b4e27-288">Key prefix</span></span>               | <span data-ttu-id="b4e27-289">Příklad</span><span class="sxs-lookup"><span data-stu-id="b4e27-289">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="b4e27-290">Žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="b4e27-290">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="b4e27-291">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="b4e27-291">Two dashes (`--`)</span></span>        | <span data-ttu-id="b4e27-292">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="b4e27-292">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="b4e27-293">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="b4e27-293">Forward slash (`/`)</span></span>      | <span data-ttu-id="b4e27-294">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="b4e27-294">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="b4e27-295">V rámci stejného příkazu Nekombinujte argument příkazového řádku páry klíč hodnota, které používají znaménko rovná se s páry klíč hodnota, které používají mezerou.</span><span class="sxs-lookup"><span data-stu-id="b4e27-295">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="b4e27-296">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="b4e27-296">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="b4e27-297">Přepnout mapování</span><span class="sxs-lookup"><span data-stu-id="b4e27-297">Switch mappings</span></span>

<span data-ttu-id="b4e27-298">Mapování přepínači povolit název klíče pro náhradní logiku.</span><span class="sxs-lookup"><span data-stu-id="b4e27-298">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="b4e27-299">Při ruční sestavení konfigurace s <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, může poskytnout slovník nahrazení přepínači <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metoda.</span><span class="sxs-lookup"><span data-stu-id="b4e27-299">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b4e27-300">Při použití přepínače slovníku mapování slovníku se kontroluje u klíč, který odpovídá key určeného tímto argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b4e27-300">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b4e27-301">Pokud je nalezen příkazového řádku klíč ve slovníku, hodnota slovníku (náhradní klíč) se předá zpět do nastavit pár klíč hodnota v konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-301">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b4e27-302">Mapování přepínač je vyžadován pro libovolného příkazového řádku klíče předponu jeden pomlčka (`-`).</span><span class="sxs-lookup"><span data-stu-id="b4e27-302">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b4e27-303">Přepnout mapování slovníku klíčových pravidel:</span><span class="sxs-lookup"><span data-stu-id="b4e27-303">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b4e27-304">Přepínače musí začínat pomlčkou (`-`) nebo dvojitou pomlčka (`--`).</span><span class="sxs-lookup"><span data-stu-id="b4e27-304">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="b4e27-305">Slovník mapování přepínač nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="b4e27-305">The switch mappings dictionary must not contain duplicate keys.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b4e27-306">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b4e27-306">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _switchMappings = 
        new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    // Do not pass the args to CreateDefaultBuilder
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder()
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddCommandLine(args, _switchMappings);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b4e27-307">Jak je znázorněno v předchozím příkladu volání `CreateDefaultBuilder` by neměla předat argumenty při mapování přepínače se používají.</span><span class="sxs-lookup"><span data-stu-id="b4e27-307">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="b4e27-308">`CreateDefaultBuilder` metody `AddCommandLine` volání neobsahuje mapované přepínače a neexistuje žádný způsob předat slovníku mapování přepínač k `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-308">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b4e27-309">Pokud argumenty obsahují mapované přepínače a jsou předány `CreateDefaultBuilder`, jeho `AddCommandLine` poskytovatele dojde k selhání inicializace s <xref:System.FormatException>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-309">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="b4e27-310">Řešení není předat argumenty, které mají `CreateDefaultBuilder` , ale místo toho povolit `ConfigurationBuilder` metody `AddCommandLine` metodu ke zpracování argumentů a slovníku mapování přepínače.</span><span class="sxs-lookup"><span data-stu-id="b4e27-310">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var switchMappings = new Dictionary<string, string>
            {
                { "-CLKey1", "CommandLineKey1" },
                { "-CLKey2", "CommandLineKey2" }
            };

        var config = new ConfigurationBuilder()
            .AddCommandLine(args, switchMappings)
            .Build();

        // Do not pass the args to CreateDefaultBuilder
        return WebHost.CreateDefaultBuilder()
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b4e27-311">Jak je znázorněno v předchozím příkladu volání `CreateDefaultBuilder` by neměla předat argumenty při mapování přepínače se používají.</span><span class="sxs-lookup"><span data-stu-id="b4e27-311">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="b4e27-312">`CreateDefaultBuilder` metody `AddCommandLine` volání neobsahuje mapované přepínače a neexistuje žádný způsob předat slovníku mapování přepínač k `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-312">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b4e27-313">Pokud argumenty obsahují mapované přepínače a jsou předány `CreateDefaultBuilder`, jeho `AddCommandLine` poskytovatele dojde k selhání inicializace s <xref:System.FormatException>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-313">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="b4e27-314">Řešení není předat argumenty, které mají `CreateDefaultBuilder` , ale místo toho povolit `ConfigurationBuilder` metody `AddCommandLine` metodu ke zpracování argumentů a slovníku mapování přepínače.</span><span class="sxs-lookup"><span data-stu-id="b4e27-314">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
public static void Main(string[] args)
{
    var switchMappings = new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    var config = new ConfigurationBuilder()
        .AddCommandLine(args, switchMappings)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .UseStartup<Startup>()
        .Start();

    using (host)
    {
        Console.ReadLine();
    }
}
```

::: moniker-end

<span data-ttu-id="b4e27-315">Po vytvoření slovníku mapování přepínač obsahuje data zobrazená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b4e27-315">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="b4e27-316">Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-316">Key</span></span>       | <span data-ttu-id="b4e27-317">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-317">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="b4e27-318">Pokud při spuštění aplikace se používají klíče mapované na přepínač, konfigurace přijímá konfigurační hodnoty klíče zadané ve slovníku:</span><span class="sxs-lookup"><span data-stu-id="b4e27-318">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="b4e27-319">Po spuštění předchozího příkazu se konfigurace obsahuje hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b4e27-319">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="b4e27-320">Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-320">Key</span></span>               | <span data-ttu-id="b4e27-321">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-321">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="b4e27-322">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-322">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="b4e27-323"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Načte konfiguraci z prostředí proměnné páry klíč hodnota v době běhu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-323">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="b4e27-324">Chcete-li aktivovat konfigurace proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-324">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b4e27-325">Při práci s hierarchické klíče v seznamu proměnných prostředí, oddělovač dvojtečka (`:`) nemusí fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b4e27-325">When working with hierarchical keys in environment variables, a colon separator (`:`) may not work on all platforms.</span></span> <span data-ttu-id="b4e27-326">Dvojitým podtržítkem (`__`) podporuje všechny platformy a nahrazuje dvojtečkou.</span><span class="sxs-lookup"><span data-stu-id="b4e27-326">A double underscore (`__`) is supported by all platforms and is replaced by a colon.</span></span>

<span data-ttu-id="b4e27-327">[Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavit proměnné prostředí na webu Azure Portal, můžete přepsat konfiguraci aplikace pomocí zprostředkovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b4e27-327">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="b4e27-328">Další informace najdete v tématu [aplikace Azure: Konfigurace aplikace přepsat pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b4e27-328">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-329">`AddEnvironmentVariables` je automaticky volána, když inicializujete novou <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-329">`AddEnvironmentVariables` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b4e27-330">Další informace najdete v tématu [webového hostitele: nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b4e27-330">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b4e27-331">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="b4e27-331">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b4e27-332">Volitelná konfigurace z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="b4e27-332">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="b4e27-333">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="b4e27-333">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b4e27-334">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b4e27-334">Command-line arguments.</span></span>

<span data-ttu-id="b4e27-335">Zprostředkovatel konfigurace proměnných prostředí je volána po navázání konfigurace z tajných kódů uživatelů a *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="b4e27-335">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="b4e27-336">Volání zprostředkovatele na této pozici umožňuje proměnné prostředí načteny za běhu přepsat konfiguraci nastavil tajných kódů uživatelů a *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="b4e27-336">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b4e27-337">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-337">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b4e27-338">`AddEnvironmentVariables` pro proměnné prostředí s předponou `ASPNETCORE_` již byla volána `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-338">`AddEnvironmentVariables` for environment variables prefixed with `ASPNETCORE_` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b4e27-339">Pokud je potřeba zadat konfigurace aplikace z další proměnné prostředí, volání další poskytovatele aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> a volat `AddEnvironmentVariables` s předponou.</span><span class="sxs-lookup"><span data-stu-id="b4e27-339">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call additional providers here as needed.
                // Call AddEnvironmentVariables last if you need to allow environment
                // variables to override values from other providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b4e27-340">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-340">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b4e27-341">Volání `AddEnvironmentVariables` rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-341">Call the `AddEnvironmentVariables` extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b4e27-342">Použít konfiguraci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> metody.</span><span class="sxs-lookup"><span data-stu-id="b4e27-342">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method.</span></span>

<span data-ttu-id="b4e27-343">`AddEnvironmentVariables` pro proměnné prostředí s předponou `ASPNETCORE_` již byla volána `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-343">`AddEnvironmentVariables` for environment variables prefixed with `ASPNETCORE_` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b4e27-344">Pokud je potřeba zadat konfigurace aplikace z další proměnné prostředí, volání další poskytovatele aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> a volat `AddEnvironmentVariables` s předponou.</span><span class="sxs-lookup"><span data-stu-id="b4e27-344">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            // Call additional providers here as needed.
            // Call AddEnvironmentVariables last if you need to allow environment
            // variables to override values from other providers.
            .AddEnvironmentVariables(prefix: "PREFIX_")
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b4e27-345">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-345">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-346">Použít konfiguraci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> metody:</span><span class="sxs-lookup"><span data-stu-id="b4e27-346">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b4e27-347">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b4e27-347">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-348">2.x ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-348">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-349">Volání 1.x ukázkové aplikace `AddEnvironmentVariables` na `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-349">The 1.x sample app calls `AddEnvironmentVariables` on a `ConfigurationBuilder`.</span></span>

::: moniker-end

1. <span data-ttu-id="b4e27-350">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-350">Run the sample app.</span></span> <span data-ttu-id="b4e27-351">Otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-351">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b4e27-352">Podívejte se, že výstup obsahuje pár klíč hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-352">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="b4e27-353">Hodnota odpovídá prostředí, ve kterém je aplikace spuštěna, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="b4e27-353">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="b4e27-354">Udržovat seznam proměnných prostředí vykreslen metodou aplikace krátký, filtry aplikace proměnných prostředí až po ty spustit následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="b4e27-354">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="b4e27-355">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="b4e27-355">ASPNETCORE_</span></span>
* <span data-ttu-id="b4e27-356">adresy URL</span><span class="sxs-lookup"><span data-stu-id="b4e27-356">urls</span></span>
* <span data-ttu-id="b4e27-357">Protokolování</span><span class="sxs-lookup"><span data-stu-id="b4e27-357">Logging</span></span>
* <span data-ttu-id="b4e27-358">PROSTŘEDÍ</span><span class="sxs-lookup"><span data-stu-id="b4e27-358">ENVIRONMENT</span></span>
* <span data-ttu-id="b4e27-359">contentRoot</span><span class="sxs-lookup"><span data-stu-id="b4e27-359">contentRoot</span></span>
* <span data-ttu-id="b4e27-360">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="b4e27-360">AllowedHosts</span></span>
* <span data-ttu-id="b4e27-361">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="b4e27-361">applicationName</span></span>
* <span data-ttu-id="b4e27-362">příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="b4e27-362">CommandLine</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-363">Pokud chcete zpřístupnit všechny proměnné prostředí k dispozici pro aplikace, změnit `FilteredConfiguration` v *Pages/Index.cshtml.cs* takto:</span><span class="sxs-lookup"><span data-stu-id="b4e27-363">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-364">Pokud chcete zpřístupnit všechny proměnné prostředí k dispozici pro aplikace, změnit `FilteredConfiguration` v *Controllers/HomeController.cs* takto:</span><span class="sxs-lookup"><span data-stu-id="b4e27-364">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Controllers/HomeController.cs* to the following:</span></span>

::: moniker-end

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="b4e27-365">Předpony adres</span><span class="sxs-lookup"><span data-stu-id="b4e27-365">Prefixes</span></span>

<span data-ttu-id="b4e27-366">Proměnné prostředí načteny do konfiguraci aplikace, jsou filtrovány, když zadáte předponu `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="b4e27-366">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="b4e27-367">Například k proměnným prostředí filtr na této předponě `CUSTOM_`, zadat předponu pro zprostředkovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b4e27-367">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="b4e27-368">Předpona, která se odstraní při vytváření konfigurace páry klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="b4e27-368">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-369">Metoda statické pohodlí `CreateDefaultBuilder` vytvoří <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> stanovit hostiteli aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-369">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="b4e27-370">Když `WebHostBuilder` je vytvořen, zjistí jeho konfigurace hostitele v proměnných prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-370">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

::: moniker-end

<span data-ttu-id="b4e27-371">**Připojovací řetězec předpony**</span><span class="sxs-lookup"><span data-stu-id="b4e27-371">**Connection string prefixes**</span></span>

<span data-ttu-id="b4e27-372">Rozhraní API konfigurace má zvláštní zpracování pravidel pro čtyři připojovací řetězec proměnné prostředí používané při konfiguraci Azure připojovací řetězce pro prostředí app.</span><span class="sxs-lookup"><span data-stu-id="b4e27-372">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b4e27-373">Proměnné prostředí s předponami uvedené v tabulce se načtou do aplikace, pokud není zadána žádná předpona k `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-373">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b4e27-374">Předpona řetězce připojení</span><span class="sxs-lookup"><span data-stu-id="b4e27-374">Connection string prefix</span></span> | <span data-ttu-id="b4e27-375">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b4e27-375">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b4e27-376">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b4e27-376">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b4e27-377">MySQL</span><span class="sxs-lookup"><span data-stu-id="b4e27-377">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b4e27-378">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="b4e27-378">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b4e27-379">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b4e27-379">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b4e27-380">Proměnné prostředí je při zjištění a načtena do konfigurace s žádným z čtyři předpony, které jsou uvedené v tabulce:</span><span class="sxs-lookup"><span data-stu-id="b4e27-380">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b4e27-381">Konfigurační klíč je vytvořen odebráním předponu proměnné prostředí a přidání konfiguračního klíče oddílu (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="b4e27-381">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b4e27-382">Je vytvořen nový pár klíč hodnota konfigurace, který představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá stanoveného zprostředkovatele).</span><span class="sxs-lookup"><span data-stu-id="b4e27-382">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b4e27-383">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b4e27-383">Environment variable key</span></span> | <span data-ttu-id="b4e27-384">Převedený konfigurační klíč</span><span class="sxs-lookup"><span data-stu-id="b4e27-384">Converted configuration key</span></span> | <span data-ttu-id="b4e27-385">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="b4e27-385">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b4e27-386">Položky konfigurace nebyl vytvořen.</span><span class="sxs-lookup"><span data-stu-id="b4e27-386">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b4e27-387">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-387">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b4e27-388">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b4e27-388">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b4e27-389">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-389">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b4e27-390">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b4e27-390">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b4e27-391">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-391">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b4e27-392">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b4e27-392">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="b4e27-393">Zprostředkovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b4e27-393">File Configuration Provider</span></span>

<span data-ttu-id="b4e27-394"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třída pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="b4e27-394"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b4e27-395">Následující zprostředkovatele konfigurace jsou vyhrazené pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="b4e27-395">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="b4e27-396">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="b4e27-396">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b4e27-397">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="b4e27-397">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="b4e27-398">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="b4e27-398">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b4e27-399">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="b4e27-399">INI Configuration Provider</span></span>

<span data-ttu-id="b4e27-400"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Načte konfiguraci z páry klíč hodnota soubor INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-400">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b4e27-401">Chcete-li aktivovat konfigurační soubor INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-401">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b4e27-402">Dvojtečka je možné k jako oddělovač oddílu v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="b4e27-402">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="b4e27-403">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="b4e27-403">Overloads permit specifying:</span></span>

* <span data-ttu-id="b4e27-404">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="b4e27-404">Whether the file is optional.</span></span>
* <span data-ttu-id="b4e27-405">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="b4e27-405">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b4e27-406"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="b4e27-406">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b4e27-407">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b4e27-407">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddIniFile("config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b4e27-408">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-408">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b4e27-409">Při volání metody `CreateDefaultBuilder`, volání <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-409">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddIniFile("config.ini", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b4e27-410">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-410">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-411">Použít konfiguraci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> metody:</span><span class="sxs-lookup"><span data-stu-id="b4e27-411">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddIniFile("config.ini", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b4e27-412">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="b4e27-412">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="b4e27-413">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-413">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b4e27-414">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-414">section0:key0</span></span>
* <span data-ttu-id="b4e27-415">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-415">section0:key1</span></span>
* <span data-ttu-id="b4e27-416">section1:subsection:Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-416">section1:subsection:key</span></span>
* <span data-ttu-id="b4e27-417">section2:subsection0:Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-417">section2:subsection0:key</span></span>
* <span data-ttu-id="b4e27-418">section2:subsection1:Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-418">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="b4e27-419">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="b4e27-419">JSON Configuration Provider</span></span>

<span data-ttu-id="b4e27-420"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Načte konfiguraci z páry klíč hodnota JSON soubor za běhu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-420">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="b4e27-421">Chcete-li aktivovat konfigurační soubor JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-421">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b4e27-422">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="b4e27-422">Overloads permit specifying:</span></span>

* <span data-ttu-id="b4e27-423">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="b4e27-423">Whether the file is optional.</span></span>
* <span data-ttu-id="b4e27-424">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="b4e27-424">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b4e27-425"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="b4e27-425">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-426">`AddJsonFile` je automaticky volána dvakrát při inicializaci nové <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-426">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b4e27-427">Načtení konfigurace z volání metody:</span><span class="sxs-lookup"><span data-stu-id="b4e27-427">The method is called to load configuration from:</span></span>

* <span data-ttu-id="b4e27-428">*appSettings.JSON* &ndash; tento soubor je nejprve pro čtení.</span><span class="sxs-lookup"><span data-stu-id="b4e27-428">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="b4e27-429">Prostředí verze souboru mohou přepsat hodnoty poskytnuté *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b4e27-429">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="b4e27-430">*appSettings. {Prostředí} .json* &ndash; načtení na základě prostředí verze souboru [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b4e27-430">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="b4e27-431">Další informace najdete v tématu [webového hostitele: nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b4e27-431">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b4e27-432">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="b4e27-432">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b4e27-433">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b4e27-433">Environment variables.</span></span>
* <span data-ttu-id="b4e27-434">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="b4e27-434">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b4e27-435">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b4e27-435">Command-line arguments.</span></span>

<span data-ttu-id="b4e27-436">Zprostředkovatel konfigurace JSON je nejprve navázat.</span><span class="sxs-lookup"><span data-stu-id="b4e27-436">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="b4e27-437">Proto tajné klíče uživatelů, proměnné a argumenty příkazového řádku přepsat nastavením konfigurace *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="b4e27-437">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b4e27-438">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace pro soubory jiné než *appsettings.json* a *appsettings. { Prostředí} .json*:</span><span class="sxs-lookup"><span data-stu-id="b4e27-438">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddJsonFile("config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b4e27-439">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-439">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b4e27-440">Můžete také přímo zavolat `AddJsonFile` rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-440">You can also directly call the `AddJsonFile` extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b4e27-441">Při volání metody `CreateDefaultBuilder`, volání <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-441">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("config.json", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b4e27-442">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-442">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-443">Použít konfiguraci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> metody:</span><span class="sxs-lookup"><span data-stu-id="b4e27-443">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("config.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b4e27-444">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b4e27-444">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-445">2.x ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, která zahrnuje dvě volání `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-445">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="b4e27-446">Konfigurace je načtena z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="b4e27-446">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-447">Volání 1.x ukázkové aplikace `AddJsonFile` dvakrát na `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-447">The 1.x sample app calls `AddJsonFile` twice on a `ConfigurationBuilder`.</span></span> <span data-ttu-id="b4e27-448">Konfigurace je načtena z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="b4e27-448">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

::: moniker-end

1. <span data-ttu-id="b4e27-449">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-449">Run the sample app.</span></span> <span data-ttu-id="b4e27-450">Otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-450">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b4e27-451">Podívejte se, že výstup obsahuje páry klíč hodnota u konfigurace uvedené v tabulce v závislosti na prostředí.</span><span class="sxs-lookup"><span data-stu-id="b4e27-451">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="b4e27-452">Protokolování konfigurační klíče pomocí dvojtečky (`:`) jako oddělovačem hierarchický.</span><span class="sxs-lookup"><span data-stu-id="b4e27-452">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="b4e27-453">Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-453">Key</span></span>                        | <span data-ttu-id="b4e27-454">Hodnota vývoj</span><span class="sxs-lookup"><span data-stu-id="b4e27-454">Development Value</span></span> | <span data-ttu-id="b4e27-455">Hodnota produkce</span><span class="sxs-lookup"><span data-stu-id="b4e27-455">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="b4e27-456">Protokolování: LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="b4e27-456">Logging:LogLevel:System</span></span>    | <span data-ttu-id="b4e27-457">Informace o</span><span class="sxs-lookup"><span data-stu-id="b4e27-457">Information</span></span>       | <span data-ttu-id="b4e27-458">Informace o</span><span class="sxs-lookup"><span data-stu-id="b4e27-458">Information</span></span>      |
| <span data-ttu-id="b4e27-459">Protokolování: LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="b4e27-459">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="b4e27-460">Informace o</span><span class="sxs-lookup"><span data-stu-id="b4e27-460">Information</span></span>       | <span data-ttu-id="b4e27-461">Informace o</span><span class="sxs-lookup"><span data-stu-id="b4e27-461">Information</span></span>      |
| <span data-ttu-id="b4e27-462">Protokolování: LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="b4e27-462">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="b4e27-463">Ladit</span><span class="sxs-lookup"><span data-stu-id="b4e27-463">Debug</span></span>             | <span data-ttu-id="b4e27-464">Chyba</span><span class="sxs-lookup"><span data-stu-id="b4e27-464">Error</span></span>            |
| <span data-ttu-id="b4e27-465">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="b4e27-465">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="b4e27-466">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="b4e27-466">XML Configuration Provider</span></span>

<span data-ttu-id="b4e27-467"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Načte konfiguraci z dvojice klíč hodnota XML soubor za běhu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-467">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b4e27-468">Chcete-li aktivovat konfigurační soubor XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-468">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b4e27-469">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="b4e27-469">Overloads permit specifying:</span></span>

* <span data-ttu-id="b4e27-470">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="b4e27-470">Whether the file is optional.</span></span>
* <span data-ttu-id="b4e27-471">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="b4e27-471">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b4e27-472"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="b4e27-472">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b4e27-473">Kořenový uzel konfiguračního souboru se ignoruje při vytváření konfigurace páry klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="b4e27-473">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="b4e27-474">V souboru zadejte dokumentu typ definice (DTD) nebo obor názvů.</span><span class="sxs-lookup"><span data-stu-id="b4e27-474">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b4e27-475">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b4e27-475">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddXmlFile("config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b4e27-476">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-476">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b4e27-477">Při volání metody `CreateDefaultBuilder`, volání <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-477">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b4e27-478">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-478">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-479">Použít konfiguraci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> metody:</span><span class="sxs-lookup"><span data-stu-id="b4e27-479">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b4e27-480">Soubory XML konfigurace můžete použít názvy různých elementů pro opakující se části:</span><span class="sxs-lookup"><span data-stu-id="b4e27-480">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="b4e27-481">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-481">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b4e27-482">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-482">section0:key0</span></span>
* <span data-ttu-id="b4e27-483">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-483">section0:key1</span></span>
* <span data-ttu-id="b4e27-484">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-484">section1:key0</span></span>
* <span data-ttu-id="b4e27-485">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-485">section1:key1</span></span>

<span data-ttu-id="b4e27-486">Opakující se elementy, které používají stejný název elementu fungovat, pokud `name` atribut se používá k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="b4e27-486">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="b4e27-487">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-487">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b4e27-488">část: section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-488">section:section0:key:key0</span></span>
* <span data-ttu-id="b4e27-489">část: section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-489">section:section0:key:key1</span></span>
* <span data-ttu-id="b4e27-490">část: section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-490">section:section1:key:key0</span></span>
* <span data-ttu-id="b4e27-491">část: section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-491">section:section1:key:key1</span></span>

<span data-ttu-id="b4e27-492">Atributy lze použít k zadání hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b4e27-492">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b4e27-493">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-493">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b4e27-494">atribut Key:</span><span class="sxs-lookup"><span data-stu-id="b4e27-494">key:attribute</span></span>
* <span data-ttu-id="b4e27-495">část: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="b4e27-495">section:key:attribute</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b4e27-496">Poskytovatel konfigurace pro každý soubor klíče</span><span class="sxs-lookup"><span data-stu-id="b4e27-496">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="b4e27-497"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Používá souborů v adresáři jako páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-497">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b4e27-498">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="b4e27-498">The key is the file name.</span></span> <span data-ttu-id="b4e27-499">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="b4e27-499">The value contains the file's contents.</span></span> <span data-ttu-id="b4e27-500">Konfiguraci poskytovatele za soubor klíče se používá ve scénářích hostování Dockeru.</span><span class="sxs-lookup"><span data-stu-id="b4e27-500">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b4e27-501">Chcete-li aktivovat konfigurace na soubor klíče, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-501">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b4e27-502">`directoryPath` k souborům musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="b4e27-502">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b4e27-503">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="b4e27-503">Overloads permit specifying:</span></span>

* <span data-ttu-id="b4e27-504">`Action<KeyPerFileConfigurationSource>` Delegáta, který nastaví jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="b4e27-504">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b4e27-505">Určuje, zda daný adresář je volitelné a cestu k adresáři.</span><span class="sxs-lookup"><span data-stu-id="b4e27-505">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b4e27-506">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b4e27-506">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b4e27-507">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-507">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
var config = new ConfigurationBuilder()
    .AddKeyPerFile(directoryPath: path, optional: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

::: moniker-end

## <a name="memory-configuration-provider"></a><span data-ttu-id="b4e27-508">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b4e27-508">Memory Configuration Provider</span></span>

<span data-ttu-id="b4e27-509"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Používá kolekci v paměti jako páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-509">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b4e27-510">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-510">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b4e27-511">Poskytovatel konfigurace mohou být inicializovány pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-511">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b4e27-512">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b4e27-512">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _dict = 
        new Dictionary<string, string>
        {
            {"MemoryCollectionKey1", "value1"},
            {"MemoryCollectionKey2", "value2"}
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddInMemoryCollection(_dict);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b4e27-513">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-513">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b4e27-514">Při volání metody `CreateDefaultBuilder`, volání <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-514">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

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
}
```

<span data-ttu-id="b4e27-515">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b4e27-515">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-516">Použít konfiguraci <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> metody:</span><span class="sxs-lookup"><span data-stu-id="b4e27-516">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="getvalue"></a><span data-ttu-id="b4e27-517">GetValue</span><span class="sxs-lookup"><span data-stu-id="b4e27-517">GetValue</span></span>

<span data-ttu-id="b4e27-518">[ConfigurationBinder.GetValue&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje hodnotu z konfigurace se zadaným klíčem a převede ho na zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="b4e27-518">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="b4e27-519">Přetížení umožňuje zadat výchozí hodnotu, pokud není nalezen klíč.</span><span class="sxs-lookup"><span data-stu-id="b4e27-519">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="b4e27-520">Následující příklad získá řetězcovou hodnotu z konfigurace s klíčem `NumberKey`, zadá hodnotu jako `int`a uloží hodnotu do proměnné `intValue`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-520">The following example extracts the string value from configuration with the key `NumberKey`, types the value as an `int`, and stores the value in the variable `intValue`.</span></span> <span data-ttu-id="b4e27-521">Pokud `NumberKey` nebyl nalezen v konfigurační klíče `intValue` přijme výchozí hodnotu `99`:</span><span class="sxs-lookup"><span data-stu-id="b4e27-521">If `NumberKey` isn't found in the configuration keys, `intValue` receives the default value of `99`:</span></span>

```csharp
var intValue = config.GetValue<int>("NumberKey", 99);
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b4e27-522">GetSection, GetChildren – a existuje</span><span class="sxs-lookup"><span data-stu-id="b4e27-522">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b4e27-523">Příklady, které následují vezměte v úvahu následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="b4e27-523">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="b4e27-524">Čtyři kódy se nacházejí ve dvou částech, z nichž jeden obsahuje dvojice pododdíly:</span><span class="sxs-lookup"><span data-stu-id="b4e27-524">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="b4e27-525">Přečtení souboru do konfigurace následujících jedinečné klíče hierarchické jsou vytvořeny pro uchování hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b4e27-525">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="b4e27-526">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-526">section0:key0</span></span>
* <span data-ttu-id="b4e27-527">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-527">section0:key1</span></span>
* <span data-ttu-id="b4e27-528">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-528">section1:key0</span></span>
* <span data-ttu-id="b4e27-529">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-529">section1:key1</span></span>
* <span data-ttu-id="b4e27-530">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-530">section2:subsection0:key0</span></span>
* <span data-ttu-id="b4e27-531">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-531">section2:subsection0:key1</span></span>
* <span data-ttu-id="b4e27-532">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="b4e27-532">section2:subsection1:key0</span></span>
* <span data-ttu-id="b4e27-533">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="b4e27-533">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="b4e27-534">GetSection</span><span class="sxs-lookup"><span data-stu-id="b4e27-534">GetSection</span></span>

<span data-ttu-id="b4e27-535">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje dílčí část konfigurace s klíčem zadaného dílčí část.</span><span class="sxs-lookup"><span data-stu-id="b4e27-535">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="b4e27-536">Se vraťte <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč hodnota v `section1`, volání `GetSection` a zadat název oddílu:</span><span class="sxs-lookup"><span data-stu-id="b4e27-536">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="b4e27-537">Podobně a získat tak hodnoty pro klíče v `section2:subsection0`, volání `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="b4e27-537">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="b4e27-538">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-538">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b4e27-539">Pokud není nalezen odpovídající části, prázdná `IConfigurationSection` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="b4e27-539">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

### <a name="getchildren"></a><span data-ttu-id="b4e27-540">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="b4e27-540">GetChildren</span></span>

<span data-ttu-id="b4e27-541">Volání [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) na `section2` získá `IEnumerable<IConfigurationSection>` , který obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b4e27-541">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

::: moniker range=">= aspnetcore-2.0"

### <a name="exists"></a><span data-ttu-id="b4e27-542">Existuje</span><span class="sxs-lookup"><span data-stu-id="b4e27-542">Exists</span></span>

<span data-ttu-id="b4e27-543">Použití [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, jestli existuje konfigurační oddíl:</span><span class="sxs-lookup"><span data-stu-id="b4e27-543">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="b4e27-544">Zadaný ukázková data `sectionExists` je `false` protože není k dispozici `section2:subsection2` části v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="b4e27-544">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

::: moniker-end

## <a name="bind-to-a-class"></a><span data-ttu-id="b4e27-545">Vytvoření vazby na třídu</span><span class="sxs-lookup"><span data-stu-id="b4e27-545">Bind to a class</span></span>

<span data-ttu-id="b4e27-546">Konfigurace může být vázaný na třídy, které představující skupiny související nastavení použití *možnosti vzor*.</span><span class="sxs-lookup"><span data-stu-id="b4e27-546">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="b4e27-547">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-547">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b4e27-548">Konfigurační hodnoty jsou vrácené jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objekty.</span><span class="sxs-lookup"><span data-stu-id="b4e27-548">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="b4e27-549">Obsahuje ukázkovou aplikaci `Starship` modelu (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="b4e27-549">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-550">`starship` Část *starship.json* soubor vytvoří konfiguraci při ukázková aplikace používá zprostředkovatele konfigurace JSON načíst konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="b4e27-550">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="b4e27-551">Následující páry klíč hodnota konfigurace jsou vytvořeny:</span><span class="sxs-lookup"><span data-stu-id="b4e27-551">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="b4e27-552">Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-552">Key</span></span>                   | <span data-ttu-id="b4e27-553">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-553">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="b4e27-554">starship: name</span><span class="sxs-lookup"><span data-stu-id="b4e27-554">starship:name</span></span>         | <span data-ttu-id="b4e27-555">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="b4e27-555">USS Enterprise</span></span>                                    |
| <span data-ttu-id="b4e27-556">starship: registru</span><span class="sxs-lookup"><span data-stu-id="b4e27-556">starship:registry</span></span>     | <span data-ttu-id="b4e27-557">PADĚLKY 1701</span><span class="sxs-lookup"><span data-stu-id="b4e27-557">NCC-1701</span></span>                                          |
| <span data-ttu-id="b4e27-558">starship: Třída</span><span class="sxs-lookup"><span data-stu-id="b4e27-558">starship:class</span></span>        | <span data-ttu-id="b4e27-559">Vytvoření</span><span class="sxs-lookup"><span data-stu-id="b4e27-559">Constitution</span></span>                                      |
| <span data-ttu-id="b4e27-560">starship: délka</span><span class="sxs-lookup"><span data-stu-id="b4e27-560">starship:length</span></span>       | <span data-ttu-id="b4e27-561">304.8</span><span class="sxs-lookup"><span data-stu-id="b4e27-561">304.8</span></span>                                             |
| <span data-ttu-id="b4e27-562">starship: stává</span><span class="sxs-lookup"><span data-stu-id="b4e27-562">starship:commissioned</span></span> | <span data-ttu-id="b4e27-563">False</span><span class="sxs-lookup"><span data-stu-id="b4e27-563">False</span></span>                                             |
| <span data-ttu-id="b4e27-564">Ochranné známky</span><span class="sxs-lookup"><span data-stu-id="b4e27-564">trademark</span></span>             | <span data-ttu-id="b4e27-565">Paramount obrázky Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="b4e27-565">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="b4e27-566">Ukázková aplikace volání `GetSection` s `starship` klíč.</span><span class="sxs-lookup"><span data-stu-id="b4e27-566">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="b4e27-567">`starship` Páry klíč hodnota jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="b4e27-567">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="b4e27-568">`Bind` Metoda je volána v podčásti předávání v instanci `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="b4e27-568">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="b4e27-569">Po navázání hodnoty instanci, instance přidruženo k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="b4e27-569">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="b4e27-570">Vytvořit vazbu grafu objektu</span><span class="sxs-lookup"><span data-stu-id="b4e27-570">Bind to an object graph</span></span>

<span data-ttu-id="b4e27-571"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopen vazby celého grafu objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="b4e27-571"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="b4e27-572">Obsahuje ukázku `TvShow` modelu, jehož graf objektu obsahuje `Metadata` a `Actors` třídy (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="b4e27-572">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-573">Ukázková aplikace má *tvshow.xml* soubor, který obsahuje konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="b4e27-573">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-xml[](index/samples/1.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="b4e27-574">Konfigurace je vázán na celý `TvShow` grafu objektu s `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="b4e27-574">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="b4e27-575">Vázané instance je přiřazená k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="b4e27-575">The bound instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
viewModel.TvShow = tvShow;
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="b4e27-576">[ConfigurationBinder.Get&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) váže a vrátí hodnotu zadaného typu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-576">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="b4e27-577">`Get<T>` je výhodnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-577">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="b4e27-578">Následující kód ukazuje, jak používat `Get<T>` z předchozího příkladu, který umožňuje vázané instance má být přímo přiřazeni k vlastnosti pro vykreslování použit:</span><span class="sxs-lookup"><span data-stu-id="b4e27-578">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b4e27-579">Svázat pole třídy</span><span class="sxs-lookup"><span data-stu-id="b4e27-579">Bind an array to a class</span></span>

<span data-ttu-id="b4e27-580">*Ukázková aplikace předvádí koncepty je popsáno v této části.*</span><span class="sxs-lookup"><span data-stu-id="b4e27-580">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="b4e27-581"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Podporuje pole vazby na objekty pomocí indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b4e27-581">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b4e27-582">Jakékoli pole formátu, který poskytuje číselné segment klíče (`:0:`, `:1:`, &hellip; `:{n}:`) je schopný vazba pole na pole třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="b4e27-582">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="b4e27-583">Vazba je poskytována konvence.</span><span class="sxs-lookup"><span data-stu-id="b4e27-583">Binding is provided by convention.</span></span> <span data-ttu-id="b4e27-584">Vlastní zprostředkovatelé konfigurace není nutné implementovat pole vazby.</span><span class="sxs-lookup"><span data-stu-id="b4e27-584">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="b4e27-585">**Zpracování v paměti pole**</span><span class="sxs-lookup"><span data-stu-id="b4e27-585">**In-memory array processing**</span></span>

<span data-ttu-id="b4e27-586">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b4e27-586">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="b4e27-587">Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-587">Key</span></span>             | <span data-ttu-id="b4e27-588">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-588">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b4e27-589">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="b4e27-589">array:entries:0</span></span> | <span data-ttu-id="b4e27-590">gamma0</span><span class="sxs-lookup"><span data-stu-id="b4e27-590">value0</span></span> |
| <span data-ttu-id="b4e27-591">pole: položek: 1</span><span class="sxs-lookup"><span data-stu-id="b4e27-591">array:entries:1</span></span> | <span data-ttu-id="b4e27-592">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="b4e27-592">value1</span></span> |
| <span data-ttu-id="b4e27-593">pole: položek: 2</span><span class="sxs-lookup"><span data-stu-id="b4e27-593">array:entries:2</span></span> | <span data-ttu-id="b4e27-594">Hodnota2</span><span class="sxs-lookup"><span data-stu-id="b4e27-594">value2</span></span> |
| <span data-ttu-id="b4e27-595">pole: položek: 4</span><span class="sxs-lookup"><span data-stu-id="b4e27-595">array:entries:4</span></span> | <span data-ttu-id="b4e27-596">value4</span><span class="sxs-lookup"><span data-stu-id="b4e27-596">value4</span></span> |
| <span data-ttu-id="b4e27-597">pole: položek: 5</span><span class="sxs-lookup"><span data-stu-id="b4e27-597">array:entries:5</span></span> | <span data-ttu-id="b4e27-598">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="b4e27-598">value5</span></span> |

<span data-ttu-id="b4e27-599">Tyto klíče a hodnoty jsou načteny v ukázkové aplikaci pomocí zprostředkovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="b4e27-599">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=5-12,16)]

::: moniker-end

<span data-ttu-id="b4e27-600">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="b4e27-600">The array skips a value for index &num;3.</span></span> <span data-ttu-id="b4e27-601">Konfigurace vazače není schopen vazby hodnoty null a vytvářet položky s hodnotou null v vázaným objektům, zrušte v okamžiku, kdy je znázorněn výsledek vazby toto pole na objekt, který se stane.</span><span class="sxs-lookup"><span data-stu-id="b4e27-601">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="b4e27-602">V ukázkové aplikaci je k dispozici pro uložení vázané konfigurační data POCO třídy:</span><span class="sxs-lookup"><span data-stu-id="b4e27-602">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-603">Konfigurační data je vázaná na objekt:</span><span class="sxs-lookup"><span data-stu-id="b4e27-603">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="b4e27-604">[ConfigurationBinder.Get&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe je také možné, povede k kompaktnějším kód:</span><span class="sxs-lookup"><span data-stu-id="b4e27-604">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="b4e27-605">Vázaný objekt, instance `ArrayExample`, přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-605">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="b4e27-606">`ArrayExample.Entries` Index</span><span class="sxs-lookup"><span data-stu-id="b4e27-606">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b4e27-607">`ArrayExample.Entries` Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-607">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b4e27-608">0</span><span class="sxs-lookup"><span data-stu-id="b4e27-608">0</span></span>                            | <span data-ttu-id="b4e27-609">gamma0</span><span class="sxs-lookup"><span data-stu-id="b4e27-609">value0</span></span>                       |
| <span data-ttu-id="b4e27-610">1</span><span class="sxs-lookup"><span data-stu-id="b4e27-610">1</span></span>                            | <span data-ttu-id="b4e27-611">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="b4e27-611">value1</span></span>                       |
| <span data-ttu-id="b4e27-612">2</span><span class="sxs-lookup"><span data-stu-id="b4e27-612">2</span></span>                            | <span data-ttu-id="b4e27-613">Hodnota2</span><span class="sxs-lookup"><span data-stu-id="b4e27-613">value2</span></span>                       |
| <span data-ttu-id="b4e27-614">3</span><span class="sxs-lookup"><span data-stu-id="b4e27-614">3</span></span>                            | <span data-ttu-id="b4e27-615">value4</span><span class="sxs-lookup"><span data-stu-id="b4e27-615">value4</span></span>                       |
| <span data-ttu-id="b4e27-616">4</span><span class="sxs-lookup"><span data-stu-id="b4e27-616">4</span></span>                            | <span data-ttu-id="b4e27-617">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="b4e27-617">value5</span></span>                       |

<span data-ttu-id="b4e27-618">Index &num;3 v vázaný objekt obsahuje konfigurační data pro `array:4` konfiguračního klíče a jeho hodnota `value4`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-618">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b4e27-619">Když je vytvořena vazba konfigurační data obsahující pole, indexy pole v konfigurační klíče se používají pouze k iteraci konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-619">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b4e27-620">Hodnotu null nelze uchovávat v konfiguračních datech a položku s hodnotou null není vytvořená v vázaný objekt, když přeskočí jednu nebo více indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b4e27-620">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b4e27-621">Chybějící položky konfigurace pro index &num;3 může být zadán před vazbu `ArrayExample` instance poskytovatelem žádné konfigurace, která vytváří správné páru klíč hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="b4e27-621">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="b4e27-622">Pokud vzorek zahrnuje další poskytovatele konfigurace JSON s chybějící páru klíč hodnota, `ArrayExample.Entries` odpovídá poli kompletní konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b4e27-622">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="b4e27-623">*missing_value.JSON*:</span><span class="sxs-lookup"><span data-stu-id="b4e27-623">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4e27-624">V <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b4e27-624">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b4e27-625">V `Startup` konstruktor:</span><span class="sxs-lookup"><span data-stu-id="b4e27-625">In the `Startup` constructor:</span></span>

```csharp
.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

<span data-ttu-id="b4e27-626">Dvojice klíč hodnota v tabulce se načtou do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-626">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="b4e27-627">Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-627">Key</span></span>             | <span data-ttu-id="b4e27-628">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-628">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b4e27-629">pole: položek: 3</span><span class="sxs-lookup"><span data-stu-id="b4e27-629">array:entries:3</span></span> | <span data-ttu-id="b4e27-630">hodnota3</span><span class="sxs-lookup"><span data-stu-id="b4e27-630">value3</span></span> |

<span data-ttu-id="b4e27-631">Pokud `ArrayExample` instance třídy je vázán po poskytovatel konfigurace JSON obsahuje položku pro index &num;3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b4e27-631">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="b4e27-632">`ArrayExample.Entries` Index</span><span class="sxs-lookup"><span data-stu-id="b4e27-632">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b4e27-633">`ArrayExample.Entries` Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-633">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b4e27-634">0</span><span class="sxs-lookup"><span data-stu-id="b4e27-634">0</span></span>                            | <span data-ttu-id="b4e27-635">gamma0</span><span class="sxs-lookup"><span data-stu-id="b4e27-635">value0</span></span>                       |
| <span data-ttu-id="b4e27-636">1</span><span class="sxs-lookup"><span data-stu-id="b4e27-636">1</span></span>                            | <span data-ttu-id="b4e27-637">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="b4e27-637">value1</span></span>                       |
| <span data-ttu-id="b4e27-638">2</span><span class="sxs-lookup"><span data-stu-id="b4e27-638">2</span></span>                            | <span data-ttu-id="b4e27-639">Hodnota2</span><span class="sxs-lookup"><span data-stu-id="b4e27-639">value2</span></span>                       |
| <span data-ttu-id="b4e27-640">3</span><span class="sxs-lookup"><span data-stu-id="b4e27-640">3</span></span>                            | <span data-ttu-id="b4e27-641">hodnota3</span><span class="sxs-lookup"><span data-stu-id="b4e27-641">value3</span></span>                       |
| <span data-ttu-id="b4e27-642">4</span><span class="sxs-lookup"><span data-stu-id="b4e27-642">4</span></span>                            | <span data-ttu-id="b4e27-643">value4</span><span class="sxs-lookup"><span data-stu-id="b4e27-643">value4</span></span>                       |
| <span data-ttu-id="b4e27-644">5</span><span class="sxs-lookup"><span data-stu-id="b4e27-644">5</span></span>                            | <span data-ttu-id="b4e27-645">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="b4e27-645">value5</span></span>                       |

<span data-ttu-id="b4e27-646">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="b4e27-646">**JSON array processing**</span></span>

<span data-ttu-id="b4e27-647">Pokud soubor JSON obsahuje pole, vytvoří se pro prvky pole s indexem založený na nule části konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b4e27-647">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="b4e27-648">V následující konfigurační soubor `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="b4e27-648">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="b4e27-649">Zprostředkovatel konfigurace JSON čte konfigurační data do následující páry klíč hodnota:</span><span class="sxs-lookup"><span data-stu-id="b4e27-649">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="b4e27-650">Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-650">Key</span></span>                     | <span data-ttu-id="b4e27-651">Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-651">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="b4e27-652">json_array:Key</span><span class="sxs-lookup"><span data-stu-id="b4e27-652">json_array:key</span></span>          | <span data-ttu-id="b4e27-653">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="b4e27-653">valueA</span></span> |
| <span data-ttu-id="b4e27-654">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="b4e27-654">json_array:subsection:0</span></span> | <span data-ttu-id="b4e27-655">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="b4e27-655">valueB</span></span> |
| <span data-ttu-id="b4e27-656">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="b4e27-656">json_array:subsection:1</span></span> | <span data-ttu-id="b4e27-657">valueC</span><span class="sxs-lookup"><span data-stu-id="b4e27-657">valueC</span></span> |
| <span data-ttu-id="b4e27-658">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="b4e27-658">json_array:subsection:2</span></span> | <span data-ttu-id="b4e27-659">Vážíme si toho</span><span class="sxs-lookup"><span data-stu-id="b4e27-659">valueD</span></span> |

<span data-ttu-id="b4e27-660">V ukázkové aplikaci je k dispozici pro vazbu páry klíč hodnota konfigurace následující třídy POCO:</span><span class="sxs-lookup"><span data-stu-id="b4e27-660">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-661">Po vytvoření vazby, `JsonArrayExample.Key` obsahuje hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-661">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="b4e27-662">Dílčí část hodnoty jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-662">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="b4e27-663">`JsonArrayExample.Subsection` Index</span><span class="sxs-lookup"><span data-stu-id="b4e27-663">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="b4e27-664">`JsonArrayExample.Subsection` Hodnota</span><span class="sxs-lookup"><span data-stu-id="b4e27-664">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="b4e27-665">0</span><span class="sxs-lookup"><span data-stu-id="b4e27-665">0</span></span>                                   | <span data-ttu-id="b4e27-666">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="b4e27-666">valueB</span></span>                              |
| <span data-ttu-id="b4e27-667">1</span><span class="sxs-lookup"><span data-stu-id="b4e27-667">1</span></span>                                   | <span data-ttu-id="b4e27-668">valueC</span><span class="sxs-lookup"><span data-stu-id="b4e27-668">valueC</span></span>                              |
| <span data-ttu-id="b4e27-669">2</span><span class="sxs-lookup"><span data-stu-id="b4e27-669">2</span></span>                                   | <span data-ttu-id="b4e27-670">Vážíme si toho</span><span class="sxs-lookup"><span data-stu-id="b4e27-670">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="b4e27-671">Vlastního poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="b4e27-671">Custom configuration provider</span></span>

<span data-ttu-id="b4e27-672">Ukázková aplikace předvádí, jak vytvořit základní konfigurace poskytovatele, který přečte dvojice klíč hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b4e27-672">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b4e27-673">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b4e27-673">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b4e27-674">EF database v paměti se používá pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="b4e27-674">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b4e27-675">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementovat sekundární `ConfigurationBuilder` zadat připojovací řetězec z jiného zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b4e27-675">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b4e27-676">Poskytovatel přečte tabulku databáze do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b4e27-676">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b4e27-677">Zprostředkovatel nepodporuje dotazy na databázi na základě-key.</span><span class="sxs-lookup"><span data-stu-id="b4e27-677">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b4e27-678">Znovu načíst na změnu není implementována, po spuštění aplikace nemá žádný vliv na konfiguraci aplikace, takže aktualizace databáze.</span><span class="sxs-lookup"><span data-stu-id="b4e27-678">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b4e27-679">Definování `EFConfigurationValue` entity pro ukládání hodnoty konfigurace v databázi.</span><span class="sxs-lookup"><span data-stu-id="b4e27-679">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b4e27-680">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4e27-680">*Models/EFConfigurationValue.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-681">Přidat `EFConfigurationContext` ukládání a přístup k nakonfigurované hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b4e27-681">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b4e27-682">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4e27-682">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-683">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-683">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b4e27-684">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4e27-684">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-685">Vytvoření vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-685">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b4e27-686">Poskytovatel konfigurace inicializuje databáze, když je prázdný.</span><span class="sxs-lookup"><span data-stu-id="b4e27-686">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="b4e27-687">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4e27-687">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-688">`AddEFConfiguration` – Metoda rozšíření umožňuje zdroj konfigurace k přidání `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-688">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b4e27-689">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4e27-689">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b4e27-690">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b4e27-690">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=24)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="b4e27-691">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="b4e27-691">Access configuration during startup</span></span>

<span data-ttu-id="b4e27-692">Vložit `IConfiguration` do `Startup` konstruktoru na hodnoty konfigurace přístupu v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b4e27-692">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b4e27-693">Získat přístup ke konfiguraci v `Startup.Configure`, buď vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="b4e27-693">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="b4e27-694">Příklad přístup ke konfiguraci pomocí vhodné metody po spuštění najdete v tématu [spuštění aplikace: vhodné metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b4e27-694">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="b4e27-695">Konfigurace přístupu v stránky Razor Pages nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="b4e27-695">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="b4e27-696">Chcete-li získat přístup k nastavení konfigurace v zobrazení MVC nebo stránky Razor Pages, přidejte [using – direktiva](xref:mvc/views/razor#using) ([referenční dokumentace jazyka C#: použití direktivy](/dotnet/csharp/language-reference/keywords/using-directive)) pro [Microsoft.Extensions.Configuration obor názvů ](xref:Microsoft.Extensions.Configuration) a vložit <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b4e27-696">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="b4e27-697">Na stránce pro stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="b4e27-697">In a Razor Pages page:</span></span>

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

<span data-ttu-id="b4e27-698">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="b4e27-698">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b4e27-699">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="b4e27-699">Add configuration from an external assembly</span></span>

<span data-ttu-id="b4e27-700"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Implementace umožňuje přidání vylepšení do aplikace při spuštění z externího sestavení mimo aplikaci prvku `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="b4e27-700">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b4e27-701">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b4e27-701">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4e27-702">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b4e27-702">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="b4e27-703">Podrobné informace o konfiguraci Microsoft</span><span class="sxs-lookup"><span data-stu-id="b4e27-703">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)

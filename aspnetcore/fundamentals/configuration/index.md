---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Zjistěte, jak použít rozhraní API pro konfiguraci ke konfiguraci aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/24/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 81820e8161965fcca2f97d00708df5a29df668de
ms.sourcegitcommit: 9691b742134563b662948b0ed63f54ef7186801e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2019
ms.locfileid: "66824827"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="b8b59-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8b59-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="b8b59-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8b59-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b8b59-105">Konfigurace aplikace v ASP.NET Core je založená na páry klíč hodnota stanovené *poskytovatelé konfigurace*.</span><span class="sxs-lookup"><span data-stu-id="b8b59-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="b8b59-106">Poskytovatelé konfigurace čtení konfiguračních dat do párů hodnot klíčů z různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b8b59-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="b8b59-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b8b59-107">Azure Key Vault</span></span>
* <span data-ttu-id="b8b59-108">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b8b59-108">Command-line arguments</span></span>
* <span data-ttu-id="b8b59-109">Vlastní zprostředkovatelé (nainstalované nebo vytváření)</span><span class="sxs-lookup"><span data-stu-id="b8b59-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b8b59-110">Adresář souborů</span><span class="sxs-lookup"><span data-stu-id="b8b59-110">Directory files</span></span>
* <span data-ttu-id="b8b59-111">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b8b59-111">Environment variables</span></span>
* <span data-ttu-id="b8b59-112">Objekty v paměti .NET</span><span class="sxs-lookup"><span data-stu-id="b8b59-112">In-memory .NET objects</span></span>
* <span data-ttu-id="b8b59-113">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="b8b59-113">Settings files</span></span>

<span data-ttu-id="b8b59-114">Balíčky pro konfiguraci pro běžné scénáře poskytovatele konfigurace jsou součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-114">Configuration packages for common configuration provider scenarios are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b8b59-115">Příklady, postupujte podle, které v ukázkové aplikaci používají kódu <xref:Microsoft.Extensions.Configuration> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="b8b59-115">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="b8b59-116">*Možnosti vzor* je rozšířením konfigurace koncepty popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-116">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="b8b59-117">Možnosti třídy používá k reprezentování skupiny související nastavení.</span><span class="sxs-lookup"><span data-stu-id="b8b59-117">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b8b59-118">Další informace o použití vzoru možnosti najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-118">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b8b59-119">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8b59-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-vs-app-configuration"></a><span data-ttu-id="b8b59-120">Hostování a konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="b8b59-120">Host vs. app configuration</span></span>

<span data-ttu-id="b8b59-121">Předtím, než aplikace je nakonfigurovaná a spuštěna, *hostitele* nakonfigurovaný a spustit.</span><span class="sxs-lookup"><span data-stu-id="b8b59-121">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b8b59-122">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="b8b59-122">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b8b59-123">Aplikace a hostitel jsou nakonfigurováni pomocí zprostředkovatele konfigurace popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-123">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b8b59-124">Páry klíč hodnota konfigurace hostitele se stanou součástí globální konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-124">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="b8b59-125">Další informace o jak konfiguraci poskytovatele se používají při vytváření hostitele a vliv zdroje konfigurace hostitele konfigurace najdete v tématu [hostitele](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="b8b59-125">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="b8b59-126">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="b8b59-126">Default configuration</span></span>

<span data-ttu-id="b8b59-127">Webové aplikace založené na technologii ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) volání šablony <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="b8b59-127">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="b8b59-128"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> poskytuje výchozí konfigurace pro aplikace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="b8b59-128"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

* <span data-ttu-id="b8b59-129">Konfigurace hostitele je k dispozici od:</span><span class="sxs-lookup"><span data-stu-id="b8b59-129">Host configuration is provided from:</span></span>
  * <span data-ttu-id="b8b59-130">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b8b59-130">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="b8b59-131">Předpona, která (`ASPNETCORE_`) je odebrána, když jsou načteny páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-131">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="b8b59-132">Argumenty příkazového řádku pomocí [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b8b59-132">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="b8b59-133">Konfigurace aplikace je k dispozici od:</span><span class="sxs-lookup"><span data-stu-id="b8b59-133">App configuration is provided from:</span></span>
  * <span data-ttu-id="b8b59-134">*appSettings.JSON* pomocí [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b8b59-134">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b8b59-135">*appSettings. {Prostředí} .json* pomocí [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b8b59-135">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b8b59-136">[Tajný klíč správce](xref:security/app-secrets) při spuštění aplikace `Development` prostředí s využitím vstupní sestavení.</span><span class="sxs-lookup"><span data-stu-id="b8b59-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="b8b59-137">Použití proměnných prostředí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b8b59-137">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="b8b59-138">Pokud použijete vlastní předpona (například `PREFIX_` s `.AddEnvironmentVariables(prefix: "PREFIX_")`), předpona, která je odstraněna, když jsou načteny páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-138">If a custom prefix is used (for example, `PREFIX_` with `.AddEnvironmentVariables(prefix: "PREFIX_")`), the prefix is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="b8b59-139">Argumenty příkazového řádku pomocí [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b8b59-139">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="b8b59-140">Poskytovatelé konfigurace jsou vysvětleny dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-140">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="b8b59-141">Další informace o hostiteli a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, naleznete v tématu <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-141">For more information on the host and <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="b8b59-142">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="b8b59-142">Security</span></span>

<span data-ttu-id="b8b59-143">Použijte následující osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="b8b59-143">Adopt the following best practices:</span></span>

* <span data-ttu-id="b8b59-144">Nikdy ukládat hesla a jiná citlivá data v konfiguraci zprostředkovatele kódu nebo v konfiguračních souborech na prostý text.</span><span class="sxs-lookup"><span data-stu-id="b8b59-144">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="b8b59-145">Nechcete používat produkční tajných kódů při vývoji nebo testovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="b8b59-145">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b8b59-146">Zadejte tajných kódů mimo projekt tak, že nemohou být omylem zaměřuje na úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-146">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b8b59-147">Další informace o [jak používat více prostředí](xref:fundamentals/environments) a správu [bezpečné ukládání tajných kódů aplikace při vývoji pomocí manažera tajných](xref:security/app-secrets) (včetně poradit se správným určením použití proměnných k ukládání citlivá data).</span><span class="sxs-lookup"><span data-stu-id="b8b59-147">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="b8b59-148">Správce tajný klíč používá zprostředkovatel konfigurace souboru ukládat tajné klíče uživatelů v souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="b8b59-148">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="b8b59-149">Zprostředkovatel konfigurace souboru je popsána dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-149">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b8b59-150">[Služba Azure Key Vault](https://azure.microsoft.com/services/key-vault/) je jednou z možností pro bezpečné ukládání tajných klíčů aplikací.</span><span class="sxs-lookup"><span data-stu-id="b8b59-150">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="b8b59-151">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-151">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b8b59-152">Hierarchické konfigurační data</span><span class="sxs-lookup"><span data-stu-id="b8b59-152">Hierarchical configuration data</span></span>

<span data-ttu-id="b8b59-153">Konfigurační rozhraní API je schopni zachovat hierarchické konfigurační data linearizovat hierarchických dat s použitím oddělovače v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b8b59-153">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b8b59-154">V následujícím souboru JSON existují čtyři kódy v hierarchii strukturovaných ze dvou částí:</span><span class="sxs-lookup"><span data-stu-id="b8b59-154">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="b8b59-155">Pokud je soubor pro čtení do konfigurace, jedinečné klíče se vytvoří zachovat původní struktury hierarchická data zdroj konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-155">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="b8b59-156">Oddíly a klíče se sloučí s použitím dvojtečkou (`:`) Chcete-li zachovat původní struktury:</span><span class="sxs-lookup"><span data-stu-id="b8b59-156">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="b8b59-157">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-157">section0:key0</span></span>
* <span data-ttu-id="b8b59-158">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-158">section0:key1</span></span>
* <span data-ttu-id="b8b59-159">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-159">section1:key0</span></span>
* <span data-ttu-id="b8b59-160">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-160">section1:key1</span></span>

<span data-ttu-id="b8b59-161"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody jsou k dispozici k izolaci oddíly a podřízené objekty daného oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="b8b59-161"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b8b59-162">Tyto metody jsou popsané dále v [GetSection GetChildren – a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="b8b59-162">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="b8b59-163">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-163">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="b8b59-164">Konvence</span><span class="sxs-lookup"><span data-stu-id="b8b59-164">Conventions</span></span>

<span data-ttu-id="b8b59-165">Konfigurace zdroje jsou při spuštění aplikace pro čtení v pořadí, že jsou uvedeny příslušné poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-165">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="b8b59-166">Poskytovatelé konfigurace, které implementují detekce změn se budou moct znovu načíst konfiguraci základní nastavení se při změně.</span><span class="sxs-lookup"><span data-stu-id="b8b59-166">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="b8b59-167">Například soubor zprostředkovatel konfigurace (popsáno dále v tomto tématu) a [konfigurace zprostředkovatele služby Azure Key Vault](xref:security/key-vault-configuration) implementovat detekce změn.</span><span class="sxs-lookup"><span data-stu-id="b8b59-167">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="b8b59-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v aplikaci prvku [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b8b59-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b8b59-169"><xref:Microsoft.Extensions.Configuration.IConfiguration> mohou být vloženy do stránky Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> získat konfiguraci pro třídu:</span><span class="sxs-lookup"><span data-stu-id="b8b59-169"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

<span data-ttu-id="b8b59-170">Poskytovatelé konfigurace nemůže využít DI, protože není k dispozici při jejich nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="b8b59-170">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="b8b59-171">Konfigurační klíče použijte následující konvence:</span><span class="sxs-lookup"><span data-stu-id="b8b59-171">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="b8b59-172">Klíče jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b8b59-172">Keys are case-insensitive.</span></span> <span data-ttu-id="b8b59-173">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="b8b59-173">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b8b59-174">Pokud poskytovateli stejné nebo různé konfigurace je nastavena hodnota pro stejný klíč, poslední hodnotu nastavit na klíč je hodnota.</span><span class="sxs-lookup"><span data-stu-id="b8b59-174">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="b8b59-175">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="b8b59-175">Hierarchical keys</span></span>
  * <span data-ttu-id="b8b59-176">V rámci rozhraní API pro konfiguraci, oddělovač dvojtečka (`:`) funguje na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b8b59-176">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b8b59-177">V seznamu proměnných prostředí oddělovač dvojtečka nemusí fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b8b59-177">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b8b59-178">Dvojitým podtržítkem (`__`) podporuje všechny platformy a je převedena na dvojtečkou.</span><span class="sxs-lookup"><span data-stu-id="b8b59-178">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="b8b59-179">Ve službě Azure Key Vault, hierarchické klíče, použijte `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="b8b59-179">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b8b59-180">Je nutné zadat kód pomlček nahraďte dvojtečkou tajné klíče jsou načtena do konfigurace vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-180">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b8b59-181"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Podporuje pole vazby na objekty pomocí indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b8b59-181">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b8b59-182">Pole vazby je popsána v [svázat pole třídy](#bind-an-array-to-a-class) oddílu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-182">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="b8b59-183">Hodnoty konfigurace přijmout následující konvence:</span><span class="sxs-lookup"><span data-stu-id="b8b59-183">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="b8b59-184">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="b8b59-184">Values are strings.</span></span>
* <span data-ttu-id="b8b59-185">Hodnoty Null nelze uložit v konfiguraci nebo vázány s objekty.</span><span class="sxs-lookup"><span data-stu-id="b8b59-185">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="b8b59-186">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="b8b59-186">Providers</span></span>

<span data-ttu-id="b8b59-187">V následující tabulce jsou uvedeny poskytovatelé konfigurace k dispozici pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8b59-187">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="b8b59-188">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="b8b59-188">Provider</span></span> | <span data-ttu-id="b8b59-189">Obsahuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="b8b59-189">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b8b59-190">[Azure zprostředkovatel konfigurace trezoru klíčů](xref:security/key-vault-configuration) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="b8b59-190">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b8b59-191">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b8b59-191">Azure Key Vault</span></span> |
| [<span data-ttu-id="b8b59-192">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b8b59-192">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b8b59-193">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b8b59-193">Command-line parameters</span></span> |
| [<span data-ttu-id="b8b59-194">Vlastního poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="b8b59-194">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b8b59-195">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="b8b59-195">Custom source</span></span> |
| [<span data-ttu-id="b8b59-196">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b8b59-196">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b8b59-197">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b8b59-197">Environment variables</span></span> |
| [<span data-ttu-id="b8b59-198">Zprostředkovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b8b59-198">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b8b59-199">Soubory INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="b8b59-199">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b8b59-200">Poskytovatel konfigurace pro každý soubor klíče</span><span class="sxs-lookup"><span data-stu-id="b8b59-200">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b8b59-201">Adresář souborů</span><span class="sxs-lookup"><span data-stu-id="b8b59-201">Directory files</span></span> |
| [<span data-ttu-id="b8b59-202">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b8b59-202">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b8b59-203">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="b8b59-203">In-memory collections</span></span> |
| <span data-ttu-id="b8b59-204">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="b8b59-204">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b8b59-205">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="b8b59-205">File in the user profile directory</span></span> |

<span data-ttu-id="b8b59-206">Konfigurace zdrojů se čtení v pořadí, že jejich poskytovatelé konfigurace jsou zadány při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b8b59-206">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="b8b59-207">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, ne v pořadí, že váš kód může uspořádat je.</span><span class="sxs-lookup"><span data-stu-id="b8b59-207">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="b8b59-208">Pořadí poskytovatelé konfigurace ve vašem kódu tak, aby vyhovoval vašim prioritám pro podkladové zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-208">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="b8b59-209">Typická posloupnost poskytovatelé konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="b8b59-209">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="b8b59-210">Soubory (*appsettings.json*, *appsettings. { Prostředí} .json*, kde `{Environment}` je aktuálním prostředí hostování aplikace)</span><span class="sxs-lookup"><span data-stu-id="b8b59-210">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="b8b59-211">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b8b59-211">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="b8b59-212">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí jenom)</span><span class="sxs-lookup"><span data-stu-id="b8b59-212">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="b8b59-213">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b8b59-213">Environment variables</span></span>
1. <span data-ttu-id="b8b59-214">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b8b59-214">Command-line arguments</span></span>

<span data-ttu-id="b8b59-215">Je obvyklé na poslední pozici poskytovatele konfigurace příkazového řádku v sérii poskytovatele, jak povolit argumenty příkazového řádku k přepsání konfigurace nastavená poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="b8b59-215">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="b8b59-216">Toto pořadí poskytovatelů přejde do místa při inicializaci nové <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-216">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b8b59-217">Další informace najdete v tématu [webového hostitele: Nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b8b59-217">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

## <a name="configureappconfiguration"></a><span data-ttu-id="b8b59-218">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b8b59-218">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b8b59-219">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby zadejte poskytovatele konfigurace aplikace kromě těch, přidání automaticky <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="b8b59-219">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

<span data-ttu-id="b8b59-220">Konfigurace zadaný pro aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici při spuštění aplikace, včetně `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-220">Configuration supplied to the app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b8b59-221">Další informace najdete v tématu [konfigurace přístupu při spuštění](#access-configuration-during-startup) oddílu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-221">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="b8b59-222">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b8b59-222">Command-line Configuration Provider</span></span>

<span data-ttu-id="b8b59-223"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Načte konfiguraci z páry klíč hodnota pro argument příkazového řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-223">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="b8b59-224">K aktivaci příkazového řádku konfigurace <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> rozšiřující metoda je volána v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-224">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b8b59-225">`AddCommandLine` je automaticky volána, když inicializujete novou <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-225">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b8b59-226">Další informace najdete v tématu [webového hostitele: Nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b8b59-226">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b8b59-227">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="b8b59-227">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b8b59-228">Volitelná konfigurace z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="b8b59-228">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="b8b59-229">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="b8b59-229">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b8b59-230">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b8b59-230">Environment variables.</span></span>

<span data-ttu-id="b8b59-231">`CreateDefaultBuilder` poslední přidá poskytovatele konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b8b59-231">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="b8b59-232">Argumenty příkazového řádku předané za běhu přepsat nastavení ostatní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-232">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="b8b59-233">`CreateDefaultBuilder` funguje, když je vytvořen hostiteli.</span><span class="sxs-lookup"><span data-stu-id="b8b59-233">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="b8b59-234">Proto příkazového řádku konfigurace aktivoval `CreateDefaultBuilder` může mít vliv na konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="b8b59-234">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="b8b59-235">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-235">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b8b59-236">`AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-236">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b8b59-237">Pokud je potřeba zadat konfiguraci aplikace a stále mít možnost přepsat tuto konfiguraci s argumenty příkazového řádku, volání další poskytovatele aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> a volat `AddCommandLine` poslední.</span><span class="sxs-lookup"><span data-stu-id="b8b59-237">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

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

<span data-ttu-id="b8b59-238">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b8b59-238">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="b8b59-239">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b8b59-239">**Example**</span></span>

<span data-ttu-id="b8b59-240">Ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-240">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="b8b59-241">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-241">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="b8b59-242">Zadat argument příkazového řádku k `dotnet run` příkazu `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-242">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="b8b59-243">Jakmile je aplikace spuštěná, otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-243">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b8b59-244">Podívejte se, že výstup obsahuje pár klíč hodnota pro argument příkazového řádku konfigurace poskytnuté `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-244">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="b8b59-245">Arguments</span><span class="sxs-lookup"><span data-stu-id="b8b59-245">Arguments</span></span>

<span data-ttu-id="b8b59-246">Hodnota musí následovat znak rovná se (`=`), nebo klíč musí mít předponu (`--` nebo `/`) když hodnota má následující formát mezerou.</span><span class="sxs-lookup"><span data-stu-id="b8b59-246">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="b8b59-247">Hodnota může mít hodnotu null, pokud se používá znak rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="b8b59-247">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="b8b59-248">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="b8b59-248">Key prefix</span></span>               | <span data-ttu-id="b8b59-249">Příklad</span><span class="sxs-lookup"><span data-stu-id="b8b59-249">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="b8b59-250">Žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="b8b59-250">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="b8b59-251">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="b8b59-251">Two dashes (`--`)</span></span>        | <span data-ttu-id="b8b59-252">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="b8b59-252">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="b8b59-253">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="b8b59-253">Forward slash (`/`)</span></span>      | <span data-ttu-id="b8b59-254">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="b8b59-254">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="b8b59-255">V rámci stejného příkazu Nekombinujte argument příkazového řádku páry klíč hodnota, které používají znaménko rovná se s páry klíč hodnota, které používají mezerou.</span><span class="sxs-lookup"><span data-stu-id="b8b59-255">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="b8b59-256">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="b8b59-256">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="b8b59-257">Přepnout mapování</span><span class="sxs-lookup"><span data-stu-id="b8b59-257">Switch mappings</span></span>

<span data-ttu-id="b8b59-258">Mapování přepínači povolit název klíče pro náhradní logiku.</span><span class="sxs-lookup"><span data-stu-id="b8b59-258">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="b8b59-259">Při ruční sestavení konfigurace s <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, může poskytnout slovník nahrazení přepínači <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metoda.</span><span class="sxs-lookup"><span data-stu-id="b8b59-259">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b8b59-260">Při použití přepínače slovníku mapování slovníku se kontroluje u klíč, který odpovídá key určeného tímto argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b8b59-260">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b8b59-261">Pokud je nalezen příkazového řádku klíč ve slovníku, hodnota slovníku (náhradní klíč) se předá zpět do nastavit pár klíč hodnota v konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-261">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b8b59-262">Mapování přepínač je vyžadován pro libovolného příkazového řádku klíče předponu jeden pomlčka (`-`).</span><span class="sxs-lookup"><span data-stu-id="b8b59-262">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b8b59-263">Přepnout mapování slovníku klíčových pravidel:</span><span class="sxs-lookup"><span data-stu-id="b8b59-263">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b8b59-264">Přepínače musí začínat pomlčkou (`-`) nebo dvojitou pomlčka (`--`).</span><span class="sxs-lookup"><span data-stu-id="b8b59-264">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="b8b59-265">Slovník mapování přepínač nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="b8b59-265">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="b8b59-266">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b8b59-266">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="b8b59-267">Jak je znázorněno v předchozím příkladu volání `CreateDefaultBuilder` by neměla předat argumenty při mapování přepínače se používají.</span><span class="sxs-lookup"><span data-stu-id="b8b59-267">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="b8b59-268">`CreateDefaultBuilder` metody `AddCommandLine` volání neobsahuje mapované přepínače a neexistuje žádný způsob předat slovníku mapování přepínač k `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-268">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b8b59-269">Pokud argumenty obsahují mapované přepínače a jsou předány `CreateDefaultBuilder`, jeho `AddCommandLine` poskytovatele dojde k selhání inicializace s <xref:System.FormatException>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-269">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="b8b59-270">Řešení není předat argumenty, které mají `CreateDefaultBuilder` , ale místo toho povolit `ConfigurationBuilder` metody `AddCommandLine` metodu ke zpracování argumentů a slovníku mapování přepínače.</span><span class="sxs-lookup"><span data-stu-id="b8b59-270">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="b8b59-271">Po vytvoření slovníku mapování přepínač obsahuje data zobrazená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b8b59-271">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="b8b59-272">Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-272">Key</span></span>       | <span data-ttu-id="b8b59-273">Value</span><span class="sxs-lookup"><span data-stu-id="b8b59-273">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="b8b59-274">Pokud při spuštění aplikace se používají klíče mapované na přepínač, konfigurace přijímá konfigurační hodnoty klíče zadané ve slovníku:</span><span class="sxs-lookup"><span data-stu-id="b8b59-274">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="b8b59-275">Po spuštění předchozího příkazu se konfigurace obsahuje hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b8b59-275">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="b8b59-276">Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-276">Key</span></span>               | <span data-ttu-id="b8b59-277">Value</span><span class="sxs-lookup"><span data-stu-id="b8b59-277">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="b8b59-278">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="b8b59-278">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="b8b59-279"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Načte konfiguraci z prostředí proměnné páry klíč hodnota v době běhu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-279">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="b8b59-280">Chcete-li aktivovat konfigurace proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-280">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b8b59-281">[Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavit proměnné prostředí na webu Azure Portal, můžete přepsat konfiguraci aplikace pomocí zprostředkovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="b8b59-281">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="b8b59-282">Další informace najdete v tématu [aplikace Azure: Přepsat konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="b8b59-282">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="b8b59-283">`AddEnvironmentVariables` slouží k načtení proměnné prostředí s předponou `ASPNETCORE_` pro [konfigurace hostitele](#host-vs-app-configuration) při novém <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je inicializován.</span><span class="sxs-lookup"><span data-stu-id="b8b59-283">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-vs-app-configuration) when a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is initialized.</span></span> <span data-ttu-id="b8b59-284">Další informace najdete v tématu [webového hostitele: Nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b8b59-284">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b8b59-285">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="b8b59-285">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b8b59-286">Konfigurace aplikace z proměnných prostředí unprefixed voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="b8b59-286">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="b8b59-287">Volitelná konfigurace z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="b8b59-287">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="b8b59-288">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="b8b59-288">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b8b59-289">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b8b59-289">Command-line arguments.</span></span>

<span data-ttu-id="b8b59-290">Zprostředkovatel konfigurace proměnných prostředí je volána po navázání konfigurace z tajných kódů uživatelů a *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="b8b59-290">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="b8b59-291">Volání zprostředkovatele na této pozici umožňuje proměnné prostředí načteny za běhu přepsat konfiguraci nastavil tajných kódů uživatelů a *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="b8b59-291">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="b8b59-292">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-292">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b8b59-293">Pokud je potřeba zadat konfigurace aplikace z další proměnné prostředí, volání další poskytovatele aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> a volat `AddEnvironmentVariables` s předponou.</span><span class="sxs-lookup"><span data-stu-id="b8b59-293">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

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
                // Call AddEnvironmentVariables last if you need to allow
                // environment variables to override values from other 
                // providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b8b59-294">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b8b59-294">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b8b59-295">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b8b59-295">**Example**</span></span>

<span data-ttu-id="b8b59-296">Ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-296">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="b8b59-297">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-297">Run the sample app.</span></span> <span data-ttu-id="b8b59-298">Otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-298">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b8b59-299">Podívejte se, že výstup obsahuje pár klíč hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-299">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="b8b59-300">Hodnota odpovídá prostředí, ve kterém je aplikace spuštěna, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="b8b59-300">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="b8b59-301">Udržovat seznam proměnných prostředí vykreslen metodou aplikace krátký, filtry aplikace proměnných prostředí až po ty spustit následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="b8b59-301">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="b8b59-302">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="b8b59-302">ASPNETCORE_</span></span>
* <span data-ttu-id="b8b59-303">adresy URL</span><span class="sxs-lookup"><span data-stu-id="b8b59-303">urls</span></span>
* <span data-ttu-id="b8b59-304">Protokolování</span><span class="sxs-lookup"><span data-stu-id="b8b59-304">Logging</span></span>
* <span data-ttu-id="b8b59-305">PROSTŘEDÍ</span><span class="sxs-lookup"><span data-stu-id="b8b59-305">ENVIRONMENT</span></span>
* <span data-ttu-id="b8b59-306">contentRoot</span><span class="sxs-lookup"><span data-stu-id="b8b59-306">contentRoot</span></span>
* <span data-ttu-id="b8b59-307">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="b8b59-307">AllowedHosts</span></span>
* <span data-ttu-id="b8b59-308">applicationName</span><span class="sxs-lookup"><span data-stu-id="b8b59-308">applicationName</span></span>
* <span data-ttu-id="b8b59-309">příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="b8b59-309">CommandLine</span></span>

<span data-ttu-id="b8b59-310">Pokud chcete zpřístupnit všechny proměnné prostředí k dispozici pro aplikace, změnit `FilteredConfiguration` v *Pages/Index.cshtml.cs* takto:</span><span class="sxs-lookup"><span data-stu-id="b8b59-310">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="b8b59-311">Předpony adres</span><span class="sxs-lookup"><span data-stu-id="b8b59-311">Prefixes</span></span>

<span data-ttu-id="b8b59-312">Proměnné prostředí načteny do konfiguraci aplikace, jsou filtrovány, když zadáte předponu `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="b8b59-312">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="b8b59-313">Například k proměnným prostředí filtr na této předponě `CUSTOM_`, zadat předponu pro zprostředkovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b8b59-313">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="b8b59-314">Předpona, která se odstraní při vytváření konfigurace páry klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="b8b59-314">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="b8b59-315">Metoda statické pohodlí `CreateDefaultBuilder` vytvoří <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> stanovit hostiteli aplikace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-315">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="b8b59-316">Když `WebHostBuilder` je vytvořen, zjistí jeho konfigurace hostitele v proměnných prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-316">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

<span data-ttu-id="b8b59-317">**Připojovací řetězec předpony**</span><span class="sxs-lookup"><span data-stu-id="b8b59-317">**Connection string prefixes**</span></span>

<span data-ttu-id="b8b59-318">Rozhraní API konfigurace má zvláštní zpracování pravidel pro čtyři připojovací řetězec proměnné prostředí používané při konfiguraci Azure připojovací řetězce pro prostředí app.</span><span class="sxs-lookup"><span data-stu-id="b8b59-318">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b8b59-319">Proměnné prostředí s předponami uvedené v tabulce se načtou do aplikace, pokud není zadána žádná předpona k `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-319">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b8b59-320">Předpona řetězce připojení</span><span class="sxs-lookup"><span data-stu-id="b8b59-320">Connection string prefix</span></span> | <span data-ttu-id="b8b59-321">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="b8b59-321">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b8b59-322">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="b8b59-322">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b8b59-323">MySQL</span><span class="sxs-lookup"><span data-stu-id="b8b59-323">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b8b59-324">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="b8b59-324">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b8b59-325">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b8b59-325">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b8b59-326">Proměnné prostředí je při zjištění a načtena do konfigurace s žádným z čtyři předpony, které jsou uvedené v tabulce:</span><span class="sxs-lookup"><span data-stu-id="b8b59-326">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b8b59-327">Konfigurační klíč je vytvořen odebráním předponu proměnné prostředí a přidání konfiguračního klíče oddílu (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="b8b59-327">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b8b59-328">Je vytvořen nový pár klíč hodnota konfigurace, který představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá stanoveného zprostředkovatele).</span><span class="sxs-lookup"><span data-stu-id="b8b59-328">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b8b59-329">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="b8b59-329">Environment variable key</span></span> | <span data-ttu-id="b8b59-330">Převedený konfigurační klíč</span><span class="sxs-lookup"><span data-stu-id="b8b59-330">Converted configuration key</span></span> | <span data-ttu-id="b8b59-331">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="b8b59-331">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b8b59-332">Položky konfigurace nebyl vytvořen.</span><span class="sxs-lookup"><span data-stu-id="b8b59-332">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b8b59-333">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b8b59-333">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b8b59-334">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b8b59-334">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b8b59-335">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b8b59-335">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b8b59-336">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b8b59-336">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b8b59-337">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="b8b59-337">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b8b59-338">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b8b59-338">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="b8b59-339">Zprostředkovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="b8b59-339">File Configuration Provider</span></span>

<span data-ttu-id="b8b59-340"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třída pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="b8b59-340"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b8b59-341">Následující zprostředkovatele konfigurace jsou vyhrazené pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="b8b59-341">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="b8b59-342">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="b8b59-342">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b8b59-343">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="b8b59-343">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="b8b59-344">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="b8b59-344">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b8b59-345">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="b8b59-345">INI Configuration Provider</span></span>

<span data-ttu-id="b8b59-346"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Načte konfiguraci z páry klíč hodnota soubor INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-346">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b8b59-347">Chcete-li aktivovat konfigurační soubor INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-347">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b8b59-348">Dvojtečka je možné k jako oddělovač oddílu v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="b8b59-348">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="b8b59-349">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="b8b59-349">Overloads permit specifying:</span></span>

* <span data-ttu-id="b8b59-350">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="b8b59-350">Whether the file is optional.</span></span>
* <span data-ttu-id="b8b59-351">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="b8b59-351">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b8b59-352"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="b8b59-352">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b8b59-353">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b8b59-353">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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
                config.AddIniFile(
                    "config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b8b59-354">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-354">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b8b59-355">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-355">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-356">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b8b59-356">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="b8b59-357">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-357">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b8b59-358">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-358">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-359">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="b8b59-359">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="b8b59-360">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="b8b59-360">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b8b59-361">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-361">section0:key0</span></span>
* <span data-ttu-id="b8b59-362">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-362">section0:key1</span></span>
* <span data-ttu-id="b8b59-363">section1:subsection:Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-363">section1:subsection:key</span></span>
* <span data-ttu-id="b8b59-364">section2:subsection0:Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-364">section2:subsection0:key</span></span>
* <span data-ttu-id="b8b59-365">section2:subsection1:Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-365">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="b8b59-366">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="b8b59-366">JSON Configuration Provider</span></span>

<span data-ttu-id="b8b59-367"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Načte konfiguraci z páry klíč hodnota JSON soubor za běhu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-367">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="b8b59-368">Chcete-li aktivovat konfigurační soubor JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-368">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b8b59-369">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="b8b59-369">Overloads permit specifying:</span></span>

* <span data-ttu-id="b8b59-370">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="b8b59-370">Whether the file is optional.</span></span>
* <span data-ttu-id="b8b59-371">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="b8b59-371">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b8b59-372"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="b8b59-372">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b8b59-373">`AddJsonFile` je automaticky volána dvakrát při inicializaci nové <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-373">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b8b59-374">Načtení konfigurace z volání metody:</span><span class="sxs-lookup"><span data-stu-id="b8b59-374">The method is called to load configuration from:</span></span>

* <span data-ttu-id="b8b59-375">*appSettings.JSON* &ndash; tento soubor je nejprve pro čtení.</span><span class="sxs-lookup"><span data-stu-id="b8b59-375">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="b8b59-376">Prostředí verze souboru mohou přepsat hodnoty poskytnuté *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b8b59-376">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="b8b59-377">*appSettings. {Prostředí} .json* &ndash; načtení na základě prostředí verze souboru [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="b8b59-377">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="b8b59-378">Další informace najdete v tématu [webového hostitele: Nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="b8b59-378">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b8b59-379">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="b8b59-379">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b8b59-380">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b8b59-380">Environment variables.</span></span>
* <span data-ttu-id="b8b59-381">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="b8b59-381">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b8b59-382">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b8b59-382">Command-line arguments.</span></span>

<span data-ttu-id="b8b59-383">Zprostředkovatel konfigurace JSON je nejprve navázat.</span><span class="sxs-lookup"><span data-stu-id="b8b59-383">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="b8b59-384">Proto tajné klíče uživatelů, proměnné a argumenty příkazového řádku přepsat nastavením konfigurace *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="b8b59-384">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="b8b59-385">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace pro soubory jiné než *appsettings.json* a *appsettings. { Prostředí} .json*:</span><span class="sxs-lookup"><span data-stu-id="b8b59-385">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

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
                config.AddJsonFile(
                    "config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b8b59-386">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-386">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b8b59-387">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-387">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-388">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b8b59-388">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="b8b59-389">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-389">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b8b59-390">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-390">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-391">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="b8b59-391">**Example**</span></span>

<span data-ttu-id="b8b59-392">Ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, která zahrnuje dvě volání `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-392">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="b8b59-393">Konfigurace je načtena z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="b8b59-393">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="b8b59-394">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-394">Run the sample app.</span></span> <span data-ttu-id="b8b59-395">Otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-395">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b8b59-396">Podívejte se, že výstup obsahuje páry klíč hodnota u konfigurace uvedené v tabulce v závislosti na prostředí.</span><span class="sxs-lookup"><span data-stu-id="b8b59-396">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="b8b59-397">Protokolování konfigurační klíče pomocí dvojtečky (`:`) jako oddělovačem hierarchický.</span><span class="sxs-lookup"><span data-stu-id="b8b59-397">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="b8b59-398">Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-398">Key</span></span>                        | <span data-ttu-id="b8b59-399">Hodnota vývoj</span><span class="sxs-lookup"><span data-stu-id="b8b59-399">Development Value</span></span> | <span data-ttu-id="b8b59-400">Hodnota produkce</span><span class="sxs-lookup"><span data-stu-id="b8b59-400">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="b8b59-401">Protokolování: LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="b8b59-401">Logging:LogLevel:System</span></span>    | <span data-ttu-id="b8b59-402">Informace o</span><span class="sxs-lookup"><span data-stu-id="b8b59-402">Information</span></span>       | <span data-ttu-id="b8b59-403">Informace o</span><span class="sxs-lookup"><span data-stu-id="b8b59-403">Information</span></span>      |
| <span data-ttu-id="b8b59-404">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="b8b59-404">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="b8b59-405">Informace o</span><span class="sxs-lookup"><span data-stu-id="b8b59-405">Information</span></span>       | <span data-ttu-id="b8b59-406">Informace o</span><span class="sxs-lookup"><span data-stu-id="b8b59-406">Information</span></span>      |
| <span data-ttu-id="b8b59-407">Protokolování: LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="b8b59-407">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="b8b59-408">Ladění</span><span class="sxs-lookup"><span data-stu-id="b8b59-408">Debug</span></span>             | <span data-ttu-id="b8b59-409">Chyba</span><span class="sxs-lookup"><span data-stu-id="b8b59-409">Error</span></span>            |
| <span data-ttu-id="b8b59-410">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="b8b59-410">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="b8b59-411">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="b8b59-411">XML Configuration Provider</span></span>

<span data-ttu-id="b8b59-412"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Načte konfiguraci z dvojice klíč hodnota XML soubor za běhu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-412">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b8b59-413">Chcete-li aktivovat konfigurační soubor XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-413">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b8b59-414">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="b8b59-414">Overloads permit specifying:</span></span>

* <span data-ttu-id="b8b59-415">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="b8b59-415">Whether the file is optional.</span></span>
* <span data-ttu-id="b8b59-416">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="b8b59-416">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b8b59-417"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="b8b59-417">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b8b59-418">Kořenový uzel konfiguračního souboru se ignoruje při vytváření konfigurace páry klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="b8b59-418">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="b8b59-419">V souboru zadejte dokumentu typ definice (DTD) nebo obor názvů.</span><span class="sxs-lookup"><span data-stu-id="b8b59-419">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="b8b59-420">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b8b59-420">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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
                config.AddXmlFile(
                    "config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b8b59-421">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-421">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b8b59-422">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-422">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-423">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b8b59-423">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="b8b59-424">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-424">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b8b59-425">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-425">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-426">Soubory XML konfigurace můžete použít názvy různých elementů pro opakující se části:</span><span class="sxs-lookup"><span data-stu-id="b8b59-426">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="b8b59-427">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="b8b59-427">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b8b59-428">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-428">section0:key0</span></span>
* <span data-ttu-id="b8b59-429">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-429">section0:key1</span></span>
* <span data-ttu-id="b8b59-430">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-430">section1:key0</span></span>
* <span data-ttu-id="b8b59-431">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-431">section1:key1</span></span>

<span data-ttu-id="b8b59-432">Opakující se elementy, které používají stejný název elementu fungovat, pokud `name` atribut se používá k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="b8b59-432">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="b8b59-433">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="b8b59-433">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b8b59-434">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-434">section:section0:key:key0</span></span>
* <span data-ttu-id="b8b59-435">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-435">section:section0:key:key1</span></span>
* <span data-ttu-id="b8b59-436">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-436">section:section1:key:key0</span></span>
* <span data-ttu-id="b8b59-437">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-437">section:section1:key:key1</span></span>

<span data-ttu-id="b8b59-438">Atributy lze použít k zadání hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b8b59-438">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b8b59-439">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="b8b59-439">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b8b59-440">atribut Key:</span><span class="sxs-lookup"><span data-stu-id="b8b59-440">key:attribute</span></span>
* <span data-ttu-id="b8b59-441">část: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="b8b59-441">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b8b59-442">Poskytovatel konfigurace pro každý soubor klíče</span><span class="sxs-lookup"><span data-stu-id="b8b59-442">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="b8b59-443"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Používá souborů v adresáři jako páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-443">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b8b59-444">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="b8b59-444">The key is the file name.</span></span> <span data-ttu-id="b8b59-445">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="b8b59-445">The value contains the file's contents.</span></span> <span data-ttu-id="b8b59-446">Konfiguraci poskytovatele za soubor klíče se používá ve scénářích hostování Dockeru.</span><span class="sxs-lookup"><span data-stu-id="b8b59-446">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b8b59-447">Chcete-li aktivovat konfigurace na soubor klíče, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-447">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b8b59-448">`directoryPath` k souborům musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="b8b59-448">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b8b59-449">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="b8b59-449">Overloads permit specifying:</span></span>

* <span data-ttu-id="b8b59-450">`Action<KeyPerFileConfigurationSource>` Delegáta, který nastaví jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="b8b59-450">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b8b59-451">Určuje, zda daný adresář je volitelné a cestu k adresáři.</span><span class="sxs-lookup"><span data-stu-id="b8b59-451">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b8b59-452">Double – podtržítko (`__`) se používá jako oddělovač klíčových konfigurace v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="b8b59-452">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b8b59-453">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-453">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b8b59-454">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b8b59-454">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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
                var path = Path.Combine(
                    Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b8b59-455">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-455">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b8b59-456">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-456">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-457">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b8b59-457">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

## <a name="memory-configuration-provider"></a><span data-ttu-id="b8b59-458">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="b8b59-458">Memory Configuration Provider</span></span>

<span data-ttu-id="b8b59-459"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Používá kolekci v paměti jako páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-459">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b8b59-460">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-460">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b8b59-461">Poskytovatel konfigurace mohou být inicializovány pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-461">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="b8b59-462">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="b8b59-462">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="b8b59-463">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="b8b59-463">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

## <a name="getvalue"></a><span data-ttu-id="b8b59-464">GetValue</span><span class="sxs-lookup"><span data-stu-id="b8b59-464">GetValue</span></span>

<span data-ttu-id="b8b59-465">[ConfigurationBinder.GetValue&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje hodnotu z konfigurace se zadaným klíčem a převede ho na zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="b8b59-465">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="b8b59-466">Přetížení umožňuje zadat výchozí hodnotu, pokud není nalezen klíč.</span><span class="sxs-lookup"><span data-stu-id="b8b59-466">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="b8b59-467">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b8b59-467">The following example:</span></span>

* <span data-ttu-id="b8b59-468">Extrahuje hodnotu řetězce s klíčem z konfigurace `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-468">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="b8b59-469">Pokud `NumberKey` nebyl nalezen v konfigurační klíče, výchozí hodnota `99` se používá.</span><span class="sxs-lookup"><span data-stu-id="b8b59-469">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="b8b59-470">Typy hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-470">Types the value as an `int`.</span></span>
* <span data-ttu-id="b8b59-471">Uloží hodnotu v `NumberConfig` vlastnost pro použití na stránce.</span><span class="sxs-lookup"><span data-stu-id="b8b59-471">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b8b59-472">GetSection, GetChildren – a existuje</span><span class="sxs-lookup"><span data-stu-id="b8b59-472">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b8b59-473">Příklady, které následují vezměte v úvahu následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="b8b59-473">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="b8b59-474">Čtyři kódy se nacházejí ve dvou částech, z nichž jeden obsahuje dvojice pododdíly:</span><span class="sxs-lookup"><span data-stu-id="b8b59-474">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="b8b59-475">Přečtení souboru do konfigurace následujících jedinečné klíče hierarchické jsou vytvořeny pro uchování hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b8b59-475">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="b8b59-476">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-476">section0:key0</span></span>
* <span data-ttu-id="b8b59-477">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-477">section0:key1</span></span>
* <span data-ttu-id="b8b59-478">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-478">section1:key0</span></span>
* <span data-ttu-id="b8b59-479">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-479">section1:key1</span></span>
* <span data-ttu-id="b8b59-480">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-480">section2:subsection0:key0</span></span>
* <span data-ttu-id="b8b59-481">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-481">section2:subsection0:key1</span></span>
* <span data-ttu-id="b8b59-482">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="b8b59-482">section2:subsection1:key0</span></span>
* <span data-ttu-id="b8b59-483">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="b8b59-483">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="b8b59-484">GetSection</span><span class="sxs-lookup"><span data-stu-id="b8b59-484">GetSection</span></span>

<span data-ttu-id="b8b59-485">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje dílčí část konfigurace s klíčem zadaného dílčí část.</span><span class="sxs-lookup"><span data-stu-id="b8b59-485">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="b8b59-486">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-486">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-487">Se vraťte <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč hodnota v `section1`, volání `GetSection` a zadat název oddílu:</span><span class="sxs-lookup"><span data-stu-id="b8b59-487">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="b8b59-488">`configSection` Nemá hodnotu, pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-488">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="b8b59-489">Podobně a získat tak hodnoty pro klíče v `section2:subsection0`, volání `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="b8b59-489">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="b8b59-490">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-490">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b8b59-491">Pokud není nalezen odpovídající části, prázdná `IConfigurationSection` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="b8b59-491">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="b8b59-492">Když `GetSection` vrátí odpovídající části <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněn.</span><span class="sxs-lookup"><span data-stu-id="b8b59-492">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="b8b59-493">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když existuje části.</span><span class="sxs-lookup"><span data-stu-id="b8b59-493">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="b8b59-494">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="b8b59-494">GetChildren</span></span>

<span data-ttu-id="b8b59-495">Volání [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) na `section2` získá `IEnumerable<IConfigurationSection>` , který obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b8b59-495">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="b8b59-496">Existuje</span><span class="sxs-lookup"><span data-stu-id="b8b59-496">Exists</span></span>

<span data-ttu-id="b8b59-497">Použití [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, jestli existuje konfigurační oddíl:</span><span class="sxs-lookup"><span data-stu-id="b8b59-497">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="b8b59-498">Zadaný ukázková data `sectionExists` je `false` protože není k dispozici `section2:subsection2` části v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="b8b59-498">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="b8b59-499">Vytvoření vazby na třídu</span><span class="sxs-lookup"><span data-stu-id="b8b59-499">Bind to a class</span></span>

<span data-ttu-id="b8b59-500">Konfigurace může být vázaný na třídy, které představující skupiny související nastavení použití *možnosti vzor*.</span><span class="sxs-lookup"><span data-stu-id="b8b59-500">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="b8b59-501">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b8b59-502">Konfigurační hodnoty jsou vrácené jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objekty.</span><span class="sxs-lookup"><span data-stu-id="b8b59-502">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="b8b59-503">`Bind` Probíhá [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-503">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-504">Obsahuje ukázkovou aplikaci `Starship` modelu (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="b8b59-504">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="b8b59-505">`starship` Část *starship.json* soubor vytvoří konfiguraci při ukázková aplikace používá zprostředkovatele konfigurace JSON načíst konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="b8b59-505">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="b8b59-506">Následující páry klíč hodnota konfigurace jsou vytvořeny:</span><span class="sxs-lookup"><span data-stu-id="b8b59-506">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="b8b59-507">Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-507">Key</span></span>                   | <span data-ttu-id="b8b59-508">Value</span><span class="sxs-lookup"><span data-stu-id="b8b59-508">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="b8b59-509">starship: name</span><span class="sxs-lookup"><span data-stu-id="b8b59-509">starship:name</span></span>         | <span data-ttu-id="b8b59-510">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="b8b59-510">USS Enterprise</span></span>                                    |
| <span data-ttu-id="b8b59-511">starship: registru</span><span class="sxs-lookup"><span data-stu-id="b8b59-511">starship:registry</span></span>     | <span data-ttu-id="b8b59-512">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="b8b59-512">NCC-1701</span></span>                                          |
| <span data-ttu-id="b8b59-513">starship: Třída</span><span class="sxs-lookup"><span data-stu-id="b8b59-513">starship:class</span></span>        | <span data-ttu-id="b8b59-514">Vytvoření</span><span class="sxs-lookup"><span data-stu-id="b8b59-514">Constitution</span></span>                                      |
| <span data-ttu-id="b8b59-515">starship: délka</span><span class="sxs-lookup"><span data-stu-id="b8b59-515">starship:length</span></span>       | <span data-ttu-id="b8b59-516">304.8</span><span class="sxs-lookup"><span data-stu-id="b8b59-516">304.8</span></span>                                             |
| <span data-ttu-id="b8b59-517">starship: stává</span><span class="sxs-lookup"><span data-stu-id="b8b59-517">starship:commissioned</span></span> | <span data-ttu-id="b8b59-518">False</span><span class="sxs-lookup"><span data-stu-id="b8b59-518">False</span></span>                                             |
| <span data-ttu-id="b8b59-519">Ochranné známky</span><span class="sxs-lookup"><span data-stu-id="b8b59-519">trademark</span></span>             | <span data-ttu-id="b8b59-520">Paramount obrázky Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="b8b59-520">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="b8b59-521">Ukázková aplikace volání `GetSection` s `starship` klíč.</span><span class="sxs-lookup"><span data-stu-id="b8b59-521">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="b8b59-522">`starship` Páry klíč hodnota jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="b8b59-522">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="b8b59-523">`Bind` Metoda je volána v podčásti předávání v instanci `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="b8b59-523">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="b8b59-524">Po navázání hodnoty instanci, instance přidruženo k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="b8b59-524">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

<span data-ttu-id="b8b59-525">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-525">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="b8b59-526">Vytvořit vazbu grafu objektu</span><span class="sxs-lookup"><span data-stu-id="b8b59-526">Bind to an object graph</span></span>

<span data-ttu-id="b8b59-527"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopen vazby celého grafu objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="b8b59-527"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="b8b59-528">`Bind` Probíhá [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-528">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-529">Obsahuje ukázku `TvShow` modelu, jehož graf objektu obsahuje `Metadata` a `Actors` třídy (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="b8b59-529">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="b8b59-530">Ukázková aplikace má *tvshow.xml* soubor, který obsahuje konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="b8b59-530">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="b8b59-531">Konfigurace je vázán na celý `TvShow` grafu objektu s `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="b8b59-531">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="b8b59-532">Vázané instance je přiřazená k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="b8b59-532">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="b8b59-533">[ConfigurationBinder.Get&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) váže a vrátí hodnotu zadaného typu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-533">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="b8b59-534">`Get<T>` je výhodnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-534">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="b8b59-535">Následující kód ukazuje, jak používat `Get<T>` z předchozího příkladu, který umožňuje vázané instance má být přímo přiřazeni k vlastnosti pro vykreslování použit:</span><span class="sxs-lookup"><span data-stu-id="b8b59-535">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

<span data-ttu-id="b8b59-536"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> Probíhá [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-536"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b8b59-537">`Get<T>` je k dispozici v ASP.NET Core 1.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="b8b59-537">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="b8b59-538">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-538">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b8b59-539">Svázat pole třídy</span><span class="sxs-lookup"><span data-stu-id="b8b59-539">Bind an array to a class</span></span>

<span data-ttu-id="b8b59-540">*Ukázková aplikace předvádí koncepty je popsáno v této části.*</span><span class="sxs-lookup"><span data-stu-id="b8b59-540">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="b8b59-541"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Podporuje pole vazby na objekty pomocí indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b8b59-541">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b8b59-542">Jakékoli pole formátu, který poskytuje číselné segment klíče (`:0:`, `:1:`, &hellip; `:{n}:`) je schopný vazba pole na pole třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="b8b59-542">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="b8b59-543">"Svázat" je v [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-543">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="b8b59-544">Vazba je poskytována konvence.</span><span class="sxs-lookup"><span data-stu-id="b8b59-544">Binding is provided by convention.</span></span> <span data-ttu-id="b8b59-545">Vlastní zprostředkovatelé konfigurace není nutné implementovat pole vazby.</span><span class="sxs-lookup"><span data-stu-id="b8b59-545">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="b8b59-546">**Zpracování v paměti pole**</span><span class="sxs-lookup"><span data-stu-id="b8b59-546">**In-memory array processing**</span></span>

<span data-ttu-id="b8b59-547">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b8b59-547">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="b8b59-548">Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-548">Key</span></span>             | <span data-ttu-id="b8b59-549">Value</span><span class="sxs-lookup"><span data-stu-id="b8b59-549">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b8b59-550">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="b8b59-550">array:entries:0</span></span> | <span data-ttu-id="b8b59-551">gamma0</span><span class="sxs-lookup"><span data-stu-id="b8b59-551">value0</span></span> |
| <span data-ttu-id="b8b59-552">pole: položek: 1</span><span class="sxs-lookup"><span data-stu-id="b8b59-552">array:entries:1</span></span> | <span data-ttu-id="b8b59-553">value1</span><span class="sxs-lookup"><span data-stu-id="b8b59-553">value1</span></span> |
| <span data-ttu-id="b8b59-554">pole: položek: 2</span><span class="sxs-lookup"><span data-stu-id="b8b59-554">array:entries:2</span></span> | <span data-ttu-id="b8b59-555">value2</span><span class="sxs-lookup"><span data-stu-id="b8b59-555">value2</span></span> |
| <span data-ttu-id="b8b59-556">pole: položek: 4</span><span class="sxs-lookup"><span data-stu-id="b8b59-556">array:entries:4</span></span> | <span data-ttu-id="b8b59-557">value4</span><span class="sxs-lookup"><span data-stu-id="b8b59-557">value4</span></span> |
| <span data-ttu-id="b8b59-558">pole: položek: 5</span><span class="sxs-lookup"><span data-stu-id="b8b59-558">array:entries:5</span></span> | <span data-ttu-id="b8b59-559">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="b8b59-559">value5</span></span> |

<span data-ttu-id="b8b59-560">Tyto klíče a hodnoty jsou načteny v ukázkové aplikaci pomocí zprostředkovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="b8b59-560">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

<span data-ttu-id="b8b59-561">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="b8b59-561">The array skips a value for index &num;3.</span></span> <span data-ttu-id="b8b59-562">Konfigurace vazače není schopen vazby hodnoty null a vytvářet položky s hodnotou null v vázaným objektům, zrušte v okamžiku, kdy je znázorněn výsledek vazby toto pole na objekt, který se stane.</span><span class="sxs-lookup"><span data-stu-id="b8b59-562">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="b8b59-563">V ukázkové aplikaci je k dispozici pro uložení vázané konfigurační data POCO třídy:</span><span class="sxs-lookup"><span data-stu-id="b8b59-563">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="b8b59-564">Konfigurační data je vázaná na objekt:</span><span class="sxs-lookup"><span data-stu-id="b8b59-564">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="b8b59-565">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b8b59-565">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8b59-566">[ConfigurationBinder.Get&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe je také možné, povede k kompaktnějším kód:</span><span class="sxs-lookup"><span data-stu-id="b8b59-566">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="b8b59-567">Vázaný objekt, instance `ArrayExample`, přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-567">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="b8b59-568">`ArrayExample.Entries` Index</span><span class="sxs-lookup"><span data-stu-id="b8b59-568">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b8b59-569">`ArrayExample.Entries` Hodnota</span><span class="sxs-lookup"><span data-stu-id="b8b59-569">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b8b59-570">0</span><span class="sxs-lookup"><span data-stu-id="b8b59-570">0</span></span>                            | <span data-ttu-id="b8b59-571">gamma0</span><span class="sxs-lookup"><span data-stu-id="b8b59-571">value0</span></span>                       |
| <span data-ttu-id="b8b59-572">1</span><span class="sxs-lookup"><span data-stu-id="b8b59-572">1</span></span>                            | <span data-ttu-id="b8b59-573">value1</span><span class="sxs-lookup"><span data-stu-id="b8b59-573">value1</span></span>                       |
| <span data-ttu-id="b8b59-574">2</span><span class="sxs-lookup"><span data-stu-id="b8b59-574">2</span></span>                            | <span data-ttu-id="b8b59-575">value2</span><span class="sxs-lookup"><span data-stu-id="b8b59-575">value2</span></span>                       |
| <span data-ttu-id="b8b59-576">3</span><span class="sxs-lookup"><span data-stu-id="b8b59-576">3</span></span>                            | <span data-ttu-id="b8b59-577">value4</span><span class="sxs-lookup"><span data-stu-id="b8b59-577">value4</span></span>                       |
| <span data-ttu-id="b8b59-578">4</span><span class="sxs-lookup"><span data-stu-id="b8b59-578">4</span></span>                            | <span data-ttu-id="b8b59-579">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="b8b59-579">value5</span></span>                       |

<span data-ttu-id="b8b59-580">Index &num;3 v vázaný objekt obsahuje konfigurační data pro `array:4` konfiguračního klíče a jeho hodnota `value4`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-580">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b8b59-581">Když je vytvořena vazba konfigurační data obsahující pole, indexy pole v konfigurační klíče se používají pouze k iteraci konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-581">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b8b59-582">Hodnotu null nelze uchovávat v konfiguračních datech a položku s hodnotou null není vytvořená v vázaný objekt, když přeskočí jednu nebo více indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b8b59-582">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b8b59-583">Chybějící položky konfigurace pro index &num;3 může být zadán před vazbu `ArrayExample` instance poskytovatelem žádné konfigurace, která vytváří správné páru klíč hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="b8b59-583">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="b8b59-584">Pokud vzorek zahrnuje další poskytovatele konfigurace JSON s chybějící páru klíč hodnota, `ArrayExample.Entries` odpovídá poli kompletní konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b8b59-584">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="b8b59-585">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="b8b59-585">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="b8b59-586">V <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="b8b59-586">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="b8b59-587">Dvojice klíč hodnota v tabulce se načtou do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-587">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="b8b59-588">Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-588">Key</span></span>             | <span data-ttu-id="b8b59-589">Value</span><span class="sxs-lookup"><span data-stu-id="b8b59-589">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b8b59-590">pole: položek: 3</span><span class="sxs-lookup"><span data-stu-id="b8b59-590">array:entries:3</span></span> | <span data-ttu-id="b8b59-591">hodnota3</span><span class="sxs-lookup"><span data-stu-id="b8b59-591">value3</span></span> |

<span data-ttu-id="b8b59-592">Pokud `ArrayExample` instance třídy je vázán po poskytovatel konfigurace JSON obsahuje položku pro index &num;3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b8b59-592">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="b8b59-593">`ArrayExample.Entries` Index</span><span class="sxs-lookup"><span data-stu-id="b8b59-593">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b8b59-594">`ArrayExample.Entries` Hodnota</span><span class="sxs-lookup"><span data-stu-id="b8b59-594">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b8b59-595">0</span><span class="sxs-lookup"><span data-stu-id="b8b59-595">0</span></span>                            | <span data-ttu-id="b8b59-596">gamma0</span><span class="sxs-lookup"><span data-stu-id="b8b59-596">value0</span></span>                       |
| <span data-ttu-id="b8b59-597">1</span><span class="sxs-lookup"><span data-stu-id="b8b59-597">1</span></span>                            | <span data-ttu-id="b8b59-598">value1</span><span class="sxs-lookup"><span data-stu-id="b8b59-598">value1</span></span>                       |
| <span data-ttu-id="b8b59-599">2</span><span class="sxs-lookup"><span data-stu-id="b8b59-599">2</span></span>                            | <span data-ttu-id="b8b59-600">value2</span><span class="sxs-lookup"><span data-stu-id="b8b59-600">value2</span></span>                       |
| <span data-ttu-id="b8b59-601">3</span><span class="sxs-lookup"><span data-stu-id="b8b59-601">3</span></span>                            | <span data-ttu-id="b8b59-602">hodnota3</span><span class="sxs-lookup"><span data-stu-id="b8b59-602">value3</span></span>                       |
| <span data-ttu-id="b8b59-603">4</span><span class="sxs-lookup"><span data-stu-id="b8b59-603">4</span></span>                            | <span data-ttu-id="b8b59-604">value4</span><span class="sxs-lookup"><span data-stu-id="b8b59-604">value4</span></span>                       |
| <span data-ttu-id="b8b59-605">5</span><span class="sxs-lookup"><span data-stu-id="b8b59-605">5</span></span>                            | <span data-ttu-id="b8b59-606">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="b8b59-606">value5</span></span>                       |

<span data-ttu-id="b8b59-607">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="b8b59-607">**JSON array processing**</span></span>

<span data-ttu-id="b8b59-608">Pokud soubor JSON obsahuje pole, vytvoří se pro prvky pole s indexem založený na nule části konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="b8b59-608">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="b8b59-609">V následující konfigurační soubor `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="b8b59-609">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="b8b59-610">Zprostředkovatel konfigurace JSON čte konfigurační data do následující páry klíč hodnota:</span><span class="sxs-lookup"><span data-stu-id="b8b59-610">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="b8b59-611">Key</span><span class="sxs-lookup"><span data-stu-id="b8b59-611">Key</span></span>                     | <span data-ttu-id="b8b59-612">Value</span><span class="sxs-lookup"><span data-stu-id="b8b59-612">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="b8b59-613">json_array:key</span><span class="sxs-lookup"><span data-stu-id="b8b59-613">json_array:key</span></span>          | <span data-ttu-id="b8b59-614">valueA</span><span class="sxs-lookup"><span data-stu-id="b8b59-614">valueA</span></span> |
| <span data-ttu-id="b8b59-615">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="b8b59-615">json_array:subsection:0</span></span> | <span data-ttu-id="b8b59-616">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="b8b59-616">valueB</span></span> |
| <span data-ttu-id="b8b59-617">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="b8b59-617">json_array:subsection:1</span></span> | <span data-ttu-id="b8b59-618">valueC</span><span class="sxs-lookup"><span data-stu-id="b8b59-618">valueC</span></span> |
| <span data-ttu-id="b8b59-619">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="b8b59-619">json_array:subsection:2</span></span> | <span data-ttu-id="b8b59-620">valueD</span><span class="sxs-lookup"><span data-stu-id="b8b59-620">valueD</span></span> |

<span data-ttu-id="b8b59-621">V ukázkové aplikaci je k dispozici pro vazbu páry klíč hodnota konfigurace následující třídy POCO:</span><span class="sxs-lookup"><span data-stu-id="b8b59-621">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="b8b59-622">Po vytvoření vazby, `JsonArrayExample.Key` obsahuje hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-622">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="b8b59-623">Dílčí část hodnoty jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-623">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="b8b59-624">`JsonArrayExample.Subsection` Index</span><span class="sxs-lookup"><span data-stu-id="b8b59-624">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="b8b59-625">`JsonArrayExample.Subsection` Hodnota</span><span class="sxs-lookup"><span data-stu-id="b8b59-625">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="b8b59-626">0</span><span class="sxs-lookup"><span data-stu-id="b8b59-626">0</span></span>                                   | <span data-ttu-id="b8b59-627">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="b8b59-627">valueB</span></span>                              |
| <span data-ttu-id="b8b59-628">1</span><span class="sxs-lookup"><span data-stu-id="b8b59-628">1</span></span>                                   | <span data-ttu-id="b8b59-629">valueC</span><span class="sxs-lookup"><span data-stu-id="b8b59-629">valueC</span></span>                              |
| <span data-ttu-id="b8b59-630">2</span><span class="sxs-lookup"><span data-stu-id="b8b59-630">2</span></span>                                   | <span data-ttu-id="b8b59-631">valueD</span><span class="sxs-lookup"><span data-stu-id="b8b59-631">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="b8b59-632">Vlastního poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="b8b59-632">Custom configuration provider</span></span>

<span data-ttu-id="b8b59-633">Ukázková aplikace předvádí, jak vytvořit základní konfigurace poskytovatele, který přečte dvojice klíč hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b8b59-633">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b8b59-634">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b8b59-634">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b8b59-635">EF database v paměti se používá pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="b8b59-635">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b8b59-636">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementovat sekundární `ConfigurationBuilder` zadat připojovací řetězec z jiného zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b8b59-636">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b8b59-637">Poskytovatel přečte tabulku databáze do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b8b59-637">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b8b59-638">Zprostředkovatel nepodporuje dotazy na databázi na základě-key.</span><span class="sxs-lookup"><span data-stu-id="b8b59-638">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b8b59-639">Znovu načíst na změnu není implementována, po spuštění aplikace nemá žádný vliv na konfiguraci aplikace, takže aktualizace databáze.</span><span class="sxs-lookup"><span data-stu-id="b8b59-639">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b8b59-640">Definování `EFConfigurationValue` entity pro ukládání hodnoty konfigurace v databázi.</span><span class="sxs-lookup"><span data-stu-id="b8b59-640">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b8b59-641">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8b59-641">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="b8b59-642">Přidat `EFConfigurationContext` ukládání a přístup k nakonfigurované hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b8b59-642">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b8b59-643">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8b59-643">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="b8b59-644">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-644">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b8b59-645">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8b59-645">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="b8b59-646">Vytvoření vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-646">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b8b59-647">Poskytovatel konfigurace inicializuje databáze, když je prázdný.</span><span class="sxs-lookup"><span data-stu-id="b8b59-647">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="b8b59-648">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8b59-648">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="b8b59-649">`AddEFConfiguration` – Metoda rozšíření umožňuje zdroj konfigurace k přidání `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-649">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b8b59-650">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8b59-650">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="b8b59-651">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8b59-651">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="b8b59-652">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="b8b59-652">Access configuration during startup</span></span>

<span data-ttu-id="b8b59-653">Vložit `IConfiguration` do `Startup` konstruktoru na hodnoty konfigurace přístupu v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8b59-653">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b8b59-654">Získat přístup ke konfiguraci v `Startup.Configure`, buď vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="b8b59-654">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="b8b59-655">Příklad přístup ke konfiguraci pomocí vhodné metody po spuštění najdete v tématu [spuštění aplikace: Vhodné metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="b8b59-655">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="b8b59-656">Konfigurace přístupu v stránky Razor Pages nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="b8b59-656">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="b8b59-657">Chcete-li získat přístup k nastavení konfigurace v zobrazení MVC nebo stránky Razor Pages, přidejte [using – direktiva](xref:mvc/views/razor#using) ([referenční dokumentace jazyka C#: použití direktivy](/dotnet/csharp/language-reference/keywords/using-directive)) pro [Microsoft.Extensions.Configuration obor názvů ](xref:Microsoft.Extensions.Configuration) a vložit <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b8b59-657">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="b8b59-658">Na stránce pro stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="b8b59-658">In a Razor Pages page:</span></span>

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

<span data-ttu-id="b8b59-659">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="b8b59-659">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b8b59-660">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="b8b59-660">Add configuration from an external assembly</span></span>

<span data-ttu-id="b8b59-661"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Implementace umožňuje přidání vylepšení do aplikace při spuštění z externího sestavení mimo aplikaci prvku `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="b8b59-661">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b8b59-662">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b8b59-662">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8b59-663">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b8b59-663">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="b8b59-664">Podrobné informace o konfiguraci Microsoft</span><span class="sxs-lookup"><span data-stu-id="b8b59-664">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)

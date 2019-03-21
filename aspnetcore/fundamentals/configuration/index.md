---
title: Konfigurace v ASP.NET Core
author: guardrex
description: 'Zjistěte, jak použít rozhraní API pro konfiguraci ke konfiguraci aplikace ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2019
uid: fundamentals/configuration/index
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="a32c9-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a32c9-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="a32c9-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a32c9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a32c9-105">Konfigurace aplikace v ASP.NET Core je založená na páry klíč hodnota stanovené *poskytovatelé konfigurace*.</span><span class="sxs-lookup"><span data-stu-id="a32c9-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="a32c9-106">Poskytovatelé konfigurace čtení konfiguračních dat do párů hodnot klíčů z různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a32c9-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="a32c9-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a32c9-107">Azure Key Vault</span></span>
* <span data-ttu-id="a32c9-108">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a32c9-108">Command-line arguments</span></span>
* <span data-ttu-id="a32c9-109">Vlastní zprostředkovatelé (nainstalované nebo vytváření)</span><span class="sxs-lookup"><span data-stu-id="a32c9-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="a32c9-110">Adresář souborů</span><span class="sxs-lookup"><span data-stu-id="a32c9-110">Directory files</span></span>
* <span data-ttu-id="a32c9-111">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a32c9-111">Environment variables</span></span>
* <span data-ttu-id="a32c9-112">Objekty v paměti .NET</span><span class="sxs-lookup"><span data-stu-id="a32c9-112">In-memory .NET objects</span></span>
* <span data-ttu-id="a32c9-113">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="a32c9-113">Settings files</span></span>

<span data-ttu-id="a32c9-114">*Možnosti vzor* je rozšířením konfigurace koncepty popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-114">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="a32c9-115">Možnosti třídy používá k reprezentování skupiny související nastavení.</span><span class="sxs-lookup"><span data-stu-id="a32c9-115">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="a32c9-116">Další informace o použití vzoru možnosti najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-116">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="a32c9-117">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a32c9-117">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a32c9-118">Tyto tři balíčky jsou součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-118">These three packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="host-vs-app-configuration"></a><span data-ttu-id="a32c9-119">Hostování a konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="a32c9-119">Host vs. app configuration</span></span>

<span data-ttu-id="a32c9-120">Předtím, než aplikace je nakonfigurovaná a spuštěna, *hostitele* nakonfigurovaný a spustit.</span><span class="sxs-lookup"><span data-stu-id="a32c9-120">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="a32c9-121">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="a32c9-121">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a32c9-122">Aplikace a hostitel jsou nakonfigurováni pomocí zprostředkovatele konfigurace popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-122">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="a32c9-123">Páry klíč hodnota konfigurace hostitele se stanou součástí globální konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-123">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="a32c9-124">Další informace o jak konfiguraci poskytovatele se používají při vytváření hostitele a vliv zdroje konfigurace hostitele konfigurace najdete v tématu [hostitele](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="a32c9-124">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="a32c9-125">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="a32c9-125">Default configuration</span></span>

<span data-ttu-id="a32c9-126">Webové aplikace založené na technologii ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) volání šablony <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="a32c9-126">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="a32c9-127"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> poskytuje výchozí konfigurace pro aplikace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="a32c9-127"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

* <span data-ttu-id="a32c9-128">Konfigurace hostitele je k dispozici od:</span><span class="sxs-lookup"><span data-stu-id="a32c9-128">Host configuration is provided from:</span></span>
  * <span data-ttu-id="a32c9-129">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a32c9-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a32c9-130">Předpona, která (`ASPNETCORE_`) je odebrána, když jsou načteny páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-130">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="a32c9-131">Argumenty příkazového řádku pomocí [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a32c9-131">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="a32c9-132">Konfigurace aplikace je k dispozici od:</span><span class="sxs-lookup"><span data-stu-id="a32c9-132">App configuration is provided from:</span></span>
  * <span data-ttu-id="a32c9-133">*appSettings.JSON* pomocí [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a32c9-133">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a32c9-134">*appSettings. {Prostředí} .json* pomocí [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a32c9-134">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a32c9-135">[Tajný klíč správce](xref:security/app-secrets) při spuštění aplikace `Development` prostředí s využitím vstupní sestavení.</span><span class="sxs-lookup"><span data-stu-id="a32c9-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="a32c9-136">Použití proměnných prostředí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a32c9-136">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a32c9-137">Pokud použijete vlastní předpona (například `PREFIX_` s `.AddEnvironmentVariables(prefix: "PREFIX_")`), předpona, která je odstraněna, když jsou načteny páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-137">If a custom prefix is used (for example, `PREFIX_` with `.AddEnvironmentVariables(prefix: "PREFIX_")`), the prefix is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="a32c9-138">Argumenty příkazového řádku pomocí [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a32c9-138">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="a32c9-139">Poskytovatelé konfigurace jsou vysvětleny dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-139">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="a32c9-140">Další informace o hostiteli a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, naleznete v tématu <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-140">For more information on the host and <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="a32c9-141">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="a32c9-141">Security</span></span>

<span data-ttu-id="a32c9-142">Použijte následující osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="a32c9-142">Adopt the following best practices:</span></span>

* <span data-ttu-id="a32c9-143">Nikdy ukládat hesla a jiná citlivá data v konfiguraci zprostředkovatele kódu nebo v konfiguračních souborech na prostý text.</span><span class="sxs-lookup"><span data-stu-id="a32c9-143">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="a32c9-144">Nechcete používat produkční tajných kódů při vývoji nebo testovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="a32c9-144">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="a32c9-145">Zadejte tajných kódů mimo projekt tak, že nemohou být omylem zaměřuje na úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-145">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="a32c9-146">Další informace o [jak používat více prostředí](xref:fundamentals/environments) a správu [bezpečné ukládání tajných kódů aplikace při vývoji pomocí manažera tajných](xref:security/app-secrets) (včetně poradit se správným určením použití proměnných k ukládání citlivá data).</span><span class="sxs-lookup"><span data-stu-id="a32c9-146">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="a32c9-147">Správce tajný klíč používá zprostředkovatel konfigurace souboru ukládat tajné klíče uživatelů v souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="a32c9-147">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="a32c9-148">Zprostředkovatel konfigurace souboru je popsána dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-148">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="a32c9-149">[Služba Azure Key Vault](https://azure.microsoft.com/services/key-vault/) je jednou z možností pro bezpečné ukládání tajných klíčů aplikací.</span><span class="sxs-lookup"><span data-stu-id="a32c9-149">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="a32c9-150">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-150">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="a32c9-151">Hierarchické konfigurační data</span><span class="sxs-lookup"><span data-stu-id="a32c9-151">Hierarchical configuration data</span></span>

<span data-ttu-id="a32c9-152">Konfigurační rozhraní API je schopni zachovat hierarchické konfigurační data linearizovat hierarchických dat s použitím oddělovače v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="a32c9-152">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="a32c9-153">V následujícím souboru JSON existují čtyři kódy v hierarchii strukturovaných ze dvou částí:</span><span class="sxs-lookup"><span data-stu-id="a32c9-153">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="a32c9-154">Pokud je soubor pro čtení do konfigurace, jedinečné klíče se vytvoří zachovat původní struktury hierarchická data zdroj konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-154">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="a32c9-155">Oddíly a klíče se sloučí s použitím dvojtečkou (`:`) Chcete-li zachovat původní struktury:</span><span class="sxs-lookup"><span data-stu-id="a32c9-155">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="a32c9-156">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-156">section0:key0</span></span>
* <span data-ttu-id="a32c9-157">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-157">section0:key1</span></span>
* <span data-ttu-id="a32c9-158">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-158">section1:key0</span></span>
* <span data-ttu-id="a32c9-159">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-159">section1:key1</span></span>

<span data-ttu-id="a32c9-160"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody jsou k dispozici k izolaci oddíly a podřízené objekty daného oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="a32c9-160"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="a32c9-161">Tyto metody jsou popsané dále v [GetSection GetChildren – a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="a32c9-161">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="a32c9-162">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-162">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="a32c9-163">Konvence</span><span class="sxs-lookup"><span data-stu-id="a32c9-163">Conventions</span></span>

<span data-ttu-id="a32c9-164">Konfigurace zdroje jsou při spuštění aplikace pro čtení v pořadí, že jsou uvedeny příslušné poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-164">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="a32c9-165">Poskytovatelé konfigurace souboru mají možnost znovu načíst konfiguraci je podkladový soubor nastavení se při změně po spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-165">File Configuration Providers have the ability to reload configuration when an underlying settings file is changed after app startup.</span></span> <span data-ttu-id="a32c9-166">Zprostředkovatel konfigurace souboru je popsána dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-166">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="a32c9-167"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v aplikaci prvku [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="a32c9-167"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a32c9-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> mohou být vloženy do stránky Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> získat konfiguraci pro třídu:</span><span class="sxs-lookup"><span data-stu-id="a32c9-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
// using Microsoft.Extensions.Configuration;

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

<span data-ttu-id="a32c9-169">Poskytovatelé konfigurace nemůže využít DI, protože není k dispozici při jejich nastavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="a32c9-169">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="a32c9-170">Konfigurační klíče použijte následující konvence:</span><span class="sxs-lookup"><span data-stu-id="a32c9-170">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="a32c9-171">Klíče jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="a32c9-171">Keys are case-insensitive.</span></span> <span data-ttu-id="a32c9-172">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="a32c9-172">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="a32c9-173">Pokud poskytovateli stejné nebo různé konfigurace je nastavena hodnota pro stejný klíč, poslední hodnotu nastavit na klíč je hodnota.</span><span class="sxs-lookup"><span data-stu-id="a32c9-173">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="a32c9-174">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="a32c9-174">Hierarchical keys</span></span>
  * <span data-ttu-id="a32c9-175">V rámci rozhraní API pro konfiguraci, oddělovač dvojtečka (`:`) funguje na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="a32c9-175">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="a32c9-176">V seznamu proměnných prostředí oddělovač dvojtečka nemusí fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="a32c9-176">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="a32c9-177">Dvojitým podtržítkem (`__`) podporuje všechny platformy a je převedena na dvojtečkou.</span><span class="sxs-lookup"><span data-stu-id="a32c9-177">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="a32c9-178">Ve službě Azure Key Vault, hierarchické klíče, použijte `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="a32c9-178">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a32c9-179">Je nutné zadat kód pomlček nahraďte dvojtečkou tajné klíče jsou načtena do konfigurace vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-179">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="a32c9-180"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Podporuje pole vazby na objekty pomocí indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="a32c9-180">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a32c9-181">Pole vazby je popsána v [svázat pole třídy](#bind-an-array-to-a-class) oddílu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-181">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="a32c9-182">Hodnoty konfigurace přijmout následující konvence:</span><span class="sxs-lookup"><span data-stu-id="a32c9-182">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="a32c9-183">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="a32c9-183">Values are strings.</span></span>
* <span data-ttu-id="a32c9-184">Hodnoty Null nelze uložit v konfiguraci nebo vázány s objekty.</span><span class="sxs-lookup"><span data-stu-id="a32c9-184">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="a32c9-185">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="a32c9-185">Providers</span></span>

<span data-ttu-id="a32c9-186">V následující tabulce jsou uvedeny poskytovatelé konfigurace k dispozici pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a32c9-186">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="a32c9-187">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="a32c9-187">Provider</span></span> | <span data-ttu-id="a32c9-188">Obsahuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="a32c9-188">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="a32c9-189">[Azure zprostředkovatel konfigurace trezoru klíčů](xref:security/key-vault-configuration) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="a32c9-189">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="a32c9-190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a32c9-190">Azure Key Vault</span></span> |
| [<span data-ttu-id="a32c9-191">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a32c9-191">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="a32c9-192">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a32c9-192">Command-line parameters</span></span> |
| [<span data-ttu-id="a32c9-193">Vlastního poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="a32c9-193">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="a32c9-194">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="a32c9-194">Custom source</span></span> |
| [<span data-ttu-id="a32c9-195">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="a32c9-195">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="a32c9-196">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a32c9-196">Environment variables</span></span> |
| [<span data-ttu-id="a32c9-197">Zprostředkovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="a32c9-197">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="a32c9-198">Soubory INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="a32c9-198">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="a32c9-199">Poskytovatel konfigurace pro každý soubor klíče</span><span class="sxs-lookup"><span data-stu-id="a32c9-199">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="a32c9-200">Adresář souborů</span><span class="sxs-lookup"><span data-stu-id="a32c9-200">Directory files</span></span> |
| [<span data-ttu-id="a32c9-201">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="a32c9-201">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="a32c9-202">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="a32c9-202">In-memory collections</span></span> |
| <span data-ttu-id="a32c9-203">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (*zabezpečení* témata)</span><span class="sxs-lookup"><span data-stu-id="a32c9-203">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="a32c9-204">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="a32c9-204">File in the user profile directory</span></span> |

<span data-ttu-id="a32c9-205">Konfigurace zdrojů se čtení v pořadí, že jejich poskytovatelé konfigurace jsou zadány při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a32c9-205">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="a32c9-206">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, ne v pořadí, že váš kód může uspořádat je.</span><span class="sxs-lookup"><span data-stu-id="a32c9-206">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="a32c9-207">Pořadí poskytovatelé konfigurace ve vašem kódu tak, aby vyhovoval vašim prioritám pro podkladové zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-207">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="a32c9-208">Typická posloupnost poskytovatelé konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="a32c9-208">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="a32c9-209">Soubory (*appsettings.json*, *appsettings. { Prostředí} .json*, kde `{Environment}` je aktuálním prostředí hostování aplikace)</span><span class="sxs-lookup"><span data-stu-id="a32c9-209">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="a32c9-210">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a32c9-210">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="a32c9-211">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí jenom)</span><span class="sxs-lookup"><span data-stu-id="a32c9-211">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="a32c9-212">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a32c9-212">Environment variables</span></span>
1. <span data-ttu-id="a32c9-213">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a32c9-213">Command-line arguments</span></span>

<span data-ttu-id="a32c9-214">Je obvyklé na poslední pozici poskytovatele konfigurace příkazového řádku v sérii poskytovatele, jak povolit argumenty příkazového řádku k přepsání konfigurace nastavená poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="a32c9-214">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="a32c9-215">Toto pořadí poskytovatelů přejde do místa při inicializaci nové <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-215">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="a32c9-216">Další informace najdete v tématu [webového hostitele: Nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="a32c9-216">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

## <a name="configureappconfiguration"></a><span data-ttu-id="a32c9-217">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="a32c9-217">ConfigureAppConfiguration</span></span>

<span data-ttu-id="a32c9-218">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby zadejte poskytovatele konfigurace aplikace kromě těch, přidání automaticky <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="a32c9-218">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

<span data-ttu-id="a32c9-219">Konfigurace zadaný pro aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici při spuštění aplikace, včetně `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-219">Configuration supplied to the app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a32c9-220">Další informace najdete v tématu [konfigurace přístupu při spuštění](#access-configuration-during-startup) oddílu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-220">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="a32c9-221">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a32c9-221">Command-line Configuration Provider</span></span>

<span data-ttu-id="a32c9-222"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Načte konfiguraci z páry klíč hodnota pro argument příkazového řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-222">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="a32c9-223">K aktivaci příkazového řádku konfigurace <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> rozšiřující metoda je volána v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-223">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a32c9-224">`AddCommandLine` je automaticky volána, když inicializujete novou <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-224">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="a32c9-225">Další informace najdete v tématu [webového hostitele: Nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="a32c9-225">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="a32c9-226">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="a32c9-226">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a32c9-227">Volitelná konfigurace z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="a32c9-227">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="a32c9-228">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="a32c9-228">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="a32c9-229">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a32c9-229">Environment variables.</span></span>

<span data-ttu-id="a32c9-230">`CreateDefaultBuilder` poslední přidá poskytovatele konfigurace příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a32c9-230">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="a32c9-231">Argumenty příkazového řádku předané za běhu přepsat nastavení ostatní poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-231">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="a32c9-232">`CreateDefaultBuilder` funguje, když je vytvořen hostiteli.</span><span class="sxs-lookup"><span data-stu-id="a32c9-232">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="a32c9-233">Proto příkazového řádku konfigurace aktivoval `CreateDefaultBuilder` může mít vliv na konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="a32c9-233">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="a32c9-234">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-234">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="a32c9-235">`AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-235">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a32c9-236">Pokud je potřeba zadat konfiguraci aplikace a stále mít možnost přepsat tuto konfiguraci s argumenty příkazového řádku, volání další poskytovatele aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> a volat `AddCommandLine` poslední.</span><span class="sxs-lookup"><span data-stu-id="a32c9-236">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

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

<span data-ttu-id="a32c9-237">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="a32c9-237">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="a32c9-238">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a32c9-238">**Example**</span></span>

<span data-ttu-id="a32c9-239">Ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-239">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="a32c9-240">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-240">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="a32c9-241">Zadat argument příkazového řádku k `dotnet run` příkazu `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-241">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="a32c9-242">Jakmile je aplikace spuštěná, otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-242">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a32c9-243">Podívejte se, že výstup obsahuje pár klíč hodnota pro argument příkazového řádku konfigurace poskytnuté `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-243">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="a32c9-244">Arguments</span><span class="sxs-lookup"><span data-stu-id="a32c9-244">Arguments</span></span>

<span data-ttu-id="a32c9-245">Hodnota musí následovat znak rovná se (`=`), nebo klíč musí mít předponu (`--` nebo `/`) když hodnota má následující formát mezerou.</span><span class="sxs-lookup"><span data-stu-id="a32c9-245">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="a32c9-246">Hodnota může mít hodnotu null, pokud se používá znak rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="a32c9-246">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="a32c9-247">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="a32c9-247">Key prefix</span></span>               | <span data-ttu-id="a32c9-248">Příklad</span><span class="sxs-lookup"><span data-stu-id="a32c9-248">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="a32c9-249">Žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="a32c9-249">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="a32c9-250">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="a32c9-250">Two dashes (`--`)</span></span>        | <span data-ttu-id="a32c9-251">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="a32c9-251">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="a32c9-252">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="a32c9-252">Forward slash (`/`)</span></span>      | <span data-ttu-id="a32c9-253">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="a32c9-253">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="a32c9-254">V rámci stejného příkazu Nekombinujte argument příkazového řádku páry klíč hodnota, které používají znaménko rovná se s páry klíč hodnota, které používají mezerou.</span><span class="sxs-lookup"><span data-stu-id="a32c9-254">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="a32c9-255">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="a32c9-255">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="a32c9-256">Přepnout mapování</span><span class="sxs-lookup"><span data-stu-id="a32c9-256">Switch mappings</span></span>

<span data-ttu-id="a32c9-257">Mapování přepínači povolit název klíče pro náhradní logiku.</span><span class="sxs-lookup"><span data-stu-id="a32c9-257">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="a32c9-258">Při ruční sestavení konfigurace s <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, může poskytnout slovník nahrazení přepínači <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metoda.</span><span class="sxs-lookup"><span data-stu-id="a32c9-258">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="a32c9-259">Při použití přepínače slovníku mapování slovníku se kontroluje u klíč, který odpovídá key určeného tímto argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a32c9-259">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="a32c9-260">Pokud je nalezen příkazového řádku klíč ve slovníku, hodnota slovníku (náhradní klíč) se předá zpět do nastavit pár klíč hodnota v konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-260">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="a32c9-261">Mapování přepínač je vyžadován pro libovolného příkazového řádku klíče předponu jeden pomlčka (`-`).</span><span class="sxs-lookup"><span data-stu-id="a32c9-261">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="a32c9-262">Přepnout mapování slovníku klíčových pravidel:</span><span class="sxs-lookup"><span data-stu-id="a32c9-262">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="a32c9-263">Přepínače musí začínat pomlčkou (`-`) nebo dvojitou pomlčka (`--`).</span><span class="sxs-lookup"><span data-stu-id="a32c9-263">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="a32c9-264">Slovník mapování přepínač nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="a32c9-264">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="a32c9-265">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="a32c9-265">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="a32c9-266">Jak je znázorněno v předchozím příkladu volání `CreateDefaultBuilder` by neměla předat argumenty při mapování přepínače se používají.</span><span class="sxs-lookup"><span data-stu-id="a32c9-266">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="a32c9-267">`CreateDefaultBuilder` metody `AddCommandLine` volání neobsahuje mapované přepínače a neexistuje žádný způsob předat slovníku mapování přepínač k `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-267">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a32c9-268">Pokud argumenty obsahují mapované přepínače a jsou předány `CreateDefaultBuilder`, jeho `AddCommandLine` poskytovatele dojde k selhání inicializace s <xref:System.FormatException>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-268">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="a32c9-269">Řešení není předat argumenty, které mají `CreateDefaultBuilder` , ale místo toho povolit `ConfigurationBuilder` metody `AddCommandLine` metodu ke zpracování argumentů a slovníku mapování přepínače.</span><span class="sxs-lookup"><span data-stu-id="a32c9-269">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="a32c9-270">Po vytvoření slovníku mapování přepínač obsahuje data zobrazená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a32c9-270">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="a32c9-271">Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-271">Key</span></span>       | <span data-ttu-id="a32c9-272">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-272">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="a32c9-273">Pokud při spuštění aplikace se používají klíče mapované na přepínač, konfigurace přijímá konfigurační hodnoty klíče zadané ve slovníku:</span><span class="sxs-lookup"><span data-stu-id="a32c9-273">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="a32c9-274">Po spuštění předchozího příkazu se konfigurace obsahuje hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a32c9-274">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="a32c9-275">Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-275">Key</span></span>               | <span data-ttu-id="a32c9-276">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-276">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="a32c9-277">Zprostředkovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="a32c9-277">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="a32c9-278"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Načte konfiguraci z prostředí proměnné páry klíč hodnota v době běhu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-278">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="a32c9-279">Chcete-li aktivovat konfigurace proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-279">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a32c9-280">Při práci s hierarchické klíče v seznamu proměnných prostředí, oddělovač dvojtečka (`:`) nemusí fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="a32c9-280">When working with hierarchical keys in environment variables, a colon separator (`:`) may not work on all platforms.</span></span> <span data-ttu-id="a32c9-281">Dvojitým podtržítkem (`__`) podporuje všechny platformy a nahrazuje dvojtečkou.</span><span class="sxs-lookup"><span data-stu-id="a32c9-281">A double underscore (`__`) is supported by all platforms and is replaced by a colon.</span></span>

<span data-ttu-id="a32c9-282">[Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavit proměnné prostředí na webu Azure Portal, můžete přepsat konfiguraci aplikace pomocí zprostředkovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="a32c9-282">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="a32c9-283">Další informace najdete v tématu [aplikace Azure: Přepsat konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="a32c9-283">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="a32c9-284">`AddEnvironmentVariables` je automaticky volána pro proměnné prostředí s předponou `ASPNETCORE_` při inicializaci nové <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-284">`AddEnvironmentVariables` is automatically called for environment variables prefixed with `ASPNETCORE_` when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="a32c9-285">Další informace najdete v tématu [webového hostitele: Nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="a32c9-285">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="a32c9-286">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="a32c9-286">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a32c9-287">Konfigurace aplikace z proměnných prostředí unprefixed voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="a32c9-287">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="a32c9-288">Volitelná konfigurace z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="a32c9-288">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="a32c9-289">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="a32c9-289">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="a32c9-290">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a32c9-290">Command-line arguments.</span></span>

<span data-ttu-id="a32c9-291">Zprostředkovatel konfigurace proměnných prostředí je volána po navázání konfigurace z tajných kódů uživatelů a *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="a32c9-291">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="a32c9-292">Volání zprostředkovatele na této pozici umožňuje proměnné prostředí načteny za běhu přepsat konfiguraci nastavil tajných kódů uživatelů a *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="a32c9-292">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="a32c9-293">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-293">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="a32c9-294">Pokud je potřeba zadat konfigurace aplikace z další proměnné prostředí, volání další poskytovatele aplikace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> a volat `AddEnvironmentVariables` s předponou.</span><span class="sxs-lookup"><span data-stu-id="a32c9-294">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

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

<span data-ttu-id="a32c9-295">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="a32c9-295">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="a32c9-296">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a32c9-296">**Example**</span></span>

<span data-ttu-id="a32c9-297">Ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-297">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="a32c9-298">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-298">Run the sample app.</span></span> <span data-ttu-id="a32c9-299">Otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-299">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a32c9-300">Podívejte se, že výstup obsahuje pár klíč hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-300">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="a32c9-301">Hodnota odpovídá prostředí, ve kterém je aplikace spuštěna, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="a32c9-301">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="a32c9-302">Udržovat seznam proměnných prostředí vykreslen metodou aplikace krátký, filtry aplikace proměnných prostředí až po ty spustit následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="a32c9-302">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="a32c9-303">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="a32c9-303">ASPNETCORE_</span></span>
* <span data-ttu-id="a32c9-304">adresy URL</span><span class="sxs-lookup"><span data-stu-id="a32c9-304">urls</span></span>
* <span data-ttu-id="a32c9-305">Protokolování</span><span class="sxs-lookup"><span data-stu-id="a32c9-305">Logging</span></span>
* <span data-ttu-id="a32c9-306">PROSTŘEDÍ</span><span class="sxs-lookup"><span data-stu-id="a32c9-306">ENVIRONMENT</span></span>
* <span data-ttu-id="a32c9-307">contentRoot</span><span class="sxs-lookup"><span data-stu-id="a32c9-307">contentRoot</span></span>
* <span data-ttu-id="a32c9-308">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="a32c9-308">AllowedHosts</span></span>
* <span data-ttu-id="a32c9-309">applicationName</span><span class="sxs-lookup"><span data-stu-id="a32c9-309">applicationName</span></span>
* <span data-ttu-id="a32c9-310">příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="a32c9-310">CommandLine</span></span>

<span data-ttu-id="a32c9-311">Pokud chcete zpřístupnit všechny proměnné prostředí k dispozici pro aplikace, změnit `FilteredConfiguration` v *Pages/Index.cshtml.cs* takto:</span><span class="sxs-lookup"><span data-stu-id="a32c9-311">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="a32c9-312">Předpony adres</span><span class="sxs-lookup"><span data-stu-id="a32c9-312">Prefixes</span></span>

<span data-ttu-id="a32c9-313">Proměnné prostředí načteny do konfiguraci aplikace, jsou filtrovány, když zadáte předponu `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="a32c9-313">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="a32c9-314">Například k proměnným prostředí filtr na této předponě `CUSTOM_`, zadat předponu pro zprostředkovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a32c9-314">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="a32c9-315">Předpona, která se odstraní při vytváření konfigurace páry klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="a32c9-315">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="a32c9-316">Metoda statické pohodlí `CreateDefaultBuilder` vytvoří <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> stanovit hostiteli aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-316">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="a32c9-317">Když `WebHostBuilder` je vytvořen, zjistí jeho konfigurace hostitele v proměnných prostředí s předponou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-317">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

<span data-ttu-id="a32c9-318">**Připojovací řetězec předpony**</span><span class="sxs-lookup"><span data-stu-id="a32c9-318">**Connection string prefixes**</span></span>

<span data-ttu-id="a32c9-319">Rozhraní API konfigurace má zvláštní zpracování pravidel pro čtyři připojovací řetězec proměnné prostředí používané při konfiguraci Azure připojovací řetězce pro prostředí app.</span><span class="sxs-lookup"><span data-stu-id="a32c9-319">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="a32c9-320">Proměnné prostředí s předponami uvedené v tabulce se načtou do aplikace, pokud není zadána žádná předpona k `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-320">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="a32c9-321">Předpona řetězce připojení</span><span class="sxs-lookup"><span data-stu-id="a32c9-321">Connection string prefix</span></span> | <span data-ttu-id="a32c9-322">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="a32c9-322">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="a32c9-323">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="a32c9-323">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="a32c9-324">MySQL</span><span class="sxs-lookup"><span data-stu-id="a32c9-324">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="a32c9-325">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="a32c9-325">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="a32c9-326">SQL Server</span><span class="sxs-lookup"><span data-stu-id="a32c9-326">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="a32c9-327">Proměnné prostředí je při zjištění a načtena do konfigurace s žádným z čtyři předpony, které jsou uvedené v tabulce:</span><span class="sxs-lookup"><span data-stu-id="a32c9-327">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="a32c9-328">Konfigurační klíč je vytvořen odebráním předponu proměnné prostředí a přidání konfiguračního klíče oddílu (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="a32c9-328">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="a32c9-329">Je vytvořen nový pár klíč hodnota konfigurace, který představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá stanoveného zprostředkovatele).</span><span class="sxs-lookup"><span data-stu-id="a32c9-329">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="a32c9-330">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a32c9-330">Environment variable key</span></span> | <span data-ttu-id="a32c9-331">Převedený konfigurační klíč</span><span class="sxs-lookup"><span data-stu-id="a32c9-331">Converted configuration key</span></span> | <span data-ttu-id="a32c9-332">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="a32c9-332">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="a32c9-333">Položky konfigurace nebyl vytvořen.</span><span class="sxs-lookup"><span data-stu-id="a32c9-333">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="a32c9-334">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a32c9-334">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="a32c9-335">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="a32c9-335">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="a32c9-336">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a32c9-336">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="a32c9-337">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a32c9-337">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="a32c9-338">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a32c9-338">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="a32c9-339">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a32c9-339">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="a32c9-340">Zprostředkovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="a32c9-340">File Configuration Provider</span></span>

<span data-ttu-id="a32c9-341"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třída pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="a32c9-341"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="a32c9-342">Následující zprostředkovatele konfigurace jsou vyhrazené pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="a32c9-342">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="a32c9-343">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="a32c9-343">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="a32c9-344">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="a32c9-344">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="a32c9-345">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="a32c9-345">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="a32c9-346">Zprostředkovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="a32c9-346">INI Configuration Provider</span></span>

<span data-ttu-id="a32c9-347"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Načte konfiguraci z páry klíč hodnota soubor INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-347">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="a32c9-348">Chcete-li aktivovat konfigurační soubor INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-348">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a32c9-349">Dvojtečka je možné k jako oddělovač oddílu v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="a32c9-349">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="a32c9-350">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="a32c9-350">Overloads permit specifying:</span></span>

* <span data-ttu-id="a32c9-351">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="a32c9-351">Whether the file is optional.</span></span>
* <span data-ttu-id="a32c9-352">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="a32c9-352">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a32c9-353"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a32c9-353">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a32c9-354">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="a32c9-354">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="a32c9-355">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-355">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="a32c9-356">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-356">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-357">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="a32c9-357">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="a32c9-358">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-358">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="a32c9-359">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-359">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-360">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="a32c9-360">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="a32c9-361">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="a32c9-361">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a32c9-362">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-362">section0:key0</span></span>
* <span data-ttu-id="a32c9-363">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-363">section0:key1</span></span>
* <span data-ttu-id="a32c9-364">section1:subsection:Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-364">section1:subsection:key</span></span>
* <span data-ttu-id="a32c9-365">section2:subsection0:Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-365">section2:subsection0:key</span></span>
* <span data-ttu-id="a32c9-366">section2:subsection1:Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-366">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="a32c9-367">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="a32c9-367">JSON Configuration Provider</span></span>

<span data-ttu-id="a32c9-368"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Načte konfiguraci z páry klíč hodnota JSON soubor za běhu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-368">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="a32c9-369">Chcete-li aktivovat konfigurační soubor JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-369">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a32c9-370">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="a32c9-370">Overloads permit specifying:</span></span>

* <span data-ttu-id="a32c9-371">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="a32c9-371">Whether the file is optional.</span></span>
* <span data-ttu-id="a32c9-372">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="a32c9-372">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a32c9-373"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a32c9-373">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a32c9-374">`AddJsonFile` je automaticky volána dvakrát při inicializaci nové <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> s <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-374">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="a32c9-375">Načtení konfigurace z volání metody:</span><span class="sxs-lookup"><span data-stu-id="a32c9-375">The method is called to load configuration from:</span></span>

* <span data-ttu-id="a32c9-376">*appSettings.JSON* &ndash; tento soubor je nejprve pro čtení.</span><span class="sxs-lookup"><span data-stu-id="a32c9-376">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="a32c9-377">Prostředí verze souboru mohou přepsat hodnoty poskytnuté *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a32c9-377">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="a32c9-378">*appSettings. {Prostředí} .json* &ndash; načtení na základě prostředí verze souboru [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="a32c9-378">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="a32c9-379">Další informace najdete v tématu [webového hostitele: Nastavení hostitele](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="a32c9-379">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="a32c9-380">`CreateDefaultBuilder` také načítání:</span><span class="sxs-lookup"><span data-stu-id="a32c9-380">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a32c9-381">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a32c9-381">Environment variables.</span></span>
* <span data-ttu-id="a32c9-382">[Tajné klíče uživatelů (tajný klíč správce)](xref:security/app-secrets) (ve vývojovém prostředí).</span><span class="sxs-lookup"><span data-stu-id="a32c9-382">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="a32c9-383">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a32c9-383">Command-line arguments.</span></span>

<span data-ttu-id="a32c9-384">Zprostředkovatel konfigurace JSON je nejprve navázat.</span><span class="sxs-lookup"><span data-stu-id="a32c9-384">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="a32c9-385">Proto tajné klíče uživatelů, proměnné a argumenty příkazového řádku přepsat nastavením konfigurace *appsettings* soubory.</span><span class="sxs-lookup"><span data-stu-id="a32c9-385">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="a32c9-386">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele k určení konfigurace aplikace pro soubory jiné než *appsettings.json* a *appsettings. { Prostředí} .json*:</span><span class="sxs-lookup"><span data-stu-id="a32c9-386">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

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

<span data-ttu-id="a32c9-387">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-387">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="a32c9-388">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-388">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-389">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="a32c9-389">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="a32c9-390">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-390">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="a32c9-391">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-391">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-392">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a32c9-392">**Example**</span></span>

<span data-ttu-id="a32c9-393">Ukázková aplikace využívá metodu statické pohodlí `CreateDefaultBuilder` k sestavení hostitele, která zahrnuje dvě volání `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-393">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="a32c9-394">Konfigurace je načtena z *appsettings.json* a *appsettings. { Prostředí} .json*.</span><span class="sxs-lookup"><span data-stu-id="a32c9-394">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="a32c9-395">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-395">Run the sample app.</span></span> <span data-ttu-id="a32c9-396">Otevřete prohlížeč na aplikaci na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-396">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a32c9-397">Podívejte se, že výstup obsahuje páry klíč hodnota u konfigurace uvedené v tabulce v závislosti na prostředí.</span><span class="sxs-lookup"><span data-stu-id="a32c9-397">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="a32c9-398">Protokolování konfigurační klíče pomocí dvojtečky (`:`) jako oddělovačem hierarchický.</span><span class="sxs-lookup"><span data-stu-id="a32c9-398">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="a32c9-399">Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-399">Key</span></span>                        | <span data-ttu-id="a32c9-400">Hodnota vývoj</span><span class="sxs-lookup"><span data-stu-id="a32c9-400">Development Value</span></span> | <span data-ttu-id="a32c9-401">Hodnota produkce</span><span class="sxs-lookup"><span data-stu-id="a32c9-401">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="a32c9-402">Protokolování: LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="a32c9-402">Logging:LogLevel:System</span></span>    | <span data-ttu-id="a32c9-403">Informace o</span><span class="sxs-lookup"><span data-stu-id="a32c9-403">Information</span></span>       | <span data-ttu-id="a32c9-404">Informace o</span><span class="sxs-lookup"><span data-stu-id="a32c9-404">Information</span></span>      |
| <span data-ttu-id="a32c9-405">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="a32c9-405">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="a32c9-406">Informace o</span><span class="sxs-lookup"><span data-stu-id="a32c9-406">Information</span></span>       | <span data-ttu-id="a32c9-407">Informace o</span><span class="sxs-lookup"><span data-stu-id="a32c9-407">Information</span></span>      |
| <span data-ttu-id="a32c9-408">Protokolování: LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="a32c9-408">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="a32c9-409">Ladit</span><span class="sxs-lookup"><span data-stu-id="a32c9-409">Debug</span></span>             | <span data-ttu-id="a32c9-410">Chyba</span><span class="sxs-lookup"><span data-stu-id="a32c9-410">Error</span></span>            |
| <span data-ttu-id="a32c9-411">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="a32c9-411">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="a32c9-412">Zprostředkovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="a32c9-412">XML Configuration Provider</span></span>

<span data-ttu-id="a32c9-413"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Načte konfiguraci z dvojice klíč hodnota XML soubor za běhu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-413">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="a32c9-414">Chcete-li aktivovat konfigurační soubor XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-414">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a32c9-415">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="a32c9-415">Overloads permit specifying:</span></span>

* <span data-ttu-id="a32c9-416">Určuje, zda soubor je volitelné.</span><span class="sxs-lookup"><span data-stu-id="a32c9-416">Whether the file is optional.</span></span>
* <span data-ttu-id="a32c9-417">Určuje, zda konfigurace opětovném načtení nástroje Pokud se soubor změní.</span><span class="sxs-lookup"><span data-stu-id="a32c9-417">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a32c9-418"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a32c9-418">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a32c9-419">Kořenový uzel konfiguračního souboru se ignoruje při vytváření konfigurace páry klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="a32c9-419">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="a32c9-420">V souboru zadejte dokumentu typ definice (DTD) nebo obor názvů.</span><span class="sxs-lookup"><span data-stu-id="a32c9-420">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="a32c9-421">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="a32c9-421">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="a32c9-422">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-422">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="a32c9-423">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-423">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-424">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="a32c9-424">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

<span data-ttu-id="a32c9-425">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-425">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="a32c9-426">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-426">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-427">Soubory XML konfigurace můžete použít názvy různých elementů pro opakující se části:</span><span class="sxs-lookup"><span data-stu-id="a32c9-427">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="a32c9-428">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="a32c9-428">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a32c9-429">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-429">section0:key0</span></span>
* <span data-ttu-id="a32c9-430">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-430">section0:key1</span></span>
* <span data-ttu-id="a32c9-431">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-431">section1:key0</span></span>
* <span data-ttu-id="a32c9-432">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-432">section1:key1</span></span>

<span data-ttu-id="a32c9-433">Opakující se elementy, které používají stejný název elementu fungovat, pokud `name` atribut se používá k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="a32c9-433">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="a32c9-434">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="a32c9-434">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a32c9-435">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-435">section:section0:key:key0</span></span>
* <span data-ttu-id="a32c9-436">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-436">section:section0:key:key1</span></span>
* <span data-ttu-id="a32c9-437">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-437">section:section1:key:key0</span></span>
* <span data-ttu-id="a32c9-438">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-438">section:section1:key:key1</span></span>

<span data-ttu-id="a32c9-439">Atributy lze použít k zadání hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a32c9-439">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="a32c9-440">Předchozí konfigurační soubor načte následujících klíčů s `value`:</span><span class="sxs-lookup"><span data-stu-id="a32c9-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a32c9-441">atribut Key:</span><span class="sxs-lookup"><span data-stu-id="a32c9-441">key:attribute</span></span>
* <span data-ttu-id="a32c9-442">část: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="a32c9-442">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="a32c9-443">Poskytovatel konfigurace pro každý soubor klíče</span><span class="sxs-lookup"><span data-stu-id="a32c9-443">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="a32c9-444"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Používá souborů v adresáři jako páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-444">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="a32c9-445">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="a32c9-445">The key is the file name.</span></span> <span data-ttu-id="a32c9-446">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="a32c9-446">The value contains the file's contents.</span></span> <span data-ttu-id="a32c9-447">Konfiguraci poskytovatele za soubor klíče se používá ve scénářích hostování Dockeru.</span><span class="sxs-lookup"><span data-stu-id="a32c9-447">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="a32c9-448">Chcete-li aktivovat konfigurace na soubor klíče, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-448">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="a32c9-449">`directoryPath` k souborům musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="a32c9-449">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="a32c9-450">Přetížení povolit zadáním:</span><span class="sxs-lookup"><span data-stu-id="a32c9-450">Overloads permit specifying:</span></span>

* <span data-ttu-id="a32c9-451">`Action<KeyPerFileConfigurationSource>` Delegáta, který nastaví jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="a32c9-451">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="a32c9-452">Určuje, zda daný adresář je volitelné a cestu k adresáři.</span><span class="sxs-lookup"><span data-stu-id="a32c9-452">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="a32c9-453">Double – podtržítko (`__`) se používá jako oddělovač klíčových konfigurace v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="a32c9-453">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="a32c9-454">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-454">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="a32c9-455">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="a32c9-455">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="a32c9-456">Nastavení základní cesty s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-456">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="a32c9-457">`SetBasePath` Probíhá [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-457">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-458">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="a32c9-458">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

## <a name="memory-configuration-provider"></a><span data-ttu-id="a32c9-459">Zprostředkovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="a32c9-459">Memory Configuration Provider</span></span>

<span data-ttu-id="a32c9-460"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Používá kolekci v paměti jako páry klíč hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="a32c9-461">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> rozšiřující metody na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a32c9-462">Poskytovatel konfigurace mohou být inicializovány pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="a32c9-463">Volání <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> při vytváření hostitele tak, aby konfiguraci aplikace, zadejte:</span><span class="sxs-lookup"><span data-stu-id="a32c9-463">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

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

<span data-ttu-id="a32c9-464">Při vytváření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> volat přímo, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> s konfigurací:</span><span class="sxs-lookup"><span data-stu-id="a32c9-464">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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

## <a name="getvalue"></a><span data-ttu-id="a32c9-465">GetValue</span><span class="sxs-lookup"><span data-stu-id="a32c9-465">GetValue</span></span>

<span data-ttu-id="a32c9-466">[ConfigurationBinder.GetValue&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje hodnotu z konfigurace se zadaným klíčem a převede ho na zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="a32c9-466">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="a32c9-467">Přetížení umožňuje zadat výchozí hodnotu, pokud není nalezen klíč.</span><span class="sxs-lookup"><span data-stu-id="a32c9-467">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="a32c9-468">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a32c9-468">The following example:</span></span>

* <span data-ttu-id="a32c9-469">Extrahuje hodnotu řetězce s klíčem z konfigurace `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-469">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="a32c9-470">Pokud `NumberKey` nebyl nalezen v konfigurační klíče, výchozí hodnota `99` se používá.</span><span class="sxs-lookup"><span data-stu-id="a32c9-470">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="a32c9-471">Typy hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-471">Types the value as an `int`.</span></span>
* <span data-ttu-id="a32c9-472">Uloží hodnotu v `NumberConfig` vlastnost pro použití na stránce.</span><span class="sxs-lookup"><span data-stu-id="a32c9-472">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
// using Microsoft.Extensions.Configuration;

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="a32c9-473">GetSection, GetChildren – a existuje</span><span class="sxs-lookup"><span data-stu-id="a32c9-473">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="a32c9-474">Příklady, které následují vezměte v úvahu následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="a32c9-474">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="a32c9-475">Čtyři kódy se nacházejí ve dvou částech, z nichž jeden obsahuje dvojice pododdíly:</span><span class="sxs-lookup"><span data-stu-id="a32c9-475">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="a32c9-476">Přečtení souboru do konfigurace následujících jedinečné klíče hierarchické jsou vytvořeny pro uchování hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a32c9-476">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="a32c9-477">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-477">section0:key0</span></span>
* <span data-ttu-id="a32c9-478">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-478">section0:key1</span></span>
* <span data-ttu-id="a32c9-479">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-479">section1:key0</span></span>
* <span data-ttu-id="a32c9-480">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-480">section1:key1</span></span>
* <span data-ttu-id="a32c9-481">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-481">section2:subsection0:key0</span></span>
* <span data-ttu-id="a32c9-482">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-482">section2:subsection0:key1</span></span>
* <span data-ttu-id="a32c9-483">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="a32c9-483">section2:subsection1:key0</span></span>
* <span data-ttu-id="a32c9-484">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="a32c9-484">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="a32c9-485">GetSection</span><span class="sxs-lookup"><span data-stu-id="a32c9-485">GetSection</span></span>

<span data-ttu-id="a32c9-486">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje dílčí část konfigurace s klíčem zadaného dílčí část.</span><span class="sxs-lookup"><span data-stu-id="a32c9-486">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="a32c9-487">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-487">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-488">Se vraťte <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč hodnota v `section1`, volání `GetSection` a zadat název oddílu:</span><span class="sxs-lookup"><span data-stu-id="a32c9-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="a32c9-489">`configSection` Nemá hodnotu, pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="a32c9-490">Podobně a získat tak hodnoty pro klíče v `section2:subsection0`, volání `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="a32c9-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="a32c9-491">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="a32c9-492">Pokud není nalezen odpovídající části, prázdná `IConfigurationSection` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="a32c9-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="a32c9-493">Když `GetSection` vrátí odpovídající části <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněn.</span><span class="sxs-lookup"><span data-stu-id="a32c9-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="a32c9-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když existuje části.</span><span class="sxs-lookup"><span data-stu-id="a32c9-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="a32c9-495">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="a32c9-495">GetChildren</span></span>

<span data-ttu-id="a32c9-496">Volání [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) na `section2` získá `IEnumerable<IConfigurationSection>` , který obsahuje:</span><span class="sxs-lookup"><span data-stu-id="a32c9-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="a32c9-497">Existuje</span><span class="sxs-lookup"><span data-stu-id="a32c9-497">Exists</span></span>

<span data-ttu-id="a32c9-498">Použití [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, jestli existuje konfigurační oddíl:</span><span class="sxs-lookup"><span data-stu-id="a32c9-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="a32c9-499">Zadaný ukázková data `sectionExists` je `false` protože není k dispozici `section2:subsection2` části v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="a32c9-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="a32c9-500">Vytvoření vazby na třídu</span><span class="sxs-lookup"><span data-stu-id="a32c9-500">Bind to a class</span></span>

<span data-ttu-id="a32c9-501">Konfigurace může být vázaný na třídy, které představující skupiny související nastavení použití *možnosti vzor*.</span><span class="sxs-lookup"><span data-stu-id="a32c9-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="a32c9-502">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="a32c9-503">Konfigurační hodnoty jsou vrácené jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objekty.</span><span class="sxs-lookup"><span data-stu-id="a32c9-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="a32c9-504">`Bind` Probíhá [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-504">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-505">Obsahuje ukázkovou aplikaci `Starship` modelu (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="a32c9-505">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="a32c9-506">`starship` Část *starship.json* soubor vytvoří konfiguraci při ukázková aplikace používá zprostředkovatele konfigurace JSON načíst konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="a32c9-506">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="a32c9-507">Následující páry klíč hodnota konfigurace jsou vytvořeny:</span><span class="sxs-lookup"><span data-stu-id="a32c9-507">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="a32c9-508">Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-508">Key</span></span>                   | <span data-ttu-id="a32c9-509">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-509">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="a32c9-510">starship: name</span><span class="sxs-lookup"><span data-stu-id="a32c9-510">starship:name</span></span>         | <span data-ttu-id="a32c9-511">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="a32c9-511">USS Enterprise</span></span>                                    |
| <span data-ttu-id="a32c9-512">starship: registru</span><span class="sxs-lookup"><span data-stu-id="a32c9-512">starship:registry</span></span>     | <span data-ttu-id="a32c9-513">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="a32c9-513">NCC-1701</span></span>                                          |
| <span data-ttu-id="a32c9-514">starship: Třída</span><span class="sxs-lookup"><span data-stu-id="a32c9-514">starship:class</span></span>        | <span data-ttu-id="a32c9-515">Vytvoření</span><span class="sxs-lookup"><span data-stu-id="a32c9-515">Constitution</span></span>                                      |
| <span data-ttu-id="a32c9-516">starship: délka</span><span class="sxs-lookup"><span data-stu-id="a32c9-516">starship:length</span></span>       | <span data-ttu-id="a32c9-517">304.8</span><span class="sxs-lookup"><span data-stu-id="a32c9-517">304.8</span></span>                                             |
| <span data-ttu-id="a32c9-518">starship: stává</span><span class="sxs-lookup"><span data-stu-id="a32c9-518">starship:commissioned</span></span> | <span data-ttu-id="a32c9-519">False</span><span class="sxs-lookup"><span data-stu-id="a32c9-519">False</span></span>                                             |
| <span data-ttu-id="a32c9-520">Ochranné známky</span><span class="sxs-lookup"><span data-stu-id="a32c9-520">trademark</span></span>             | <span data-ttu-id="a32c9-521">Paramount obrázky Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="a32c9-521">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="a32c9-522">Ukázková aplikace volání `GetSection` s `starship` klíč.</span><span class="sxs-lookup"><span data-stu-id="a32c9-522">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="a32c9-523">`starship` Páry klíč hodnota jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="a32c9-523">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="a32c9-524">`Bind` Metoda je volána v podčásti předávání v instanci `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="a32c9-524">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="a32c9-525">Po navázání hodnoty instanci, instance přidruženo k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="a32c9-525">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

<span data-ttu-id="a32c9-526">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-526">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="a32c9-527">Vytvořit vazbu grafu objektu</span><span class="sxs-lookup"><span data-stu-id="a32c9-527">Bind to an object graph</span></span>

<span data-ttu-id="a32c9-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopen vazby celého grafu objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="a32c9-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="a32c9-529">`Bind` Probíhá [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-529">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-530">Obsahuje ukázku `TvShow` modelu, jehož graf objektu obsahuje `Metadata` a `Actors` třídy (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="a32c9-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="a32c9-531">Ukázková aplikace má *tvshow.xml* soubor, který obsahuje konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="a32c9-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="a32c9-532">Konfigurace je vázán na celý `TvShow` grafu objektu s `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="a32c9-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="a32c9-533">Vázané instance je přiřazená k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="a32c9-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="a32c9-534">[ConfigurationBinder.Get&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) váže a vrátí hodnotu zadaného typu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-534">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a32c9-535">`Get<T>` je výhodnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="a32c9-536">Následující kód ukazuje, jak používat `Get<T>` z předchozího příkladu, který umožňuje vázané instance má být přímo přiřazeni k vlastnosti pro vykreslování použit:</span><span class="sxs-lookup"><span data-stu-id="a32c9-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

<span data-ttu-id="a32c9-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> Probíhá [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="a32c9-538">`Get<T>` je k dispozici v ASP.NET Core 1.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="a32c9-538">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a32c9-539">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-539">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a32c9-540">Svázat pole třídy</span><span class="sxs-lookup"><span data-stu-id="a32c9-540">Bind an array to a class</span></span>

<span data-ttu-id="a32c9-541">*Ukázková aplikace předvádí koncepty je popsáno v této části.*</span><span class="sxs-lookup"><span data-stu-id="a32c9-541">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="a32c9-542"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Podporuje pole vazby na objekty pomocí indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="a32c9-542">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a32c9-543">Jakékoli pole formátu, který poskytuje číselné segment klíče (`:0:`, `:1:`, &hellip; `:{n}:`) je schopný vazba pole na pole třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="a32c9-543">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="a32c9-544">"Svázat" je v [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-544">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="a32c9-545">Vazba je poskytována konvence.</span><span class="sxs-lookup"><span data-stu-id="a32c9-545">Binding is provided by convention.</span></span> <span data-ttu-id="a32c9-546">Vlastní zprostředkovatelé konfigurace není nutné implementovat pole vazby.</span><span class="sxs-lookup"><span data-stu-id="a32c9-546">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="a32c9-547">**Zpracování v paměti pole**</span><span class="sxs-lookup"><span data-stu-id="a32c9-547">**In-memory array processing**</span></span>

<span data-ttu-id="a32c9-548">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a32c9-548">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="a32c9-549">Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-549">Key</span></span>             | <span data-ttu-id="a32c9-550">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-550">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a32c9-551">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="a32c9-551">array:entries:0</span></span> | <span data-ttu-id="a32c9-552">gamma0</span><span class="sxs-lookup"><span data-stu-id="a32c9-552">value0</span></span> |
| <span data-ttu-id="a32c9-553">pole: položek: 1</span><span class="sxs-lookup"><span data-stu-id="a32c9-553">array:entries:1</span></span> | <span data-ttu-id="a32c9-554">value1</span><span class="sxs-lookup"><span data-stu-id="a32c9-554">value1</span></span> |
| <span data-ttu-id="a32c9-555">pole: položek: 2</span><span class="sxs-lookup"><span data-stu-id="a32c9-555">array:entries:2</span></span> | <span data-ttu-id="a32c9-556">value2</span><span class="sxs-lookup"><span data-stu-id="a32c9-556">value2</span></span> |
| <span data-ttu-id="a32c9-557">pole: položek: 4</span><span class="sxs-lookup"><span data-stu-id="a32c9-557">array:entries:4</span></span> | <span data-ttu-id="a32c9-558">value4</span><span class="sxs-lookup"><span data-stu-id="a32c9-558">value4</span></span> |
| <span data-ttu-id="a32c9-559">pole: položek: 5</span><span class="sxs-lookup"><span data-stu-id="a32c9-559">array:entries:5</span></span> | <span data-ttu-id="a32c9-560">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="a32c9-560">value5</span></span> |

<span data-ttu-id="a32c9-561">Tyto klíče a hodnoty jsou načteny v ukázkové aplikaci pomocí zprostředkovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="a32c9-561">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

<span data-ttu-id="a32c9-562">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="a32c9-562">The array skips a value for index &num;3.</span></span> <span data-ttu-id="a32c9-563">Konfigurace vazače není schopen vazby hodnoty null a vytvářet položky s hodnotou null v vázaným objektům, zrušte v okamžiku, kdy je znázorněn výsledek vazby toto pole na objekt, který se stane.</span><span class="sxs-lookup"><span data-stu-id="a32c9-563">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="a32c9-564">V ukázkové aplikaci je k dispozici pro uložení vázané konfigurační data POCO třídy:</span><span class="sxs-lookup"><span data-stu-id="a32c9-564">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="a32c9-565">Konfigurační data je vázaná na objekt:</span><span class="sxs-lookup"><span data-stu-id="a32c9-565">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="a32c9-566">`GetSection` Probíhá [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a32c9-566">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a32c9-567">[ConfigurationBinder.Get&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe je také možné, povede k kompaktnějším kód:</span><span class="sxs-lookup"><span data-stu-id="a32c9-567">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="a32c9-568">Vázaný objekt, instance `ArrayExample`, přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-568">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="a32c9-569">`ArrayExample.Entries` Index</span><span class="sxs-lookup"><span data-stu-id="a32c9-569">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a32c9-570">`ArrayExample.Entries` Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-570">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a32c9-571">0</span><span class="sxs-lookup"><span data-stu-id="a32c9-571">0</span></span>                            | <span data-ttu-id="a32c9-572">gamma0</span><span class="sxs-lookup"><span data-stu-id="a32c9-572">value0</span></span>                       |
| <span data-ttu-id="a32c9-573">1</span><span class="sxs-lookup"><span data-stu-id="a32c9-573">1</span></span>                            | <span data-ttu-id="a32c9-574">value1</span><span class="sxs-lookup"><span data-stu-id="a32c9-574">value1</span></span>                       |
| <span data-ttu-id="a32c9-575">2</span><span class="sxs-lookup"><span data-stu-id="a32c9-575">2</span></span>                            | <span data-ttu-id="a32c9-576">value2</span><span class="sxs-lookup"><span data-stu-id="a32c9-576">value2</span></span>                       |
| <span data-ttu-id="a32c9-577">3</span><span class="sxs-lookup"><span data-stu-id="a32c9-577">3</span></span>                            | <span data-ttu-id="a32c9-578">value4</span><span class="sxs-lookup"><span data-stu-id="a32c9-578">value4</span></span>                       |
| <span data-ttu-id="a32c9-579">4</span><span class="sxs-lookup"><span data-stu-id="a32c9-579">4</span></span>                            | <span data-ttu-id="a32c9-580">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="a32c9-580">value5</span></span>                       |

<span data-ttu-id="a32c9-581">Index &num;3 v vázaný objekt obsahuje konfigurační data pro `array:4` konfiguračního klíče a jeho hodnota `value4`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-581">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="a32c9-582">Když je vytvořena vazba konfigurační data obsahující pole, indexy pole v konfigurační klíče se používají pouze k iteraci konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-582">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="a32c9-583">Hodnotu null nelze uchovávat v konfiguračních datech a položku s hodnotou null není vytvořená v vázaný objekt, když přeskočí jednu nebo více indexy pole v konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="a32c9-583">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="a32c9-584">Chybějící položky konfigurace pro index &num;3 může být zadán před vazbu `ArrayExample` instance poskytovatelem žádné konfigurace, která vytváří správné páru klíč hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="a32c9-584">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="a32c9-585">Pokud vzorek zahrnuje další poskytovatele konfigurace JSON s chybějící páru klíč hodnota, `ArrayExample.Entries` odpovídá poli kompletní konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a32c9-585">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="a32c9-586">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="a32c9-586">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="a32c9-587">V <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="a32c9-587">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="a32c9-588">Dvojice klíč hodnota v tabulce se načtou do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-588">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="a32c9-589">Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-589">Key</span></span>             | <span data-ttu-id="a32c9-590">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-590">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a32c9-591">pole: položek: 3</span><span class="sxs-lookup"><span data-stu-id="a32c9-591">array:entries:3</span></span> | <span data-ttu-id="a32c9-592">hodnota3</span><span class="sxs-lookup"><span data-stu-id="a32c9-592">value3</span></span> |

<span data-ttu-id="a32c9-593">Pokud `ArrayExample` instance třídy je vázán po poskytovatel konfigurace JSON obsahuje položku pro index &num;3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a32c9-593">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="a32c9-594">`ArrayExample.Entries` Index</span><span class="sxs-lookup"><span data-stu-id="a32c9-594">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a32c9-595">`ArrayExample.Entries` Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-595">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a32c9-596">0</span><span class="sxs-lookup"><span data-stu-id="a32c9-596">0</span></span>                            | <span data-ttu-id="a32c9-597">gamma0</span><span class="sxs-lookup"><span data-stu-id="a32c9-597">value0</span></span>                       |
| <span data-ttu-id="a32c9-598">1</span><span class="sxs-lookup"><span data-stu-id="a32c9-598">1</span></span>                            | <span data-ttu-id="a32c9-599">value1</span><span class="sxs-lookup"><span data-stu-id="a32c9-599">value1</span></span>                       |
| <span data-ttu-id="a32c9-600">2</span><span class="sxs-lookup"><span data-stu-id="a32c9-600">2</span></span>                            | <span data-ttu-id="a32c9-601">value2</span><span class="sxs-lookup"><span data-stu-id="a32c9-601">value2</span></span>                       |
| <span data-ttu-id="a32c9-602">3</span><span class="sxs-lookup"><span data-stu-id="a32c9-602">3</span></span>                            | <span data-ttu-id="a32c9-603">hodnota3</span><span class="sxs-lookup"><span data-stu-id="a32c9-603">value3</span></span>                       |
| <span data-ttu-id="a32c9-604">4</span><span class="sxs-lookup"><span data-stu-id="a32c9-604">4</span></span>                            | <span data-ttu-id="a32c9-605">value4</span><span class="sxs-lookup"><span data-stu-id="a32c9-605">value4</span></span>                       |
| <span data-ttu-id="a32c9-606">5</span><span class="sxs-lookup"><span data-stu-id="a32c9-606">5</span></span>                            | <span data-ttu-id="a32c9-607">Hodnota5</span><span class="sxs-lookup"><span data-stu-id="a32c9-607">value5</span></span>                       |

<span data-ttu-id="a32c9-608">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="a32c9-608">**JSON array processing**</span></span>

<span data-ttu-id="a32c9-609">Pokud soubor JSON obsahuje pole, vytvoří se pro prvky pole s indexem založený na nule části konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="a32c9-609">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="a32c9-610">V následující konfigurační soubor `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="a32c9-610">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="a32c9-611">Zprostředkovatel konfigurace JSON čte konfigurační data do následující páry klíč hodnota:</span><span class="sxs-lookup"><span data-stu-id="a32c9-611">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="a32c9-612">Key</span><span class="sxs-lookup"><span data-stu-id="a32c9-612">Key</span></span>                     | <span data-ttu-id="a32c9-613">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-613">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="a32c9-614">json_array:key</span><span class="sxs-lookup"><span data-stu-id="a32c9-614">json_array:key</span></span>          | <span data-ttu-id="a32c9-615">valueA</span><span class="sxs-lookup"><span data-stu-id="a32c9-615">valueA</span></span> |
| <span data-ttu-id="a32c9-616">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="a32c9-616">json_array:subsection:0</span></span> | <span data-ttu-id="a32c9-617">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="a32c9-617">valueB</span></span> |
| <span data-ttu-id="a32c9-618">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="a32c9-618">json_array:subsection:1</span></span> | <span data-ttu-id="a32c9-619">valueC</span><span class="sxs-lookup"><span data-stu-id="a32c9-619">valueC</span></span> |
| <span data-ttu-id="a32c9-620">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="a32c9-620">json_array:subsection:2</span></span> | <span data-ttu-id="a32c9-621">valueD</span><span class="sxs-lookup"><span data-stu-id="a32c9-621">valueD</span></span> |

<span data-ttu-id="a32c9-622">V ukázkové aplikaci je k dispozici pro vazbu páry klíč hodnota konfigurace následující třídy POCO:</span><span class="sxs-lookup"><span data-stu-id="a32c9-622">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="a32c9-623">Po vytvoření vazby, `JsonArrayExample.Key` obsahuje hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-623">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="a32c9-624">Dílčí část hodnoty jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-624">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="a32c9-625">`JsonArrayExample.Subsection` Index</span><span class="sxs-lookup"><span data-stu-id="a32c9-625">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="a32c9-626">`JsonArrayExample.Subsection` Hodnota</span><span class="sxs-lookup"><span data-stu-id="a32c9-626">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="a32c9-627">0</span><span class="sxs-lookup"><span data-stu-id="a32c9-627">0</span></span>                                   | <span data-ttu-id="a32c9-628">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="a32c9-628">valueB</span></span>                              |
| <span data-ttu-id="a32c9-629">1</span><span class="sxs-lookup"><span data-stu-id="a32c9-629">1</span></span>                                   | <span data-ttu-id="a32c9-630">valueC</span><span class="sxs-lookup"><span data-stu-id="a32c9-630">valueC</span></span>                              |
| <span data-ttu-id="a32c9-631">2</span><span class="sxs-lookup"><span data-stu-id="a32c9-631">2</span></span>                                   | <span data-ttu-id="a32c9-632">valueD</span><span class="sxs-lookup"><span data-stu-id="a32c9-632">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="a32c9-633">Vlastního poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="a32c9-633">Custom configuration provider</span></span>

<span data-ttu-id="a32c9-634">Ukázková aplikace předvádí, jak vytvořit základní konfigurace poskytovatele, který přečte dvojice klíč hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a32c9-634">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="a32c9-635">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a32c9-635">The provider has the following characteristics:</span></span>

* <span data-ttu-id="a32c9-636">EF database v paměti se používá pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="a32c9-636">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="a32c9-637">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementovat sekundární `ConfigurationBuilder` zadat připojovací řetězec z jiného zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a32c9-637">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="a32c9-638">Poskytovatel přečte tabulku databáze do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a32c9-638">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="a32c9-639">Zprostředkovatel nepodporuje dotazy na databázi na základě-key.</span><span class="sxs-lookup"><span data-stu-id="a32c9-639">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="a32c9-640">Znovu načíst na změnu není implementována, po spuštění aplikace nemá žádný vliv na konfiguraci aplikace, takže aktualizace databáze.</span><span class="sxs-lookup"><span data-stu-id="a32c9-640">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="a32c9-641">Definování `EFConfigurationValue` entity pro ukládání hodnoty konfigurace v databázi.</span><span class="sxs-lookup"><span data-stu-id="a32c9-641">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="a32c9-642">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="a32c9-642">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="a32c9-643">Přidat `EFConfigurationContext` ukládání a přístup k nakonfigurované hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a32c9-643">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="a32c9-644">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="a32c9-644">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="a32c9-645">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-645">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="a32c9-646">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="a32c9-646">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="a32c9-647">Vytvoření vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-647">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="a32c9-648">Poskytovatel konfigurace inicializuje databáze, když je prázdný.</span><span class="sxs-lookup"><span data-stu-id="a32c9-648">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="a32c9-649">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="a32c9-649">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="a32c9-650">`AddEFConfiguration` – Metoda rozšíření umožňuje zdroj konfigurace k přidání `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-650">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="a32c9-651">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="a32c9-651">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="a32c9-652">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a32c9-652">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="a32c9-653">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="a32c9-653">Access configuration during startup</span></span>

<span data-ttu-id="a32c9-654">Vložit `IConfiguration` do `Startup` konstruktoru na hodnoty konfigurace přístupu v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a32c9-654">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a32c9-655">Získat přístup ke konfiguraci v `Startup.Configure`, buď vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="a32c9-655">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="a32c9-656">Příklad přístup ke konfiguraci pomocí vhodné metody po spuštění najdete v tématu [spuštění aplikace: Vhodné metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="a32c9-656">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="a32c9-657">Konfigurace přístupu v stránky Razor Pages nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="a32c9-657">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="a32c9-658">Chcete-li získat přístup k nastavení konfigurace v zobrazení MVC nebo stránky Razor Pages, přidejte [using – direktiva](xref:mvc/views/razor#using) ([referenční dokumentace jazyka C#: použití direktivy](/dotnet/csharp/language-reference/keywords/using-directive)) pro [Microsoft.Extensions.Configuration obor názvů ](xref:Microsoft.Extensions.Configuration) a vložit <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a32c9-658">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="a32c9-659">Na stránce pro stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="a32c9-659">In a Razor Pages page:</span></span>

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

<span data-ttu-id="a32c9-660">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="a32c9-660">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="a32c9-661">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="a32c9-661">Add configuration from an external assembly</span></span>

<span data-ttu-id="a32c9-662"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Implementace umožňuje přidání vylepšení do aplikace při spuštění z externího sestavení mimo aplikaci prvku `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="a32c9-662">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="a32c9-663">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a32c9-663">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a32c9-664">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a32c9-664">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="a32c9-665">Podrobné informace o konfiguraci Microsoft</span><span class="sxs-lookup"><span data-stu-id="a32c9-665">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)

---
<span data-ttu-id="42b22-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-102">'Blazor'</span></span>
- <span data-ttu-id="42b22-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-103">'Identity'</span></span>
- <span data-ttu-id="42b22-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-105">'Razor'</span></span>
- <span data-ttu-id="42b22-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="42b22-107">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42b22-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="42b22-108">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="42b22-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42b22-109">Konfigurace v ASP.NET Core provádí použití jednoho nebo více [poskytovatelů konfigurace](#cp).</span><span class="sxs-lookup"><span data-stu-id="42b22-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="42b22-110">Poskytovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="42b22-111">Soubory nastavení, například *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="42b22-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="42b22-112">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-112">Environment variables</span></span>
* <span data-ttu-id="42b22-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="42b22-113">Azure Key Vault</span></span>
* <span data-ttu-id="42b22-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="42b22-114">Azure App Configuration</span></span>
* <span data-ttu-id="42b22-115">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="42b22-115">Command-line arguments</span></span>
* <span data-ttu-id="42b22-116">Vlastní zprostředkovatelé, nainstalované nebo vytvořené</span><span class="sxs-lookup"><span data-stu-id="42b22-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="42b22-117">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="42b22-117">Directory files</span></span>
* <span data-ttu-id="42b22-118">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="42b22-118">In-memory .NET objects</span></span>

<span data-ttu-id="42b22-119">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42b22-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="42b22-120">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="42b22-120">Default configuration</span></span>

<span data-ttu-id="42b22-121">ASP.NET Core webové aplikace vytvořené pomocí [dotnet New](/dotnet/core/tools/dotnet-new) nebo sady Visual Studio generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="42b22-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="42b22-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="42b22-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="42b22-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá existující `IConfiguration` jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="42b22-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="42b22-124">Ve výchozím případu konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj konfigurace _aplikace_ .</span><span class="sxs-lookup"><span data-stu-id="42b22-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="42b22-125">[appSettings. JSON](#appsettingsjson) s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="42b22-126">*appSettings.* `Environment` *. JSON* s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="42b22-127">Například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="42b22-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="42b22-128">[Tajné kódy aplikace](xref:security/app-secrets) v případě, že aplikace běží v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="42b22-129">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="42b22-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="42b22-130">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line).</span><span class="sxs-lookup"><span data-stu-id="42b22-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="42b22-131">Poskytovatelé konfigurace, kteří jsou přidáni později, přepíší předchozí nastavení klíče.</span><span class="sxs-lookup"><span data-stu-id="42b22-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="42b22-132">Například pokud `MyKey` je nastavena v souboru *appSettings. JSON* i v prostředí, je použita hodnota prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="42b22-133">Pomocí výchozích zprostředkovatelů konfigurace přepíše [Poskytovatel konfigurace příkazového řádku](#command-line-configuration-provider) všechny ostatní poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="42b22-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="42b22-134">Další informace o najdete v `CreateDefaultBuilder` tématu [výchozí nastavení tvůrce](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="42b22-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="42b22-135">Následující kód zobrazuje povolené poskytovatele konfigurace v pořadí, v jakém byly přidány:</span><span class="sxs-lookup"><span data-stu-id="42b22-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="42b22-136">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="42b22-136">appsettings.json</span></span>

<span data-ttu-id="42b22-137">Vezměte v úvahu následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="42b22-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="42b22-138">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-139">Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načtení konfigurace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="42b22-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="42b22-140">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="42b22-140">*appsettings.json*</span></span>
1. <span data-ttu-id="42b22-141">*appSettings.* `Environment` *. JSON* : například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="42b22-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="42b22-142">Verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="42b22-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="42b22-143">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="42b22-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="42b22-144">*appSettings*. `Environment` .. hodnoty *JSON* přepíší klíče v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="42b22-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="42b22-145">Ve výchozím nastavení by například:</span><span class="sxs-lookup"><span data-stu-id="42b22-145">For example, by default:</span></span>

* <span data-ttu-id="42b22-146">Ve vývoji, *appSettings*. ***Vývoj***. konfigurace *JSON* Přepisuje hodnoty nalezené v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="42b22-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="42b22-147">V produkčním prostředí, *appSettings*. ***Provozní***prostředí. konfigurace *JSON* Přepisuje hodnoty nalezené v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="42b22-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="42b22-148">Například při nasazení aplikace do Azure.</span><span class="sxs-lookup"><span data-stu-id="42b22-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="42b22-149">Vytvoření vazby hierarchických konfiguračních dat pomocí vzoru možností</span><span class="sxs-lookup"><span data-stu-id="42b22-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="42b22-150">Pomocí [výchozí](#default) konfigurace, *appSettings. JSON* a *appSettings.* `Environment` soubory *. JSON* jsou povolené pomocí [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="42b22-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="42b22-151">Změny provedené v souboru *appSettings. JSON* a *appSettings.* `Environment` soubor *. JSON* ***po*** spuštění aplikace si přečte [Poskytovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="42b22-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="42b22-152">Informace o přidání dalších konfiguračních souborů JSON najdete v části [Poskytovatel konfigurace JSON](#jcp) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="42b22-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="42b22-153">Správce zabezpečení a tajného klíče</span><span class="sxs-lookup"><span data-stu-id="42b22-153">Security and secret manager</span></span>

<span data-ttu-id="42b22-154">Pokyny pro konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="42b22-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="42b22-155">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="42b22-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="42b22-156">[Správce tajného klíče](xref:security/app-secrets) se dá použít k ukládání tajných kódů do vývoje.</span><span class="sxs-lookup"><span data-stu-id="42b22-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="42b22-157">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="42b22-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="42b22-158">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="42b22-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="42b22-159">Ve [výchozím nastavení](#default)přečte [správce tajných klíčů](xref:security/app-secrets) nastavení konfigurace po souboru *appSettings. JSON* a *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="42b22-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="42b22-160">Další informace o ukládání hesel nebo jiných citlivých dat:</span><span class="sxs-lookup"><span data-stu-id="42b22-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="42b22-161"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="42b22-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="42b22-162">Správce tajného kódu používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="42b22-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="42b22-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="42b22-164">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="42b22-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="42b22-165">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-165">Environment variables</span></span>

<span data-ttu-id="42b22-166">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z proměnných prostředí klíč-hodnota po čtení *appSettings. JSON*, *appSettings.* `Environment` *. JSON*a [správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="42b22-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="42b22-167">Proto hodnoty klíčů načtené z prostředí přepisují hodnoty načtené z hodnot *appSettings. JSON*, *appSettings.* `Environment` *. JSON*a správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="42b22-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="42b22-168">Následující `set` příkazy:</span><span class="sxs-lookup"><span data-stu-id="42b22-168">The following `set` commands:</span></span>

* <span data-ttu-id="42b22-169">Nastavte klíče prostředí a hodnoty v [předchozím příkladu](#appsettingsjson) ve Windows.</span><span class="sxs-lookup"><span data-stu-id="42b22-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="42b22-170">Při použití [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)otestujte nastavení.</span><span class="sxs-lookup"><span data-stu-id="42b22-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="42b22-171">`dotnet run`Příkaz musí být spuštěn v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="42b22-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="42b22-172">Předchozí nastavení prostředí:</span><span class="sxs-lookup"><span data-stu-id="42b22-172">The preceding environment settings:</span></span>

* <span data-ttu-id="42b22-173">Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.</span><span class="sxs-lookup"><span data-stu-id="42b22-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="42b22-174">Nebudou přečteny pomocí prohlížečů spuštěných v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="42b22-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="42b22-175">K nastavení klíčů a hodnot prostředí ve Windows se dají použít následující příkazy [setx](/windows-server/administration/windows-commands/setx) .</span><span class="sxs-lookup"><span data-stu-id="42b22-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="42b22-176">Na rozdíl `set` od `setx` platí, že nastavení jsou trvalá.</span><span class="sxs-lookup"><span data-stu-id="42b22-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="42b22-177">`/M`nastaví proměnnou v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="42b22-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="42b22-178">Pokud se `/M` přepínač nepoužívá, je nastavena proměnná prostředí uživatele.</span><span class="sxs-lookup"><span data-stu-id="42b22-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="42b22-179">Chcete-li otestovat předchozí příkazy, přepište *appSettings. JSON* a *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="42b22-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="42b22-180">Se sadou Visual Studio: Ukončete a restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="42b22-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="42b22-181">Pomocí rozhraní příkazového řádku: spusťte nové příkazové okno a zadejte `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="42b22-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="42b22-182">Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem pro určení předpony pro proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="42b22-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="42b22-183">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="42b22-183">In the preceding code:</span></span>

* <span data-ttu-id="42b22-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="42b22-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="42b22-185">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="42b22-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="42b22-186">Proměnné prostředí nastavené s `MyCustomPrefix_` předponou přepíšou [výchozí poskytovatele konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="42b22-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="42b22-187">To zahrnuje proměnné prostředí bez předpony.</span><span class="sxs-lookup"><span data-stu-id="42b22-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="42b22-188">Pokud jsou načteny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="42b22-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="42b22-189">Vlastní předponu otestujete následujícími příkazy:</span><span class="sxs-lookup"><span data-stu-id="42b22-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="42b22-190">[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty příkazového řádku s předponou `DOTNET_` a `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="42b22-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="42b22-191">`DOTNET_` `ASPNETCORE_` Předpony a jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele.</span><span class="sxs-lookup"><span data-stu-id="42b22-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="42b22-192">Další informace o konfiguraci hostitele a aplikace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="42b22-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="42b22-193">V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="42b22-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="42b22-194">Azure App Service nastavení aplikace:</span><span class="sxs-lookup"><span data-stu-id="42b22-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="42b22-195">Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.</span><span class="sxs-lookup"><span data-stu-id="42b22-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="42b22-196">Vystaveno jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-196">Exposed as environment variables.</span></span>

<span data-ttu-id="42b22-197">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="42b22-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="42b22-198">Informace o připojovacích řetězcích Azure Database najdete v tématu [předpony připojovacího řetězce](#constr) .</span><span class="sxs-lookup"><span data-stu-id="42b22-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="42b22-199">Příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="42b22-199">Command-line</span></span>

<span data-ttu-id="42b22-200">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načte konfiguraci z dvojice klíč-hodnota argumentu klíč-hodnota po následujících zdrojích konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="42b22-201">*appSettings. JSON* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="42b22-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="42b22-202">[Tajné kódy aplikací (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="42b22-203">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-203">Environment variables.</span></span>

<span data-ttu-id="42b22-204">Ve [výchozím nastavení](#default)jsou konfigurační hodnoty nastavené pro přepisy hodnot konfigurace na příkazovém řádku nastavené se všemi ostatními zprostředkovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="42b22-205">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="42b22-205">Command-line arguments</span></span>

<span data-ttu-id="42b22-206">Následující příkaz nastaví klíče a hodnoty pomocí `=` :</span><span class="sxs-lookup"><span data-stu-id="42b22-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="42b22-207">Následující příkaz nastaví klíče a hodnoty pomocí `/` :</span><span class="sxs-lookup"><span data-stu-id="42b22-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="42b22-208">Následující příkaz nastaví klíče a hodnoty pomocí `--` :</span><span class="sxs-lookup"><span data-stu-id="42b22-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="42b22-209">Hodnota klíče:</span><span class="sxs-lookup"><span data-stu-id="42b22-209">The key value:</span></span>

* <span data-ttu-id="42b22-210">Musí následovat po `=` , nebo klíč musí mít předponu `--` nebo, `/` Pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="42b22-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="42b22-211">Není vyžadováno `=` , pokud je použit.</span><span class="sxs-lookup"><span data-stu-id="42b22-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="42b22-212">Například, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="42b22-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="42b22-213">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které `=` se používají s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="42b22-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="42b22-214">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="42b22-214">Switch mappings</span></span>

<span data-ttu-id="42b22-215">Mapování přepínačů povolují logiku nahrazení názvu **klíče** .</span><span class="sxs-lookup"><span data-stu-id="42b22-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="42b22-216">Poskytněte slovník pro nahrazení přepínačů v <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodě.</span><span class="sxs-lookup"><span data-stu-id="42b22-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="42b22-217">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="42b22-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="42b22-218">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota Dictionary zpátky, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="42b22-219">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="42b22-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="42b22-220">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="42b22-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="42b22-221">Přepínače musí začínat na `-` nebo `--` .</span><span class="sxs-lookup"><span data-stu-id="42b22-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="42b22-222">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="42b22-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="42b22-223">Chcete-li použít slovník mapování přepínačů, předejte jej do volání `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="42b22-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="42b22-224">Následující kód ukazuje klíčové hodnoty pro nahrazené klíče:</span><span class="sxs-lookup"><span data-stu-id="42b22-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-225">Spusťte následující příkaz pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="42b22-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="42b22-226">Poznámka: v současné době `=` nelze použít k nastavení hodnot nahrazení klíče jedinou pomlčkou `-` .</span><span class="sxs-lookup"><span data-stu-id="42b22-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="42b22-227">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="42b22-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="42b22-228">Následující příkaz funguje pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="42b22-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="42b22-229">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="42b22-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="42b22-230">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="42b22-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="42b22-231">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty i slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="42b22-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="42b22-232">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="42b22-232">Hierarchical configuration data</span></span>

<span data-ttu-id="42b22-233">Rozhraní API pro konfiguraci čte hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="42b22-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="42b22-234">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="42b22-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="42b22-235">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-236">Upřednostňovaným způsobem, jak číst hierarchická konfigurační data, je použít vzor možností.</span><span class="sxs-lookup"><span data-stu-id="42b22-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="42b22-237">Další informace najdete v tématu [vázání hierarchických konfiguračních dat](#optpat) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="42b22-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="42b22-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="42b22-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="42b22-239">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="42b22-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="42b22-240">Konfigurační klíče a hodnoty</span><span class="sxs-lookup"><span data-stu-id="42b22-240">Configuration keys and values</span></span>

<span data-ttu-id="42b22-241">Konfigurační klíče:</span><span class="sxs-lookup"><span data-stu-id="42b22-241">Configuration keys:</span></span>

* <span data-ttu-id="42b22-242">Rozlišují se malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="42b22-242">Are case-insensitive.</span></span> <span data-ttu-id="42b22-243">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="42b22-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="42b22-244">Pokud je klíč a hodnota nastavená ve více poskytovatelích konfigurace, použije se hodnota z posledního přidaného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="42b22-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="42b22-245">Další informace najdete v tématu [výchozí konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="42b22-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="42b22-246">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="42b22-246">Hierarchical keys</span></span>
  * <span data-ttu-id="42b22-247">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="42b22-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="42b22-248">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="42b22-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="42b22-249">Dvojité podtržítko, `__` , je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:` .</span><span class="sxs-lookup"><span data-stu-id="42b22-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="42b22-250">V Azure Key Vault hierarchické klíče používají `--` jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="42b22-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="42b22-251">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) se `--` `:` při načtení tajných klíčů do konfigurace aplikace automaticky nahradí.</span><span class="sxs-lookup"><span data-stu-id="42b22-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="42b22-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="42b22-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="42b22-253">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#boa) .</span><span class="sxs-lookup"><span data-stu-id="42b22-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="42b22-254">Konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="42b22-254">Configuration values:</span></span>

* <span data-ttu-id="42b22-255">Jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="42b22-255">Are strings.</span></span>
* <span data-ttu-id="42b22-256">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="42b22-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="42b22-257">Poskytovatelé konfigurace</span><span class="sxs-lookup"><span data-stu-id="42b22-257">Configuration providers</span></span>

<span data-ttu-id="42b22-258">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="42b22-259">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="42b22-259">Provider</span></span> | <span data-ttu-id="42b22-260">Poskytuje konfiguraci z</span><span class="sxs-lookup"><span data-stu-id="42b22-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="42b22-261">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-262">'Blazor'</span></span>
- <span data-ttu-id="42b22-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-263">'Identity'</span></span>
- <span data-ttu-id="42b22-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-265">'Razor'</span></span>
- <span data-ttu-id="42b22-266">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-267">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-268">'Blazor'</span></span>
- <span data-ttu-id="42b22-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-269">'Identity'</span></span>
- <span data-ttu-id="42b22-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-271">'Razor'</span></span>
- <span data-ttu-id="42b22-272">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-272">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-273">---- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-274">'Blazor'</span></span>
- <span data-ttu-id="42b22-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-275">'Identity'</span></span>
- <span data-ttu-id="42b22-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-277">'Razor'</span></span>
- <span data-ttu-id="42b22-278">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-279">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-280">'Blazor'</span></span>
- <span data-ttu-id="42b22-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-281">'Identity'</span></span>
- <span data-ttu-id="42b22-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-283">'Razor'</span></span>
- <span data-ttu-id="42b22-284">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-285">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-286">'Blazor'</span></span>
- <span data-ttu-id="42b22-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-287">'Identity'</span></span>
- <span data-ttu-id="42b22-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-289">'Razor'</span></span>
- <span data-ttu-id="42b22-290">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-291">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-292">'Blazor'</span></span>
- <span data-ttu-id="42b22-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-293">'Identity'</span></span>
- <span data-ttu-id="42b22-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-295">'Razor'</span></span>
- <span data-ttu-id="42b22-296">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-297">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-298">'Blazor'</span></span>
- <span data-ttu-id="42b22-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-299">'Identity'</span></span>
- <span data-ttu-id="42b22-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-301">'Razor'</span></span>
- <span data-ttu-id="42b22-302">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-303">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-304">'Blazor'</span></span>
- <span data-ttu-id="42b22-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-305">'Identity'</span></span>
- <span data-ttu-id="42b22-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-307">'Razor'</span></span>
- <span data-ttu-id="42b22-308">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-309">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-310">'Blazor'</span></span>
- <span data-ttu-id="42b22-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-311">'Identity'</span></span>
- <span data-ttu-id="42b22-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-313">'Razor'</span></span>
- <span data-ttu-id="42b22-314">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-315">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-316">'Blazor'</span></span>
- <span data-ttu-id="42b22-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-317">'Identity'</span></span>
- <span data-ttu-id="42b22-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-319">'Razor'</span></span>
- <span data-ttu-id="42b22-320">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-321">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-322">'Blazor'</span></span>
- <span data-ttu-id="42b22-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-323">'Identity'</span></span>
- <span data-ttu-id="42b22-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-325">'Razor'</span></span>
- <span data-ttu-id="42b22-326">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-327">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-328">'Blazor'</span></span>
- <span data-ttu-id="42b22-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-329">'Identity'</span></span>
- <span data-ttu-id="42b22-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-331">'Razor'</span></span>
- <span data-ttu-id="42b22-332">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-333">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-334">'Blazor'</span></span>
- <span data-ttu-id="42b22-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-335">'Identity'</span></span>
- <span data-ttu-id="42b22-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-337">'Razor'</span></span>
- <span data-ttu-id="42b22-338">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-339">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-340">'Blazor'</span></span>
- <span data-ttu-id="42b22-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-341">'Identity'</span></span>
- <span data-ttu-id="42b22-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-343">'Razor'</span></span>
- <span data-ttu-id="42b22-344">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-345">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-346">'Blazor'</span></span>
- <span data-ttu-id="42b22-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-347">'Identity'</span></span>
- <span data-ttu-id="42b22-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-349">'Razor'</span></span>
- <span data-ttu-id="42b22-350">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-351">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-352">'Blazor'</span></span>
- <span data-ttu-id="42b22-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-353">'Identity'</span></span>
- <span data-ttu-id="42b22-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-355">'Razor'</span></span>
- <span data-ttu-id="42b22-356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-357">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-358">'Blazor'</span></span>
- <span data-ttu-id="42b22-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-359">'Identity'</span></span>
- <span data-ttu-id="42b22-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-361">'Razor'</span></span>
- <span data-ttu-id="42b22-362">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-362">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-363">------------------ | | [Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault | | [Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Konfigurace aplikace Azure | | [Poskytovatel konfigurace příkazového řádku](#clcp) | Parametry příkazového řádku | | [Vlastní poskytovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj | | [Poskytovatel konfigurace proměnných prostředí](#evcp) | Proměnné prostředí | | [Poskytovatel konfigurace souboru](#file-configuration-provider) | Soubory INI, JSON a XML | | [Poskytovatel konfigurace klíče na soubor](#key-per-file-configuration-provider) | Soubory adresáře | | [Poskytovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti | | [Správce tajných klíčů](xref:security/app-secrets) | Soubor v adresáři profilu uživatele |</span><span class="sxs-lookup"><span data-stu-id="42b22-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="42b22-364">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou zadáni poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="42b22-365">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="42b22-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="42b22-366">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="42b22-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="42b22-367">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="42b22-367">*appsettings.json*</span></span>
1. <span data-ttu-id="42b22-368">*appSettings*. `Environment` .. formát *JSON*</span><span class="sxs-lookup"><span data-stu-id="42b22-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="42b22-369">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="42b22-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="42b22-370">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="42b22-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="42b22-371">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="42b22-372">Běžný postup je přidat poskytovatele konfigurace příkazového řádku na poslední v řadě poskytovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="42b22-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="42b22-373">Předchozí sekvence zprostředkovatelů se používá ve [výchozí konfiguraci](#default).</span><span class="sxs-lookup"><span data-stu-id="42b22-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="42b22-374">Předpony připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="42b22-374">Connection string prefixes</span></span>

<span data-ttu-id="42b22-375">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro proměnné prostředí pro čtyři připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="42b22-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="42b22-376">Tyto připojovací řetězce jsou součástí konfigurace připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="42b22-377">Proměnné prostředí s předponami, které jsou uvedené v tabulce, se načtou do aplikace s [výchozí konfigurací](#default) nebo když není dodána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="42b22-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="42b22-378">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="42b22-378">Connection string prefix</span></span> | <span data-ttu-id="42b22-379">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="42b22-379">Provider</span></span> |
| ---
<span data-ttu-id="42b22-380">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-381">'Blazor'</span></span>
- <span data-ttu-id="42b22-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-382">'Identity'</span></span>
- <span data-ttu-id="42b22-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-384">'Razor'</span></span>
- <span data-ttu-id="42b22-385">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-386">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-387">'Blazor'</span></span>
- <span data-ttu-id="42b22-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-388">'Identity'</span></span>
- <span data-ttu-id="42b22-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-390">'Razor'</span></span>
- <span data-ttu-id="42b22-391">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-392">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-393">'Blazor'</span></span>
- <span data-ttu-id="42b22-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-394">'Identity'</span></span>
- <span data-ttu-id="42b22-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-396">'Razor'</span></span>
- <span data-ttu-id="42b22-397">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-398">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-399">'Blazor'</span></span>
- <span data-ttu-id="42b22-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-400">'Identity'</span></span>
- <span data-ttu-id="42b22-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-402">'Razor'</span></span>
- <span data-ttu-id="42b22-403">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-404">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-405">'Blazor'</span></span>
- <span data-ttu-id="42b22-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-406">'Identity'</span></span>
- <span data-ttu-id="42b22-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-408">'Razor'</span></span>
- <span data-ttu-id="42b22-409">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-410">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-411">'Blazor'</span></span>
- <span data-ttu-id="42b22-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-412">'Identity'</span></span>
- <span data-ttu-id="42b22-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-414">'Razor'</span></span>
- <span data-ttu-id="42b22-415">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-416">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-417">'Blazor'</span></span>
- <span data-ttu-id="42b22-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-418">'Identity'</span></span>
- <span data-ttu-id="42b22-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-420">'Razor'</span></span>
- <span data-ttu-id="42b22-421">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-422">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-423">'Blazor'</span></span>
- <span data-ttu-id="42b22-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-424">'Identity'</span></span>
- <span data-ttu-id="42b22-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-426">'Razor'</span></span>
- <span data-ttu-id="42b22-427">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-428">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-429">'Blazor'</span></span>
- <span data-ttu-id="42b22-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-430">'Identity'</span></span>
- <span data-ttu-id="42b22-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-432">'Razor'</span></span>
- <span data-ttu-id="42b22-433">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-434">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-435">'Blazor'</span></span>
- <span data-ttu-id="42b22-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-436">'Identity'</span></span>
- <span data-ttu-id="42b22-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-438">'Razor'</span></span>
- <span data-ttu-id="42b22-439">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-439">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-440">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-441">'Blazor'</span></span>
- <span data-ttu-id="42b22-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-442">'Identity'</span></span>
- <span data-ttu-id="42b22-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-444">'Razor'</span></span>
- <span data-ttu-id="42b22-445">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-446">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-447">'Blazor'</span></span>
- <span data-ttu-id="42b22-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-448">'Identity'</span></span>
- <span data-ttu-id="42b22-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-450">'Razor'</span></span>
- <span data-ttu-id="42b22-451">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-451">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-452">---- | | `CUSTOMCONNSTR_` | Vlastní poskytovatel | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="42b22-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="42b22-453">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="42b22-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="42b22-454">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="42b22-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="42b22-455">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="42b22-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="42b22-456">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-456">Environment variable key</span></span> | <span data-ttu-id="42b22-457">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="42b22-457">Converted configuration key</span></span> | <span data-ttu-id="42b22-458">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="42b22-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="42b22-459">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-460">'Blazor'</span></span>
- <span data-ttu-id="42b22-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-461">'Identity'</span></span>
- <span data-ttu-id="42b22-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-463">'Razor'</span></span>
- <span data-ttu-id="42b22-464">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-465">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-466">'Blazor'</span></span>
- <span data-ttu-id="42b22-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-467">'Identity'</span></span>
- <span data-ttu-id="42b22-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-469">'Razor'</span></span>
- <span data-ttu-id="42b22-470">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-471">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-472">'Blazor'</span></span>
- <span data-ttu-id="42b22-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-473">'Identity'</span></span>
- <span data-ttu-id="42b22-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-475">'Razor'</span></span>
- <span data-ttu-id="42b22-476">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-477">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-478">'Blazor'</span></span>
- <span data-ttu-id="42b22-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-479">'Identity'</span></span>
- <span data-ttu-id="42b22-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-481">'Razor'</span></span>
- <span data-ttu-id="42b22-482">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-483">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-484">'Blazor'</span></span>
- <span data-ttu-id="42b22-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-485">'Identity'</span></span>
- <span data-ttu-id="42b22-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-487">'Razor'</span></span>
- <span data-ttu-id="42b22-488">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-489">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-490">'Blazor'</span></span>
- <span data-ttu-id="42b22-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-491">'Identity'</span></span>
- <span data-ttu-id="42b22-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-493">'Razor'</span></span>
- <span data-ttu-id="42b22-494">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-495">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-496">'Blazor'</span></span>
- <span data-ttu-id="42b22-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-497">'Identity'</span></span>
- <span data-ttu-id="42b22-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-499">'Razor'</span></span>
- <span data-ttu-id="42b22-500">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-501">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-502">'Blazor'</span></span>
- <span data-ttu-id="42b22-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-503">'Identity'</span></span>
- <span data-ttu-id="42b22-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-505">'Razor'</span></span>
- <span data-ttu-id="42b22-506">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-507">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-508">'Blazor'</span></span>
- <span data-ttu-id="42b22-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-509">'Identity'</span></span>
- <span data-ttu-id="42b22-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-511">'Razor'</span></span>
- <span data-ttu-id="42b22-512">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-513">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-514">'Blazor'</span></span>
- <span data-ttu-id="42b22-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-515">'Identity'</span></span>
- <span data-ttu-id="42b22-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-517">'Razor'</span></span>
- <span data-ttu-id="42b22-518">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-518">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-519">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-520">'Blazor'</span></span>
- <span data-ttu-id="42b22-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-521">'Identity'</span></span>
- <span data-ttu-id="42b22-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-523">'Razor'</span></span>
- <span data-ttu-id="42b22-524">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-525">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-526">'Blazor'</span></span>
- <span data-ttu-id="42b22-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-527">'Identity'</span></span>
- <span data-ttu-id="42b22-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-529">'Razor'</span></span>
- <span data-ttu-id="42b22-530">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-531">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-532">'Blazor'</span></span>
- <span data-ttu-id="42b22-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-533">'Identity'</span></span>
- <span data-ttu-id="42b22-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-535">'Razor'</span></span>
- <span data-ttu-id="42b22-536">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-537">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-538">'Blazor'</span></span>
- <span data-ttu-id="42b22-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-539">'Identity'</span></span>
- <span data-ttu-id="42b22-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-541">'Razor'</span></span>
- <span data-ttu-id="42b22-542">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-543">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-544">'Blazor'</span></span>
- <span data-ttu-id="42b22-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-545">'Identity'</span></span>
- <span data-ttu-id="42b22-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-547">'Razor'</span></span>
- <span data-ttu-id="42b22-548">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-549">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-550">'Blazor'</span></span>
- <span data-ttu-id="42b22-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-551">'Identity'</span></span>
- <span data-ttu-id="42b22-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-553">'Razor'</span></span>
- <span data-ttu-id="42b22-554">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-555">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-556">'Blazor'</span></span>
- <span data-ttu-id="42b22-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-557">'Identity'</span></span>
- <span data-ttu-id="42b22-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-559">'Razor'</span></span>
- <span data-ttu-id="42b22-560">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-561">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-562">'Blazor'</span></span>
- <span data-ttu-id="42b22-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-563">'Identity'</span></span>
- <span data-ttu-id="42b22-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-565">'Razor'</span></span>
- <span data-ttu-id="42b22-566">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-567">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-568">'Blazor'</span></span>
- <span data-ttu-id="42b22-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-569">'Identity'</span></span>
- <span data-ttu-id="42b22-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-571">'Razor'</span></span>
- <span data-ttu-id="42b22-572">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-573">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-574">'Blazor'</span></span>
- <span data-ttu-id="42b22-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-575">'Identity'</span></span>
- <span data-ttu-id="42b22-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-577">'Razor'</span></span>
- <span data-ttu-id="42b22-578">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-579">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-580">'Blazor'</span></span>
- <span data-ttu-id="42b22-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-581">'Identity'</span></span>
- <span data-ttu-id="42b22-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-583">'Razor'</span></span>
- <span data-ttu-id="42b22-584">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-584">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-585">-------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-586">'Blazor'</span></span>
- <span data-ttu-id="42b22-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-587">'Identity'</span></span>
- <span data-ttu-id="42b22-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-589">'Razor'</span></span>
- <span data-ttu-id="42b22-590">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-591">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-592">'Blazor'</span></span>
- <span data-ttu-id="42b22-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-593">'Identity'</span></span>
- <span data-ttu-id="42b22-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-595">'Razor'</span></span>
- <span data-ttu-id="42b22-596">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-597">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-598">'Blazor'</span></span>
- <span data-ttu-id="42b22-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-599">'Identity'</span></span>
- <span data-ttu-id="42b22-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-601">'Razor'</span></span>
- <span data-ttu-id="42b22-602">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-603">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-604">'Blazor'</span></span>
- <span data-ttu-id="42b22-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-605">'Identity'</span></span>
- <span data-ttu-id="42b22-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-607">'Razor'</span></span>
- <span data-ttu-id="42b22-608">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-609">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-610">'Blazor'</span></span>
- <span data-ttu-id="42b22-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-611">'Identity'</span></span>
- <span data-ttu-id="42b22-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-613">'Razor'</span></span>
- <span data-ttu-id="42b22-614">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-615">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-616">'Blazor'</span></span>
- <span data-ttu-id="42b22-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-617">'Identity'</span></span>
- <span data-ttu-id="42b22-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-619">'Razor'</span></span>
- <span data-ttu-id="42b22-620">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-621">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-622">'Blazor'</span></span>
- <span data-ttu-id="42b22-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-623">'Identity'</span></span>
- <span data-ttu-id="42b22-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-625">'Razor'</span></span>
- <span data-ttu-id="42b22-626">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-627">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-628">'Blazor'</span></span>
- <span data-ttu-id="42b22-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-629">'Identity'</span></span>
- <span data-ttu-id="42b22-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-631">'Razor'</span></span>
- <span data-ttu-id="42b22-632">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-633">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-634">'Blazor'</span></span>
- <span data-ttu-id="42b22-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-635">'Identity'</span></span>
- <span data-ttu-id="42b22-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-637">'Razor'</span></span>
- <span data-ttu-id="42b22-638">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-639">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-640">'Blazor'</span></span>
- <span data-ttu-id="42b22-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-641">'Identity'</span></span>
- <span data-ttu-id="42b22-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-643">'Razor'</span></span>
- <span data-ttu-id="42b22-644">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-645">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-646">'Blazor'</span></span>
- <span data-ttu-id="42b22-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-647">'Identity'</span></span>
- <span data-ttu-id="42b22-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-649">'Razor'</span></span>
- <span data-ttu-id="42b22-650">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-651">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-652">'Blazor'</span></span>
- <span data-ttu-id="42b22-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-653">'Identity'</span></span>
- <span data-ttu-id="42b22-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-655">'Razor'</span></span>
- <span data-ttu-id="42b22-656">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-657">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-658">'Blazor'</span></span>
- <span data-ttu-id="42b22-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-659">'Identity'</span></span>
- <span data-ttu-id="42b22-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-661">'Razor'</span></span>
- <span data-ttu-id="42b22-662">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-663">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-664">'Blazor'</span></span>
- <span data-ttu-id="42b22-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-665">'Identity'</span></span>
- <span data-ttu-id="42b22-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-667">'Razor'</span></span>
- <span data-ttu-id="42b22-668">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-669">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-670">'Blazor'</span></span>
- <span data-ttu-id="42b22-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-671">'Identity'</span></span>
- <span data-ttu-id="42b22-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-673">'Razor'</span></span>
- <span data-ttu-id="42b22-674">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-675">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-676">'Blazor'</span></span>
- <span data-ttu-id="42b22-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-677">'Identity'</span></span>
- <span data-ttu-id="42b22-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-679">'Razor'</span></span>
- <span data-ttu-id="42b22-680">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-681">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-682">'Blazor'</span></span>
- <span data-ttu-id="42b22-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-683">'Identity'</span></span>
- <span data-ttu-id="42b22-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-685">'Razor'</span></span>
- <span data-ttu-id="42b22-686">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-687">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-688">'Blazor'</span></span>
- <span data-ttu-id="42b22-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-689">'Identity'</span></span>
- <span data-ttu-id="42b22-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-691">'Razor'</span></span>
- <span data-ttu-id="42b22-692">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-693">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-694">'Blazor'</span></span>
- <span data-ttu-id="42b22-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-695">'Identity'</span></span>
- <span data-ttu-id="42b22-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-697">'Razor'</span></span>
- <span data-ttu-id="42b22-698">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-699">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-700">'Blazor'</span></span>
- <span data-ttu-id="42b22-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-701">'Identity'</span></span>
- <span data-ttu-id="42b22-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-703">'Razor'</span></span>
- <span data-ttu-id="42b22-704">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-705">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-706">'Blazor'</span></span>
- <span data-ttu-id="42b22-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-707">'Identity'</span></span>
- <span data-ttu-id="42b22-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-709">'Razor'</span></span>
- <span data-ttu-id="42b22-710">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-711">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-712">'Blazor'</span></span>
- <span data-ttu-id="42b22-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-713">'Identity'</span></span>
- <span data-ttu-id="42b22-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-715">'Razor'</span></span>
- <span data-ttu-id="42b22-716">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-717">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-718">'Blazor'</span></span>
- <span data-ttu-id="42b22-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-719">'Identity'</span></span>
- <span data-ttu-id="42b22-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-721">'Razor'</span></span>
- <span data-ttu-id="42b22-722">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-723">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-724">'Blazor'</span></span>
- <span data-ttu-id="42b22-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-725">'Identity'</span></span>
- <span data-ttu-id="42b22-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-727">'Razor'</span></span>
- <span data-ttu-id="42b22-728">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-729">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-730">'Blazor'</span></span>
- <span data-ttu-id="42b22-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-731">'Identity'</span></span>
- <span data-ttu-id="42b22-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-733">'Razor'</span></span>
- <span data-ttu-id="42b22-734">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-735">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-736">'Blazor'</span></span>
- <span data-ttu-id="42b22-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-737">'Identity'</span></span>
- <span data-ttu-id="42b22-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-739">'Razor'</span></span>
- <span data-ttu-id="42b22-740">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-741">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-742">'Blazor'</span></span>
- <span data-ttu-id="42b22-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-743">'Identity'</span></span>
- <span data-ttu-id="42b22-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-745">'Razor'</span></span>
- <span data-ttu-id="42b22-746">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-747">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-748">'Blazor'</span></span>
- <span data-ttu-id="42b22-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-749">'Identity'</span></span>
- <span data-ttu-id="42b22-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-751">'Razor'</span></span>
- <span data-ttu-id="42b22-752">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-753">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-754">'Blazor'</span></span>
- <span data-ttu-id="42b22-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-755">'Identity'</span></span>
- <span data-ttu-id="42b22-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-757">'Razor'</span></span>
- <span data-ttu-id="42b22-758">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-759">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-760">'Blazor'</span></span>
- <span data-ttu-id="42b22-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-761">'Identity'</span></span>
- <span data-ttu-id="42b22-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-763">'Razor'</span></span>
- <span data-ttu-id="42b22-764">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-765">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-766">'Blazor'</span></span>
- <span data-ttu-id="42b22-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-767">'Identity'</span></span>
- <span data-ttu-id="42b22-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-769">'Razor'</span></span>
- <span data-ttu-id="42b22-770">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-771">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-772">'Blazor'</span></span>
- <span data-ttu-id="42b22-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-773">'Identity'</span></span>
- <span data-ttu-id="42b22-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-775">'Razor'</span></span>
- <span data-ttu-id="42b22-776">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-777">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-778">'Blazor'</span></span>
- <span data-ttu-id="42b22-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-779">'Identity'</span></span>
- <span data-ttu-id="42b22-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-781">'Razor'</span></span>
- <span data-ttu-id="42b22-782">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-783">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-784">'Blazor'</span></span>
- <span data-ttu-id="42b22-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-785">'Identity'</span></span>
- <span data-ttu-id="42b22-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-787">'Razor'</span></span>
- <span data-ttu-id="42b22-788">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-789">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-790">'Blazor'</span></span>
- <span data-ttu-id="42b22-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-791">'Identity'</span></span>
- <span data-ttu-id="42b22-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-793">'Razor'</span></span>
- <span data-ttu-id="42b22-794">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-795">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-796">'Blazor'</span></span>
- <span data-ttu-id="42b22-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-797">'Identity'</span></span>
- <span data-ttu-id="42b22-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-799">'Razor'</span></span>
- <span data-ttu-id="42b22-800">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-801">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-802">'Blazor'</span></span>
- <span data-ttu-id="42b22-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-803">'Identity'</span></span>
- <span data-ttu-id="42b22-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-805">'Razor'</span></span>
- <span data-ttu-id="42b22-806">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-806">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="42b22-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="42b22-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="42b22-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="42b22-809">Hodnota: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="42b22-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="42b22-810">Hodnota: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="42b22-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="42b22-811">Osa`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="42b22-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="42b22-812">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="42b22-812">JSON configuration provider</span></span>

<span data-ttu-id="42b22-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načte konfiguraci z párů klíč-hodnota souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="42b22-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="42b22-814">Přetížení můžou specifikovat:</span><span class="sxs-lookup"><span data-stu-id="42b22-814">Overloads can specify:</span></span>

* <span data-ttu-id="42b22-815">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="42b22-815">Whether the file is optional.</span></span>
* <span data-ttu-id="42b22-816">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="42b22-817">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="42b22-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="42b22-818">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="42b22-818">The preceding code:</span></span>

* <span data-ttu-id="42b22-819">Nakonfiguruje zprostředkovatele konfigurace JSON pro načtení souboru *MyConfig. JSON* s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="42b22-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="42b22-820">`optional: true`: Soubor je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="42b22-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="42b22-821">`reloadOnChange: true`: Soubor se znovu načte při uložení změn.</span><span class="sxs-lookup"><span data-stu-id="42b22-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="42b22-822">Přečte [výchozí poskytovatele konfigurace](#default) před souborem *MyConfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="42b22-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="42b22-823">Nastavení v přepsání souboru *MyConfig. JSON* ve výchozích poskytovatelích konfigurace, včetně [poskytovatele konfigurace proměnných prostředí](#evcp) a [poskytovatele konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="42b22-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="42b22-824">***Obvykle nechcete, aby vlastní*** soubor JSON přepsal hodnoty nastavené ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a v [poskytovateli konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="42b22-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="42b22-825">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="42b22-826">V předchozím kódu se jedná o nastavení v *MyConfig. JSON* a *MyConfig*. `Environment` . soubory *JSON* :</span><span class="sxs-lookup"><span data-stu-id="42b22-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="42b22-827">Přepsat nastavení v souboru *appSettings. JSON* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="42b22-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="42b22-828">Jsou přepsány nastavením ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a [poskytovatelem konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="42b22-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="42b22-829">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyConfig. JSON* :</span><span class="sxs-lookup"><span data-stu-id="42b22-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="42b22-830">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="42b22-831">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="42b22-831">File configuration provider</span></span>

<span data-ttu-id="42b22-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="42b22-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="42b22-833">Následující poskytovatelé konfigurace jsou odvozeni z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="42b22-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="42b22-834">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="42b22-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="42b22-835">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="42b22-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="42b22-836">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="42b22-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="42b22-837">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="42b22-837">INI configuration provider</span></span>

<span data-ttu-id="42b22-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="42b22-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="42b22-839">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="42b22-840">V předchozím kódu jsou nastavení v *souboru MyIniConfig. ini* a *MyIniConfig*. `Environment` . soubory *ini* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="42b22-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="42b22-841">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="42b22-842">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="42b22-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="42b22-843">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="42b22-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="42b22-844">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="42b22-845">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="42b22-845">XML configuration provider</span></span>

<span data-ttu-id="42b22-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="42b22-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="42b22-847">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="42b22-848">V předchozím kódu, nastavení v *souboru MyXMLFile. XML* a *MyXMLFile*. `Environment` . soubory *XML* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="42b22-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="42b22-849">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="42b22-850">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="42b22-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="42b22-851">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="42b22-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="42b22-852">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-853">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="42b22-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="42b22-854">Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:</span><span class="sxs-lookup"><span data-stu-id="42b22-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-855">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="42b22-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="42b22-856">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="42b22-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="42b22-857">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="42b22-857">key:attribute</span></span>
* <span data-ttu-id="42b22-858">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="42b22-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="42b22-859">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="42b22-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="42b22-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="42b22-861">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-861">The key is the file name.</span></span> <span data-ttu-id="42b22-862">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-862">The value contains the file's contents.</span></span> <span data-ttu-id="42b22-863">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="42b22-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="42b22-864">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42b22-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="42b22-865">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="42b22-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="42b22-866">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="42b22-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="42b22-867">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="42b22-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="42b22-868">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="42b22-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="42b22-869">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="42b22-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="42b22-870">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="42b22-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="42b22-871">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="42b22-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="42b22-872">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="42b22-872">Memory configuration provider</span></span>

<span data-ttu-id="42b22-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="42b22-874">Následující kód přidá kolekci paměti do konfiguračního systému:</span><span class="sxs-lookup"><span data-stu-id="42b22-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="42b22-875">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-876">V předchozím kódu `config.AddInMemoryCollection(Dict)` se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="42b22-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="42b22-877">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="42b22-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="42b22-878">Viz [vazba pole](#boa) pro jiný příklad pomocí `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="42b22-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="42b22-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="42b22-879">GetValue</span></span>

<span data-ttu-id="42b22-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:</span><span class="sxs-lookup"><span data-stu-id="42b22-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-881">Pokud v předchozím kódu `NumberKey` není v konfiguraci nalezeno, `99` je použita výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="42b22-882">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="42b22-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="42b22-883">V následujících příkladech zvažte následující soubor *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="42b22-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="42b22-884">Následující kód přidá *MySubsection. JSON* ke zprostředkovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="42b22-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="42b22-885">GetSection</span></span>

<span data-ttu-id="42b22-886">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí dílčí část konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="42b22-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="42b22-887">Následující kód vrátí hodnoty pro `section1` :</span><span class="sxs-lookup"><span data-stu-id="42b22-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-888">Následující kód vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="42b22-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-889">`GetSection`nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="42b22-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="42b22-890">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="42b22-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="42b22-891">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="42b22-892"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="42b22-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="42b22-893">GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="42b22-893">GetChildren and Exists</span></span>

<span data-ttu-id="42b22-894">Následující kód volá [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="42b22-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-895">Předchozí kód volá [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pro ověření, zda existuje oddíl:</span><span class="sxs-lookup"><span data-stu-id="42b22-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="42b22-896">Vazba pole</span><span class="sxs-lookup"><span data-stu-id="42b22-896">Bind an array</span></span>

<span data-ttu-id="42b22-897">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="42b22-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="42b22-898">Libovolný formát pole, který zveřejňuje numerický klíčový segment, je schopný vytvořit vazbu pole k poli třídy [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="42b22-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="42b22-899">V [ukázkovém stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)zvažte *myArray. JSON* :</span><span class="sxs-lookup"><span data-stu-id="42b22-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="42b22-900">Následující kód přidá *myArray. JSON* ke zprostředkovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="42b22-901">Následující kód přečte konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="42b22-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-902">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="42b22-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="42b22-903">V předchozím výstupu má index 3 hodnotu odpovídající hodnotě `value40` `"4": "value40",` v *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="42b22-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="42b22-904">Vazby vázaného pole jsou souvislé a nejsou vázané na index konfiguračního klíče.</span><span class="sxs-lookup"><span data-stu-id="42b22-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="42b22-905">Pořadač konfigurace nemůže svázat hodnoty null ani vytvářet položky null ve vázaných objektech.</span><span class="sxs-lookup"><span data-stu-id="42b22-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="42b22-906">Následující kód načte `array:entries` konfiguraci pomocí <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="42b22-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="42b22-907">Následující kód přečte konfiguraci v `arrayDict` `Dictionary` a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="42b22-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-908">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="42b22-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="42b22-909">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="42b22-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="42b22-910">Když jsou data konfigurace obsahující pole svázané, používají se k iteraci konfiguračních dat při vytváření objektu pole indexy v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="42b22-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="42b22-911">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="42b22-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="42b22-912">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na instanci doplní `ArrayExample` jakýmkoli poskytovatelem konfigurace, který načte &num; dvojici klíč/hodnota indexu 3.</span><span class="sxs-lookup"><span data-stu-id="42b22-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="42b22-913">V ukázkovém stažení zvažte následující soubor *hodnota3. JSON* :</span><span class="sxs-lookup"><span data-stu-id="42b22-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="42b22-914">Následující kód obsahuje konfiguraci pro *hodnota3. JSON* a `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="42b22-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="42b22-915">Následující kód přečte předchozí konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="42b22-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-916">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="42b22-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="42b22-917">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="42b22-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="42b22-918">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="42b22-918">Custom configuration provider</span></span>

<span data-ttu-id="42b22-919">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="42b22-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="42b22-920">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="42b22-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="42b22-921">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="42b22-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="42b22-922">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="42b22-923">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="42b22-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="42b22-924">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="42b22-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="42b22-925">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="42b22-926">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="42b22-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="42b22-927">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="42b22-928">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="42b22-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="42b22-929">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="42b22-930">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="42b22-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="42b22-931">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="42b22-932">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="42b22-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="42b22-933">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="42b22-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="42b22-934">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="42b22-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="42b22-935">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="42b22-936">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="42b22-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="42b22-937">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="42b22-938">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="42b22-939">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="42b22-939">Access configuration in Startup</span></span>

<span data-ttu-id="42b22-940">Následující kód zobrazuje konfigurační data v `Startup` metodách:</span><span class="sxs-lookup"><span data-stu-id="42b22-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="42b22-941">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="42b22-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="42b22-942">Přístup ke konfiguraci na Razor stránkách</span><span class="sxs-lookup"><span data-stu-id="42b22-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="42b22-943">Následující kód zobrazuje konfigurační data na Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="42b22-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="42b22-944">V následujícím kódu `MyOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="42b22-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="42b22-945">Následující kód používá [`@inject`](xref:mvc/views/razor#inject) Razor direktivu k vyřešení a zobrazení hodnot možností:</span><span class="sxs-lookup"><span data-stu-id="42b22-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="42b22-946">Přístup ke konfiguraci v souboru zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="42b22-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="42b22-947">Následující kód zobrazuje konfigurační data v zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="42b22-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="42b22-948">Konfigurace možností s delegátem</span><span class="sxs-lookup"><span data-stu-id="42b22-948">Configure options with a delegate</span></span>

<span data-ttu-id="42b22-949">Možnosti nakonfigurované v delegátech přepíší hodnoty nastavené ve zprostředkovatelích konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="42b22-950">Konfigurace možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="42b22-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="42b22-951">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je služba přidána do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="42b22-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="42b22-952">Pomocí delegáta konfiguruje hodnoty pro `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="42b22-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="42b22-953">Následující kód zobrazí hodnoty možností:</span><span class="sxs-lookup"><span data-stu-id="42b22-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="42b22-954">V předchozím příkladu `Option1` jsou hodnoty a `Option2` zadány v souboru *appSettings. JSON* a následně přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="42b22-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="42b22-955">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="42b22-955">Host versus app configuration</span></span>

<span data-ttu-id="42b22-956">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="42b22-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="42b22-957">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="42b22-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="42b22-958">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="42b22-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="42b22-959">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="42b22-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="42b22-960">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="42b22-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="42b22-961">Výchozí konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="42b22-961">Default host configuration</span></span>

<span data-ttu-id="42b22-962">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="42b22-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="42b22-963">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="42b22-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="42b22-964">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="42b22-965">Předpona ( `DOTNET_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="42b22-966">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="42b22-967">Je navázána výchozí konfigurace webového hostitele ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="42b22-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="42b22-968">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="42b22-969">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="42b22-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="42b22-970">Přidejte middleware předávaných hlaviček, pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="42b22-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="42b22-971">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="42b22-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="42b22-972">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="42b22-972">Other configuration</span></span>

<span data-ttu-id="42b22-973">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="42b22-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="42b22-974">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="42b22-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="42b22-975">*Launch. JSON* / *launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané:</span><span class="sxs-lookup"><span data-stu-id="42b22-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="42b22-976">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="42b22-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="42b22-977">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="42b22-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="42b22-978">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="42b22-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="42b22-979">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="42b22-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="42b22-980">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="42b22-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="42b22-981"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="42b22-982">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="42b22-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42b22-983">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="42b22-983">Additional resources</span></span>

* [<span data-ttu-id="42b22-984">Zdrojový kód konfigurace</span><span class="sxs-lookup"><span data-stu-id="42b22-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42b22-985">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="42b22-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="42b22-986">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="42b22-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="42b22-987">Azure Key Vault</span></span>
* <span data-ttu-id="42b22-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="42b22-988">Azure App Configuration</span></span>
* <span data-ttu-id="42b22-989">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="42b22-989">Command-line arguments</span></span>
* <span data-ttu-id="42b22-990">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="42b22-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="42b22-991">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="42b22-991">Directory files</span></span>
* <span data-ttu-id="42b22-992">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-992">Environment variables</span></span>
* <span data-ttu-id="42b22-993">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="42b22-993">In-memory .NET objects</span></span>
* <span data-ttu-id="42b22-994">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="42b22-994">Settings files</span></span>

<span data-ttu-id="42b22-995">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="42b22-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="42b22-996">Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="42b22-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="42b22-997">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="42b22-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="42b22-998">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="42b22-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="42b22-999">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="42b22-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="42b22-1000">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42b22-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="42b22-1001">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="42b22-1001">Host versus app configuration</span></span>

<span data-ttu-id="42b22-1002">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="42b22-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="42b22-1003">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="42b22-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="42b22-1004">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="42b22-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="42b22-1005">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="42b22-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="42b22-1006">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="42b22-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="42b22-1007">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="42b22-1007">Other configuration</span></span>

<span data-ttu-id="42b22-1008">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="42b22-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="42b22-1009">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="42b22-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="42b22-1010">*Launch. JSON* / *launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané:</span><span class="sxs-lookup"><span data-stu-id="42b22-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="42b22-1011">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="42b22-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="42b22-1012">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="42b22-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="42b22-1013">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="42b22-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="42b22-1014">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="42b22-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="42b22-1015">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="42b22-1015">Default configuration</span></span>

<span data-ttu-id="42b22-1016">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="42b22-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="42b22-1017">`CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="42b22-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="42b22-1018">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="42b22-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="42b22-1019">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="42b22-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="42b22-1020">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="42b22-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="42b22-1021">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="42b22-1022">Předpona ( `ASPNETCORE_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="42b22-1023">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="42b22-1024">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="42b22-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="42b22-1025">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="42b22-1026">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="42b22-1027">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v `Development` prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="42b22-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="42b22-1028">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="42b22-1029">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="42b22-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="42b22-1030">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="42b22-1030">Security</span></span>

<span data-ttu-id="42b22-1031">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="42b22-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="42b22-1032">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="42b22-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="42b22-1033">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="42b22-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="42b22-1034">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="42b22-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="42b22-1035">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="42b22-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="42b22-1036"><xref:security/app-secrets>&ndash;Obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="42b22-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="42b22-1037">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="42b22-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="42b22-1038">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="42b22-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="42b22-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="42b22-1040">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="42b22-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="42b22-1041">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="42b22-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="42b22-1042">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="42b22-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="42b22-1043">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="42b22-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="42b22-1044">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="42b22-1045">Oddíly a klíče jsou shrnuty s použitím dvojtečky ( `:` ) pro zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="42b22-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="42b22-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-1046">section0:key0</span></span>
* <span data-ttu-id="42b22-1047">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-1047">section0:key1</span></span>
* <span data-ttu-id="42b22-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-1048">section1:key0</span></span>
* <span data-ttu-id="42b22-1049">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-1049">section1:key1</span></span>

<span data-ttu-id="42b22-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="42b22-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="42b22-1051">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="42b22-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="42b22-1052">Konvence</span><span class="sxs-lookup"><span data-stu-id="42b22-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="42b22-1053">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="42b22-1053">Sources and providers</span></span>

<span data-ttu-id="42b22-1054">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="42b22-1055">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="42b22-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="42b22-1056">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="42b22-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="42b22-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="42b22-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>lze vložit do Razor stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pro získání konfigurace pro třídu.</span><span class="sxs-lookup"><span data-stu-id="42b22-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="42b22-1059">V následujících příkladech se `_config` pole používá pro přístup k hodnotám konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="42b22-1060">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="42b22-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="42b22-1061">Klíče</span><span class="sxs-lookup"><span data-stu-id="42b22-1061">Keys</span></span>

<span data-ttu-id="42b22-1062">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="42b22-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="42b22-1063">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="42b22-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="42b22-1064">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="42b22-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="42b22-1065">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="42b22-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="42b22-1066">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="42b22-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="42b22-1067">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="42b22-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="42b22-1068">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="42b22-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="42b22-1069">Dvojité podtržítko ( `__` ) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="42b22-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="42b22-1070">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="42b22-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="42b22-1071">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="42b22-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="42b22-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="42b22-1073">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="42b22-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="42b22-1074">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="42b22-1074">Values</span></span>

<span data-ttu-id="42b22-1075">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="42b22-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="42b22-1076">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="42b22-1076">Values are strings.</span></span>
* <span data-ttu-id="42b22-1077">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="42b22-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="42b22-1078">Poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="42b22-1078">Providers</span></span>

<span data-ttu-id="42b22-1079">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="42b22-1080">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="42b22-1080">Provider</span></span> | <span data-ttu-id="42b22-1081">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="42b22-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="42b22-1082">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1083">'Blazor'</span></span>
- <span data-ttu-id="42b22-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1084">'Identity'</span></span>
- <span data-ttu-id="42b22-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1086">'Razor'</span></span>
- <span data-ttu-id="42b22-1087">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1088">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1089">'Blazor'</span></span>
- <span data-ttu-id="42b22-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1090">'Identity'</span></span>
- <span data-ttu-id="42b22-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1092">'Razor'</span></span>
- <span data-ttu-id="42b22-1093">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1094">---- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1095">'Blazor'</span></span>
- <span data-ttu-id="42b22-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1096">'Identity'</span></span>
- <span data-ttu-id="42b22-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1098">'Razor'</span></span>
- <span data-ttu-id="42b22-1099">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1100">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1101">'Blazor'</span></span>
- <span data-ttu-id="42b22-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1102">'Identity'</span></span>
- <span data-ttu-id="42b22-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1104">'Razor'</span></span>
- <span data-ttu-id="42b22-1105">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1106">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1107">'Blazor'</span></span>
- <span data-ttu-id="42b22-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1108">'Identity'</span></span>
- <span data-ttu-id="42b22-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1110">'Razor'</span></span>
- <span data-ttu-id="42b22-1111">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1112">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1113">'Blazor'</span></span>
- <span data-ttu-id="42b22-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1114">'Identity'</span></span>
- <span data-ttu-id="42b22-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1116">'Razor'</span></span>
- <span data-ttu-id="42b22-1117">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1118">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1119">'Blazor'</span></span>
- <span data-ttu-id="42b22-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1120">'Identity'</span></span>
- <span data-ttu-id="42b22-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1122">'Razor'</span></span>
- <span data-ttu-id="42b22-1123">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1124">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1125">'Blazor'</span></span>
- <span data-ttu-id="42b22-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1126">'Identity'</span></span>
- <span data-ttu-id="42b22-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1128">'Razor'</span></span>
- <span data-ttu-id="42b22-1129">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1130">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1131">'Blazor'</span></span>
- <span data-ttu-id="42b22-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1132">'Identity'</span></span>
- <span data-ttu-id="42b22-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1134">'Razor'</span></span>
- <span data-ttu-id="42b22-1135">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1136">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1137">'Blazor'</span></span>
- <span data-ttu-id="42b22-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1138">'Identity'</span></span>
- <span data-ttu-id="42b22-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1140">'Razor'</span></span>
- <span data-ttu-id="42b22-1141">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1142">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1143">'Blazor'</span></span>
- <span data-ttu-id="42b22-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1144">'Identity'</span></span>
- <span data-ttu-id="42b22-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1146">'Razor'</span></span>
- <span data-ttu-id="42b22-1147">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1148">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1149">'Blazor'</span></span>
- <span data-ttu-id="42b22-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1150">'Identity'</span></span>
- <span data-ttu-id="42b22-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1152">'Razor'</span></span>
- <span data-ttu-id="42b22-1153">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1154">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1155">'Blazor'</span></span>
- <span data-ttu-id="42b22-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1156">'Identity'</span></span>
- <span data-ttu-id="42b22-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1158">'Razor'</span></span>
- <span data-ttu-id="42b22-1159">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1160">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1161">'Blazor'</span></span>
- <span data-ttu-id="42b22-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1162">'Identity'</span></span>
- <span data-ttu-id="42b22-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1164">'Razor'</span></span>
- <span data-ttu-id="42b22-1165">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1166">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1167">'Blazor'</span></span>
- <span data-ttu-id="42b22-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1168">'Identity'</span></span>
- <span data-ttu-id="42b22-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1170">'Razor'</span></span>
- <span data-ttu-id="42b22-1171">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1172">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1173">'Blazor'</span></span>
- <span data-ttu-id="42b22-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1174">'Identity'</span></span>
- <span data-ttu-id="42b22-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1176">'Razor'</span></span>
- <span data-ttu-id="42b22-1177">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1178">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1179">'Blazor'</span></span>
- <span data-ttu-id="42b22-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1180">'Identity'</span></span>
- <span data-ttu-id="42b22-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1182">'Razor'</span></span>
- <span data-ttu-id="42b22-1183">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1184">------------------ | | [Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* ) | Azure Key Vault | | [Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure) | Konfigurace aplikace Azure | | [Poskytovatel konfigurace příkazového řádku](#command-line-configuration-provider) | Parametry příkazového řádku | | [Vlastní poskytovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj | | [Poskytovatel konfigurace proměnných prostředí](#environment-variables-configuration-provider) | Proměnné prostředí | | [Poskytovatel konfigurace souboru](#file-configuration-provider) | Soubory (INI, JSON, XML) | | [Poskytovatel konfigurace klíče na soubor](#key-per-file-configuration-provider) | Soubory adresáře | | [Poskytovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti | | [Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata) | Soubor v adresáři profilu uživatele |</span><span class="sxs-lookup"><span data-stu-id="42b22-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="42b22-1185">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="42b22-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="42b22-1186">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="42b22-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="42b22-1187">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="42b22-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="42b22-1188">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="42b22-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="42b22-1189">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace</span><span class="sxs-lookup"><span data-stu-id="42b22-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="42b22-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="42b22-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="42b22-1191">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="42b22-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="42b22-1192">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-1192">Environment variables</span></span>
1. <span data-ttu-id="42b22-1193">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="42b22-1193">Command-line arguments</span></span>

<span data-ttu-id="42b22-1194">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="42b22-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="42b22-1195">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="42b22-1196">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="42b22-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="42b22-1197">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="42b22-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="42b22-1198">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> na tvůrce hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="42b22-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="42b22-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="42b22-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="42b22-1200">Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s automaticky přidanými uživateli `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="42b22-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="42b22-1201">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="42b22-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="42b22-1202">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte jako `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="42b22-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="42b22-1203">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="42b22-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="42b22-1204">Chcete-li odebrat poskytovatele přidaných nástrojem `CreateDefaultBuilder` , zavolejte nejprve [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="42b22-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="42b22-1205">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="42b22-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="42b22-1206">Konfigurace dodaná do aplikace v nástroji `ConfigureAppConfiguration` je k dispozici během spouštění aplikace, včetně `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="42b22-1207">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="42b22-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="42b22-1208">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="42b22-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="42b22-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="42b22-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="42b22-1210">Chcete-li aktivovat konfiguraci příkazového řádku, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> je metoda rozšíření volána na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42b22-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="42b22-1211">`AddCommandLine`je automaticky volána `CreateDefaultBuilder(string [])` , když je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="42b22-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="42b22-1212">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="42b22-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="42b22-1213">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="42b22-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="42b22-1214">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="42b22-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="42b22-1215">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="42b22-1216">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-1216">Environment variables.</span></span>

<span data-ttu-id="42b22-1217">`CreateDefaultBuilder`přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="42b22-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="42b22-1218">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="42b22-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="42b22-1219">`CreateDefaultBuilder`funguje, když je hostitel vytvořen.</span><span class="sxs-lookup"><span data-stu-id="42b22-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="42b22-1220">Proto může konfigurace z příkazového řádku aktivované nástrojem `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="42b22-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="42b22-1221">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány pomocí `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="42b22-1222">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte na `AddCommandLine` Poslední.</span><span class="sxs-lookup"><span data-stu-id="42b22-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="42b22-1223">**Případě**</span><span class="sxs-lookup"><span data-stu-id="42b22-1223">**Example**</span></span>

<span data-ttu-id="42b22-1224">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="42b22-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="42b22-1225">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="42b22-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="42b22-1226">Zadejte do příkazu argument příkazového řádku `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="42b22-1227">Po spuštění aplikace otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="42b22-1228">Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k dispozici pro příkaz `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="42b22-1229">Argumenty</span><span class="sxs-lookup"><span data-stu-id="42b22-1229">Arguments</span></span>

<span data-ttu-id="42b22-1230">Hodnota musí následovat po znaménku rovná se ( `=` ), nebo klíč musí obsahovat předponu ( `--` nebo `/` ), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="42b22-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="42b22-1231">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="42b22-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="42b22-1232">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="42b22-1232">Key prefix</span></span>               | <span data-ttu-id="42b22-1233">Příklad</span><span class="sxs-lookup"><span data-stu-id="42b22-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="42b22-1234">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1235">'Blazor'</span></span>
- <span data-ttu-id="42b22-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1236">'Identity'</span></span>
- <span data-ttu-id="42b22-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1238">'Razor'</span></span>
- <span data-ttu-id="42b22-1239">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1240">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1241">'Blazor'</span></span>
- <span data-ttu-id="42b22-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1242">'Identity'</span></span>
- <span data-ttu-id="42b22-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1244">'Razor'</span></span>
- <span data-ttu-id="42b22-1245">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1246">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1247">'Blazor'</span></span>
- <span data-ttu-id="42b22-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1248">'Identity'</span></span>
- <span data-ttu-id="42b22-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1250">'Razor'</span></span>
- <span data-ttu-id="42b22-1251">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1252">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1253">'Blazor'</span></span>
- <span data-ttu-id="42b22-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1254">'Identity'</span></span>
- <span data-ttu-id="42b22-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1256">'Razor'</span></span>
- <span data-ttu-id="42b22-1257">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1258">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1259">'Blazor'</span></span>
- <span data-ttu-id="42b22-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1260">'Identity'</span></span>
- <span data-ttu-id="42b22-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1262">'Razor'</span></span>
- <span data-ttu-id="42b22-1263">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1264">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1265">'Blazor'</span></span>
- <span data-ttu-id="42b22-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1266">'Identity'</span></span>
- <span data-ttu-id="42b22-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1268">'Razor'</span></span>
- <span data-ttu-id="42b22-1269">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1270">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1271">'Blazor'</span></span>
- <span data-ttu-id="42b22-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1272">'Identity'</span></span>
- <span data-ttu-id="42b22-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1274">'Razor'</span></span>
- <span data-ttu-id="42b22-1275">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1276">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1277">'Blazor'</span></span>
- <span data-ttu-id="42b22-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1278">'Identity'</span></span>
- <span data-ttu-id="42b22-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1280">'Razor'</span></span>
- <span data-ttu-id="42b22-1281">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1282">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1283">'Blazor'</span></span>
- <span data-ttu-id="42b22-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1284">'Identity'</span></span>
- <span data-ttu-id="42b22-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1286">'Razor'</span></span>
- <span data-ttu-id="42b22-1287">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1288">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1289">'Blazor'</span></span>
- <span data-ttu-id="42b22-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1290">'Identity'</span></span>
- <span data-ttu-id="42b22-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1292">'Razor'</span></span>
- <span data-ttu-id="42b22-1293">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1294">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1295">'Blazor'</span></span>
- <span data-ttu-id="42b22-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1296">'Identity'</span></span>
- <span data-ttu-id="42b22-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1298">'Razor'</span></span>
- <span data-ttu-id="42b22-1299">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1300">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1301">'Blazor'</span></span>
- <span data-ttu-id="42b22-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1302">'Identity'</span></span>
- <span data-ttu-id="42b22-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1304">'Razor'</span></span>
- <span data-ttu-id="42b22-1305">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1306">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1307">'Blazor'</span></span>
- <span data-ttu-id="42b22-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1308">'Identity'</span></span>
- <span data-ttu-id="42b22-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1310">'Razor'</span></span>
- <span data-ttu-id="42b22-1311">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1312">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1313">'Blazor'</span></span>
- <span data-ttu-id="42b22-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1314">'Identity'</span></span>
- <span data-ttu-id="42b22-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1316">'Razor'</span></span>
- <span data-ttu-id="42b22-1317">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1318">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1319">'Blazor'</span></span>
- <span data-ttu-id="42b22-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1320">'Identity'</span></span>
- <span data-ttu-id="42b22-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1322">'Razor'</span></span>
- <span data-ttu-id="42b22-1323">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1324">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1325">'Blazor'</span></span>
- <span data-ttu-id="42b22-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1326">'Identity'</span></span>
- <span data-ttu-id="42b22-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1328">'Razor'</span></span>
- <span data-ttu-id="42b22-1329">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1330">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1331">'Blazor'</span></span>
- <span data-ttu-id="42b22-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1332">'Identity'</span></span>
- <span data-ttu-id="42b22-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1334">'Razor'</span></span>
- <span data-ttu-id="42b22-1335">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1336">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1337">'Blazor'</span></span>
- <span data-ttu-id="42b22-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1338">'Identity'</span></span>
- <span data-ttu-id="42b22-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1340">'Razor'</span></span>
- <span data-ttu-id="42b22-1341">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1342">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1343">'Blazor'</span></span>
- <span data-ttu-id="42b22-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1344">'Identity'</span></span>
- <span data-ttu-id="42b22-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1346">'Razor'</span></span>
- <span data-ttu-id="42b22-1347">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1348">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1349">'Blazor'</span></span>
- <span data-ttu-id="42b22-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1350">'Identity'</span></span>
- <span data-ttu-id="42b22-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1352">'Razor'</span></span>
- <span data-ttu-id="42b22-1353">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1354">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1355">'Blazor'</span></span>
- <span data-ttu-id="42b22-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1356">'Identity'</span></span>
- <span data-ttu-id="42b22-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1358">'Razor'</span></span>
- <span data-ttu-id="42b22-1359">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1360">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1361">'Blazor'</span></span>
- <span data-ttu-id="42b22-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1362">'Identity'</span></span>
- <span data-ttu-id="42b22-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1364">'Razor'</span></span>
- <span data-ttu-id="42b22-1365">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1366">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1367">'Blazor'</span></span>
- <span data-ttu-id="42b22-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1368">'Identity'</span></span>
- <span data-ttu-id="42b22-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1370">'Razor'</span></span>
- <span data-ttu-id="42b22-1371">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1372">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1373">'Blazor'</span></span>
- <span data-ttu-id="42b22-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1374">'Identity'</span></span>
- <span data-ttu-id="42b22-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1376">'Razor'</span></span>
- <span data-ttu-id="42b22-1377">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1378">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1379">'Blazor'</span></span>
- <span data-ttu-id="42b22-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1380">'Identity'</span></span>
- <span data-ttu-id="42b22-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1382">'Razor'</span></span>
- <span data-ttu-id="42b22-1383">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1384">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1385">'Blazor'</span></span>
- <span data-ttu-id="42b22-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1386">'Identity'</span></span>
- <span data-ttu-id="42b22-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1388">'Razor'</span></span>
- <span data-ttu-id="42b22-1389">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1390">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1391">'Blazor'</span></span>
- <span data-ttu-id="42b22-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1392">'Identity'</span></span>
- <span data-ttu-id="42b22-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1394">'Razor'</span></span>
- <span data-ttu-id="42b22-1395">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1396">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1397">'Blazor'</span></span>
- <span data-ttu-id="42b22-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1398">'Identity'</span></span>
- <span data-ttu-id="42b22-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1400">'Razor'</span></span>
- <span data-ttu-id="42b22-1401">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1402">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1403">'Blazor'</span></span>
- <span data-ttu-id="42b22-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1404">'Identity'</span></span>
- <span data-ttu-id="42b22-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1406">'Razor'</span></span>
- <span data-ttu-id="42b22-1407">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1408">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1409">'Blazor'</span></span>
- <span data-ttu-id="42b22-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1410">'Identity'</span></span>
- <span data-ttu-id="42b22-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1412">'Razor'</span></span>
- <span data-ttu-id="42b22-1413">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1414">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1415">'Blazor'</span></span>
- <span data-ttu-id="42b22-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1416">'Identity'</span></span>
- <span data-ttu-id="42b22-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1418">'Razor'</span></span>
- <span data-ttu-id="42b22-1419">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1420">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1421">'Blazor'</span></span>
- <span data-ttu-id="42b22-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1422">'Identity'</span></span>
- <span data-ttu-id="42b22-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1424">'Razor'</span></span>
- <span data-ttu-id="42b22-1425">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1426">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1427">'Blazor'</span></span>
- <span data-ttu-id="42b22-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1428">'Identity'</span></span>
- <span data-ttu-id="42b22-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1430">'Razor'</span></span>
- <span data-ttu-id="42b22-1431">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1432">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1433">'Blazor'</span></span>
- <span data-ttu-id="42b22-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1434">'Identity'</span></span>
- <span data-ttu-id="42b22-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1436">'Razor'</span></span>
- <span data-ttu-id="42b22-1437">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1438">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1439">'Blazor'</span></span>
- <span data-ttu-id="42b22-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1440">'Identity'</span></span>
- <span data-ttu-id="42b22-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1442">'Razor'</span></span>
- <span data-ttu-id="42b22-1443">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1444">--------------------------- | | Bez předpony | `CommandLineKey1=value1`                               |
| Dvě pomlčky ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Lomítko ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="42b22-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="42b22-1445">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="42b22-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="42b22-1446">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="42b22-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="42b22-1447">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="42b22-1447">Switch mappings</span></span>

<span data-ttu-id="42b22-1448">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="42b22-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="42b22-1449">Při ručním sestavování konfigurace pomocí nástroje <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> Zadejte slovník přepínačů pro vložení do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="42b22-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="42b22-1450">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="42b22-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="42b22-1451">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="42b22-1452">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="42b22-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="42b22-1453">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="42b22-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="42b22-1454">Přepínače musí začínat spojovníkem ( `-` ) nebo dvojitou čárkou ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="42b22-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="42b22-1455">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="42b22-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="42b22-1456">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="42b22-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="42b22-1457">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="42b22-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="42b22-1458">Po sestavení hostitele zavolejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="42b22-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="42b22-1459">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="42b22-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="42b22-1460">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="42b22-1461">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="42b22-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="42b22-1462">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="42b22-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="42b22-1463">Klíč</span><span class="sxs-lookup"><span data-stu-id="42b22-1463">Key</span></span>       | <span data-ttu-id="42b22-1464">Hodnota</span><span class="sxs-lookup"><span data-stu-id="42b22-1464">Value</span></span>             |
| ---
<span data-ttu-id="42b22-1465">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1466">'Blazor'</span></span>
- <span data-ttu-id="42b22-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1467">'Identity'</span></span>
- <span data-ttu-id="42b22-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1469">'Razor'</span></span>
- <span data-ttu-id="42b22-1470">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1471">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1472">'Blazor'</span></span>
- <span data-ttu-id="42b22-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1473">'Identity'</span></span>
- <span data-ttu-id="42b22-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1475">'Razor'</span></span>
- <span data-ttu-id="42b22-1476">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1477">----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1478">'Blazor'</span></span>
- <span data-ttu-id="42b22-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1479">'Identity'</span></span>
- <span data-ttu-id="42b22-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1481">'Razor'</span></span>
- <span data-ttu-id="42b22-1482">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1483">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1484">'Blazor'</span></span>
- <span data-ttu-id="42b22-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1485">'Identity'</span></span>
- <span data-ttu-id="42b22-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1487">'Razor'</span></span>
- <span data-ttu-id="42b22-1488">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1489">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1490">'Blazor'</span></span>
- <span data-ttu-id="42b22-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1491">'Identity'</span></span>
- <span data-ttu-id="42b22-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1493">'Razor'</span></span>
- <span data-ttu-id="42b22-1494">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1495">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1496">'Blazor'</span></span>
- <span data-ttu-id="42b22-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1497">'Identity'</span></span>
- <span data-ttu-id="42b22-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1499">'Razor'</span></span>
- <span data-ttu-id="42b22-1500">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1501">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1502">'Blazor'</span></span>
- <span data-ttu-id="42b22-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1503">'Identity'</span></span>
- <span data-ttu-id="42b22-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1505">'Razor'</span></span>
- <span data-ttu-id="42b22-1506">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1507">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1508">'Blazor'</span></span>
- <span data-ttu-id="42b22-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1509">'Identity'</span></span>
- <span data-ttu-id="42b22-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1511">'Razor'</span></span>
- <span data-ttu-id="42b22-1512">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="42b22-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="42b22-1514">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="42b22-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="42b22-1515">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="42b22-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="42b22-1516">Klíč</span><span class="sxs-lookup"><span data-stu-id="42b22-1516">Key</span></span>               | <span data-ttu-id="42b22-1517">Hodnota</span><span class="sxs-lookup"><span data-stu-id="42b22-1517">Value</span></span>    |
| ---
<span data-ttu-id="42b22-1518">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1519">'Blazor'</span></span>
- <span data-ttu-id="42b22-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1520">'Identity'</span></span>
- <span data-ttu-id="42b22-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1522">'Razor'</span></span>
- <span data-ttu-id="42b22-1523">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1524">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1525">'Blazor'</span></span>
- <span data-ttu-id="42b22-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1526">'Identity'</span></span>
- <span data-ttu-id="42b22-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1528">'Razor'</span></span>
- <span data-ttu-id="42b22-1529">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1530">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1531">'Blazor'</span></span>
- <span data-ttu-id="42b22-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1532">'Identity'</span></span>
- <span data-ttu-id="42b22-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1534">'Razor'</span></span>
- <span data-ttu-id="42b22-1535">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1536">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1537">'Blazor'</span></span>
- <span data-ttu-id="42b22-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1538">'Identity'</span></span>
- <span data-ttu-id="42b22-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1540">'Razor'</span></span>
- <span data-ttu-id="42b22-1541">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1542">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1543">'Blazor'</span></span>
- <span data-ttu-id="42b22-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1544">'Identity'</span></span>
- <span data-ttu-id="42b22-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1546">'Razor'</span></span>
- <span data-ttu-id="42b22-1547">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1548">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1549">'Blazor'</span></span>
- <span data-ttu-id="42b22-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1550">'Identity'</span></span>
- <span data-ttu-id="42b22-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1552">'Razor'</span></span>
- <span data-ttu-id="42b22-1553">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1554">--------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1555">'Blazor'</span></span>
- <span data-ttu-id="42b22-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1556">'Identity'</span></span>
- <span data-ttu-id="42b22-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1558">'Razor'</span></span>
- <span data-ttu-id="42b22-1559">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1560">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1561">'Blazor'</span></span>
- <span data-ttu-id="42b22-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1562">'Identity'</span></span>
- <span data-ttu-id="42b22-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1564">'Razor'</span></span>
- <span data-ttu-id="42b22-1565">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="42b22-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="42b22-1567">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="42b22-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="42b22-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="42b22-1569">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42b22-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="42b22-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="42b22-1571">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="42b22-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="42b22-1572">`AddEnvironmentVariables`slouží k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán.</span><span class="sxs-lookup"><span data-stu-id="42b22-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="42b22-1573">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="42b22-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="42b22-1574">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="42b22-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="42b22-1575">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="42b22-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="42b22-1576">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="42b22-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="42b22-1577">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="42b22-1578">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="42b22-1578">Command-line arguments.</span></span>

<span data-ttu-id="42b22-1579">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="42b22-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="42b22-1580">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="42b22-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="42b22-1581">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu:</span><span class="sxs-lookup"><span data-stu-id="42b22-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="42b22-1582">Zavolejte `AddEnvironmentVariables` jako poslední, pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="42b22-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="42b22-1583">**Případě**</span><span class="sxs-lookup"><span data-stu-id="42b22-1583">**Example**</span></span>

<span data-ttu-id="42b22-1584">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="42b22-1585">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="42b22-1585">Run the sample app.</span></span> <span data-ttu-id="42b22-1586">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="42b22-1587">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="42b22-1588">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.</span><span class="sxs-lookup"><span data-stu-id="42b22-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="42b22-1589">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="42b22-1590">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="42b22-1591">K vystavení všech proměnných prostředí, které jsou k dispozici pro aplikaci, změňte na `FilteredConfiguration` *stránkách/index. cshtml. cs* následující:</span><span class="sxs-lookup"><span data-stu-id="42b22-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="42b22-1592">Předpony</span><span class="sxs-lookup"><span data-stu-id="42b22-1592">Prefixes</span></span>

<span data-ttu-id="42b22-1593">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="42b22-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="42b22-1594">Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_` , zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="42b22-1595">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="42b22-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="42b22-1596">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="42b22-1597">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="42b22-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="42b22-1598">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="42b22-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="42b22-1599">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="42b22-1600">Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="42b22-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="42b22-1601">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="42b22-1601">Connection string prefix</span></span> | <span data-ttu-id="42b22-1602">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="42b22-1602">Provider</span></span> |
| ---
<span data-ttu-id="42b22-1603">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1604">'Blazor'</span></span>
- <span data-ttu-id="42b22-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1605">'Identity'</span></span>
- <span data-ttu-id="42b22-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1607">'Razor'</span></span>
- <span data-ttu-id="42b22-1608">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1609">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1610">'Blazor'</span></span>
- <span data-ttu-id="42b22-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1611">'Identity'</span></span>
- <span data-ttu-id="42b22-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1613">'Razor'</span></span>
- <span data-ttu-id="42b22-1614">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1615">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1616">'Blazor'</span></span>
- <span data-ttu-id="42b22-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1617">'Identity'</span></span>
- <span data-ttu-id="42b22-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1619">'Razor'</span></span>
- <span data-ttu-id="42b22-1620">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1621">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1622">'Blazor'</span></span>
- <span data-ttu-id="42b22-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1623">'Identity'</span></span>
- <span data-ttu-id="42b22-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1625">'Razor'</span></span>
- <span data-ttu-id="42b22-1626">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1627">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1628">'Blazor'</span></span>
- <span data-ttu-id="42b22-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1629">'Identity'</span></span>
- <span data-ttu-id="42b22-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1631">'Razor'</span></span>
- <span data-ttu-id="42b22-1632">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1633">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1634">'Blazor'</span></span>
- <span data-ttu-id="42b22-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1635">'Identity'</span></span>
- <span data-ttu-id="42b22-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1637">'Razor'</span></span>
- <span data-ttu-id="42b22-1638">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1639">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1640">'Blazor'</span></span>
- <span data-ttu-id="42b22-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1641">'Identity'</span></span>
- <span data-ttu-id="42b22-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1643">'Razor'</span></span>
- <span data-ttu-id="42b22-1644">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1645">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1646">'Blazor'</span></span>
- <span data-ttu-id="42b22-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1647">'Identity'</span></span>
- <span data-ttu-id="42b22-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1649">'Razor'</span></span>
- <span data-ttu-id="42b22-1650">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1651">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1652">'Blazor'</span></span>
- <span data-ttu-id="42b22-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1653">'Identity'</span></span>
- <span data-ttu-id="42b22-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1655">'Razor'</span></span>
- <span data-ttu-id="42b22-1656">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1657">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1658">'Blazor'</span></span>
- <span data-ttu-id="42b22-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1659">'Identity'</span></span>
- <span data-ttu-id="42b22-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1661">'Razor'</span></span>
- <span data-ttu-id="42b22-1662">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1663">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1664">'Blazor'</span></span>
- <span data-ttu-id="42b22-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1665">'Identity'</span></span>
- <span data-ttu-id="42b22-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1667">'Razor'</span></span>
- <span data-ttu-id="42b22-1668">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1669">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1670">'Blazor'</span></span>
- <span data-ttu-id="42b22-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1671">'Identity'</span></span>
- <span data-ttu-id="42b22-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1673">'Razor'</span></span>
- <span data-ttu-id="42b22-1674">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1675">---- | | `CUSTOMCONNSTR_` | Vlastní poskytovatel | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="42b22-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="42b22-1676">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="42b22-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="42b22-1677">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="42b22-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="42b22-1678">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="42b22-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="42b22-1679">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="42b22-1679">Environment variable key</span></span> | <span data-ttu-id="42b22-1680">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="42b22-1680">Converted configuration key</span></span> | <span data-ttu-id="42b22-1681">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="42b22-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="42b22-1682">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1683">'Blazor'</span></span>
- <span data-ttu-id="42b22-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1684">'Identity'</span></span>
- <span data-ttu-id="42b22-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1686">'Razor'</span></span>
- <span data-ttu-id="42b22-1687">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1688">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1689">'Blazor'</span></span>
- <span data-ttu-id="42b22-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1690">'Identity'</span></span>
- <span data-ttu-id="42b22-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1692">'Razor'</span></span>
- <span data-ttu-id="42b22-1693">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1694">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1695">'Blazor'</span></span>
- <span data-ttu-id="42b22-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1696">'Identity'</span></span>
- <span data-ttu-id="42b22-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1698">'Razor'</span></span>
- <span data-ttu-id="42b22-1699">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1700">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1701">'Blazor'</span></span>
- <span data-ttu-id="42b22-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1702">'Identity'</span></span>
- <span data-ttu-id="42b22-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1704">'Razor'</span></span>
- <span data-ttu-id="42b22-1705">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1706">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1707">'Blazor'</span></span>
- <span data-ttu-id="42b22-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1708">'Identity'</span></span>
- <span data-ttu-id="42b22-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1710">'Razor'</span></span>
- <span data-ttu-id="42b22-1711">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1712">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1713">'Blazor'</span></span>
- <span data-ttu-id="42b22-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1714">'Identity'</span></span>
- <span data-ttu-id="42b22-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1716">'Razor'</span></span>
- <span data-ttu-id="42b22-1717">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1718">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1719">'Blazor'</span></span>
- <span data-ttu-id="42b22-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1720">'Identity'</span></span>
- <span data-ttu-id="42b22-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1722">'Razor'</span></span>
- <span data-ttu-id="42b22-1723">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1724">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1725">'Blazor'</span></span>
- <span data-ttu-id="42b22-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1726">'Identity'</span></span>
- <span data-ttu-id="42b22-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1728">'Razor'</span></span>
- <span data-ttu-id="42b22-1729">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1730">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1731">'Blazor'</span></span>
- <span data-ttu-id="42b22-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1732">'Identity'</span></span>
- <span data-ttu-id="42b22-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1734">'Razor'</span></span>
- <span data-ttu-id="42b22-1735">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1736">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1737">'Blazor'</span></span>
- <span data-ttu-id="42b22-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1738">'Identity'</span></span>
- <span data-ttu-id="42b22-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1740">'Razor'</span></span>
- <span data-ttu-id="42b22-1741">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1742">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1743">'Blazor'</span></span>
- <span data-ttu-id="42b22-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1744">'Identity'</span></span>
- <span data-ttu-id="42b22-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1746">'Razor'</span></span>
- <span data-ttu-id="42b22-1747">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1748">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1749">'Blazor'</span></span>
- <span data-ttu-id="42b22-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1750">'Identity'</span></span>
- <span data-ttu-id="42b22-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1752">'Razor'</span></span>
- <span data-ttu-id="42b22-1753">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1754">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1755">'Blazor'</span></span>
- <span data-ttu-id="42b22-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1756">'Identity'</span></span>
- <span data-ttu-id="42b22-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1758">'Razor'</span></span>
- <span data-ttu-id="42b22-1759">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1760">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1761">'Blazor'</span></span>
- <span data-ttu-id="42b22-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1762">'Identity'</span></span>
- <span data-ttu-id="42b22-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1764">'Razor'</span></span>
- <span data-ttu-id="42b22-1765">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1766">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1767">'Blazor'</span></span>
- <span data-ttu-id="42b22-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1768">'Identity'</span></span>
- <span data-ttu-id="42b22-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1770">'Razor'</span></span>
- <span data-ttu-id="42b22-1771">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1772">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1773">'Blazor'</span></span>
- <span data-ttu-id="42b22-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1774">'Identity'</span></span>
- <span data-ttu-id="42b22-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1776">'Razor'</span></span>
- <span data-ttu-id="42b22-1777">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1778">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1779">'Blazor'</span></span>
- <span data-ttu-id="42b22-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1780">'Identity'</span></span>
- <span data-ttu-id="42b22-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1782">'Razor'</span></span>
- <span data-ttu-id="42b22-1783">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1784">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1785">'Blazor'</span></span>
- <span data-ttu-id="42b22-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1786">'Identity'</span></span>
- <span data-ttu-id="42b22-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1788">'Razor'</span></span>
- <span data-ttu-id="42b22-1789">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1790">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1791">'Blazor'</span></span>
- <span data-ttu-id="42b22-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1792">'Identity'</span></span>
- <span data-ttu-id="42b22-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1794">'Razor'</span></span>
- <span data-ttu-id="42b22-1795">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1796">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1797">'Blazor'</span></span>
- <span data-ttu-id="42b22-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1798">'Identity'</span></span>
- <span data-ttu-id="42b22-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1800">'Razor'</span></span>
- <span data-ttu-id="42b22-1801">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1802">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1803">'Blazor'</span></span>
- <span data-ttu-id="42b22-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1804">'Identity'</span></span>
- <span data-ttu-id="42b22-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1806">'Razor'</span></span>
- <span data-ttu-id="42b22-1807">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-1808">-------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1809">'Blazor'</span></span>
- <span data-ttu-id="42b22-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1810">'Identity'</span></span>
- <span data-ttu-id="42b22-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1812">'Razor'</span></span>
- <span data-ttu-id="42b22-1813">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1814">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1815">'Blazor'</span></span>
- <span data-ttu-id="42b22-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1816">'Identity'</span></span>
- <span data-ttu-id="42b22-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1818">'Razor'</span></span>
- <span data-ttu-id="42b22-1819">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1820">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1821">'Blazor'</span></span>
- <span data-ttu-id="42b22-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1822">'Identity'</span></span>
- <span data-ttu-id="42b22-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1824">'Razor'</span></span>
- <span data-ttu-id="42b22-1825">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1826">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1827">'Blazor'</span></span>
- <span data-ttu-id="42b22-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1828">'Identity'</span></span>
- <span data-ttu-id="42b22-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1830">'Razor'</span></span>
- <span data-ttu-id="42b22-1831">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1832">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1833">'Blazor'</span></span>
- <span data-ttu-id="42b22-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1834">'Identity'</span></span>
- <span data-ttu-id="42b22-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1836">'Razor'</span></span>
- <span data-ttu-id="42b22-1837">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1838">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1839">'Blazor'</span></span>
- <span data-ttu-id="42b22-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1840">'Identity'</span></span>
- <span data-ttu-id="42b22-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1842">'Razor'</span></span>
- <span data-ttu-id="42b22-1843">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1844">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1845">'Blazor'</span></span>
- <span data-ttu-id="42b22-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1846">'Identity'</span></span>
- <span data-ttu-id="42b22-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1848">'Razor'</span></span>
- <span data-ttu-id="42b22-1849">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1850">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1851">'Blazor'</span></span>
- <span data-ttu-id="42b22-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1852">'Identity'</span></span>
- <span data-ttu-id="42b22-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1854">'Razor'</span></span>
- <span data-ttu-id="42b22-1855">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1856">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1857">'Blazor'</span></span>
- <span data-ttu-id="42b22-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1858">'Identity'</span></span>
- <span data-ttu-id="42b22-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1860">'Razor'</span></span>
- <span data-ttu-id="42b22-1861">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1862">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1863">'Blazor'</span></span>
- <span data-ttu-id="42b22-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1864">'Identity'</span></span>
- <span data-ttu-id="42b22-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1866">'Razor'</span></span>
- <span data-ttu-id="42b22-1867">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1868">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1869">'Blazor'</span></span>
- <span data-ttu-id="42b22-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1870">'Identity'</span></span>
- <span data-ttu-id="42b22-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1872">'Razor'</span></span>
- <span data-ttu-id="42b22-1873">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1874">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1875">'Blazor'</span></span>
- <span data-ttu-id="42b22-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1876">'Identity'</span></span>
- <span data-ttu-id="42b22-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1878">'Razor'</span></span>
- <span data-ttu-id="42b22-1879">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1880">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1881">'Blazor'</span></span>
- <span data-ttu-id="42b22-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1882">'Identity'</span></span>
- <span data-ttu-id="42b22-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1884">'Razor'</span></span>
- <span data-ttu-id="42b22-1885">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1886">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1887">'Blazor'</span></span>
- <span data-ttu-id="42b22-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1888">'Identity'</span></span>
- <span data-ttu-id="42b22-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1890">'Razor'</span></span>
- <span data-ttu-id="42b22-1891">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1892">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1893">'Blazor'</span></span>
- <span data-ttu-id="42b22-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1894">'Identity'</span></span>
- <span data-ttu-id="42b22-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1896">'Razor'</span></span>
- <span data-ttu-id="42b22-1897">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1898">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1899">'Blazor'</span></span>
- <span data-ttu-id="42b22-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1900">'Identity'</span></span>
- <span data-ttu-id="42b22-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1902">'Razor'</span></span>
- <span data-ttu-id="42b22-1903">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1904">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1905">'Blazor'</span></span>
- <span data-ttu-id="42b22-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1906">'Identity'</span></span>
- <span data-ttu-id="42b22-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1908">'Razor'</span></span>
- <span data-ttu-id="42b22-1909">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1910">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1911">'Blazor'</span></span>
- <span data-ttu-id="42b22-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1912">'Identity'</span></span>
- <span data-ttu-id="42b22-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1914">'Razor'</span></span>
- <span data-ttu-id="42b22-1915">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1916">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1917">'Blazor'</span></span>
- <span data-ttu-id="42b22-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1918">'Identity'</span></span>
- <span data-ttu-id="42b22-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1920">'Razor'</span></span>
- <span data-ttu-id="42b22-1921">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1922">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1923">'Blazor'</span></span>
- <span data-ttu-id="42b22-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1924">'Identity'</span></span>
- <span data-ttu-id="42b22-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1926">'Razor'</span></span>
- <span data-ttu-id="42b22-1927">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1928">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1929">'Blazor'</span></span>
- <span data-ttu-id="42b22-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1930">'Identity'</span></span>
- <span data-ttu-id="42b22-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1932">'Razor'</span></span>
- <span data-ttu-id="42b22-1933">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1934">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1935">'Blazor'</span></span>
- <span data-ttu-id="42b22-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1936">'Identity'</span></span>
- <span data-ttu-id="42b22-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1938">'Razor'</span></span>
- <span data-ttu-id="42b22-1939">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1940">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1941">'Blazor'</span></span>
- <span data-ttu-id="42b22-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1942">'Identity'</span></span>
- <span data-ttu-id="42b22-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1944">'Razor'</span></span>
- <span data-ttu-id="42b22-1945">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1946">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1947">'Blazor'</span></span>
- <span data-ttu-id="42b22-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1948">'Identity'</span></span>
- <span data-ttu-id="42b22-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1950">'Razor'</span></span>
- <span data-ttu-id="42b22-1951">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1952">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1953">'Blazor'</span></span>
- <span data-ttu-id="42b22-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1954">'Identity'</span></span>
- <span data-ttu-id="42b22-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1956">'Razor'</span></span>
- <span data-ttu-id="42b22-1957">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1958">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1959">'Blazor'</span></span>
- <span data-ttu-id="42b22-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1960">'Identity'</span></span>
- <span data-ttu-id="42b22-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1962">'Razor'</span></span>
- <span data-ttu-id="42b22-1963">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1964">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1965">'Blazor'</span></span>
- <span data-ttu-id="42b22-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1966">'Identity'</span></span>
- <span data-ttu-id="42b22-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1968">'Razor'</span></span>
- <span data-ttu-id="42b22-1969">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1970">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1971">'Blazor'</span></span>
- <span data-ttu-id="42b22-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1972">'Identity'</span></span>
- <span data-ttu-id="42b22-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1974">'Razor'</span></span>
- <span data-ttu-id="42b22-1975">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1976">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1977">'Blazor'</span></span>
- <span data-ttu-id="42b22-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1978">'Identity'</span></span>
- <span data-ttu-id="42b22-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1980">'Razor'</span></span>
- <span data-ttu-id="42b22-1981">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1982">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1983">'Blazor'</span></span>
- <span data-ttu-id="42b22-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1984">'Identity'</span></span>
- <span data-ttu-id="42b22-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1986">'Razor'</span></span>
- <span data-ttu-id="42b22-1987">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1988">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1989">'Blazor'</span></span>
- <span data-ttu-id="42b22-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1990">'Identity'</span></span>
- <span data-ttu-id="42b22-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1992">'Razor'</span></span>
- <span data-ttu-id="42b22-1993">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-1994">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1995">'Blazor'</span></span>
- <span data-ttu-id="42b22-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-1996">'Identity'</span></span>
- <span data-ttu-id="42b22-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-1998">'Razor'</span></span>
- <span data-ttu-id="42b22-1999">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2000">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2001">'Blazor'</span></span>
- <span data-ttu-id="42b22-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2002">'Identity'</span></span>
- <span data-ttu-id="42b22-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2004">'Razor'</span></span>
- <span data-ttu-id="42b22-2005">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2006">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2007">'Blazor'</span></span>
- <span data-ttu-id="42b22-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2008">'Identity'</span></span>
- <span data-ttu-id="42b22-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2010">'Razor'</span></span>
- <span data-ttu-id="42b22-2011">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2012">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2013">'Blazor'</span></span>
- <span data-ttu-id="42b22-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2014">'Identity'</span></span>
- <span data-ttu-id="42b22-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2016">'Razor'</span></span>
- <span data-ttu-id="42b22-2017">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2018">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2019">'Blazor'</span></span>
- <span data-ttu-id="42b22-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2020">'Identity'</span></span>
- <span data-ttu-id="42b22-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2022">'Razor'</span></span>
- <span data-ttu-id="42b22-2023">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2024">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2025">'Blazor'</span></span>
- <span data-ttu-id="42b22-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2026">'Identity'</span></span>
- <span data-ttu-id="42b22-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2028">'Razor'</span></span>
- <span data-ttu-id="42b22-2029">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="42b22-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="42b22-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="42b22-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="42b22-2032">Hodnota: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="42b22-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="42b22-2033">Hodnota: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="42b22-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="42b22-2034">Osa`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="42b22-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="42b22-2035">**Případě**</span><span class="sxs-lookup"><span data-stu-id="42b22-2035">**Example**</span></span>

<span data-ttu-id="42b22-2036">Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="42b22-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="42b22-2037">Název &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="42b22-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="42b22-2038">Hodnota &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="42b22-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="42b22-2039">Pokud `IConfiguration` je vloženo a přiřazeno k poli s názvem `_config` , přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="42b22-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="42b22-2040">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="42b22-2040">File Configuration Provider</span></span>

<span data-ttu-id="42b22-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="42b22-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="42b22-2042">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="42b22-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="42b22-2043">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="42b22-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="42b22-2044">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="42b22-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="42b22-2045">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="42b22-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="42b22-2046">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="42b22-2046">INI Configuration Provider</span></span>

<span data-ttu-id="42b22-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="42b22-2048">Chcete-li aktivovat konfiguraci souboru INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42b22-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="42b22-2049">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="42b22-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="42b22-2050">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="42b22-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="42b22-2051">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="42b22-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="42b22-2052">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="42b22-2053"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="42b22-2054">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="42b22-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="42b22-2055">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="42b22-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="42b22-2056">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="42b22-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="42b22-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2057">section0:key0</span></span>
* <span data-ttu-id="42b22-2058">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2058">section0:key1</span></span>
* <span data-ttu-id="42b22-2059">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="42b22-2059">section1:subsection:key</span></span>
* <span data-ttu-id="42b22-2060">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="42b22-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="42b22-2061">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="42b22-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="42b22-2062">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="42b22-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="42b22-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="42b22-2064">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42b22-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="42b22-2065">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="42b22-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="42b22-2066">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="42b22-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="42b22-2067">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="42b22-2068"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="42b22-2069">`AddJsonFile`se automaticky volá dvakrát při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="42b22-2070">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="42b22-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="42b22-2071">*appSettings. JSON* &ndash; Tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="42b22-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="42b22-2072">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="42b22-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="42b22-2073">*appSettings. {Environment}. JSON* &ndash; verze souboru je načtená na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="42b22-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="42b22-2074">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="42b22-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="42b22-2075">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="42b22-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="42b22-2076">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-2076">Environment variables.</span></span>
* <span data-ttu-id="42b22-2077">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="42b22-2078">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="42b22-2078">Command-line arguments.</span></span>

<span data-ttu-id="42b22-2079">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="42b22-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="42b22-2080">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="42b22-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="42b22-2081">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="42b22-2082">**Případě**</span><span class="sxs-lookup"><span data-stu-id="42b22-2082">**Example**</span></span>

<span data-ttu-id="42b22-2083">Ukázková aplikace využívá ke sestavení hostitele výhodu technologie statického usnadnění `CreateDefaultBuilder` , která zahrnuje dvě volání `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="42b22-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="42b22-2084">První volání `AddJsonFile` načtení konfigurace z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="42b22-2085">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="42b22-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="42b22-2086">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="42b22-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="42b22-2087">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="42b22-2087">Run the sample app.</span></span> <span data-ttu-id="42b22-2088">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="42b22-2089">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="42b22-2090">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="42b22-2091">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="42b22-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="42b22-2092">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="42b22-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="42b22-2093">V `ConfigurationSample` profilu změňte hodnotu `ASPNETCORE_ENVIRONMENT` proměnné prostředí na `Production` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="42b22-2094">Uložte soubor a spusťte aplikaci `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="42b22-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="42b22-2095">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="42b22-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="42b22-2096">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Warning` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="42b22-2097">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="42b22-2097">XML Configuration Provider</span></span>

<span data-ttu-id="42b22-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="42b22-2099">Chcete-li aktivovat konfiguraci souboru XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42b22-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="42b22-2100">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="42b22-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="42b22-2101">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="42b22-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="42b22-2102">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="42b22-2103"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="42b22-2104">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="42b22-2105">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="42b22-2106">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="42b22-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="42b22-2107">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="42b22-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="42b22-2108">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="42b22-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="42b22-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2109">section0:key0</span></span>
* <span data-ttu-id="42b22-2110">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2110">section0:key1</span></span>
* <span data-ttu-id="42b22-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2111">section1:key0</span></span>
* <span data-ttu-id="42b22-2112">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2112">section1:key1</span></span>

<span data-ttu-id="42b22-2113">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="42b22-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="42b22-2114">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="42b22-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="42b22-2115">oddíl: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="42b22-2116">oddíl: section0: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="42b22-2117">oddíl: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="42b22-2118">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2118">section:section1:key:key1</span></span>

<span data-ttu-id="42b22-2119">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="42b22-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="42b22-2120">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="42b22-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="42b22-2121">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="42b22-2121">key:attribute</span></span>
* <span data-ttu-id="42b22-2122">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="42b22-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="42b22-2123">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="42b22-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="42b22-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="42b22-2125">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2125">The key is the file name.</span></span> <span data-ttu-id="42b22-2126">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="42b22-2126">The value contains the file's contents.</span></span> <span data-ttu-id="42b22-2127">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="42b22-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="42b22-2128">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42b22-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="42b22-2129">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="42b22-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="42b22-2130">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="42b22-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="42b22-2131">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="42b22-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="42b22-2132">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="42b22-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="42b22-2133">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="42b22-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="42b22-2134">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="42b22-2135">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="42b22-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="42b22-2136">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="42b22-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="42b22-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="42b22-2138">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="42b22-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="42b22-2139">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="42b22-2140">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="42b22-2141">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="42b22-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="42b22-2142">Slovník se používá s voláním k `AddInMemoryCollection` poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="42b22-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="42b22-2143">GetValue</span></span>

<span data-ttu-id="42b22-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="42b22-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="42b22-2145">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="42b22-2146">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="42b22-2146">The following example:</span></span>

* <span data-ttu-id="42b22-2147">Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="42b22-2148">Pokud se `NumberKey` v konfiguračních klíčích nenajde, použije se výchozí hodnota `99` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="42b22-2149">Zadá hodnotu jako `int` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="42b22-2150">Ukládá hodnotu `NumberConfig` vlastnosti pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="42b22-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="42b22-2151">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="42b22-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="42b22-2152">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="42b22-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="42b22-2153">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="42b22-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="42b22-2154">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="42b22-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2155">section0:key0</span></span>
* <span data-ttu-id="42b22-2156">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2156">section0:key1</span></span>
* <span data-ttu-id="42b22-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2157">section1:key0</span></span>
* <span data-ttu-id="42b22-2158">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2158">section1:key1</span></span>
* <span data-ttu-id="42b22-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="42b22-2160">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="42b22-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="42b22-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="42b22-2162">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="42b22-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="42b22-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="42b22-2163">GetSection</span></span>

<span data-ttu-id="42b22-2164">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="42b22-2165">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1` , zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="42b22-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="42b22-2166">`configSection`Hodnota neobsahuje pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="42b22-2167">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0` , zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="42b22-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="42b22-2168">`GetSection`nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="42b22-2169">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="42b22-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="42b22-2170">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="42b22-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="42b22-2171"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="42b22-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="42b22-2172">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="42b22-2172">GetChildren</span></span>

<span data-ttu-id="42b22-2173">Volání [IConfiguration. Getchildrens](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="42b22-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="42b22-2174">Existuje</span><span class="sxs-lookup"><span data-stu-id="42b22-2174">Exists</span></span>

<span data-ttu-id="42b22-2175">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="42b22-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="42b22-2176">Vzhledem k ukázkovým datům `sectionExists` je `false` to proto, že `section2:subsection2` v konfiguračních datech není oddíl.</span><span class="sxs-lookup"><span data-stu-id="42b22-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="42b22-2177">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="42b22-2177">Bind to an object graph</span></span>

<span data-ttu-id="42b22-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="42b22-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="42b22-2179">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="42b22-2180">Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="42b22-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="42b22-2181">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="42b22-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="42b22-2182">Konfigurace je svázána s celým `TvShow` grafem objektů pomocí `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="42b22-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="42b22-2183">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="42b22-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="42b22-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="42b22-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="42b22-2185">`Get<T>`je pohodlnější než použití `Bind` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="42b22-2186">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem:</span><span class="sxs-lookup"><span data-stu-id="42b22-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="42b22-2187">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="42b22-2187">Bind an array to a class</span></span>

<span data-ttu-id="42b22-2188">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="42b22-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="42b22-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="42b22-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="42b22-2190">Libovolný formát pole, který zveřejňuje numerický klíčový segment ( `:0:` , `:1:` ,), &hellip; `:{n}:` je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="42b22-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="42b22-2191">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="42b22-2191">Binding is provided by convention.</span></span> <span data-ttu-id="42b22-2192">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="42b22-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="42b22-2193">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="42b22-2193">**In-memory array processing**</span></span>

<span data-ttu-id="42b22-2194">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="42b22-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="42b22-2195">Klíč</span><span class="sxs-lookup"><span data-stu-id="42b22-2195">Key</span></span>             | <span data-ttu-id="42b22-2196">Hodnota</span><span class="sxs-lookup"><span data-stu-id="42b22-2196">Value</span></span>  |
| :---
<span data-ttu-id="42b22-2197">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2198">'Blazor'</span></span>
- <span data-ttu-id="42b22-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2199">'Identity'</span></span>
- <span data-ttu-id="42b22-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2201">'Razor'</span></span>
- <span data-ttu-id="42b22-2202">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2203">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2204">'Blazor'</span></span>
- <span data-ttu-id="42b22-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2205">'Identity'</span></span>
- <span data-ttu-id="42b22-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2207">'Razor'</span></span>
- <span data-ttu-id="42b22-2208">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2209">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2210">'Blazor'</span></span>
- <span data-ttu-id="42b22-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2211">'Identity'</span></span>
- <span data-ttu-id="42b22-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2213">'Razor'</span></span>
- <span data-ttu-id="42b22-2214">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2215">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2216">'Blazor'</span></span>
- <span data-ttu-id="42b22-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2217">'Identity'</span></span>
- <span data-ttu-id="42b22-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2219">'Razor'</span></span>
- <span data-ttu-id="42b22-2220">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2221">-------: | :----: | | pole: položky: 0 | value0 | | pole: položky: 1 | Hodnota1 | | pole: položky: 2 | hodnota2 | | pole: položky: 4 | value4 | | Array: položky: 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="42b22-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="42b22-2222">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="42b22-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="42b22-2223">Pole přeskočí hodnotu indexu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="42b22-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="42b22-2224">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="42b22-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="42b22-2225">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="42b22-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="42b22-2226">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="42b22-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="42b22-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lze také použít syntaxi, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="42b22-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="42b22-2228">Vázaný objekt, instance `ArrayExample` , přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="42b22-2229">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="42b22-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="42b22-2230">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="42b22-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="42b22-2231">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2232">'Blazor'</span></span>
- <span data-ttu-id="42b22-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2233">'Identity'</span></span>
- <span data-ttu-id="42b22-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2235">'Razor'</span></span>
- <span data-ttu-id="42b22-2236">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2237">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2238">'Blazor'</span></span>
- <span data-ttu-id="42b22-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2239">'Identity'</span></span>
- <span data-ttu-id="42b22-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2241">'Razor'</span></span>
- <span data-ttu-id="42b22-2242">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2243">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2244">'Blazor'</span></span>
- <span data-ttu-id="42b22-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2245">'Identity'</span></span>
- <span data-ttu-id="42b22-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2247">'Razor'</span></span>
- <span data-ttu-id="42b22-2248">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2249">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2250">'Blazor'</span></span>
- <span data-ttu-id="42b22-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2251">'Identity'</span></span>
- <span data-ttu-id="42b22-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2253">'Razor'</span></span>
- <span data-ttu-id="42b22-2254">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2255">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2256">'Blazor'</span></span>
- <span data-ttu-id="42b22-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2257">'Identity'</span></span>
- <span data-ttu-id="42b22-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2259">'Razor'</span></span>
- <span data-ttu-id="42b22-2260">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2261">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2262">'Blazor'</span></span>
- <span data-ttu-id="42b22-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2263">'Identity'</span></span>
- <span data-ttu-id="42b22-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2265">'Razor'</span></span>
- <span data-ttu-id="42b22-2266">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2267">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2268">'Blazor'</span></span>
- <span data-ttu-id="42b22-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2269">'Identity'</span></span>
- <span data-ttu-id="42b22-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2271">'Razor'</span></span>
- <span data-ttu-id="42b22-2272">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2273">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2274">'Blazor'</span></span>
- <span data-ttu-id="42b22-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2275">'Identity'</span></span>
- <span data-ttu-id="42b22-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2277">'Razor'</span></span>
- <span data-ttu-id="42b22-2278">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2279">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2280">'Blazor'</span></span>
- <span data-ttu-id="42b22-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2281">'Identity'</span></span>
- <span data-ttu-id="42b22-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2283">'Razor'</span></span>
- <span data-ttu-id="42b22-2284">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2285">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2286">'Blazor'</span></span>
- <span data-ttu-id="42b22-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2287">'Identity'</span></span>
- <span data-ttu-id="42b22-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2289">'Razor'</span></span>
- <span data-ttu-id="42b22-2290">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2291">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2292">'Blazor'</span></span>
- <span data-ttu-id="42b22-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2293">'Identity'</span></span>
- <span data-ttu-id="42b22-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2295">'Razor'</span></span>
- <span data-ttu-id="42b22-2296">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2297">-------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2298">'Blazor'</span></span>
- <span data-ttu-id="42b22-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2299">'Identity'</span></span>
- <span data-ttu-id="42b22-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2301">'Razor'</span></span>
- <span data-ttu-id="42b22-2302">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2303">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2304">'Blazor'</span></span>
- <span data-ttu-id="42b22-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2305">'Identity'</span></span>
- <span data-ttu-id="42b22-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2307">'Razor'</span></span>
- <span data-ttu-id="42b22-2308">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2309">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2310">'Blazor'</span></span>
- <span data-ttu-id="42b22-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2311">'Identity'</span></span>
- <span data-ttu-id="42b22-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2313">'Razor'</span></span>
- <span data-ttu-id="42b22-2314">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2315">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2316">'Blazor'</span></span>
- <span data-ttu-id="42b22-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2317">'Identity'</span></span>
- <span data-ttu-id="42b22-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2319">'Razor'</span></span>
- <span data-ttu-id="42b22-2320">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2321">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2322">'Blazor'</span></span>
- <span data-ttu-id="42b22-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2323">'Identity'</span></span>
- <span data-ttu-id="42b22-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2325">'Razor'</span></span>
- <span data-ttu-id="42b22-2326">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2327">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2328">'Blazor'</span></span>
- <span data-ttu-id="42b22-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2329">'Identity'</span></span>
- <span data-ttu-id="42b22-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2331">'Razor'</span></span>
- <span data-ttu-id="42b22-2332">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2333">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2334">'Blazor'</span></span>
- <span data-ttu-id="42b22-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2335">'Identity'</span></span>
- <span data-ttu-id="42b22-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2337">'Razor'</span></span>
- <span data-ttu-id="42b22-2338">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2339">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2340">'Blazor'</span></span>
- <span data-ttu-id="42b22-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2341">'Identity'</span></span>
- <span data-ttu-id="42b22-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2343">'Razor'</span></span>
- <span data-ttu-id="42b22-2344">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2345">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2346">'Blazor'</span></span>
- <span data-ttu-id="42b22-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2347">'Identity'</span></span>
- <span data-ttu-id="42b22-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2349">'Razor'</span></span>
- <span data-ttu-id="42b22-2350">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2351">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2352">'Blazor'</span></span>
- <span data-ttu-id="42b22-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2353">'Identity'</span></span>
- <span data-ttu-id="42b22-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2355">'Razor'</span></span>
- <span data-ttu-id="42b22-2356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2357">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2358">'Blazor'</span></span>
- <span data-ttu-id="42b22-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2359">'Identity'</span></span>
- <span data-ttu-id="42b22-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2361">'Razor'</span></span>
- <span data-ttu-id="42b22-2362">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2363">-------------: | | 0 | value0 | | 1 | Hodnota1 | | 2 | hodnota2 | | 3 | value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="42b22-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="42b22-2364">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="42b22-2365">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="42b22-2366">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="42b22-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="42b22-2367">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na `ArrayExample` instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="42b22-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="42b22-2368">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="42b22-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="42b22-2369">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="42b22-2370">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="42b22-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="42b22-2371">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="42b22-2372">Klíč</span><span class="sxs-lookup"><span data-stu-id="42b22-2372">Key</span></span>             | <span data-ttu-id="42b22-2373">Hodnota</span><span class="sxs-lookup"><span data-stu-id="42b22-2373">Value</span></span>  |
| :---
<span data-ttu-id="42b22-2374">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2375">'Blazor'</span></span>
- <span data-ttu-id="42b22-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2376">'Identity'</span></span>
- <span data-ttu-id="42b22-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2378">'Razor'</span></span>
- <span data-ttu-id="42b22-2379">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2380">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2381">'Blazor'</span></span>
- <span data-ttu-id="42b22-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2382">'Identity'</span></span>
- <span data-ttu-id="42b22-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2384">'Razor'</span></span>
- <span data-ttu-id="42b22-2385">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2386">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2387">'Blazor'</span></span>
- <span data-ttu-id="42b22-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2388">'Identity'</span></span>
- <span data-ttu-id="42b22-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2390">'Razor'</span></span>
- <span data-ttu-id="42b22-2391">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2392">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2393">'Blazor'</span></span>
- <span data-ttu-id="42b22-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2394">'Identity'</span></span>
- <span data-ttu-id="42b22-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2396">'Razor'</span></span>
- <span data-ttu-id="42b22-2397">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2398">-------: | :----: | | pole: položky: 3 | hodnota3 |</span><span class="sxs-lookup"><span data-stu-id="42b22-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="42b22-2399">Je-li `ArrayExample` instance třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro index &num; 3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="42b22-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="42b22-2400">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="42b22-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="42b22-2401">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="42b22-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="42b22-2402">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2403">'Blazor'</span></span>
- <span data-ttu-id="42b22-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2404">'Identity'</span></span>
- <span data-ttu-id="42b22-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2406">'Razor'</span></span>
- <span data-ttu-id="42b22-2407">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2408">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2409">'Blazor'</span></span>
- <span data-ttu-id="42b22-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2410">'Identity'</span></span>
- <span data-ttu-id="42b22-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2412">'Razor'</span></span>
- <span data-ttu-id="42b22-2413">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2414">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2415">'Blazor'</span></span>
- <span data-ttu-id="42b22-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2416">'Identity'</span></span>
- <span data-ttu-id="42b22-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2418">'Razor'</span></span>
- <span data-ttu-id="42b22-2419">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2420">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2421">'Blazor'</span></span>
- <span data-ttu-id="42b22-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2422">'Identity'</span></span>
- <span data-ttu-id="42b22-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2424">'Razor'</span></span>
- <span data-ttu-id="42b22-2425">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2426">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2427">'Blazor'</span></span>
- <span data-ttu-id="42b22-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2428">'Identity'</span></span>
- <span data-ttu-id="42b22-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2430">'Razor'</span></span>
- <span data-ttu-id="42b22-2431">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2432">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2433">'Blazor'</span></span>
- <span data-ttu-id="42b22-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2434">'Identity'</span></span>
- <span data-ttu-id="42b22-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2436">'Razor'</span></span>
- <span data-ttu-id="42b22-2437">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2438">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2439">'Blazor'</span></span>
- <span data-ttu-id="42b22-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2440">'Identity'</span></span>
- <span data-ttu-id="42b22-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2442">'Razor'</span></span>
- <span data-ttu-id="42b22-2443">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2444">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2445">'Blazor'</span></span>
- <span data-ttu-id="42b22-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2446">'Identity'</span></span>
- <span data-ttu-id="42b22-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2448">'Razor'</span></span>
- <span data-ttu-id="42b22-2449">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2450">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2451">'Blazor'</span></span>
- <span data-ttu-id="42b22-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2452">'Identity'</span></span>
- <span data-ttu-id="42b22-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2454">'Razor'</span></span>
- <span data-ttu-id="42b22-2455">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2456">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2457">'Blazor'</span></span>
- <span data-ttu-id="42b22-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2458">'Identity'</span></span>
- <span data-ttu-id="42b22-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2460">'Razor'</span></span>
- <span data-ttu-id="42b22-2461">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2462">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2463">'Blazor'</span></span>
- <span data-ttu-id="42b22-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2464">'Identity'</span></span>
- <span data-ttu-id="42b22-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2466">'Razor'</span></span>
- <span data-ttu-id="42b22-2467">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2468">-------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2469">'Blazor'</span></span>
- <span data-ttu-id="42b22-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2470">'Identity'</span></span>
- <span data-ttu-id="42b22-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2472">'Razor'</span></span>
- <span data-ttu-id="42b22-2473">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2474">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2475">'Blazor'</span></span>
- <span data-ttu-id="42b22-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2476">'Identity'</span></span>
- <span data-ttu-id="42b22-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2478">'Razor'</span></span>
- <span data-ttu-id="42b22-2479">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2480">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2481">'Blazor'</span></span>
- <span data-ttu-id="42b22-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2482">'Identity'</span></span>
- <span data-ttu-id="42b22-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2484">'Razor'</span></span>
- <span data-ttu-id="42b22-2485">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2486">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2487">'Blazor'</span></span>
- <span data-ttu-id="42b22-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2488">'Identity'</span></span>
- <span data-ttu-id="42b22-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2490">'Razor'</span></span>
- <span data-ttu-id="42b22-2491">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2492">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2493">'Blazor'</span></span>
- <span data-ttu-id="42b22-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2494">'Identity'</span></span>
- <span data-ttu-id="42b22-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2496">'Razor'</span></span>
- <span data-ttu-id="42b22-2497">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2498">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2499">'Blazor'</span></span>
- <span data-ttu-id="42b22-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2500">'Identity'</span></span>
- <span data-ttu-id="42b22-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2502">'Razor'</span></span>
- <span data-ttu-id="42b22-2503">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2504">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2505">'Blazor'</span></span>
- <span data-ttu-id="42b22-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2506">'Identity'</span></span>
- <span data-ttu-id="42b22-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2508">'Razor'</span></span>
- <span data-ttu-id="42b22-2509">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2510">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2511">'Blazor'</span></span>
- <span data-ttu-id="42b22-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2512">'Identity'</span></span>
- <span data-ttu-id="42b22-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2514">'Razor'</span></span>
- <span data-ttu-id="42b22-2515">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2516">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2517">'Blazor'</span></span>
- <span data-ttu-id="42b22-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2518">'Identity'</span></span>
- <span data-ttu-id="42b22-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2520">'Razor'</span></span>
- <span data-ttu-id="42b22-2521">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2522">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2523">'Blazor'</span></span>
- <span data-ttu-id="42b22-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2524">'Identity'</span></span>
- <span data-ttu-id="42b22-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2526">'Razor'</span></span>
- <span data-ttu-id="42b22-2527">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2528">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2529">'Blazor'</span></span>
- <span data-ttu-id="42b22-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2530">'Identity'</span></span>
- <span data-ttu-id="42b22-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2532">'Razor'</span></span>
- <span data-ttu-id="42b22-2533">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2534">-------------: | | 0 | value0 | | 1 | Hodnota1 | | 2 | hodnota2 | | 3 | hodnota3 | | 4 | value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="42b22-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="42b22-2535">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="42b22-2535">**JSON array processing**</span></span>

<span data-ttu-id="42b22-2536">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="42b22-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="42b22-2537">V následujícím konfiguračním souboru `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="42b22-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="42b22-2538">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="42b22-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="42b22-2539">Klíč</span><span class="sxs-lookup"><span data-stu-id="42b22-2539">Key</span></span>                     | <span data-ttu-id="42b22-2540">Hodnota</span><span class="sxs-lookup"><span data-stu-id="42b22-2540">Value</span></span>  |
| ---
<span data-ttu-id="42b22-2541">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2542">'Blazor'</span></span>
- <span data-ttu-id="42b22-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2543">'Identity'</span></span>
- <span data-ttu-id="42b22-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2545">'Razor'</span></span>
- <span data-ttu-id="42b22-2546">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2547">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2548">'Blazor'</span></span>
- <span data-ttu-id="42b22-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2549">'Identity'</span></span>
- <span data-ttu-id="42b22-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2551">'Razor'</span></span>
- <span data-ttu-id="42b22-2552">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2553">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2554">'Blazor'</span></span>
- <span data-ttu-id="42b22-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2555">'Identity'</span></span>
- <span data-ttu-id="42b22-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2557">'Razor'</span></span>
- <span data-ttu-id="42b22-2558">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2559">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2560">'Blazor'</span></span>
- <span data-ttu-id="42b22-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2561">'Identity'</span></span>
- <span data-ttu-id="42b22-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2563">'Razor'</span></span>
- <span data-ttu-id="42b22-2564">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2565">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2566">'Blazor'</span></span>
- <span data-ttu-id="42b22-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2567">'Identity'</span></span>
- <span data-ttu-id="42b22-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2569">'Razor'</span></span>
- <span data-ttu-id="42b22-2570">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2571">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2572">'Blazor'</span></span>
- <span data-ttu-id="42b22-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2573">'Identity'</span></span>
- <span data-ttu-id="42b22-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2575">'Razor'</span></span>
- <span data-ttu-id="42b22-2576">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2577">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2578">'Blazor'</span></span>
- <span data-ttu-id="42b22-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2579">'Identity'</span></span>
- <span data-ttu-id="42b22-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2581">'Razor'</span></span>
- <span data-ttu-id="42b22-2582">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2583">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2584">'Blazor'</span></span>
- <span data-ttu-id="42b22-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2585">'Identity'</span></span>
- <span data-ttu-id="42b22-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2587">'Razor'</span></span>
- <span data-ttu-id="42b22-2588">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2589">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2590">'Blazor'</span></span>
- <span data-ttu-id="42b22-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2591">'Identity'</span></span>
- <span data-ttu-id="42b22-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2593">'Razor'</span></span>
- <span data-ttu-id="42b22-2594">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2595">------------ | :----: | | json_array: klíč | hodnota | | json_array: dílčí oddíl: 0 | Hodnotab | | json_array: pododdíl: 1 | valueC | | json_array: pododdíl: 2 | Oceněno |</span><span class="sxs-lookup"><span data-stu-id="42b22-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="42b22-2596">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="42b22-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="42b22-2597">Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu `valueA` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="42b22-2598">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="42b22-2599">`JsonArrayExample.Subsection`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="42b22-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="42b22-2600">`JsonArrayExample.Subsection`Osa</span><span class="sxs-lookup"><span data-stu-id="42b22-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="42b22-2601">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2602">'Blazor'</span></span>
- <span data-ttu-id="42b22-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2603">'Identity'</span></span>
- <span data-ttu-id="42b22-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2605">'Razor'</span></span>
- <span data-ttu-id="42b22-2606">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2607">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2608">'Blazor'</span></span>
- <span data-ttu-id="42b22-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2609">'Identity'</span></span>
- <span data-ttu-id="42b22-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2611">'Razor'</span></span>
- <span data-ttu-id="42b22-2612">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2613">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2614">'Blazor'</span></span>
- <span data-ttu-id="42b22-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2615">'Identity'</span></span>
- <span data-ttu-id="42b22-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2617">'Razor'</span></span>
- <span data-ttu-id="42b22-2618">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2619">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2620">'Blazor'</span></span>
- <span data-ttu-id="42b22-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2621">'Identity'</span></span>
- <span data-ttu-id="42b22-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2623">'Razor'</span></span>
- <span data-ttu-id="42b22-2624">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2625">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2626">'Blazor'</span></span>
- <span data-ttu-id="42b22-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2627">'Identity'</span></span>
- <span data-ttu-id="42b22-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2629">'Razor'</span></span>
- <span data-ttu-id="42b22-2630">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2631">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2632">'Blazor'</span></span>
- <span data-ttu-id="42b22-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2633">'Identity'</span></span>
- <span data-ttu-id="42b22-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2635">'Razor'</span></span>
- <span data-ttu-id="42b22-2636">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2637">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2638">'Blazor'</span></span>
- <span data-ttu-id="42b22-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2639">'Identity'</span></span>
- <span data-ttu-id="42b22-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2641">'Razor'</span></span>
- <span data-ttu-id="42b22-2642">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2643">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2644">'Blazor'</span></span>
- <span data-ttu-id="42b22-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2645">'Identity'</span></span>
- <span data-ttu-id="42b22-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2647">'Razor'</span></span>
- <span data-ttu-id="42b22-2648">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2649">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2650">'Blazor'</span></span>
- <span data-ttu-id="42b22-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2651">'Identity'</span></span>
- <span data-ttu-id="42b22-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2653">'Razor'</span></span>
- <span data-ttu-id="42b22-2654">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2655">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2656">'Blazor'</span></span>
- <span data-ttu-id="42b22-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2657">'Identity'</span></span>
- <span data-ttu-id="42b22-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2659">'Razor'</span></span>
- <span data-ttu-id="42b22-2660">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2661">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2662">'Blazor'</span></span>
- <span data-ttu-id="42b22-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2663">'Identity'</span></span>
- <span data-ttu-id="42b22-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2665">'Razor'</span></span>
- <span data-ttu-id="42b22-2666">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2667">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2668">'Blazor'</span></span>
- <span data-ttu-id="42b22-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2669">'Identity'</span></span>
- <span data-ttu-id="42b22-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2671">'Razor'</span></span>
- <span data-ttu-id="42b22-2672">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2673">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2674">'Blazor'</span></span>
- <span data-ttu-id="42b22-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2675">'Identity'</span></span>
- <span data-ttu-id="42b22-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2677">'Razor'</span></span>
- <span data-ttu-id="42b22-2678">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2679">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2680">'Blazor'</span></span>
- <span data-ttu-id="42b22-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2681">'Identity'</span></span>
- <span data-ttu-id="42b22-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2683">'Razor'</span></span>
- <span data-ttu-id="42b22-2684">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2685">-----------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2686">'Blazor'</span></span>
- <span data-ttu-id="42b22-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2687">'Identity'</span></span>
- <span data-ttu-id="42b22-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2689">'Razor'</span></span>
- <span data-ttu-id="42b22-2690">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2691">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2692">'Blazor'</span></span>
- <span data-ttu-id="42b22-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2693">'Identity'</span></span>
- <span data-ttu-id="42b22-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2695">'Razor'</span></span>
- <span data-ttu-id="42b22-2696">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2697">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2698">'Blazor'</span></span>
- <span data-ttu-id="42b22-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2699">'Identity'</span></span>
- <span data-ttu-id="42b22-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2701">'Razor'</span></span>
- <span data-ttu-id="42b22-2702">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2703">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2704">'Blazor'</span></span>
- <span data-ttu-id="42b22-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2705">'Identity'</span></span>
- <span data-ttu-id="42b22-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2707">'Razor'</span></span>
- <span data-ttu-id="42b22-2708">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2709">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2710">'Blazor'</span></span>
- <span data-ttu-id="42b22-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2711">'Identity'</span></span>
- <span data-ttu-id="42b22-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2713">'Razor'</span></span>
- <span data-ttu-id="42b22-2714">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2715">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2716">'Blazor'</span></span>
- <span data-ttu-id="42b22-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2717">'Identity'</span></span>
- <span data-ttu-id="42b22-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2719">'Razor'</span></span>
- <span data-ttu-id="42b22-2720">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2721">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2722">'Blazor'</span></span>
- <span data-ttu-id="42b22-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2723">'Identity'</span></span>
- <span data-ttu-id="42b22-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2725">'Razor'</span></span>
- <span data-ttu-id="42b22-2726">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2727">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2728">'Blazor'</span></span>
- <span data-ttu-id="42b22-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2729">'Identity'</span></span>
- <span data-ttu-id="42b22-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2731">'Razor'</span></span>
- <span data-ttu-id="42b22-2732">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2733">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2734">'Blazor'</span></span>
- <span data-ttu-id="42b22-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2735">'Identity'</span></span>
- <span data-ttu-id="42b22-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2737">'Razor'</span></span>
- <span data-ttu-id="42b22-2738">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2739">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2740">'Blazor'</span></span>
- <span data-ttu-id="42b22-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2741">'Identity'</span></span>
- <span data-ttu-id="42b22-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2743">'Razor'</span></span>
- <span data-ttu-id="42b22-2744">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2745">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2746">'Blazor'</span></span>
- <span data-ttu-id="42b22-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2747">'Identity'</span></span>
- <span data-ttu-id="42b22-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2749">'Razor'</span></span>
- <span data-ttu-id="42b22-2750">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2751">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2752">'Blazor'</span></span>
- <span data-ttu-id="42b22-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2753">'Identity'</span></span>
- <span data-ttu-id="42b22-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2755">'Razor'</span></span>
- <span data-ttu-id="42b22-2756">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2757">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2758">'Blazor'</span></span>
- <span data-ttu-id="42b22-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2759">'Identity'</span></span>
- <span data-ttu-id="42b22-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2761">'Razor'</span></span>
- <span data-ttu-id="42b22-2762">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="42b22-2763">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="42b22-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="42b22-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2764">'Blazor'</span></span>
- <span data-ttu-id="42b22-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="42b22-2765">'Identity'</span></span>
- <span data-ttu-id="42b22-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="42b22-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="42b22-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="42b22-2767">'Razor'</span></span>
- <span data-ttu-id="42b22-2768">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="42b22-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="42b22-2769">-----------------: | | 0 | Hodnotab | | 1 | valueC | | 2 | Oceněno |</span><span class="sxs-lookup"><span data-stu-id="42b22-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="42b22-2770">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="42b22-2770">Custom configuration provider</span></span>

<span data-ttu-id="42b22-2771">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="42b22-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="42b22-2772">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="42b22-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="42b22-2773">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="42b22-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="42b22-2774">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42b22-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="42b22-2775">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="42b22-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="42b22-2776">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="42b22-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="42b22-2777">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="42b22-2778">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="42b22-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="42b22-2779">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="42b22-2780">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="42b22-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="42b22-2781">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="42b22-2782">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="42b22-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="42b22-2783">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="42b22-2784">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="42b22-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="42b22-2785">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="42b22-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="42b22-2786">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="42b22-2787">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="42b22-2788">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="42b22-2789">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="42b22-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="42b22-2790">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="42b22-2790">Access configuration during startup</span></span>

<span data-ttu-id="42b22-2791">Vložení `IConfiguration` do `Startup` konstruktoru pro přístup k hodnotám konfigurace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="42b22-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="42b22-2792">Chcete-li získat přístup ke konfiguraci v `Startup.Configure` , buď `IConfiguration` přímo do metody, nebo použijte instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="42b22-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="42b22-2793">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="42b22-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="42b22-2794">Přístup ke konfiguraci na Razor stránce stránky nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="42b22-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="42b22-2795">Chcete-li získat přístup k nastavení konfigurace na Razor stránce stránky nebo zobrazení MVC, [přidejte direktivu using](xref:mvc/views/razor#using) ([Referenční dokumentace jazyka C#: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> ji do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="42b22-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="42b22-2796">Na Razor stránce stránky:</span><span class="sxs-lookup"><span data-stu-id="42b22-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="42b22-2797">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="42b22-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="42b22-2798">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="42b22-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="42b22-2799"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="42b22-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="42b22-2800">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="42b22-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42b22-2801">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="42b22-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

---
<span data-ttu-id="9ed47-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-102">'Blazor'</span></span>
- <span data-ttu-id="9ed47-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-103">'Identity'</span></span>
- <span data-ttu-id="9ed47-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-105">'Razor'</span></span>
- <span data-ttu-id="9ed47-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="9ed47-107">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ed47-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="9ed47-108">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="9ed47-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ed47-109">Konfigurace v ASP.NET Core provádí použití jednoho nebo více [poskytovatelů konfigurace](#cp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="9ed47-110">Poskytovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="9ed47-111">Soubory nastavení, například *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="9ed47-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="9ed47-112">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-112">Environment variables</span></span>
* <span data-ttu-id="9ed47-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9ed47-113">Azure Key Vault</span></span>
* <span data-ttu-id="9ed47-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9ed47-114">Azure App Configuration</span></span>
* <span data-ttu-id="9ed47-115">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="9ed47-115">Command-line arguments</span></span>
* <span data-ttu-id="9ed47-116">Vlastní zprostředkovatelé, nainstalované nebo vytvořené</span><span class="sxs-lookup"><span data-stu-id="9ed47-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="9ed47-117">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="9ed47-117">Directory files</span></span>
* <span data-ttu-id="9ed47-118">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="9ed47-118">In-memory .NET objects</span></span>

<span data-ttu-id="9ed47-119">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ed47-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="9ed47-120">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ed47-120">Default configuration</span></span>

<span data-ttu-id="9ed47-121">ASP.NET Core webové aplikace vytvořené pomocí [dotnet New](/dotnet/core/tools/dotnet-new) nebo sady Visual Studio generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="9ed47-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="9ed47-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="9ed47-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="9ed47-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá existující `IConfiguration` jako zdroj.</span><span class="sxs-lookup"><span data-stu-id="9ed47-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="9ed47-124">Ve výchozím případu konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj konfigurace _aplikace_ .</span><span class="sxs-lookup"><span data-stu-id="9ed47-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="9ed47-125">[appSettings. JSON](#appsettingsjson) s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="9ed47-126">*appSettings.* `Environment` *. JSON* s použitím [poskytovatele konfigurace JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="9ed47-127">Například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="9ed47-128">[Tajné kódy aplikace](xref:security/app-secrets) v případě, že aplikace běží v `Development` prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="9ed47-129">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="9ed47-130">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line).</span><span class="sxs-lookup"><span data-stu-id="9ed47-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="9ed47-131">Poskytovatelé konfigurace, kteří jsou přidáni později, přepíší předchozí nastavení klíče.</span><span class="sxs-lookup"><span data-stu-id="9ed47-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="9ed47-132">Například pokud `MyKey` je nastavena v souboru *appSettings. JSON* i v prostředí, je použita hodnota prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="9ed47-133">Pomocí výchozích zprostředkovatelů konfigurace přepíše [Poskytovatel konfigurace příkazového řádku](#command-line-configuration-provider) všechny ostatní poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="9ed47-134">Další informace o najdete v `CreateDefaultBuilder` tématu [výchozí nastavení tvůrce](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="9ed47-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="9ed47-135">Následující kód zobrazuje povolené poskytovatele konfigurace v pořadí, v jakém byly přidány:</span><span class="sxs-lookup"><span data-stu-id="9ed47-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="9ed47-136">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="9ed47-136">appsettings.json</span></span>

<span data-ttu-id="9ed47-137">Vezměte v úvahu následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="9ed47-138">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-139">Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načtení konfigurace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="9ed47-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="9ed47-140">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="9ed47-140">*appsettings.json*</span></span>
1. <span data-ttu-id="9ed47-141">*appSettings.* `Environment` *. JSON* : například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="9ed47-142">Verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="9ed47-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="9ed47-143">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9ed47-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9ed47-144">*appSettings*. `Environment` .. hodnoty *JSON* přepíší klíče v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="9ed47-145">Ve výchozím nastavení by například:</span><span class="sxs-lookup"><span data-stu-id="9ed47-145">For example, by default:</span></span>

* <span data-ttu-id="9ed47-146">Ve vývoji, *appSettings*. ***Vývoj***. konfigurace *JSON* Přepisuje hodnoty nalezené v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="9ed47-147">V produkčním prostředí, *appSettings*. ***Provozní***prostředí. konfigurace *JSON* Přepisuje hodnoty nalezené v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="9ed47-148">Například při nasazení aplikace do Azure.</span><span class="sxs-lookup"><span data-stu-id="9ed47-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="9ed47-149">Vytvoření vazby hierarchických konfiguračních dat pomocí vzoru možností</span><span class="sxs-lookup"><span data-stu-id="9ed47-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="9ed47-150">Pomocí [výchozí](#default) konfigurace, *appSettings. JSON* a *appSettings.* `Environment` soubory *. JSON* jsou povolené pomocí [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="9ed47-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="9ed47-151">Změny provedené v souboru *appSettings. JSON* a *appSettings.* `Environment` soubor *. JSON* ***po*** spuštění aplikace si přečte [Poskytovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="9ed47-152">Informace o přidání dalších konfiguračních souborů JSON najdete v části [Poskytovatel konfigurace JSON](#jcp) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="9ed47-153">Správce zabezpečení a tajného klíče</span><span class="sxs-lookup"><span data-stu-id="9ed47-153">Security and secret manager</span></span>

<span data-ttu-id="9ed47-154">Pokyny pro konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="9ed47-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="9ed47-155">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="9ed47-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="9ed47-156">[Správce tajného klíče](xref:security/app-secrets) se dá použít k ukládání tajných kódů do vývoje.</span><span class="sxs-lookup"><span data-stu-id="9ed47-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="9ed47-157">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9ed47-158">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9ed47-159">Ve [výchozím nastavení](#default)přečte [správce tajných klíčů](xref:security/app-secrets) nastavení konfigurace po souboru *appSettings. JSON* a *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="9ed47-160">Další informace o ukládání hesel nebo jiných citlivých dat:</span><span class="sxs-lookup"><span data-stu-id="9ed47-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9ed47-161"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="9ed47-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9ed47-162">Správce tajného kódu používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="9ed47-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="9ed47-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9ed47-164">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9ed47-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="9ed47-165">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-165">Environment variables</span></span>

<span data-ttu-id="9ed47-166">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z proměnných prostředí klíč-hodnota po čtení *appSettings. JSON*, *appSettings.* `Environment` *. JSON*a [správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9ed47-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="9ed47-167">Proto hodnoty klíčů načtené z prostředí přepisují hodnoty načtené z hodnot *appSettings. JSON*, *appSettings.* `Environment` *. JSON*a správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9ed47-168">Následující `set` příkazy:</span><span class="sxs-lookup"><span data-stu-id="9ed47-168">The following `set` commands:</span></span>

* <span data-ttu-id="9ed47-169">Nastavte klíče prostředí a hodnoty v [předchozím příkladu](#appsettingsjson) ve Windows.</span><span class="sxs-lookup"><span data-stu-id="9ed47-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="9ed47-170">Při použití [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)otestujte nastavení.</span><span class="sxs-lookup"><span data-stu-id="9ed47-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="9ed47-171">`dotnet run`Příkaz musí být spuštěn v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="9ed47-172">Předchozí nastavení prostředí:</span><span class="sxs-lookup"><span data-stu-id="9ed47-172">The preceding environment settings:</span></span>

* <span data-ttu-id="9ed47-173">Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.</span><span class="sxs-lookup"><span data-stu-id="9ed47-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="9ed47-174">Nebudou přečteny pomocí prohlížečů spuštěných v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9ed47-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="9ed47-175">K nastavení klíčů a hodnot prostředí ve Windows se dají použít následující příkazy [setx](/windows-server/administration/windows-commands/setx) .</span><span class="sxs-lookup"><span data-stu-id="9ed47-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="9ed47-176">Na rozdíl `set` od `setx` platí, že nastavení jsou trvalá.</span><span class="sxs-lookup"><span data-stu-id="9ed47-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="9ed47-177">`/M`nastaví proměnnou v prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="9ed47-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="9ed47-178">Pokud se `/M` přepínač nepoužívá, je nastavena proměnná prostředí uživatele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="9ed47-179">Chcete-li otestovat předchozí příkazy, přepište *appSettings. JSON* a *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="9ed47-180">Se sadou Visual Studio: Ukončete a restartujte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9ed47-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="9ed47-181">Pomocí rozhraní příkazového řádku: spusťte nové příkazové okno a zadejte `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="9ed47-182">Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem pro určení předpony pro proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="9ed47-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="9ed47-183">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="9ed47-183">In the preceding code:</span></span>

* <span data-ttu-id="9ed47-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="9ed47-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="9ed47-185">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="9ed47-186">Proměnné prostředí nastavené s `MyCustomPrefix_` předponou přepíšou [výchozí poskytovatele konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="9ed47-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="9ed47-187">To zahrnuje proměnné prostředí bez předpony.</span><span class="sxs-lookup"><span data-stu-id="9ed47-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="9ed47-188">Pokud jsou načteny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="9ed47-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="9ed47-189">Vlastní předponu otestujete následujícími příkazy:</span><span class="sxs-lookup"><span data-stu-id="9ed47-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="9ed47-190">[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty příkazového řádku s předponou `DOTNET_` a `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="9ed47-191">`DOTNET_` `ASPNETCORE_` Předpony a jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="9ed47-192">Další informace o konfiguraci hostitele a aplikace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="9ed47-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="9ed47-193">V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** .</span><span class="sxs-lookup"><span data-stu-id="9ed47-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="9ed47-194">Azure App Service nastavení aplikace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="9ed47-195">Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.</span><span class="sxs-lookup"><span data-stu-id="9ed47-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="9ed47-196">Vystaveno jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-196">Exposed as environment variables.</span></span>

<span data-ttu-id="9ed47-197">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="9ed47-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9ed47-198">Informace o připojovacích řetězcích Azure Database najdete v tématu [předpony připojovacího řetězce](#constr) .</span><span class="sxs-lookup"><span data-stu-id="9ed47-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="9ed47-199">Příkazový řádek</span><span class="sxs-lookup"><span data-stu-id="9ed47-199">Command-line</span></span>

<span data-ttu-id="9ed47-200">Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načte konfiguraci z dvojice klíč-hodnota argumentu klíč-hodnota po následujících zdrojích konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="9ed47-201">*appSettings. JSON* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="9ed47-202">[Tajné kódy aplikací (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9ed47-203">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-203">Environment variables.</span></span>

<span data-ttu-id="9ed47-204">Ve [výchozím nastavení](#default)jsou konfigurační hodnoty nastavené pro přepisy hodnot konfigurace na příkazovém řádku nastavené se všemi ostatními zprostředkovateli konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="9ed47-205">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="9ed47-205">Command-line arguments</span></span>

<span data-ttu-id="9ed47-206">Následující příkaz nastaví klíče a hodnoty pomocí `=` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="9ed47-207">Následující příkaz nastaví klíče a hodnoty pomocí `/` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="9ed47-208">Následující příkaz nastaví klíče a hodnoty pomocí `--` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="9ed47-209">Hodnota klíče:</span><span class="sxs-lookup"><span data-stu-id="9ed47-209">The key value:</span></span>

* <span data-ttu-id="9ed47-210">Musí následovat po `=` , nebo klíč musí mít předponu `--` nebo, `/` Pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="9ed47-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="9ed47-211">Není vyžadováno `=` , pokud je použit.</span><span class="sxs-lookup"><span data-stu-id="9ed47-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="9ed47-212">Například, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="9ed47-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="9ed47-213">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které `=` se používají s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="9ed47-214">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="9ed47-214">Switch mappings</span></span>

<span data-ttu-id="9ed47-215">Mapování přepínačů povolují logiku nahrazení názvu **klíče** .</span><span class="sxs-lookup"><span data-stu-id="9ed47-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="9ed47-216">Poskytněte slovník pro nahrazení přepínačů v <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodě.</span><span class="sxs-lookup"><span data-stu-id="9ed47-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9ed47-217">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="9ed47-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9ed47-218">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota Dictionary zpátky, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9ed47-219">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="9ed47-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9ed47-220">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="9ed47-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9ed47-221">Přepínače musí začínat na `-` nebo `--` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="9ed47-222">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="9ed47-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9ed47-223">Chcete-li použít slovník mapování přepínačů, předejte jej do volání `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="9ed47-224">Následující kód ukazuje klíčové hodnoty pro nahrazené klíče:</span><span class="sxs-lookup"><span data-stu-id="9ed47-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-225">Spusťte následující příkaz pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="9ed47-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="9ed47-226">Poznámka: v současné době `=` nelze použít k nastavení hodnot nahrazení klíče jedinou pomlčkou `-` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="9ed47-227">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="9ed47-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="9ed47-228">Následující příkaz funguje pro otestování nahrazení klíče:</span><span class="sxs-lookup"><span data-stu-id="9ed47-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="9ed47-229">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="9ed47-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9ed47-230">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9ed47-231">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty i slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9ed47-232">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="9ed47-232">Hierarchical configuration data</span></span>

<span data-ttu-id="9ed47-233">Rozhraní API pro konfiguraci čte hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9ed47-234">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="9ed47-235">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-236">Upřednostňovaným způsobem, jak číst hierarchická konfigurační data, je použít vzor možností.</span><span class="sxs-lookup"><span data-stu-id="9ed47-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="9ed47-237">Další informace najdete v tématu [vázání hierarchických konfiguračních dat](#optpat) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="9ed47-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="9ed47-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9ed47-239">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="9ed47-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="9ed47-240">Konfigurační klíče a hodnoty</span><span class="sxs-lookup"><span data-stu-id="9ed47-240">Configuration keys and values</span></span>

<span data-ttu-id="9ed47-241">Konfigurační klíče:</span><span class="sxs-lookup"><span data-stu-id="9ed47-241">Configuration keys:</span></span>

* <span data-ttu-id="9ed47-242">Rozlišují se malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="9ed47-242">Are case-insensitive.</span></span> <span data-ttu-id="9ed47-243">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="9ed47-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9ed47-244">Pokud je klíč a hodnota nastavená ve více poskytovatelích konfigurace, použije se hodnota z posledního přidaného zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="9ed47-245">Další informace najdete v tématu [výchozí konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="9ed47-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="9ed47-246">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="9ed47-246">Hierarchical keys</span></span>
  * <span data-ttu-id="9ed47-247">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="9ed47-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9ed47-248">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="9ed47-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9ed47-249">Dvojité podtržítko, `__` , je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="9ed47-250">V Azure Key Vault hierarchické klíče používají `--` jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="9ed47-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="9ed47-251">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) se `--` `:` při načtení tajných klíčů do konfigurace aplikace automaticky nahradí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9ed47-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9ed47-253">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#boa) .</span><span class="sxs-lookup"><span data-stu-id="9ed47-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="9ed47-254">Konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="9ed47-254">Configuration values:</span></span>

* <span data-ttu-id="9ed47-255">Jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="9ed47-255">Are strings.</span></span>
* <span data-ttu-id="9ed47-256">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="9ed47-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="9ed47-257">Poskytovatelé konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ed47-257">Configuration providers</span></span>

<span data-ttu-id="9ed47-258">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9ed47-259">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="9ed47-259">Provider</span></span> | <span data-ttu-id="9ed47-260">Poskytuje konfiguraci z</span><span class="sxs-lookup"><span data-stu-id="9ed47-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="9ed47-261">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-262">'Blazor'</span></span>
- <span data-ttu-id="9ed47-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-263">'Identity'</span></span>
- <span data-ttu-id="9ed47-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-265">'Razor'</span></span>
- <span data-ttu-id="9ed47-266">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-267">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-268">'Blazor'</span></span>
- <span data-ttu-id="9ed47-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-269">'Identity'</span></span>
- <span data-ttu-id="9ed47-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-271">'Razor'</span></span>
- <span data-ttu-id="9ed47-272">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-272">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-273">---- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-274">'Blazor'</span></span>
- <span data-ttu-id="9ed47-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-275">'Identity'</span></span>
- <span data-ttu-id="9ed47-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-277">'Razor'</span></span>
- <span data-ttu-id="9ed47-278">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-279">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-280">'Blazor'</span></span>
- <span data-ttu-id="9ed47-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-281">'Identity'</span></span>
- <span data-ttu-id="9ed47-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-283">'Razor'</span></span>
- <span data-ttu-id="9ed47-284">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-285">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-286">'Blazor'</span></span>
- <span data-ttu-id="9ed47-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-287">'Identity'</span></span>
- <span data-ttu-id="9ed47-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-289">'Razor'</span></span>
- <span data-ttu-id="9ed47-290">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-291">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-292">'Blazor'</span></span>
- <span data-ttu-id="9ed47-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-293">'Identity'</span></span>
- <span data-ttu-id="9ed47-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-295">'Razor'</span></span>
- <span data-ttu-id="9ed47-296">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-297">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-298">'Blazor'</span></span>
- <span data-ttu-id="9ed47-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-299">'Identity'</span></span>
- <span data-ttu-id="9ed47-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-301">'Razor'</span></span>
- <span data-ttu-id="9ed47-302">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-303">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-304">'Blazor'</span></span>
- <span data-ttu-id="9ed47-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-305">'Identity'</span></span>
- <span data-ttu-id="9ed47-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-307">'Razor'</span></span>
- <span data-ttu-id="9ed47-308">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-309">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-310">'Blazor'</span></span>
- <span data-ttu-id="9ed47-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-311">'Identity'</span></span>
- <span data-ttu-id="9ed47-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-313">'Razor'</span></span>
- <span data-ttu-id="9ed47-314">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-315">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-316">'Blazor'</span></span>
- <span data-ttu-id="9ed47-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-317">'Identity'</span></span>
- <span data-ttu-id="9ed47-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-319">'Razor'</span></span>
- <span data-ttu-id="9ed47-320">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-321">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-322">'Blazor'</span></span>
- <span data-ttu-id="9ed47-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-323">'Identity'</span></span>
- <span data-ttu-id="9ed47-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-325">'Razor'</span></span>
- <span data-ttu-id="9ed47-326">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-327">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-328">'Blazor'</span></span>
- <span data-ttu-id="9ed47-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-329">'Identity'</span></span>
- <span data-ttu-id="9ed47-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-331">'Razor'</span></span>
- <span data-ttu-id="9ed47-332">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-333">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-334">'Blazor'</span></span>
- <span data-ttu-id="9ed47-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-335">'Identity'</span></span>
- <span data-ttu-id="9ed47-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-337">'Razor'</span></span>
- <span data-ttu-id="9ed47-338">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-339">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-340">'Blazor'</span></span>
- <span data-ttu-id="9ed47-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-341">'Identity'</span></span>
- <span data-ttu-id="9ed47-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-343">'Razor'</span></span>
- <span data-ttu-id="9ed47-344">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-345">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-346">'Blazor'</span></span>
- <span data-ttu-id="9ed47-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-347">'Identity'</span></span>
- <span data-ttu-id="9ed47-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-349">'Razor'</span></span>
- <span data-ttu-id="9ed47-350">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-351">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-352">'Blazor'</span></span>
- <span data-ttu-id="9ed47-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-353">'Identity'</span></span>
- <span data-ttu-id="9ed47-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-355">'Razor'</span></span>
- <span data-ttu-id="9ed47-356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-357">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-358">'Blazor'</span></span>
- <span data-ttu-id="9ed47-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-359">'Identity'</span></span>
- <span data-ttu-id="9ed47-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-361">'Razor'</span></span>
- <span data-ttu-id="9ed47-362">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-362">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-363">------------------ | | [Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault | | [Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Konfigurace aplikace Azure | | [Poskytovatel konfigurace příkazového řádku](#clcp) | Parametry příkazového řádku | | [Vlastní poskytovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj | | [Poskytovatel konfigurace proměnných prostředí](#evcp) | Proměnné prostředí | | [Poskytovatel konfigurace souboru](#file-configuration-provider) | Soubory INI, JSON a XML | | [Poskytovatel konfigurace klíče na soubor](#key-per-file-configuration-provider) | Soubory adresáře | | [Poskytovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti | | [Správce tajných klíčů](xref:security/app-secrets) | Soubor v adresáři profilu uživatele |</span><span class="sxs-lookup"><span data-stu-id="9ed47-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="9ed47-364">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou zadáni poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="9ed47-365">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="9ed47-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9ed47-366">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="9ed47-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9ed47-367">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="9ed47-367">*appsettings.json*</span></span>
1. <span data-ttu-id="9ed47-368">*appSettings*. `Environment` .. formát *JSON*</span><span class="sxs-lookup"><span data-stu-id="9ed47-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="9ed47-369">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="9ed47-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="9ed47-370">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="9ed47-371">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="9ed47-372">Běžný postup je přidat poskytovatele konfigurace příkazového řádku na poslední v řadě poskytovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="9ed47-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9ed47-373">Předchozí sekvence zprostředkovatelů se používá ve [výchozí konfiguraci](#default).</span><span class="sxs-lookup"><span data-stu-id="9ed47-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="9ed47-374">Předpony připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="9ed47-374">Connection string prefixes</span></span>

<span data-ttu-id="9ed47-375">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro proměnné prostředí pro čtyři připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="9ed47-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="9ed47-376">Tyto připojovací řetězce jsou součástí konfigurace připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9ed47-377">Proměnné prostředí s předponami, které jsou uvedené v tabulce, se načtou do aplikace s [výchozí konfigurací](#default) nebo když není dodána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9ed47-378">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="9ed47-378">Connection string prefix</span></span> | <span data-ttu-id="9ed47-379">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="9ed47-379">Provider</span></span> |
| ---
<span data-ttu-id="9ed47-380">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-381">'Blazor'</span></span>
- <span data-ttu-id="9ed47-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-382">'Identity'</span></span>
- <span data-ttu-id="9ed47-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-384">'Razor'</span></span>
- <span data-ttu-id="9ed47-385">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-386">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-387">'Blazor'</span></span>
- <span data-ttu-id="9ed47-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-388">'Identity'</span></span>
- <span data-ttu-id="9ed47-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-390">'Razor'</span></span>
- <span data-ttu-id="9ed47-391">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-392">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-393">'Blazor'</span></span>
- <span data-ttu-id="9ed47-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-394">'Identity'</span></span>
- <span data-ttu-id="9ed47-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-396">'Razor'</span></span>
- <span data-ttu-id="9ed47-397">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-398">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-399">'Blazor'</span></span>
- <span data-ttu-id="9ed47-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-400">'Identity'</span></span>
- <span data-ttu-id="9ed47-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-402">'Razor'</span></span>
- <span data-ttu-id="9ed47-403">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-404">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-405">'Blazor'</span></span>
- <span data-ttu-id="9ed47-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-406">'Identity'</span></span>
- <span data-ttu-id="9ed47-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-408">'Razor'</span></span>
- <span data-ttu-id="9ed47-409">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-410">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-411">'Blazor'</span></span>
- <span data-ttu-id="9ed47-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-412">'Identity'</span></span>
- <span data-ttu-id="9ed47-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-414">'Razor'</span></span>
- <span data-ttu-id="9ed47-415">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-416">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-417">'Blazor'</span></span>
- <span data-ttu-id="9ed47-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-418">'Identity'</span></span>
- <span data-ttu-id="9ed47-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-420">'Razor'</span></span>
- <span data-ttu-id="9ed47-421">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-422">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-423">'Blazor'</span></span>
- <span data-ttu-id="9ed47-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-424">'Identity'</span></span>
- <span data-ttu-id="9ed47-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-426">'Razor'</span></span>
- <span data-ttu-id="9ed47-427">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-428">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-429">'Blazor'</span></span>
- <span data-ttu-id="9ed47-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-430">'Identity'</span></span>
- <span data-ttu-id="9ed47-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-432">'Razor'</span></span>
- <span data-ttu-id="9ed47-433">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-434">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-435">'Blazor'</span></span>
- <span data-ttu-id="9ed47-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-436">'Identity'</span></span>
- <span data-ttu-id="9ed47-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-438">'Razor'</span></span>
- <span data-ttu-id="9ed47-439">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-439">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-440">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-441">'Blazor'</span></span>
- <span data-ttu-id="9ed47-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-442">'Identity'</span></span>
- <span data-ttu-id="9ed47-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-444">'Razor'</span></span>
- <span data-ttu-id="9ed47-445">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-446">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-447">'Blazor'</span></span>
- <span data-ttu-id="9ed47-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-448">'Identity'</span></span>
- <span data-ttu-id="9ed47-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-450">'Razor'</span></span>
- <span data-ttu-id="9ed47-451">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-451">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-452">---- | | `CUSTOMCONNSTR_` | Vlastní poskytovatel | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="9ed47-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="9ed47-453">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="9ed47-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9ed47-454">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="9ed47-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9ed47-455">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="9ed47-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9ed47-456">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-456">Environment variable key</span></span> | <span data-ttu-id="9ed47-457">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="9ed47-457">Converted configuration key</span></span> | <span data-ttu-id="9ed47-458">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="9ed47-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="9ed47-459">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-460">'Blazor'</span></span>
- <span data-ttu-id="9ed47-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-461">'Identity'</span></span>
- <span data-ttu-id="9ed47-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-463">'Razor'</span></span>
- <span data-ttu-id="9ed47-464">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-465">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-466">'Blazor'</span></span>
- <span data-ttu-id="9ed47-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-467">'Identity'</span></span>
- <span data-ttu-id="9ed47-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-469">'Razor'</span></span>
- <span data-ttu-id="9ed47-470">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-471">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-472">'Blazor'</span></span>
- <span data-ttu-id="9ed47-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-473">'Identity'</span></span>
- <span data-ttu-id="9ed47-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-475">'Razor'</span></span>
- <span data-ttu-id="9ed47-476">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-477">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-478">'Blazor'</span></span>
- <span data-ttu-id="9ed47-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-479">'Identity'</span></span>
- <span data-ttu-id="9ed47-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-481">'Razor'</span></span>
- <span data-ttu-id="9ed47-482">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-483">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-484">'Blazor'</span></span>
- <span data-ttu-id="9ed47-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-485">'Identity'</span></span>
- <span data-ttu-id="9ed47-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-487">'Razor'</span></span>
- <span data-ttu-id="9ed47-488">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-489">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-490">'Blazor'</span></span>
- <span data-ttu-id="9ed47-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-491">'Identity'</span></span>
- <span data-ttu-id="9ed47-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-493">'Razor'</span></span>
- <span data-ttu-id="9ed47-494">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-495">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-496">'Blazor'</span></span>
- <span data-ttu-id="9ed47-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-497">'Identity'</span></span>
- <span data-ttu-id="9ed47-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-499">'Razor'</span></span>
- <span data-ttu-id="9ed47-500">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-501">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-502">'Blazor'</span></span>
- <span data-ttu-id="9ed47-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-503">'Identity'</span></span>
- <span data-ttu-id="9ed47-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-505">'Razor'</span></span>
- <span data-ttu-id="9ed47-506">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-507">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-508">'Blazor'</span></span>
- <span data-ttu-id="9ed47-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-509">'Identity'</span></span>
- <span data-ttu-id="9ed47-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-511">'Razor'</span></span>
- <span data-ttu-id="9ed47-512">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-513">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-514">'Blazor'</span></span>
- <span data-ttu-id="9ed47-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-515">'Identity'</span></span>
- <span data-ttu-id="9ed47-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-517">'Razor'</span></span>
- <span data-ttu-id="9ed47-518">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-518">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-519">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-520">'Blazor'</span></span>
- <span data-ttu-id="9ed47-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-521">'Identity'</span></span>
- <span data-ttu-id="9ed47-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-523">'Razor'</span></span>
- <span data-ttu-id="9ed47-524">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-525">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-526">'Blazor'</span></span>
- <span data-ttu-id="9ed47-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-527">'Identity'</span></span>
- <span data-ttu-id="9ed47-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-529">'Razor'</span></span>
- <span data-ttu-id="9ed47-530">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-531">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-532">'Blazor'</span></span>
- <span data-ttu-id="9ed47-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-533">'Identity'</span></span>
- <span data-ttu-id="9ed47-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-535">'Razor'</span></span>
- <span data-ttu-id="9ed47-536">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-537">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-538">'Blazor'</span></span>
- <span data-ttu-id="9ed47-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-539">'Identity'</span></span>
- <span data-ttu-id="9ed47-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-541">'Razor'</span></span>
- <span data-ttu-id="9ed47-542">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-543">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-544">'Blazor'</span></span>
- <span data-ttu-id="9ed47-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-545">'Identity'</span></span>
- <span data-ttu-id="9ed47-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-547">'Razor'</span></span>
- <span data-ttu-id="9ed47-548">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-549">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-550">'Blazor'</span></span>
- <span data-ttu-id="9ed47-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-551">'Identity'</span></span>
- <span data-ttu-id="9ed47-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-553">'Razor'</span></span>
- <span data-ttu-id="9ed47-554">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-555">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-556">'Blazor'</span></span>
- <span data-ttu-id="9ed47-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-557">'Identity'</span></span>
- <span data-ttu-id="9ed47-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-559">'Razor'</span></span>
- <span data-ttu-id="9ed47-560">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-561">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-562">'Blazor'</span></span>
- <span data-ttu-id="9ed47-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-563">'Identity'</span></span>
- <span data-ttu-id="9ed47-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-565">'Razor'</span></span>
- <span data-ttu-id="9ed47-566">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-567">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-568">'Blazor'</span></span>
- <span data-ttu-id="9ed47-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-569">'Identity'</span></span>
- <span data-ttu-id="9ed47-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-571">'Razor'</span></span>
- <span data-ttu-id="9ed47-572">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-573">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-574">'Blazor'</span></span>
- <span data-ttu-id="9ed47-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-575">'Identity'</span></span>
- <span data-ttu-id="9ed47-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-577">'Razor'</span></span>
- <span data-ttu-id="9ed47-578">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-579">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-580">'Blazor'</span></span>
- <span data-ttu-id="9ed47-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-581">'Identity'</span></span>
- <span data-ttu-id="9ed47-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-583">'Razor'</span></span>
- <span data-ttu-id="9ed47-584">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-584">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-585">-------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-586">'Blazor'</span></span>
- <span data-ttu-id="9ed47-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-587">'Identity'</span></span>
- <span data-ttu-id="9ed47-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-589">'Razor'</span></span>
- <span data-ttu-id="9ed47-590">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-591">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-592">'Blazor'</span></span>
- <span data-ttu-id="9ed47-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-593">'Identity'</span></span>
- <span data-ttu-id="9ed47-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-595">'Razor'</span></span>
- <span data-ttu-id="9ed47-596">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-597">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-598">'Blazor'</span></span>
- <span data-ttu-id="9ed47-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-599">'Identity'</span></span>
- <span data-ttu-id="9ed47-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-601">'Razor'</span></span>
- <span data-ttu-id="9ed47-602">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-603">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-604">'Blazor'</span></span>
- <span data-ttu-id="9ed47-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-605">'Identity'</span></span>
- <span data-ttu-id="9ed47-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-607">'Razor'</span></span>
- <span data-ttu-id="9ed47-608">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-609">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-610">'Blazor'</span></span>
- <span data-ttu-id="9ed47-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-611">'Identity'</span></span>
- <span data-ttu-id="9ed47-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-613">'Razor'</span></span>
- <span data-ttu-id="9ed47-614">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-615">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-616">'Blazor'</span></span>
- <span data-ttu-id="9ed47-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-617">'Identity'</span></span>
- <span data-ttu-id="9ed47-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-619">'Razor'</span></span>
- <span data-ttu-id="9ed47-620">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-621">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-622">'Blazor'</span></span>
- <span data-ttu-id="9ed47-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-623">'Identity'</span></span>
- <span data-ttu-id="9ed47-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-625">'Razor'</span></span>
- <span data-ttu-id="9ed47-626">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-627">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-628">'Blazor'</span></span>
- <span data-ttu-id="9ed47-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-629">'Identity'</span></span>
- <span data-ttu-id="9ed47-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-631">'Razor'</span></span>
- <span data-ttu-id="9ed47-632">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-633">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-634">'Blazor'</span></span>
- <span data-ttu-id="9ed47-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-635">'Identity'</span></span>
- <span data-ttu-id="9ed47-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-637">'Razor'</span></span>
- <span data-ttu-id="9ed47-638">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-639">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-640">'Blazor'</span></span>
- <span data-ttu-id="9ed47-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-641">'Identity'</span></span>
- <span data-ttu-id="9ed47-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-643">'Razor'</span></span>
- <span data-ttu-id="9ed47-644">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-645">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-646">'Blazor'</span></span>
- <span data-ttu-id="9ed47-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-647">'Identity'</span></span>
- <span data-ttu-id="9ed47-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-649">'Razor'</span></span>
- <span data-ttu-id="9ed47-650">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-651">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-652">'Blazor'</span></span>
- <span data-ttu-id="9ed47-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-653">'Identity'</span></span>
- <span data-ttu-id="9ed47-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-655">'Razor'</span></span>
- <span data-ttu-id="9ed47-656">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-657">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-658">'Blazor'</span></span>
- <span data-ttu-id="9ed47-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-659">'Identity'</span></span>
- <span data-ttu-id="9ed47-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-661">'Razor'</span></span>
- <span data-ttu-id="9ed47-662">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-663">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-664">'Blazor'</span></span>
- <span data-ttu-id="9ed47-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-665">'Identity'</span></span>
- <span data-ttu-id="9ed47-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-667">'Razor'</span></span>
- <span data-ttu-id="9ed47-668">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-669">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-670">'Blazor'</span></span>
- <span data-ttu-id="9ed47-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-671">'Identity'</span></span>
- <span data-ttu-id="9ed47-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-673">'Razor'</span></span>
- <span data-ttu-id="9ed47-674">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-675">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-676">'Blazor'</span></span>
- <span data-ttu-id="9ed47-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-677">'Identity'</span></span>
- <span data-ttu-id="9ed47-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-679">'Razor'</span></span>
- <span data-ttu-id="9ed47-680">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-681">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-682">'Blazor'</span></span>
- <span data-ttu-id="9ed47-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-683">'Identity'</span></span>
- <span data-ttu-id="9ed47-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-685">'Razor'</span></span>
- <span data-ttu-id="9ed47-686">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-687">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-688">'Blazor'</span></span>
- <span data-ttu-id="9ed47-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-689">'Identity'</span></span>
- <span data-ttu-id="9ed47-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-691">'Razor'</span></span>
- <span data-ttu-id="9ed47-692">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-693">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-694">'Blazor'</span></span>
- <span data-ttu-id="9ed47-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-695">'Identity'</span></span>
- <span data-ttu-id="9ed47-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-697">'Razor'</span></span>
- <span data-ttu-id="9ed47-698">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-699">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-700">'Blazor'</span></span>
- <span data-ttu-id="9ed47-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-701">'Identity'</span></span>
- <span data-ttu-id="9ed47-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-703">'Razor'</span></span>
- <span data-ttu-id="9ed47-704">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-705">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-706">'Blazor'</span></span>
- <span data-ttu-id="9ed47-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-707">'Identity'</span></span>
- <span data-ttu-id="9ed47-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-709">'Razor'</span></span>
- <span data-ttu-id="9ed47-710">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-711">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-712">'Blazor'</span></span>
- <span data-ttu-id="9ed47-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-713">'Identity'</span></span>
- <span data-ttu-id="9ed47-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-715">'Razor'</span></span>
- <span data-ttu-id="9ed47-716">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-717">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-718">'Blazor'</span></span>
- <span data-ttu-id="9ed47-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-719">'Identity'</span></span>
- <span data-ttu-id="9ed47-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-721">'Razor'</span></span>
- <span data-ttu-id="9ed47-722">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-723">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-724">'Blazor'</span></span>
- <span data-ttu-id="9ed47-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-725">'Identity'</span></span>
- <span data-ttu-id="9ed47-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-727">'Razor'</span></span>
- <span data-ttu-id="9ed47-728">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-729">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-730">'Blazor'</span></span>
- <span data-ttu-id="9ed47-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-731">'Identity'</span></span>
- <span data-ttu-id="9ed47-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-733">'Razor'</span></span>
- <span data-ttu-id="9ed47-734">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-735">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-736">'Blazor'</span></span>
- <span data-ttu-id="9ed47-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-737">'Identity'</span></span>
- <span data-ttu-id="9ed47-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-739">'Razor'</span></span>
- <span data-ttu-id="9ed47-740">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-741">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-742">'Blazor'</span></span>
- <span data-ttu-id="9ed47-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-743">'Identity'</span></span>
- <span data-ttu-id="9ed47-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-745">'Razor'</span></span>
- <span data-ttu-id="9ed47-746">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-747">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-748">'Blazor'</span></span>
- <span data-ttu-id="9ed47-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-749">'Identity'</span></span>
- <span data-ttu-id="9ed47-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-751">'Razor'</span></span>
- <span data-ttu-id="9ed47-752">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-753">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-754">'Blazor'</span></span>
- <span data-ttu-id="9ed47-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-755">'Identity'</span></span>
- <span data-ttu-id="9ed47-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-757">'Razor'</span></span>
- <span data-ttu-id="9ed47-758">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-759">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-760">'Blazor'</span></span>
- <span data-ttu-id="9ed47-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-761">'Identity'</span></span>
- <span data-ttu-id="9ed47-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-763">'Razor'</span></span>
- <span data-ttu-id="9ed47-764">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-765">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-766">'Blazor'</span></span>
- <span data-ttu-id="9ed47-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-767">'Identity'</span></span>
- <span data-ttu-id="9ed47-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-769">'Razor'</span></span>
- <span data-ttu-id="9ed47-770">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-771">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-772">'Blazor'</span></span>
- <span data-ttu-id="9ed47-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-773">'Identity'</span></span>
- <span data-ttu-id="9ed47-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-775">'Razor'</span></span>
- <span data-ttu-id="9ed47-776">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-777">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-778">'Blazor'</span></span>
- <span data-ttu-id="9ed47-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-779">'Identity'</span></span>
- <span data-ttu-id="9ed47-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-781">'Razor'</span></span>
- <span data-ttu-id="9ed47-782">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-783">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-784">'Blazor'</span></span>
- <span data-ttu-id="9ed47-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-785">'Identity'</span></span>
- <span data-ttu-id="9ed47-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-787">'Razor'</span></span>
- <span data-ttu-id="9ed47-788">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-789">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-790">'Blazor'</span></span>
- <span data-ttu-id="9ed47-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-791">'Identity'</span></span>
- <span data-ttu-id="9ed47-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-793">'Razor'</span></span>
- <span data-ttu-id="9ed47-794">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-795">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-796">'Blazor'</span></span>
- <span data-ttu-id="9ed47-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-797">'Identity'</span></span>
- <span data-ttu-id="9ed47-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-799">'Razor'</span></span>
- <span data-ttu-id="9ed47-800">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-801">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-802">'Blazor'</span></span>
- <span data-ttu-id="9ed47-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-803">'Identity'</span></span>
- <span data-ttu-id="9ed47-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-805">'Razor'</span></span>
- <span data-ttu-id="9ed47-806">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-806">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="9ed47-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="9ed47-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9ed47-809">Hodnota: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9ed47-810">Hodnota: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9ed47-811">Osa`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="9ed47-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="9ed47-812">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="9ed47-812">JSON configuration provider</span></span>

<span data-ttu-id="9ed47-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načte konfiguraci z párů klíč-hodnota souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="9ed47-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="9ed47-814">Přetížení můžou specifikovat:</span><span class="sxs-lookup"><span data-stu-id="9ed47-814">Overloads can specify:</span></span>

* <span data-ttu-id="9ed47-815">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="9ed47-815">Whether the file is optional.</span></span>
* <span data-ttu-id="9ed47-816">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="9ed47-817">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="9ed47-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="9ed47-818">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="9ed47-818">The preceding code:</span></span>

* <span data-ttu-id="9ed47-819">Nakonfiguruje zprostředkovatele konfigurace JSON pro načtení souboru *MyConfig. JSON* s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="9ed47-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="9ed47-820">`optional: true`: Soubor je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="9ed47-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="9ed47-821">`reloadOnChange: true`: Soubor se znovu načte při uložení změn.</span><span class="sxs-lookup"><span data-stu-id="9ed47-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="9ed47-822">Přečte [výchozí poskytovatele konfigurace](#default) před souborem *MyConfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="9ed47-823">Nastavení v přepsání souboru *MyConfig. JSON* ve výchozích poskytovatelích konfigurace, včetně [poskytovatele konfigurace proměnných prostředí](#evcp) a [poskytovatele konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9ed47-824">***Obvykle nechcete, aby vlastní*** soubor JSON přepsal hodnoty nastavené ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a v [poskytovateli konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9ed47-825">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="9ed47-826">V předchozím kódu se jedná o nastavení v *MyConfig. JSON* a *MyConfig*. `Environment` . soubory *JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="9ed47-827">Přepsat nastavení v souboru *appSettings. JSON* a *appSettings*. `Environment` .. soubory *JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="9ed47-828">Jsou přepsány nastavením ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a [poskytovatelem konfigurace příkazového řádku](#clcp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9ed47-829">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyConfig. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="9ed47-830">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="9ed47-831">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="9ed47-831">File configuration provider</span></span>

<span data-ttu-id="9ed47-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9ed47-833">Následující poskytovatelé konfigurace jsou odvozeni z `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="9ed47-834">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="9ed47-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9ed47-835">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="9ed47-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="9ed47-836">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="9ed47-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9ed47-837">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="9ed47-837">INI configuration provider</span></span>

<span data-ttu-id="9ed47-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9ed47-839">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="9ed47-840">V předchozím kódu jsou nastavení v *souboru MyIniConfig. ini* a *MyIniConfig*. `Environment` . soubory *ini* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="9ed47-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="9ed47-841">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="9ed47-842">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="9ed47-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9ed47-843">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig. ini* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="9ed47-844">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="9ed47-845">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="9ed47-845">XML configuration provider</span></span>

<span data-ttu-id="9ed47-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9ed47-847">Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="9ed47-848">V předchozím kódu, nastavení v *souboru MyXMLFile. XML* a *MyXMLFile*. `Environment` . soubory *XML* jsou přepsány nastavením v:</span><span class="sxs-lookup"><span data-stu-id="9ed47-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="9ed47-849">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="9ed47-850">[Poskytovatel konfigurace příkazového řádku](#clcp)</span><span class="sxs-lookup"><span data-stu-id="9ed47-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9ed47-851">[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile. XML* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="9ed47-852">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-853">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="9ed47-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="9ed47-854">Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:</span><span class="sxs-lookup"><span data-stu-id="9ed47-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-855">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="9ed47-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9ed47-856">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9ed47-857">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="9ed47-857">key:attribute</span></span>
* <span data-ttu-id="9ed47-858">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="9ed47-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9ed47-859">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="9ed47-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="9ed47-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9ed47-861">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-861">The key is the file name.</span></span> <span data-ttu-id="9ed47-862">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-862">The value contains the file's contents.</span></span> <span data-ttu-id="9ed47-863">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="9ed47-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9ed47-864">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9ed47-865">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="9ed47-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9ed47-866">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="9ed47-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="9ed47-867">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="9ed47-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9ed47-868">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="9ed47-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9ed47-869">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9ed47-870">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9ed47-871">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="9ed47-872">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="9ed47-872">Memory configuration provider</span></span>

<span data-ttu-id="9ed47-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9ed47-874">Následující kód přidá kolekci paměti do konfiguračního systému:</span><span class="sxs-lookup"><span data-stu-id="9ed47-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="9ed47-875">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-876">V předchozím kódu `config.AddInMemoryCollection(Dict)` se přidá za [výchozí poskytovatelé konfigurace](#default).</span><span class="sxs-lookup"><span data-stu-id="9ed47-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="9ed47-877">Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="9ed47-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="9ed47-878">Viz [vazba pole](#boa) pro jiný příklad pomocí `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="9ed47-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="9ed47-879">GetValue</span></span>

<span data-ttu-id="9ed47-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:</span><span class="sxs-lookup"><span data-stu-id="9ed47-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-881">Pokud v předchozím kódu `NumberKey` není v konfiguraci nalezeno, `99` je použita výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9ed47-882">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="9ed47-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9ed47-883">V následujících příkladech zvažte následující soubor *MySubsection. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="9ed47-884">Následující kód přidá *MySubsection. JSON* ke zprostředkovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="9ed47-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="9ed47-885">GetSection</span></span>

<span data-ttu-id="9ed47-886">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí dílčí část konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9ed47-887">Následující kód vrátí hodnoty pro `section1` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-888">Následující kód vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-889">`GetSection`nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9ed47-890">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="9ed47-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9ed47-891">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9ed47-892"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="9ed47-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="9ed47-893">GetChildren a existuje</span><span class="sxs-lookup"><span data-stu-id="9ed47-893">GetChildren and Exists</span></span>

<span data-ttu-id="9ed47-894">Následující kód volá [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a vrátí hodnoty pro `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-895">Předchozí kód volá [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pro ověření, zda existuje oddíl:</span><span class="sxs-lookup"><span data-stu-id="9ed47-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="9ed47-896">Vazba pole</span><span class="sxs-lookup"><span data-stu-id="9ed47-896">Bind an array</span></span>

<span data-ttu-id="9ed47-897">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9ed47-898">Libovolný formát pole, který zveřejňuje numerický klíčový segment, je schopný vytvořit vazbu pole k poli třídy [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="9ed47-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="9ed47-899">V [ukázkovém stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)zvažte *myArray. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="9ed47-900">Následující kód přidá *myArray. JSON* ke zprostředkovatelům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="9ed47-901">Následující kód přečte konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="9ed47-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-902">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="9ed47-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="9ed47-903">V předchozím výstupu má index 3 hodnotu odpovídající hodnotě `value40` `"4": "value40",` v *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="9ed47-904">Vazby vázaného pole jsou souvislé a nejsou vázané na index konfiguračního klíče.</span><span class="sxs-lookup"><span data-stu-id="9ed47-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="9ed47-905">Pořadač konfigurace nemůže svázat hodnoty null ani vytvářet položky null ve vázaných objektech.</span><span class="sxs-lookup"><span data-stu-id="9ed47-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="9ed47-906">Následující kód načte `array:entries` konfiguraci pomocí <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="9ed47-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="9ed47-907">Následující kód přečte konfiguraci v `arrayDict` `Dictionary` a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="9ed47-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-908">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="9ed47-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="9ed47-909">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9ed47-910">Když jsou data konfigurace obsahující pole svázané, používají se k iteraci konfiguračních dat při vytváření objektu pole indexy v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9ed47-911">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9ed47-912">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na instanci doplní `ArrayExample` jakýmkoli poskytovatelem konfigurace, který načte &num; dvojici klíč/hodnota indexu 3.</span><span class="sxs-lookup"><span data-stu-id="9ed47-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="9ed47-913">V ukázkovém stažení zvažte následující soubor *hodnota3. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9ed47-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="9ed47-914">Následující kód obsahuje konfiguraci pro *hodnota3. JSON* a `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="9ed47-915">Následující kód přečte předchozí konfiguraci a zobrazí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="9ed47-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-916">Předchozí kód vrátí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="9ed47-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="9ed47-917">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="9ed47-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="9ed47-918">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ed47-918">Custom configuration provider</span></span>

<span data-ttu-id="9ed47-919">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="9ed47-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9ed47-920">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="9ed47-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9ed47-921">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="9ed47-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9ed47-922">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9ed47-923">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="9ed47-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9ed47-924">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9ed47-925">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9ed47-926">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="9ed47-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9ed47-927">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9ed47-928">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="9ed47-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9ed47-929">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9ed47-930">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9ed47-931">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9ed47-932">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9ed47-933">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="9ed47-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="9ed47-934">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="9ed47-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="9ed47-935">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9ed47-936">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9ed47-937">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9ed47-938">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="9ed47-939">Konfigurace přístupu při spuštění</span><span class="sxs-lookup"><span data-stu-id="9ed47-939">Access configuration in Startup</span></span>

<span data-ttu-id="9ed47-940">Následující kód zobrazuje konfigurační data v `Startup` metodách:</span><span class="sxs-lookup"><span data-stu-id="9ed47-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="9ed47-941">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="9ed47-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="9ed47-942">Přístup ke konfiguraci na Razor stránkách</span><span class="sxs-lookup"><span data-stu-id="9ed47-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="9ed47-943">Následující kód zobrazuje konfigurační data na Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="9ed47-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="9ed47-944">V následujícím kódu `MyOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="9ed47-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="9ed47-945">Následující kód používá [`@inject`](xref:mvc/views/razor#inject) Razor direktivu k vyřešení a zobrazení hodnot možností:</span><span class="sxs-lookup"><span data-stu-id="9ed47-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="9ed47-946">Přístup ke konfiguraci v souboru zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="9ed47-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="9ed47-947">Následující kód zobrazuje konfigurační data v zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="9ed47-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="9ed47-948">Konfigurace možností s delegátem</span><span class="sxs-lookup"><span data-stu-id="9ed47-948">Configure options with a delegate</span></span>

<span data-ttu-id="9ed47-949">Možnosti nakonfigurované v delegátech přepíší hodnoty nastavené ve zprostředkovatelích konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="9ed47-950">Konfigurace možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ed47-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="9ed47-951">V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je služba přidána do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="9ed47-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9ed47-952">Pomocí delegáta konfiguruje hodnoty pro `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="9ed47-953">Následující kód zobrazí hodnoty možností:</span><span class="sxs-lookup"><span data-stu-id="9ed47-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="9ed47-954">V předchozím příkladu `Option1` jsou hodnoty a `Option2` zadány v souboru *appSettings. JSON* a následně přepsány nakonfigurovaným delegátem.</span><span class="sxs-lookup"><span data-stu-id="9ed47-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9ed47-955">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="9ed47-955">Host versus app configuration</span></span>

<span data-ttu-id="9ed47-956">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="9ed47-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9ed47-957">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9ed47-958">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9ed47-959">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9ed47-960">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="9ed47-961">Výchozí konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="9ed47-961">Default host configuration</span></span>

<span data-ttu-id="9ed47-962">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="9ed47-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="9ed47-963">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="9ed47-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9ed47-964">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9ed47-965">Předpona ( `DOTNET_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9ed47-966">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9ed47-967">Je navázána výchozí konfigurace webového hostitele ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="9ed47-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="9ed47-968">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="9ed47-969">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="9ed47-970">Přidejte middleware předávaných hlaviček, pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="9ed47-971">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9ed47-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9ed47-972">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ed47-972">Other configuration</span></span>

<span data-ttu-id="9ed47-973">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9ed47-974">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="9ed47-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9ed47-975">*Launch. JSON* / *launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané:</span><span class="sxs-lookup"><span data-stu-id="9ed47-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9ed47-976">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9ed47-977">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="9ed47-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9ed47-978">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="9ed47-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9ed47-979">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9ed47-980">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="9ed47-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="9ed47-981"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9ed47-982">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9ed47-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ed47-983">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9ed47-983">Additional resources</span></span>

* [<span data-ttu-id="9ed47-984">Zdrojový kód konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ed47-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9ed47-985">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="9ed47-986">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="9ed47-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9ed47-987">Azure Key Vault</span></span>
* <span data-ttu-id="9ed47-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9ed47-988">Azure App Configuration</span></span>
* <span data-ttu-id="9ed47-989">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="9ed47-989">Command-line arguments</span></span>
* <span data-ttu-id="9ed47-990">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="9ed47-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="9ed47-991">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="9ed47-991">Directory files</span></span>
* <span data-ttu-id="9ed47-992">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-992">Environment variables</span></span>
* <span data-ttu-id="9ed47-993">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="9ed47-993">In-memory .NET objects</span></span>
* <span data-ttu-id="9ed47-994">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="9ed47-994">Settings files</span></span>

<span data-ttu-id="9ed47-995">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9ed47-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9ed47-996">Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="9ed47-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9ed47-997">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="9ed47-998">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="9ed47-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="9ed47-999">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="9ed47-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9ed47-1000">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ed47-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9ed47-1001">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="9ed47-1001">Host versus app configuration</span></span>

<span data-ttu-id="9ed47-1002">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9ed47-1003">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9ed47-1004">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9ed47-1005">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9ed47-1006">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9ed47-1007">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ed47-1007">Other configuration</span></span>

<span data-ttu-id="9ed47-1008">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9ed47-1009">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9ed47-1010">*Launch. JSON* / *launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9ed47-1011">V <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9ed47-1012">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9ed47-1013">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9ed47-1014">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="9ed47-1015">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ed47-1015">Default configuration</span></span>

<span data-ttu-id="9ed47-1016">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="9ed47-1017">`CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="9ed47-1018">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9ed47-1019">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="9ed47-1020">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9ed47-1021">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9ed47-1022">Předpona ( `ASPNETCORE_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9ed47-1023">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9ed47-1024">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="9ed47-1025">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9ed47-1026">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9ed47-1027">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v `Development` prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="9ed47-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="9ed47-1028">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="9ed47-1029">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="9ed47-1030">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="9ed47-1030">Security</span></span>

<span data-ttu-id="9ed47-1031">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="9ed47-1032">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="9ed47-1033">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9ed47-1034">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9ed47-1035">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9ed47-1036"><xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1036"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9ed47-1037">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="9ed47-1038">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="9ed47-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9ed47-1040">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9ed47-1041">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="9ed47-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="9ed47-1042">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9ed47-1043">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="9ed47-1044">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="9ed47-1045">Oddíly a klíče jsou shrnuty s použitím dvojtečky ( `:` ) pro zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="9ed47-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-1046">section0:key0</span></span>
* <span data-ttu-id="9ed47-1047">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-1047">section0:key1</span></span>
* <span data-ttu-id="9ed47-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-1048">section1:key0</span></span>
* <span data-ttu-id="9ed47-1049">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-1049">section1:key1</span></span>

<span data-ttu-id="9ed47-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9ed47-1051">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="9ed47-1052">Konvence</span><span class="sxs-lookup"><span data-stu-id="9ed47-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="9ed47-1053">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="9ed47-1053">Sources and providers</span></span>

<span data-ttu-id="9ed47-1054">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="9ed47-1055">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="9ed47-1056">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="9ed47-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9ed47-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>lze vložit do Razor stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pro získání konfigurace pro třídu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="9ed47-1059">V následujících příkladech se `_config` pole používá pro přístup k hodnotám konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="9ed47-1060">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="9ed47-1061">Klíče</span><span class="sxs-lookup"><span data-stu-id="9ed47-1061">Keys</span></span>

<span data-ttu-id="9ed47-1062">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="9ed47-1063">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="9ed47-1064">Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9ed47-1065">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="9ed47-1066">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="9ed47-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="9ed47-1067">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9ed47-1068">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9ed47-1069">Dvojité podtržítko ( `__` ) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="9ed47-1070">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9ed47-1071">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9ed47-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9ed47-1073">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="9ed47-1074">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="9ed47-1074">Values</span></span>

<span data-ttu-id="9ed47-1075">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="9ed47-1076">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1076">Values are strings.</span></span>
* <span data-ttu-id="9ed47-1077">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="9ed47-1078">Poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="9ed47-1078">Providers</span></span>

<span data-ttu-id="9ed47-1079">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9ed47-1080">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="9ed47-1080">Provider</span></span> | <span data-ttu-id="9ed47-1081">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="9ed47-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="9ed47-1082">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1083">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1084">'Identity'</span></span>
- <span data-ttu-id="9ed47-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1086">'Razor'</span></span>
- <span data-ttu-id="9ed47-1087">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1088">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1089">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1090">'Identity'</span></span>
- <span data-ttu-id="9ed47-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1092">'Razor'</span></span>
- <span data-ttu-id="9ed47-1093">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1094">---- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1095">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1096">'Identity'</span></span>
- <span data-ttu-id="9ed47-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1098">'Razor'</span></span>
- <span data-ttu-id="9ed47-1099">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1100">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1101">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1102">'Identity'</span></span>
- <span data-ttu-id="9ed47-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1104">'Razor'</span></span>
- <span data-ttu-id="9ed47-1105">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1106">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1107">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1108">'Identity'</span></span>
- <span data-ttu-id="9ed47-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1110">'Razor'</span></span>
- <span data-ttu-id="9ed47-1111">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1112">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1113">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1114">'Identity'</span></span>
- <span data-ttu-id="9ed47-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1116">'Razor'</span></span>
- <span data-ttu-id="9ed47-1117">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1118">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1119">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1120">'Identity'</span></span>
- <span data-ttu-id="9ed47-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1122">'Razor'</span></span>
- <span data-ttu-id="9ed47-1123">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1124">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1125">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1126">'Identity'</span></span>
- <span data-ttu-id="9ed47-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1128">'Razor'</span></span>
- <span data-ttu-id="9ed47-1129">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1130">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1131">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1132">'Identity'</span></span>
- <span data-ttu-id="9ed47-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1134">'Razor'</span></span>
- <span data-ttu-id="9ed47-1135">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1136">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1137">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1138">'Identity'</span></span>
- <span data-ttu-id="9ed47-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1140">'Razor'</span></span>
- <span data-ttu-id="9ed47-1141">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1142">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1143">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1144">'Identity'</span></span>
- <span data-ttu-id="9ed47-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1146">'Razor'</span></span>
- <span data-ttu-id="9ed47-1147">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1148">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1149">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1150">'Identity'</span></span>
- <span data-ttu-id="9ed47-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1152">'Razor'</span></span>
- <span data-ttu-id="9ed47-1153">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1154">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1155">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1156">'Identity'</span></span>
- <span data-ttu-id="9ed47-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1158">'Razor'</span></span>
- <span data-ttu-id="9ed47-1159">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1160">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1161">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1162">'Identity'</span></span>
- <span data-ttu-id="9ed47-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1164">'Razor'</span></span>
- <span data-ttu-id="9ed47-1165">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1166">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1167">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1168">'Identity'</span></span>
- <span data-ttu-id="9ed47-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1170">'Razor'</span></span>
- <span data-ttu-id="9ed47-1171">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1172">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1173">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1174">'Identity'</span></span>
- <span data-ttu-id="9ed47-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1176">'Razor'</span></span>
- <span data-ttu-id="9ed47-1177">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1178">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1179">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1180">'Identity'</span></span>
- <span data-ttu-id="9ed47-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1182">'Razor'</span></span>
- <span data-ttu-id="9ed47-1183">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1184">------------------ | | [Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* ) | Azure Key Vault | | [Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure) | Konfigurace aplikace Azure | | [Poskytovatel konfigurace příkazového řádku](#command-line-configuration-provider) | Parametry příkazového řádku | | [Vlastní poskytovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj | | [Poskytovatel konfigurace proměnných prostředí](#environment-variables-configuration-provider) | Proměnné prostředí | | [Poskytovatel konfigurace souboru](#file-configuration-provider) | Soubory (INI, JSON, XML) | | [Poskytovatel konfigurace klíče na soubor](#key-per-file-configuration-provider) | Soubory adresáře | | [Poskytovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti | | [Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata) | Soubor v adresáři profilu uživatele |</span><span class="sxs-lookup"><span data-stu-id="9ed47-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="9ed47-1185">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="9ed47-1186">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="9ed47-1187">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9ed47-1188">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9ed47-1189">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace</span><span class="sxs-lookup"><span data-stu-id="9ed47-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="9ed47-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9ed47-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="9ed47-1191">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="9ed47-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="9ed47-1192">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-1192">Environment variables</span></span>
1. <span data-ttu-id="9ed47-1193">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="9ed47-1193">Command-line arguments</span></span>

<span data-ttu-id="9ed47-1194">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9ed47-1195">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9ed47-1196">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="9ed47-1197">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="9ed47-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="9ed47-1198">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> na tvůrce hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="9ed47-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9ed47-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9ed47-1200">Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s automaticky přidanými uživateli `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="9ed47-1201">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="9ed47-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="9ed47-1202">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte jako `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="9ed47-1203">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="9ed47-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="9ed47-1204">Chcete-li odebrat poskytovatele přidaných nástrojem `CreateDefaultBuilder` , zavolejte nejprve [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="9ed47-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="9ed47-1205">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="9ed47-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="9ed47-1206">Konfigurace dodaná do aplikace v nástroji `ConfigureAppConfiguration` je k dispozici během spouštění aplikace, včetně `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9ed47-1207">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="9ed47-1208">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="9ed47-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="9ed47-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="9ed47-1210">Chcete-li aktivovat konfiguraci příkazového řádku, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> je metoda rozšíření volána na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9ed47-1211">`AddCommandLine`je automaticky volána `CreateDefaultBuilder(string [])` , když je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="9ed47-1212">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9ed47-1213">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9ed47-1214">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9ed47-1215">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9ed47-1216">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1216">Environment variables.</span></span>

<span data-ttu-id="9ed47-1217">`CreateDefaultBuilder`přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="9ed47-1218">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="9ed47-1219">`CreateDefaultBuilder`funguje, když je hostitel vytvořen.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="9ed47-1220">Proto může konfigurace z příkazového řádku aktivované nástrojem `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="9ed47-1221">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány pomocí `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9ed47-1222">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte na `AddCommandLine` Poslední.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="9ed47-1223">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="9ed47-1223">**Example**</span></span>

<span data-ttu-id="9ed47-1224">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="9ed47-1225">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="9ed47-1226">Zadejte do příkazu argument příkazového řádku `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="9ed47-1227">Po spuštění aplikace otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9ed47-1228">Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k dispozici pro příkaz `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="9ed47-1229">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9ed47-1229">Arguments</span></span>

<span data-ttu-id="9ed47-1230">Hodnota musí následovat po znaménku rovná se ( `=` ), nebo klíč musí obsahovat předponu ( `--` nebo `/` ), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="9ed47-1231">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="9ed47-1232">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="9ed47-1232">Key prefix</span></span>               | <span data-ttu-id="9ed47-1233">Příklad</span><span class="sxs-lookup"><span data-stu-id="9ed47-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="9ed47-1234">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1235">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1236">'Identity'</span></span>
- <span data-ttu-id="9ed47-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1238">'Razor'</span></span>
- <span data-ttu-id="9ed47-1239">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1240">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1241">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1242">'Identity'</span></span>
- <span data-ttu-id="9ed47-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1244">'Razor'</span></span>
- <span data-ttu-id="9ed47-1245">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1246">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1247">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1248">'Identity'</span></span>
- <span data-ttu-id="9ed47-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1250">'Razor'</span></span>
- <span data-ttu-id="9ed47-1251">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1252">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1253">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1254">'Identity'</span></span>
- <span data-ttu-id="9ed47-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1256">'Razor'</span></span>
- <span data-ttu-id="9ed47-1257">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1258">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1259">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1260">'Identity'</span></span>
- <span data-ttu-id="9ed47-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1262">'Razor'</span></span>
- <span data-ttu-id="9ed47-1263">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1264">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1265">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1266">'Identity'</span></span>
- <span data-ttu-id="9ed47-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1268">'Razor'</span></span>
- <span data-ttu-id="9ed47-1269">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1270">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1271">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1272">'Identity'</span></span>
- <span data-ttu-id="9ed47-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1274">'Razor'</span></span>
- <span data-ttu-id="9ed47-1275">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1276">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1277">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1278">'Identity'</span></span>
- <span data-ttu-id="9ed47-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1280">'Razor'</span></span>
- <span data-ttu-id="9ed47-1281">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1282">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1283">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1284">'Identity'</span></span>
- <span data-ttu-id="9ed47-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1286">'Razor'</span></span>
- <span data-ttu-id="9ed47-1287">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1288">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1289">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1290">'Identity'</span></span>
- <span data-ttu-id="9ed47-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1292">'Razor'</span></span>
- <span data-ttu-id="9ed47-1293">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1294">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1295">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1296">'Identity'</span></span>
- <span data-ttu-id="9ed47-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1298">'Razor'</span></span>
- <span data-ttu-id="9ed47-1299">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1300">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1301">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1302">'Identity'</span></span>
- <span data-ttu-id="9ed47-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1304">'Razor'</span></span>
- <span data-ttu-id="9ed47-1305">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1306">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1307">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1308">'Identity'</span></span>
- <span data-ttu-id="9ed47-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1310">'Razor'</span></span>
- <span data-ttu-id="9ed47-1311">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1312">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1313">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1314">'Identity'</span></span>
- <span data-ttu-id="9ed47-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1316">'Razor'</span></span>
- <span data-ttu-id="9ed47-1317">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1318">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1319">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1320">'Identity'</span></span>
- <span data-ttu-id="9ed47-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1322">'Razor'</span></span>
- <span data-ttu-id="9ed47-1323">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1324">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1325">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1326">'Identity'</span></span>
- <span data-ttu-id="9ed47-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1328">'Razor'</span></span>
- <span data-ttu-id="9ed47-1329">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1330">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1331">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1332">'Identity'</span></span>
- <span data-ttu-id="9ed47-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1334">'Razor'</span></span>
- <span data-ttu-id="9ed47-1335">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1336">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1337">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1338">'Identity'</span></span>
- <span data-ttu-id="9ed47-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1340">'Razor'</span></span>
- <span data-ttu-id="9ed47-1341">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1342">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1343">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1344">'Identity'</span></span>
- <span data-ttu-id="9ed47-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1346">'Razor'</span></span>
- <span data-ttu-id="9ed47-1347">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1348">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1349">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1350">'Identity'</span></span>
- <span data-ttu-id="9ed47-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1352">'Razor'</span></span>
- <span data-ttu-id="9ed47-1353">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1354">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1355">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1356">'Identity'</span></span>
- <span data-ttu-id="9ed47-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1358">'Razor'</span></span>
- <span data-ttu-id="9ed47-1359">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1360">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1361">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1362">'Identity'</span></span>
- <span data-ttu-id="9ed47-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1364">'Razor'</span></span>
- <span data-ttu-id="9ed47-1365">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1366">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1367">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1368">'Identity'</span></span>
- <span data-ttu-id="9ed47-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1370">'Razor'</span></span>
- <span data-ttu-id="9ed47-1371">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1372">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1373">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1374">'Identity'</span></span>
- <span data-ttu-id="9ed47-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1376">'Razor'</span></span>
- <span data-ttu-id="9ed47-1377">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1378">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1379">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1380">'Identity'</span></span>
- <span data-ttu-id="9ed47-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1382">'Razor'</span></span>
- <span data-ttu-id="9ed47-1383">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1384">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1385">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1386">'Identity'</span></span>
- <span data-ttu-id="9ed47-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1388">'Razor'</span></span>
- <span data-ttu-id="9ed47-1389">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1390">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1391">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1392">'Identity'</span></span>
- <span data-ttu-id="9ed47-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1394">'Razor'</span></span>
- <span data-ttu-id="9ed47-1395">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1396">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1397">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1398">'Identity'</span></span>
- <span data-ttu-id="9ed47-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1400">'Razor'</span></span>
- <span data-ttu-id="9ed47-1401">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1402">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1403">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1404">'Identity'</span></span>
- <span data-ttu-id="9ed47-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1406">'Razor'</span></span>
- <span data-ttu-id="9ed47-1407">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1408">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1409">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1410">'Identity'</span></span>
- <span data-ttu-id="9ed47-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1412">'Razor'</span></span>
- <span data-ttu-id="9ed47-1413">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1414">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1415">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1416">'Identity'</span></span>
- <span data-ttu-id="9ed47-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1418">'Razor'</span></span>
- <span data-ttu-id="9ed47-1419">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1420">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1421">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1422">'Identity'</span></span>
- <span data-ttu-id="9ed47-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1424">'Razor'</span></span>
- <span data-ttu-id="9ed47-1425">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1426">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1427">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1428">'Identity'</span></span>
- <span data-ttu-id="9ed47-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1430">'Razor'</span></span>
- <span data-ttu-id="9ed47-1431">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1432">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1433">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1434">'Identity'</span></span>
- <span data-ttu-id="9ed47-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1436">'Razor'</span></span>
- <span data-ttu-id="9ed47-1437">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1438">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1439">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1440">'Identity'</span></span>
- <span data-ttu-id="9ed47-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1442">'Razor'</span></span>
- <span data-ttu-id="9ed47-1443">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1444">--------------------------- | | Bez předpony | `CommandLineKey1=value1`                               |
| Dvě pomlčky ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Lomítko ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="9ed47-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="9ed47-1445">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="9ed47-1446">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="9ed47-1447">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="9ed47-1447">Switch mappings</span></span>

<span data-ttu-id="9ed47-1448">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="9ed47-1449">Při ručním sestavování konfigurace pomocí nástroje <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> Zadejte slovník přepínačů pro vložení do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9ed47-1450">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9ed47-1451">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9ed47-1452">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9ed47-1453">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9ed47-1454">Přepínače musí začínat spojovníkem ( `-` ) nebo dvojitou čárkou ( `--` ).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="9ed47-1455">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9ed47-1456">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="9ed47-1457">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="9ed47-1458">Po sestavení hostitele zavolejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="9ed47-1459">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9ed47-1460">`CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9ed47-1461">Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="9ed47-1462">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="9ed47-1463">Klíč</span><span class="sxs-lookup"><span data-stu-id="9ed47-1463">Key</span></span>       | <span data-ttu-id="9ed47-1464">Hodnota</span><span class="sxs-lookup"><span data-stu-id="9ed47-1464">Value</span></span>             |
| ---
<span data-ttu-id="9ed47-1465">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1466">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1467">'Identity'</span></span>
- <span data-ttu-id="9ed47-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1469">'Razor'</span></span>
- <span data-ttu-id="9ed47-1470">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1471">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1472">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1473">'Identity'</span></span>
- <span data-ttu-id="9ed47-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1475">'Razor'</span></span>
- <span data-ttu-id="9ed47-1476">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1477">----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1478">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1479">'Identity'</span></span>
- <span data-ttu-id="9ed47-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1481">'Razor'</span></span>
- <span data-ttu-id="9ed47-1482">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1483">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1484">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1485">'Identity'</span></span>
- <span data-ttu-id="9ed47-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1487">'Razor'</span></span>
- <span data-ttu-id="9ed47-1488">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1489">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1490">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1491">'Identity'</span></span>
- <span data-ttu-id="9ed47-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1493">'Razor'</span></span>
- <span data-ttu-id="9ed47-1494">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1495">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1496">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1497">'Identity'</span></span>
- <span data-ttu-id="9ed47-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1499">'Razor'</span></span>
- <span data-ttu-id="9ed47-1500">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1501">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1502">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1503">'Identity'</span></span>
- <span data-ttu-id="9ed47-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1505">'Razor'</span></span>
- <span data-ttu-id="9ed47-1506">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1507">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1508">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1509">'Identity'</span></span>
- <span data-ttu-id="9ed47-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1511">'Razor'</span></span>
- <span data-ttu-id="9ed47-1512">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="9ed47-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="9ed47-1514">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="9ed47-1515">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="9ed47-1516">Klíč</span><span class="sxs-lookup"><span data-stu-id="9ed47-1516">Key</span></span>               | <span data-ttu-id="9ed47-1517">Hodnota</span><span class="sxs-lookup"><span data-stu-id="9ed47-1517">Value</span></span>    |
| ---
<span data-ttu-id="9ed47-1518">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1519">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1520">'Identity'</span></span>
- <span data-ttu-id="9ed47-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1522">'Razor'</span></span>
- <span data-ttu-id="9ed47-1523">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1524">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1525">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1526">'Identity'</span></span>
- <span data-ttu-id="9ed47-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1528">'Razor'</span></span>
- <span data-ttu-id="9ed47-1529">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1530">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1531">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1532">'Identity'</span></span>
- <span data-ttu-id="9ed47-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1534">'Razor'</span></span>
- <span data-ttu-id="9ed47-1535">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1536">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1537">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1538">'Identity'</span></span>
- <span data-ttu-id="9ed47-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1540">'Razor'</span></span>
- <span data-ttu-id="9ed47-1541">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1542">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1543">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1544">'Identity'</span></span>
- <span data-ttu-id="9ed47-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1546">'Razor'</span></span>
- <span data-ttu-id="9ed47-1547">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1548">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1549">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1550">'Identity'</span></span>
- <span data-ttu-id="9ed47-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1552">'Razor'</span></span>
- <span data-ttu-id="9ed47-1553">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1554">--------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1555">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1556">'Identity'</span></span>
- <span data-ttu-id="9ed47-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1558">'Razor'</span></span>
- <span data-ttu-id="9ed47-1559">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1560">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1561">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1562">'Identity'</span></span>
- <span data-ttu-id="9ed47-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1564">'Razor'</span></span>
- <span data-ttu-id="9ed47-1565">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="9ed47-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="9ed47-1567">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="9ed47-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="9ed47-1569">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9ed47-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="9ed47-1571">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9ed47-1572">`AddEnvironmentVariables`slouží k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="9ed47-1573">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9ed47-1574">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9ed47-1575">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="9ed47-1576">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9ed47-1577">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9ed47-1578">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1578">Command-line arguments.</span></span>

<span data-ttu-id="9ed47-1579">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="9ed47-1580">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="9ed47-1581">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="9ed47-1582">Zavolejte `AddEnvironmentVariables` jako poslední, pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="9ed47-1583">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="9ed47-1583">**Example**</span></span>

<span data-ttu-id="9ed47-1584">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="9ed47-1585">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1585">Run the sample app.</span></span> <span data-ttu-id="9ed47-1586">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9ed47-1587">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="9ed47-1588">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="9ed47-1589">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="9ed47-1590">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="9ed47-1591">K vystavení všech proměnných prostředí, které jsou k dispozici pro aplikaci, změňte na `FilteredConfiguration` *stránkách/index. cshtml. cs* následující:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="9ed47-1592">Předpony</span><span class="sxs-lookup"><span data-stu-id="9ed47-1592">Prefixes</span></span>

<span data-ttu-id="9ed47-1593">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="9ed47-1594">Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_` , zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="9ed47-1595">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="9ed47-1596">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="9ed47-1597">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9ed47-1598">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="9ed47-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="9ed47-1599">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9ed47-1600">Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána žádná předpona `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9ed47-1601">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="9ed47-1601">Connection string prefix</span></span> | <span data-ttu-id="9ed47-1602">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="9ed47-1602">Provider</span></span> |
| ---
<span data-ttu-id="9ed47-1603">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1604">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1605">'Identity'</span></span>
- <span data-ttu-id="9ed47-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1607">'Razor'</span></span>
- <span data-ttu-id="9ed47-1608">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1609">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1610">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1611">'Identity'</span></span>
- <span data-ttu-id="9ed47-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1613">'Razor'</span></span>
- <span data-ttu-id="9ed47-1614">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1615">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1616">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1617">'Identity'</span></span>
- <span data-ttu-id="9ed47-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1619">'Razor'</span></span>
- <span data-ttu-id="9ed47-1620">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1621">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1622">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1623">'Identity'</span></span>
- <span data-ttu-id="9ed47-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1625">'Razor'</span></span>
- <span data-ttu-id="9ed47-1626">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1627">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1628">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1629">'Identity'</span></span>
- <span data-ttu-id="9ed47-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1631">'Razor'</span></span>
- <span data-ttu-id="9ed47-1632">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1633">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1634">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1635">'Identity'</span></span>
- <span data-ttu-id="9ed47-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1637">'Razor'</span></span>
- <span data-ttu-id="9ed47-1638">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1639">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1640">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1641">'Identity'</span></span>
- <span data-ttu-id="9ed47-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1643">'Razor'</span></span>
- <span data-ttu-id="9ed47-1644">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1645">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1646">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1647">'Identity'</span></span>
- <span data-ttu-id="9ed47-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1649">'Razor'</span></span>
- <span data-ttu-id="9ed47-1650">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1651">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1652">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1653">'Identity'</span></span>
- <span data-ttu-id="9ed47-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1655">'Razor'</span></span>
- <span data-ttu-id="9ed47-1656">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1657">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1658">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1659">'Identity'</span></span>
- <span data-ttu-id="9ed47-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1661">'Razor'</span></span>
- <span data-ttu-id="9ed47-1662">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1663">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1664">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1665">'Identity'</span></span>
- <span data-ttu-id="9ed47-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1667">'Razor'</span></span>
- <span data-ttu-id="9ed47-1668">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1669">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1670">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1671">'Identity'</span></span>
- <span data-ttu-id="9ed47-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1673">'Razor'</span></span>
- <span data-ttu-id="9ed47-1674">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1675">---- | | `CUSTOMCONNSTR_` | Vlastní poskytovatel | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="9ed47-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="9ed47-1676">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9ed47-1677">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9ed47-1678">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="9ed47-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9ed47-1679">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="9ed47-1679">Environment variable key</span></span> | <span data-ttu-id="9ed47-1680">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="9ed47-1680">Converted configuration key</span></span> | <span data-ttu-id="9ed47-1681">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="9ed47-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="9ed47-1682">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1683">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1684">'Identity'</span></span>
- <span data-ttu-id="9ed47-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1686">'Razor'</span></span>
- <span data-ttu-id="9ed47-1687">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1688">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1689">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1690">'Identity'</span></span>
- <span data-ttu-id="9ed47-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1692">'Razor'</span></span>
- <span data-ttu-id="9ed47-1693">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1694">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1695">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1696">'Identity'</span></span>
- <span data-ttu-id="9ed47-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1698">'Razor'</span></span>
- <span data-ttu-id="9ed47-1699">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1700">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1701">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1702">'Identity'</span></span>
- <span data-ttu-id="9ed47-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1704">'Razor'</span></span>
- <span data-ttu-id="9ed47-1705">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1706">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1707">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1708">'Identity'</span></span>
- <span data-ttu-id="9ed47-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1710">'Razor'</span></span>
- <span data-ttu-id="9ed47-1711">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1712">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1713">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1714">'Identity'</span></span>
- <span data-ttu-id="9ed47-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1716">'Razor'</span></span>
- <span data-ttu-id="9ed47-1717">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1718">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1719">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1720">'Identity'</span></span>
- <span data-ttu-id="9ed47-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1722">'Razor'</span></span>
- <span data-ttu-id="9ed47-1723">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1724">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1725">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1726">'Identity'</span></span>
- <span data-ttu-id="9ed47-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1728">'Razor'</span></span>
- <span data-ttu-id="9ed47-1729">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1730">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1731">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1732">'Identity'</span></span>
- <span data-ttu-id="9ed47-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1734">'Razor'</span></span>
- <span data-ttu-id="9ed47-1735">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1736">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1737">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1738">'Identity'</span></span>
- <span data-ttu-id="9ed47-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1740">'Razor'</span></span>
- <span data-ttu-id="9ed47-1741">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1742">------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1743">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1744">'Identity'</span></span>
- <span data-ttu-id="9ed47-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1746">'Razor'</span></span>
- <span data-ttu-id="9ed47-1747">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1748">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1749">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1750">'Identity'</span></span>
- <span data-ttu-id="9ed47-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1752">'Razor'</span></span>
- <span data-ttu-id="9ed47-1753">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1754">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1755">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1756">'Identity'</span></span>
- <span data-ttu-id="9ed47-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1758">'Razor'</span></span>
- <span data-ttu-id="9ed47-1759">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1760">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1761">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1762">'Identity'</span></span>
- <span data-ttu-id="9ed47-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1764">'Razor'</span></span>
- <span data-ttu-id="9ed47-1765">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1766">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1767">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1768">'Identity'</span></span>
- <span data-ttu-id="9ed47-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1770">'Razor'</span></span>
- <span data-ttu-id="9ed47-1771">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1772">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1773">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1774">'Identity'</span></span>
- <span data-ttu-id="9ed47-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1776">'Razor'</span></span>
- <span data-ttu-id="9ed47-1777">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1778">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1779">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1780">'Identity'</span></span>
- <span data-ttu-id="9ed47-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1782">'Razor'</span></span>
- <span data-ttu-id="9ed47-1783">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1784">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1785">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1786">'Identity'</span></span>
- <span data-ttu-id="9ed47-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1788">'Razor'</span></span>
- <span data-ttu-id="9ed47-1789">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1790">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1791">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1792">'Identity'</span></span>
- <span data-ttu-id="9ed47-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1794">'Razor'</span></span>
- <span data-ttu-id="9ed47-1795">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1796">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1797">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1798">'Identity'</span></span>
- <span data-ttu-id="9ed47-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1800">'Razor'</span></span>
- <span data-ttu-id="9ed47-1801">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1802">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1803">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1804">'Identity'</span></span>
- <span data-ttu-id="9ed47-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1806">'Razor'</span></span>
- <span data-ttu-id="9ed47-1807">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-1808">-------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1809">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1810">'Identity'</span></span>
- <span data-ttu-id="9ed47-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1812">'Razor'</span></span>
- <span data-ttu-id="9ed47-1813">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1814">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1815">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1816">'Identity'</span></span>
- <span data-ttu-id="9ed47-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1818">'Razor'</span></span>
- <span data-ttu-id="9ed47-1819">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1820">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1821">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1822">'Identity'</span></span>
- <span data-ttu-id="9ed47-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1824">'Razor'</span></span>
- <span data-ttu-id="9ed47-1825">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1826">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1827">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1828">'Identity'</span></span>
- <span data-ttu-id="9ed47-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1830">'Razor'</span></span>
- <span data-ttu-id="9ed47-1831">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1832">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1833">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1834">'Identity'</span></span>
- <span data-ttu-id="9ed47-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1836">'Razor'</span></span>
- <span data-ttu-id="9ed47-1837">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1838">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1839">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1840">'Identity'</span></span>
- <span data-ttu-id="9ed47-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1842">'Razor'</span></span>
- <span data-ttu-id="9ed47-1843">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1844">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1845">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1846">'Identity'</span></span>
- <span data-ttu-id="9ed47-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1848">'Razor'</span></span>
- <span data-ttu-id="9ed47-1849">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1850">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1851">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1852">'Identity'</span></span>
- <span data-ttu-id="9ed47-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1854">'Razor'</span></span>
- <span data-ttu-id="9ed47-1855">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1856">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1857">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1858">'Identity'</span></span>
- <span data-ttu-id="9ed47-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1860">'Razor'</span></span>
- <span data-ttu-id="9ed47-1861">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1862">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1863">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1864">'Identity'</span></span>
- <span data-ttu-id="9ed47-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1866">'Razor'</span></span>
- <span data-ttu-id="9ed47-1867">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1868">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1869">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1870">'Identity'</span></span>
- <span data-ttu-id="9ed47-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1872">'Razor'</span></span>
- <span data-ttu-id="9ed47-1873">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1874">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1875">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1876">'Identity'</span></span>
- <span data-ttu-id="9ed47-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1878">'Razor'</span></span>
- <span data-ttu-id="9ed47-1879">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1880">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1881">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1882">'Identity'</span></span>
- <span data-ttu-id="9ed47-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1884">'Razor'</span></span>
- <span data-ttu-id="9ed47-1885">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1886">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1887">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1888">'Identity'</span></span>
- <span data-ttu-id="9ed47-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1890">'Razor'</span></span>
- <span data-ttu-id="9ed47-1891">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1892">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1893">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1894">'Identity'</span></span>
- <span data-ttu-id="9ed47-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1896">'Razor'</span></span>
- <span data-ttu-id="9ed47-1897">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1898">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1899">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1900">'Identity'</span></span>
- <span data-ttu-id="9ed47-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1902">'Razor'</span></span>
- <span data-ttu-id="9ed47-1903">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1904">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1905">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1906">'Identity'</span></span>
- <span data-ttu-id="9ed47-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1908">'Razor'</span></span>
- <span data-ttu-id="9ed47-1909">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1910">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1911">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1912">'Identity'</span></span>
- <span data-ttu-id="9ed47-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1914">'Razor'</span></span>
- <span data-ttu-id="9ed47-1915">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1916">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1917">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1918">'Identity'</span></span>
- <span data-ttu-id="9ed47-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1920">'Razor'</span></span>
- <span data-ttu-id="9ed47-1921">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1922">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1923">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1924">'Identity'</span></span>
- <span data-ttu-id="9ed47-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1926">'Razor'</span></span>
- <span data-ttu-id="9ed47-1927">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1928">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1929">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1930">'Identity'</span></span>
- <span data-ttu-id="9ed47-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1932">'Razor'</span></span>
- <span data-ttu-id="9ed47-1933">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1934">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1935">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1936">'Identity'</span></span>
- <span data-ttu-id="9ed47-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1938">'Razor'</span></span>
- <span data-ttu-id="9ed47-1939">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1940">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1941">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1942">'Identity'</span></span>
- <span data-ttu-id="9ed47-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1944">'Razor'</span></span>
- <span data-ttu-id="9ed47-1945">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1946">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1947">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1948">'Identity'</span></span>
- <span data-ttu-id="9ed47-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1950">'Razor'</span></span>
- <span data-ttu-id="9ed47-1951">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1952">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1953">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1954">'Identity'</span></span>
- <span data-ttu-id="9ed47-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1956">'Razor'</span></span>
- <span data-ttu-id="9ed47-1957">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1958">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1959">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1960">'Identity'</span></span>
- <span data-ttu-id="9ed47-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1962">'Razor'</span></span>
- <span data-ttu-id="9ed47-1963">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1964">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1965">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1966">'Identity'</span></span>
- <span data-ttu-id="9ed47-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1968">'Razor'</span></span>
- <span data-ttu-id="9ed47-1969">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1970">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1971">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1972">'Identity'</span></span>
- <span data-ttu-id="9ed47-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1974">'Razor'</span></span>
- <span data-ttu-id="9ed47-1975">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1976">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1977">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1978">'Identity'</span></span>
- <span data-ttu-id="9ed47-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1980">'Razor'</span></span>
- <span data-ttu-id="9ed47-1981">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1982">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1983">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1984">'Identity'</span></span>
- <span data-ttu-id="9ed47-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1986">'Razor'</span></span>
- <span data-ttu-id="9ed47-1987">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1988">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1989">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1990">'Identity'</span></span>
- <span data-ttu-id="9ed47-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1992">'Razor'</span></span>
- <span data-ttu-id="9ed47-1993">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-1994">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1995">'Blazor'</span></span>
- <span data-ttu-id="9ed47-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1996">'Identity'</span></span>
- <span data-ttu-id="9ed47-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-1998">'Razor'</span></span>
- <span data-ttu-id="9ed47-1999">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2000">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2001">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2002">'Identity'</span></span>
- <span data-ttu-id="9ed47-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2004">'Razor'</span></span>
- <span data-ttu-id="9ed47-2005">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2006">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2007">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2008">'Identity'</span></span>
- <span data-ttu-id="9ed47-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2010">'Razor'</span></span>
- <span data-ttu-id="9ed47-2011">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2012">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2013">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2014">'Identity'</span></span>
- <span data-ttu-id="9ed47-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2016">'Razor'</span></span>
- <span data-ttu-id="9ed47-2017">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2018">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2019">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2020">'Identity'</span></span>
- <span data-ttu-id="9ed47-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2022">'Razor'</span></span>
- <span data-ttu-id="9ed47-2023">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2024">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2025">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2026">'Identity'</span></span>
- <span data-ttu-id="9ed47-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2028">'Razor'</span></span>
- <span data-ttu-id="9ed47-2029">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="9ed47-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9ed47-2032">Hodnota: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9ed47-2033">Hodnota: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9ed47-2034">Osa`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="9ed47-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="9ed47-2035">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="9ed47-2035">**Example**</span></span>

<span data-ttu-id="9ed47-2036">Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="9ed47-2037">Jméno:`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="9ed47-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="9ed47-2038">Hodnota: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="9ed47-2038">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="9ed47-2039">Pokud `IConfiguration` je vloženo a přiřazeno k poli s názvem `_config` , přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="9ed47-2040">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="9ed47-2040">File Configuration Provider</span></span>

<span data-ttu-id="9ed47-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9ed47-2042">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="9ed47-2043">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="9ed47-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9ed47-2044">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="9ed47-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="9ed47-2045">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="9ed47-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9ed47-2046">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="9ed47-2046">INI Configuration Provider</span></span>

<span data-ttu-id="9ed47-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9ed47-2048">Chcete-li aktivovat konfiguraci souboru INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9ed47-2049">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="9ed47-2050">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="9ed47-2051">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="9ed47-2052">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9ed47-2053"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9ed47-2054">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9ed47-2055">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="9ed47-2056">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9ed47-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2057">section0:key0</span></span>
* <span data-ttu-id="9ed47-2058">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2058">section0:key1</span></span>
* <span data-ttu-id="9ed47-2059">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="9ed47-2059">section1:subsection:key</span></span>
* <span data-ttu-id="9ed47-2060">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="9ed47-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="9ed47-2061">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="9ed47-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="9ed47-2062">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="9ed47-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="9ed47-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="9ed47-2064">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9ed47-2065">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="9ed47-2066">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="9ed47-2067">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9ed47-2068"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9ed47-2069">`AddJsonFile`se automaticky volá dvakrát při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9ed47-2070">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="9ed47-2071">*appSettings. JSON*: Tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2071">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="9ed47-2072">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="9ed47-2073">*appSettings. {Environment}. JSON*: verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="9ed47-2073">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="9ed47-2074">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9ed47-2075">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9ed47-2076">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2076">Environment variables.</span></span>
* <span data-ttu-id="9ed47-2077">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9ed47-2078">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2078">Command-line arguments.</span></span>

<span data-ttu-id="9ed47-2079">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="9ed47-2080">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="9ed47-2081">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9ed47-2082">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="9ed47-2082">**Example**</span></span>

<span data-ttu-id="9ed47-2083">Ukázková aplikace využívá ke sestavení hostitele výhodu technologie statického usnadnění `CreateDefaultBuilder` , která zahrnuje dvě volání `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="9ed47-2084">První volání `AddJsonFile` načtení konfigurace z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="9ed47-2085">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="9ed47-2086">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="9ed47-2087">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2087">Run the sample app.</span></span> <span data-ttu-id="9ed47-2088">Otevřete v aplikaci prohlížeč `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9ed47-2089">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="9ed47-2090">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="9ed47-2091">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="9ed47-2092">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="9ed47-2093">V `ConfigurationSample` profilu změňte hodnotu `ASPNETCORE_ENVIRONMENT` proměnné prostředí na `Production` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="9ed47-2094">Uložte soubor a spusťte aplikaci `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="9ed47-2095">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="9ed47-2096">Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Warning` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="9ed47-2097">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="9ed47-2097">XML Configuration Provider</span></span>

<span data-ttu-id="9ed47-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9ed47-2099">Chcete-li aktivovat konfiguraci souboru XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9ed47-2100">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="9ed47-2101">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="9ed47-2102">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9ed47-2103"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9ed47-2104">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="9ed47-2105">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="9ed47-2106">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9ed47-2107">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="9ed47-2108">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9ed47-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2109">section0:key0</span></span>
* <span data-ttu-id="9ed47-2110">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2110">section0:key1</span></span>
* <span data-ttu-id="9ed47-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2111">section1:key0</span></span>
* <span data-ttu-id="9ed47-2112">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2112">section1:key1</span></span>

<span data-ttu-id="9ed47-2113">Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="9ed47-2114">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9ed47-2115">oddíl: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="9ed47-2116">oddíl: section0: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="9ed47-2117">oddíl: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="9ed47-2118">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2118">section:section1:key:key1</span></span>

<span data-ttu-id="9ed47-2119">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9ed47-2120">Předchozí konfigurační soubor načte následující klíče pomocí `value` :</span><span class="sxs-lookup"><span data-stu-id="9ed47-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9ed47-2121">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="9ed47-2121">key:attribute</span></span>
* <span data-ttu-id="9ed47-2122">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="9ed47-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9ed47-2123">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="9ed47-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="9ed47-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9ed47-2125">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2125">The key is the file name.</span></span> <span data-ttu-id="9ed47-2126">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2126">The value contains the file's contents.</span></span> <span data-ttu-id="9ed47-2127">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9ed47-2128">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9ed47-2129">`directoryPath`Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9ed47-2130">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="9ed47-2131">`Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9ed47-2132">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9ed47-2133">Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9ed47-2134">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9ed47-2135">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="9ed47-2136">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="9ed47-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="9ed47-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9ed47-2138">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9ed47-2139">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="9ed47-2140">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="9ed47-2141">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="9ed47-2142">Slovník se používá s voláním k `AddInMemoryCollection` poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="9ed47-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="9ed47-2143">GetValue</span></span>

<span data-ttu-id="9ed47-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="9ed47-2145">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="9ed47-2146">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2146">The following example:</span></span>

* <span data-ttu-id="9ed47-2147">Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="9ed47-2148">Pokud se `NumberKey` v konfiguračních klíčích nenajde, použije se výchozí hodnota `99` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="9ed47-2149">Zadá hodnotu jako `int` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="9ed47-2150">Ukládá hodnotu `NumberConfig` vlastnosti pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9ed47-2151">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="9ed47-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9ed47-2152">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="9ed47-2153">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="9ed47-2154">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="9ed47-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2155">section0:key0</span></span>
* <span data-ttu-id="9ed47-2156">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2156">section0:key1</span></span>
* <span data-ttu-id="9ed47-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2157">section1:key0</span></span>
* <span data-ttu-id="9ed47-2158">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2158">section1:key1</span></span>
* <span data-ttu-id="9ed47-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="9ed47-2160">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="9ed47-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="9ed47-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="9ed47-2162">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="9ed47-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="9ed47-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="9ed47-2163">GetSection</span></span>

<span data-ttu-id="9ed47-2164">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9ed47-2165">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1` , zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="9ed47-2166">`configSection`Hodnota neobsahuje pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="9ed47-2167">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0` , zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="9ed47-2168">`GetSection`nikdy nevrátí `null` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9ed47-2169">Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9ed47-2170">Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9ed47-2171"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="9ed47-2172">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="9ed47-2172">GetChildren</span></span>

<span data-ttu-id="9ed47-2173">Volání [IConfiguration. Getchildrens](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="9ed47-2174">Existuje</span><span class="sxs-lookup"><span data-stu-id="9ed47-2174">Exists</span></span>

<span data-ttu-id="9ed47-2175">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="9ed47-2176">Vzhledem k ukázkovým datům `sectionExists` je `false` to proto, že `section2:subsection2` v konfiguračních datech není oddíl.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="9ed47-2177">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="9ed47-2177">Bind to an object graph</span></span>

<span data-ttu-id="9ed47-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="9ed47-2179">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="9ed47-2180">Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="9ed47-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="9ed47-2181">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="9ed47-2182">Konfigurace je svázána s celým `TvShow` grafem objektů pomocí `Bind` metody.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="9ed47-2183">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="9ed47-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="9ed47-2185">`Get<T>`je pohodlnější než použití `Bind` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="9ed47-2186">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9ed47-2187">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="9ed47-2187">Bind an array to a class</span></span>

<span data-ttu-id="9ed47-2188">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="9ed47-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="9ed47-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9ed47-2190">Libovolný formát pole, který zveřejňuje numerický klíčový segment ( `:0:` , `:1:` ,), &hellip; `:{n}:` je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="9ed47-2191">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2191">Binding is provided by convention.</span></span> <span data-ttu-id="9ed47-2192">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="9ed47-2193">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="9ed47-2193">**In-memory array processing**</span></span>

<span data-ttu-id="9ed47-2194">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="9ed47-2195">Klíč</span><span class="sxs-lookup"><span data-stu-id="9ed47-2195">Key</span></span>             | <span data-ttu-id="9ed47-2196">Hodnota</span><span class="sxs-lookup"><span data-stu-id="9ed47-2196">Value</span></span>  |
| :---
<span data-ttu-id="9ed47-2197">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2198">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2199">'Identity'</span></span>
- <span data-ttu-id="9ed47-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2201">'Razor'</span></span>
- <span data-ttu-id="9ed47-2202">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2203">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2204">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2205">'Identity'</span></span>
- <span data-ttu-id="9ed47-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2207">'Razor'</span></span>
- <span data-ttu-id="9ed47-2208">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2209">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2210">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2211">'Identity'</span></span>
- <span data-ttu-id="9ed47-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2213">'Razor'</span></span>
- <span data-ttu-id="9ed47-2214">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2215">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2216">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2217">'Identity'</span></span>
- <span data-ttu-id="9ed47-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2219">'Razor'</span></span>
- <span data-ttu-id="9ed47-2220">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2221">-------: | :----: | | pole: položky: 0 | value0 | | pole: položky: 1 | Hodnota1 | | pole: položky: 2 | hodnota2 | | pole: položky: 4 | value4 | | Array: položky: 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="9ed47-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="9ed47-2222">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="9ed47-2223">Pole přeskočí hodnotu indexu &num; 3.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="9ed47-2224">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="9ed47-2225">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="9ed47-2226">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="9ed47-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lze také použít syntaxi, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="9ed47-2228">Vázaný objekt, instance `ArrayExample` , přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="9ed47-2229">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="9ed47-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9ed47-2230">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="9ed47-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="9ed47-2231">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2232">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2233">'Identity'</span></span>
- <span data-ttu-id="9ed47-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2235">'Razor'</span></span>
- <span data-ttu-id="9ed47-2236">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2237">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2238">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2239">'Identity'</span></span>
- <span data-ttu-id="9ed47-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2241">'Razor'</span></span>
- <span data-ttu-id="9ed47-2242">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2243">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2244">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2245">'Identity'</span></span>
- <span data-ttu-id="9ed47-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2247">'Razor'</span></span>
- <span data-ttu-id="9ed47-2248">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2249">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2250">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2251">'Identity'</span></span>
- <span data-ttu-id="9ed47-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2253">'Razor'</span></span>
- <span data-ttu-id="9ed47-2254">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2255">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2256">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2257">'Identity'</span></span>
- <span data-ttu-id="9ed47-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2259">'Razor'</span></span>
- <span data-ttu-id="9ed47-2260">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2261">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2262">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2263">'Identity'</span></span>
- <span data-ttu-id="9ed47-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2265">'Razor'</span></span>
- <span data-ttu-id="9ed47-2266">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2267">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2268">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2269">'Identity'</span></span>
- <span data-ttu-id="9ed47-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2271">'Razor'</span></span>
- <span data-ttu-id="9ed47-2272">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2273">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2274">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2275">'Identity'</span></span>
- <span data-ttu-id="9ed47-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2277">'Razor'</span></span>
- <span data-ttu-id="9ed47-2278">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2279">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2280">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2281">'Identity'</span></span>
- <span data-ttu-id="9ed47-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2283">'Razor'</span></span>
- <span data-ttu-id="9ed47-2284">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2285">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2286">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2287">'Identity'</span></span>
- <span data-ttu-id="9ed47-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2289">'Razor'</span></span>
- <span data-ttu-id="9ed47-2290">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2291">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2292">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2293">'Identity'</span></span>
- <span data-ttu-id="9ed47-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2295">'Razor'</span></span>
- <span data-ttu-id="9ed47-2296">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2297">-------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2298">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2299">'Identity'</span></span>
- <span data-ttu-id="9ed47-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2301">'Razor'</span></span>
- <span data-ttu-id="9ed47-2302">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2303">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2304">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2305">'Identity'</span></span>
- <span data-ttu-id="9ed47-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2307">'Razor'</span></span>
- <span data-ttu-id="9ed47-2308">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2309">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2310">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2311">'Identity'</span></span>
- <span data-ttu-id="9ed47-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2313">'Razor'</span></span>
- <span data-ttu-id="9ed47-2314">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2315">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2316">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2317">'Identity'</span></span>
- <span data-ttu-id="9ed47-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2319">'Razor'</span></span>
- <span data-ttu-id="9ed47-2320">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2321">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2322">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2323">'Identity'</span></span>
- <span data-ttu-id="9ed47-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2325">'Razor'</span></span>
- <span data-ttu-id="9ed47-2326">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2327">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2328">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2329">'Identity'</span></span>
- <span data-ttu-id="9ed47-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2331">'Razor'</span></span>
- <span data-ttu-id="9ed47-2332">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2333">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2334">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2335">'Identity'</span></span>
- <span data-ttu-id="9ed47-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2337">'Razor'</span></span>
- <span data-ttu-id="9ed47-2338">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2339">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2340">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2341">'Identity'</span></span>
- <span data-ttu-id="9ed47-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2343">'Razor'</span></span>
- <span data-ttu-id="9ed47-2344">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2345">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2346">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2347">'Identity'</span></span>
- <span data-ttu-id="9ed47-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2349">'Razor'</span></span>
- <span data-ttu-id="9ed47-2350">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2351">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2352">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2353">'Identity'</span></span>
- <span data-ttu-id="9ed47-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2355">'Razor'</span></span>
- <span data-ttu-id="9ed47-2356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2357">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2358">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2359">'Identity'</span></span>
- <span data-ttu-id="9ed47-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2361">'Razor'</span></span>
- <span data-ttu-id="9ed47-2362">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2363">-------------: | | 0 | value0 | | 1 | Hodnota1 | | 2 | hodnota2 | | 3 | value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="9ed47-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="9ed47-2364">Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9ed47-2365">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9ed47-2366">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9ed47-2367">Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na `ArrayExample` instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="9ed47-2368">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="9ed47-2369">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="9ed47-2370">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="9ed47-2371">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="9ed47-2372">Klíč</span><span class="sxs-lookup"><span data-stu-id="9ed47-2372">Key</span></span>             | <span data-ttu-id="9ed47-2373">Hodnota</span><span class="sxs-lookup"><span data-stu-id="9ed47-2373">Value</span></span>  |
| :---
<span data-ttu-id="9ed47-2374">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2375">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2376">'Identity'</span></span>
- <span data-ttu-id="9ed47-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2378">'Razor'</span></span>
- <span data-ttu-id="9ed47-2379">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2380">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2381">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2382">'Identity'</span></span>
- <span data-ttu-id="9ed47-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2384">'Razor'</span></span>
- <span data-ttu-id="9ed47-2385">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2386">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2387">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2388">'Identity'</span></span>
- <span data-ttu-id="9ed47-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2390">'Razor'</span></span>
- <span data-ttu-id="9ed47-2391">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2392">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2393">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2394">'Identity'</span></span>
- <span data-ttu-id="9ed47-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2396">'Razor'</span></span>
- <span data-ttu-id="9ed47-2397">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2398">-------: | :----: | | pole: položky: 3 | hodnota3 |</span><span class="sxs-lookup"><span data-stu-id="9ed47-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="9ed47-2399">Je-li `ArrayExample` instance třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro index &num; 3, `ArrayExample.Entries` pole obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="9ed47-2400">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="9ed47-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9ed47-2401">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="9ed47-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="9ed47-2402">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2403">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2404">'Identity'</span></span>
- <span data-ttu-id="9ed47-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2406">'Razor'</span></span>
- <span data-ttu-id="9ed47-2407">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2408">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2409">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2410">'Identity'</span></span>
- <span data-ttu-id="9ed47-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2412">'Razor'</span></span>
- <span data-ttu-id="9ed47-2413">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2414">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2415">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2416">'Identity'</span></span>
- <span data-ttu-id="9ed47-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2418">'Razor'</span></span>
- <span data-ttu-id="9ed47-2419">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2420">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2421">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2422">'Identity'</span></span>
- <span data-ttu-id="9ed47-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2424">'Razor'</span></span>
- <span data-ttu-id="9ed47-2425">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2426">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2427">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2428">'Identity'</span></span>
- <span data-ttu-id="9ed47-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2430">'Razor'</span></span>
- <span data-ttu-id="9ed47-2431">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2432">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2433">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2434">'Identity'</span></span>
- <span data-ttu-id="9ed47-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2436">'Razor'</span></span>
- <span data-ttu-id="9ed47-2437">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2438">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2439">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2440">'Identity'</span></span>
- <span data-ttu-id="9ed47-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2442">'Razor'</span></span>
- <span data-ttu-id="9ed47-2443">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2444">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2445">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2446">'Identity'</span></span>
- <span data-ttu-id="9ed47-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2448">'Razor'</span></span>
- <span data-ttu-id="9ed47-2449">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2450">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2451">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2452">'Identity'</span></span>
- <span data-ttu-id="9ed47-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2454">'Razor'</span></span>
- <span data-ttu-id="9ed47-2455">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2456">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2457">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2458">'Identity'</span></span>
- <span data-ttu-id="9ed47-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2460">'Razor'</span></span>
- <span data-ttu-id="9ed47-2461">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2462">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2463">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2464">'Identity'</span></span>
- <span data-ttu-id="9ed47-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2466">'Razor'</span></span>
- <span data-ttu-id="9ed47-2467">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2468">-------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2469">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2470">'Identity'</span></span>
- <span data-ttu-id="9ed47-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2472">'Razor'</span></span>
- <span data-ttu-id="9ed47-2473">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2474">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2475">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2476">'Identity'</span></span>
- <span data-ttu-id="9ed47-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2478">'Razor'</span></span>
- <span data-ttu-id="9ed47-2479">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2480">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2481">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2482">'Identity'</span></span>
- <span data-ttu-id="9ed47-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2484">'Razor'</span></span>
- <span data-ttu-id="9ed47-2485">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2486">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2487">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2488">'Identity'</span></span>
- <span data-ttu-id="9ed47-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2490">'Razor'</span></span>
- <span data-ttu-id="9ed47-2491">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2492">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2493">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2494">'Identity'</span></span>
- <span data-ttu-id="9ed47-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2496">'Razor'</span></span>
- <span data-ttu-id="9ed47-2497">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2498">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2499">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2500">'Identity'</span></span>
- <span data-ttu-id="9ed47-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2502">'Razor'</span></span>
- <span data-ttu-id="9ed47-2503">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2504">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2505">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2506">'Identity'</span></span>
- <span data-ttu-id="9ed47-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2508">'Razor'</span></span>
- <span data-ttu-id="9ed47-2509">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2510">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2511">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2512">'Identity'</span></span>
- <span data-ttu-id="9ed47-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2514">'Razor'</span></span>
- <span data-ttu-id="9ed47-2515">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2516">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2517">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2518">'Identity'</span></span>
- <span data-ttu-id="9ed47-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2520">'Razor'</span></span>
- <span data-ttu-id="9ed47-2521">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2522">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2523">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2524">'Identity'</span></span>
- <span data-ttu-id="9ed47-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2526">'Razor'</span></span>
- <span data-ttu-id="9ed47-2527">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2528">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2529">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2530">'Identity'</span></span>
- <span data-ttu-id="9ed47-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2532">'Razor'</span></span>
- <span data-ttu-id="9ed47-2533">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2534">-------------: | | 0 | value0 | | 1 | Hodnota1 | | 2 | hodnota2 | | 3 | hodnota3 | | 4 | value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="9ed47-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="9ed47-2535">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="9ed47-2535">**JSON array processing**</span></span>

<span data-ttu-id="9ed47-2536">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="9ed47-2537">V následujícím konfiguračním souboru `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="9ed47-2538">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="9ed47-2539">Klíč</span><span class="sxs-lookup"><span data-stu-id="9ed47-2539">Key</span></span>                     | <span data-ttu-id="9ed47-2540">Hodnota</span><span class="sxs-lookup"><span data-stu-id="9ed47-2540">Value</span></span>  |
| ---
<span data-ttu-id="9ed47-2541">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2542">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2543">'Identity'</span></span>
- <span data-ttu-id="9ed47-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2545">'Razor'</span></span>
- <span data-ttu-id="9ed47-2546">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2547">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2548">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2549">'Identity'</span></span>
- <span data-ttu-id="9ed47-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2551">'Razor'</span></span>
- <span data-ttu-id="9ed47-2552">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2553">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2554">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2555">'Identity'</span></span>
- <span data-ttu-id="9ed47-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2557">'Razor'</span></span>
- <span data-ttu-id="9ed47-2558">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2559">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2560">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2561">'Identity'</span></span>
- <span data-ttu-id="9ed47-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2563">'Razor'</span></span>
- <span data-ttu-id="9ed47-2564">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2565">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2566">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2567">'Identity'</span></span>
- <span data-ttu-id="9ed47-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2569">'Razor'</span></span>
- <span data-ttu-id="9ed47-2570">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2571">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2572">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2573">'Identity'</span></span>
- <span data-ttu-id="9ed47-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2575">'Razor'</span></span>
- <span data-ttu-id="9ed47-2576">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2577">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2578">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2579">'Identity'</span></span>
- <span data-ttu-id="9ed47-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2581">'Razor'</span></span>
- <span data-ttu-id="9ed47-2582">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2583">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2584">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2585">'Identity'</span></span>
- <span data-ttu-id="9ed47-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2587">'Razor'</span></span>
- <span data-ttu-id="9ed47-2588">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2589">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2590">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2591">'Identity'</span></span>
- <span data-ttu-id="9ed47-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2593">'Razor'</span></span>
- <span data-ttu-id="9ed47-2594">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2595">------------ | :----: | | json_array: klíč | hodnota | | json_array: dílčí oddíl: 0 | Hodnotab | | json_array: pododdíl: 1 | valueC | | json_array: pododdíl: 2 | Oceněno |</span><span class="sxs-lookup"><span data-stu-id="9ed47-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="9ed47-2596">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="9ed47-2597">Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu `valueA` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="9ed47-2598">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="9ed47-2599">`JsonArrayExample.Subsection`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="9ed47-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="9ed47-2600">`JsonArrayExample.Subsection`Osa</span><span class="sxs-lookup"><span data-stu-id="9ed47-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="9ed47-2601">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2602">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2603">'Identity'</span></span>
- <span data-ttu-id="9ed47-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2605">'Razor'</span></span>
- <span data-ttu-id="9ed47-2606">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2607">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2608">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2609">'Identity'</span></span>
- <span data-ttu-id="9ed47-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2611">'Razor'</span></span>
- <span data-ttu-id="9ed47-2612">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2613">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2614">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2615">'Identity'</span></span>
- <span data-ttu-id="9ed47-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2617">'Razor'</span></span>
- <span data-ttu-id="9ed47-2618">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2619">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2620">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2621">'Identity'</span></span>
- <span data-ttu-id="9ed47-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2623">'Razor'</span></span>
- <span data-ttu-id="9ed47-2624">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2625">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2626">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2627">'Identity'</span></span>
- <span data-ttu-id="9ed47-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2629">'Razor'</span></span>
- <span data-ttu-id="9ed47-2630">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2631">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2632">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2633">'Identity'</span></span>
- <span data-ttu-id="9ed47-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2635">'Razor'</span></span>
- <span data-ttu-id="9ed47-2636">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2637">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2638">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2639">'Identity'</span></span>
- <span data-ttu-id="9ed47-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2641">'Razor'</span></span>
- <span data-ttu-id="9ed47-2642">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2643">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2644">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2645">'Identity'</span></span>
- <span data-ttu-id="9ed47-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2647">'Razor'</span></span>
- <span data-ttu-id="9ed47-2648">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2649">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2650">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2651">'Identity'</span></span>
- <span data-ttu-id="9ed47-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2653">'Razor'</span></span>
- <span data-ttu-id="9ed47-2654">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2655">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2656">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2657">'Identity'</span></span>
- <span data-ttu-id="9ed47-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2659">'Razor'</span></span>
- <span data-ttu-id="9ed47-2660">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2661">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2662">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2663">'Identity'</span></span>
- <span data-ttu-id="9ed47-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2665">'Razor'</span></span>
- <span data-ttu-id="9ed47-2666">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2667">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2668">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2669">'Identity'</span></span>
- <span data-ttu-id="9ed47-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2671">'Razor'</span></span>
- <span data-ttu-id="9ed47-2672">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2673">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2674">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2675">'Identity'</span></span>
- <span data-ttu-id="9ed47-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2677">'Razor'</span></span>
- <span data-ttu-id="9ed47-2678">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2679">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2680">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2681">'Identity'</span></span>
- <span data-ttu-id="9ed47-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2683">'Razor'</span></span>
- <span data-ttu-id="9ed47-2684">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2685">-----------------: | :---název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2686">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2687">'Identity'</span></span>
- <span data-ttu-id="9ed47-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2689">'Razor'</span></span>
- <span data-ttu-id="9ed47-2690">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2691">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2692">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2693">'Identity'</span></span>
- <span data-ttu-id="9ed47-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2695">'Razor'</span></span>
- <span data-ttu-id="9ed47-2696">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2697">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2698">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2699">'Identity'</span></span>
- <span data-ttu-id="9ed47-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2701">'Razor'</span></span>
- <span data-ttu-id="9ed47-2702">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2703">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2704">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2705">'Identity'</span></span>
- <span data-ttu-id="9ed47-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2707">'Razor'</span></span>
- <span data-ttu-id="9ed47-2708">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2709">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2710">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2711">'Identity'</span></span>
- <span data-ttu-id="9ed47-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2713">'Razor'</span></span>
- <span data-ttu-id="9ed47-2714">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2715">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2716">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2717">'Identity'</span></span>
- <span data-ttu-id="9ed47-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2719">'Razor'</span></span>
- <span data-ttu-id="9ed47-2720">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2721">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2722">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2723">'Identity'</span></span>
- <span data-ttu-id="9ed47-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2725">'Razor'</span></span>
- <span data-ttu-id="9ed47-2726">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2727">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2728">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2729">'Identity'</span></span>
- <span data-ttu-id="9ed47-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2731">'Razor'</span></span>
- <span data-ttu-id="9ed47-2732">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2733">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2734">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2735">'Identity'</span></span>
- <span data-ttu-id="9ed47-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2737">'Razor'</span></span>
- <span data-ttu-id="9ed47-2738">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2739">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2740">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2741">'Identity'</span></span>
- <span data-ttu-id="9ed47-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2743">'Razor'</span></span>
- <span data-ttu-id="9ed47-2744">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2745">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2746">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2747">'Identity'</span></span>
- <span data-ttu-id="9ed47-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2749">'Razor'</span></span>
- <span data-ttu-id="9ed47-2750">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2751">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2752">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2753">'Identity'</span></span>
- <span data-ttu-id="9ed47-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2755">'Razor'</span></span>
- <span data-ttu-id="9ed47-2756">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2757">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2758">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2759">'Identity'</span></span>
- <span data-ttu-id="9ed47-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2761">'Razor'</span></span>
- <span data-ttu-id="9ed47-2762">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9ed47-2763">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9ed47-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2764">'Blazor'</span></span>
- <span data-ttu-id="9ed47-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2765">'Identity'</span></span>
- <span data-ttu-id="9ed47-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="9ed47-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9ed47-2767">'Razor'</span></span>
- <span data-ttu-id="9ed47-2768">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="9ed47-2769">-----------------: | | 0 | Hodnotab | | 1 | valueC | | 2 | Oceněno |</span><span class="sxs-lookup"><span data-stu-id="9ed47-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="9ed47-2770">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="9ed47-2770">Custom configuration provider</span></span>

<span data-ttu-id="9ed47-2771">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="9ed47-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9ed47-2772">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9ed47-2773">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9ed47-2774">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9ed47-2775">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9ed47-2776">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9ed47-2777">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9ed47-2778">Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9ed47-2779">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9ed47-2780">Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9ed47-2781">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9ed47-2782">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9ed47-2783">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9ed47-2784">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9ed47-2785">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="9ed47-2786">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9ed47-2787">`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9ed47-2788">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9ed47-2789">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="9ed47-2790">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="9ed47-2790">Access configuration during startup</span></span>

<span data-ttu-id="9ed47-2791">Vložení `IConfiguration` do `Startup` konstruktoru pro přístup k hodnotám konfigurace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9ed47-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9ed47-2792">Chcete-li získat přístup ke konfiguraci v `Startup.Configure` , buď `IConfiguration` přímo do metody, nebo použijte instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="9ed47-2793">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="9ed47-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="9ed47-2794">Přístup ke konfiguraci na Razor stránce stránky nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="9ed47-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="9ed47-2795">Chcete-li získat přístup k nastavení konfigurace na Razor stránce stránky nebo zobrazení MVC, [přidejte direktivu using](xref:mvc/views/razor#using) ([Referenční dokumentace jazyka C#: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> ji do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="9ed47-2796">Na Razor stránce stránky:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="9ed47-2797">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="9ed47-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9ed47-2798">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="9ed47-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="9ed47-2799"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9ed47-2800">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9ed47-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ed47-2801">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9ed47-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

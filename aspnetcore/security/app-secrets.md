---
title: Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ukládat a načítat citlivé informace jako tajné kódy aplikací během vývoje aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661806"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="286e6-103">Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="286e6-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="286e6-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="286e6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="286e6-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="286e6-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="286e6-106">Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje aplikace ASP.NET Core na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="286e6-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="286e6-107">Nikdy neuklápějte hesla nebo jiná citlivá data do zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="286e6-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="286e6-108">Produkční tajné kódy by neměly být používány pro vývoj nebo testování.</span><span class="sxs-lookup"><span data-stu-id="286e6-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="286e6-109">Tajné klíče by neměly být nasazeny s aplikací.</span><span class="sxs-lookup"><span data-stu-id="286e6-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="286e6-110">Místo toho tajné klíče by měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Tajné klíče azure test a produkční verze můžete ukládat a chránit pomocí [zprostředkovatele konfigurace azure key vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="286e6-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="286e6-111">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="286e6-111">Environment variables</span></span>

<span data-ttu-id="286e6-112">Proměnné prostředí se používají k zabránění ukládání tajných kódů aplikací v kódu nebo v místních konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="286e6-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="286e6-113">Proměnné prostředí přepíší hodnoty konfigurace pro všechny dříve určené zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="286e6-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="286e6-114">Zvažte ASP.NET základní webovou aplikaci, ve které je povoleno zabezpečení **jednotlivých uživatelských účtů.**</span><span class="sxs-lookup"><span data-stu-id="286e6-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="286e6-115">Výchozí připojovací řetězec databáze je součástí souboru *appsettings.json* projektu s klíčem `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="286e6-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="286e6-116">Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo.</span><span class="sxs-lookup"><span data-stu-id="286e6-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="286e6-117">Během nasazení aplikace `DefaultConnection` může být hodnota klíče přepsána hodnotou proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="286e6-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="286e6-118">Proměnná prostředí může uložit úplný připojovací řetězec s citlivými pověřeními.</span><span class="sxs-lookup"><span data-stu-id="286e6-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="286e6-119">Proměnné prostředí jsou obvykle uloženy v prostém, nešifrovaném textu.</span><span class="sxs-lookup"><span data-stu-id="286e6-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="286e6-120">Pokud je počítač nebo proces ohrožen, proměnné prostředí mohou přistupovat nedůvěryhodné strany.</span><span class="sxs-lookup"><span data-stu-id="286e6-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="286e6-121">Mohou být vyžadována další opatření, která zabrání zveřejnění tajných tajemství uživatelů.</span><span class="sxs-lookup"><span data-stu-id="286e6-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="286e6-122">Tajný správce</span><span class="sxs-lookup"><span data-stu-id="286e6-122">Secret Manager</span></span>

<span data-ttu-id="286e6-123">Nástroj Správce tajných kódů ukládá citlivá data během vývoje projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="286e6-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="286e6-124">V tomto kontextu část citlivá data je tajný klíč aplikace.</span><span class="sxs-lookup"><span data-stu-id="286e6-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="286e6-125">Tajné klíče aplikací jsou uloženy na jiném místě ze stromu projektu.</span><span class="sxs-lookup"><span data-stu-id="286e6-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="286e6-126">Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny v několika projektech.</span><span class="sxs-lookup"><span data-stu-id="286e6-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="286e6-127">Tajné klíče aplikace nejsou zaškrtnuté do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="286e6-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="286e6-128">Nástroj Správce tajných barev nešifruje uložené tajné klíče a neměl by být považován za důvěryhodný obchod.</span><span class="sxs-lookup"><span data-stu-id="286e6-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="286e6-129">Je to jen pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="286e6-129">It's for development purposes only.</span></span> <span data-ttu-id="286e6-130">Klíče a hodnoty jsou uloženy v konfiguračním souboru JSON v adresáři profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="286e6-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="286e6-131">Jak nástroj Správce tajných služeb funguje</span><span class="sxs-lookup"><span data-stu-id="286e6-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="286e6-132">Nástroj Správce tajných služeb abstrahuje podrobnosti implementace, například kde a jak jsou uloženy hodnoty.</span><span class="sxs-lookup"><span data-stu-id="286e6-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="286e6-133">Nástroj můžete použít bez znalosti těchto podrobností implementace.</span><span class="sxs-lookup"><span data-stu-id="286e6-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="286e6-134">Hodnoty jsou uloženy v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:</span><span class="sxs-lookup"><span data-stu-id="286e6-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="286e6-135">Windows</span><span class="sxs-lookup"><span data-stu-id="286e6-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="286e6-136">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="286e6-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="286e6-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="286e6-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="286e6-138">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="286e6-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="286e6-139">V předchozích cestách souborů `<user_secrets_id>` nahraďte hodnotou `UserSecretsId` zadanou v souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="286e6-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="286e6-140">Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných barev.</span><span class="sxs-lookup"><span data-stu-id="286e6-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="286e6-141">Tyto podrobnosti implementace se mohou změnit.</span><span class="sxs-lookup"><span data-stu-id="286e6-141">These implementation details may change.</span></span> <span data-ttu-id="286e6-142">Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="286e6-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="286e6-143">Povolení tajného úložiště</span><span class="sxs-lookup"><span data-stu-id="286e6-143">Enable secret storage</span></span>

<span data-ttu-id="286e6-144">Nástroj Správce tajných barev pracuje s nastavením konfigurace specifickým pro projekt uloženým ve vašem uživatelském profilu.</span><span class="sxs-lookup"><span data-stu-id="286e6-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="286e6-145">Nástroj Správce tajných `init` barev obsahuje příkaz v sadě .NET Core SDK 3.0.100 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="286e6-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="286e6-146">Chcete-li použít tajné klíče uživatelů, spusťte v adresáři projektu následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="286e6-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="286e6-147">Předchozí příkaz přidá `UserSecretsId` prvek do `PropertyGroup` souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="286e6-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="286e6-148">Ve výchozím nastavení `UserSecretsId` je vnitřní text identifikátoru GUID.</span><span class="sxs-lookup"><span data-stu-id="286e6-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="286e6-149">Vnitřní text je libovolný, ale je jedinečný pro projekt.</span><span class="sxs-lookup"><span data-stu-id="286e6-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="286e6-150">V sadě Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumníkovi řešení a v místní nabídce vyberte **Spravovat tajné kódy uživatelů.**</span><span class="sxs-lookup"><span data-stu-id="286e6-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="286e6-151">Toto gesto `UserSecretsId` přidá prvek naplněný identifikátorem GUID do souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="286e6-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="286e6-152">Nastavení tajného klíče</span><span class="sxs-lookup"><span data-stu-id="286e6-152">Set a secret</span></span>

<span data-ttu-id="286e6-153">Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty.</span><span class="sxs-lookup"><span data-stu-id="286e6-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="286e6-154">Tajný klíč je spojen s `UserSecretsId` hodnotou projektu.</span><span class="sxs-lookup"><span data-stu-id="286e6-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="286e6-155">Spusťte například následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="286e6-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="286e6-156">V předchozím příkladu dvojtečka `Movies` označuje, že je `ServiceApiKey` literál objektu s vlastností.</span><span class="sxs-lookup"><span data-stu-id="286e6-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="286e6-157">Nástroj Správce tajných barev lze použít také z jiných adresářů.</span><span class="sxs-lookup"><span data-stu-id="286e6-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="286e6-158">Pomocí `--project` možnosti můžete zadat cestu k systému souborů, ve které existuje soubor *CSProJ.*</span><span class="sxs-lookup"><span data-stu-id="286e6-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="286e6-159">Příklad:</span><span class="sxs-lookup"><span data-stu-id="286e6-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="286e6-160">Sloučení struktury JSON v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="286e6-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="286e6-161">Gesto Spravovat **tajné klíče uživatelů** sady Visual Studio otevře soubor *secrets.json* v textovém editoru.</span><span class="sxs-lookup"><span data-stu-id="286e6-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="286e6-162">Nahraďte obsah *souboru secrets.json* dvojicemi klíč-hodnota, které mají být uloženy.</span><span class="sxs-lookup"><span data-stu-id="286e6-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="286e6-163">Příklad:</span><span class="sxs-lookup"><span data-stu-id="286e6-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="286e6-164">Struktura JSON je po úpravách `dotnet user-secrets remove` zploštělá pomocí nebo `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="286e6-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="286e6-165">Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí literál objektu. `Movies`</span><span class="sxs-lookup"><span data-stu-id="286e6-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="286e6-166">Změněný soubor se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="286e6-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="286e6-167">Nastavení více tajných kódů</span><span class="sxs-lookup"><span data-stu-id="286e6-167">Set multiple secrets</span></span>

<span data-ttu-id="286e6-168">Dávku tajných kódů lze nastavit potrubím `set` JSON do příkazu.</span><span class="sxs-lookup"><span data-stu-id="286e6-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="286e6-169">V následujícím příkladu jsou obsah souboru *input.json* `set` uveden do příkazu.</span><span class="sxs-lookup"><span data-stu-id="286e6-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="286e6-170">Windows</span><span class="sxs-lookup"><span data-stu-id="286e6-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="286e6-171">Otevřete příkazové prostředí a proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="286e6-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="286e6-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="286e6-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="286e6-173">Otevřete příkazové prostředí a proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="286e6-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="286e6-174">Přístup k tajnému klíči</span><span class="sxs-lookup"><span data-stu-id="286e6-174">Access a secret</span></span>

<span data-ttu-id="286e6-175">Rozhraní [ASP.NET jádrové konfigurace rozhraní API](xref:fundamentals/configuration/index) poskytuje přístup k tajným klíčům správce tajných barev.</span><span class="sxs-lookup"><span data-stu-id="286e6-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="286e6-176">V ASP.NET Core 2.0 nebo novější, zdroj konfigurace uživatelských tajných <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> kódů je automaticky přidán do režimu vývoje, když projekt volá inicializovat novou instanci hostitele s předkonfigurovanými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="286e6-176">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="286e6-177">`CreateDefaultBuilder`volání, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> když <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>je :</span><span class="sxs-lookup"><span data-stu-id="286e6-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="286e6-178">Když `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> uživatelských `Startup` tajných kódů explicitně voláním v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="286e6-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="286e6-179">Volat `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="286e6-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="286e6-180">Uživatelské tajné klíče lze `Configuration` načíst prostřednictvím rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="286e6-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="286e6-181">Mapovat tajné kódy na POCO</span><span class="sxs-lookup"><span data-stu-id="286e6-181">Map secrets to a POCO</span></span>

<span data-ttu-id="286e6-182">Mapování literálu celého objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečné pro agregaci souvisejících vlastností.</span><span class="sxs-lookup"><span data-stu-id="286e6-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="286e6-183">Chcete-li mapovat předchozí tajné klíče `Configuration` na POCO, použijte funkci [vazby vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="286e6-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="286e6-184">Následující kód se váže `MovieSettings` na vlastní POCO `ServiceApiKey` a přistupuje k hodnotě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="286e6-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="286e6-185">A `Movies:ConnectionString` `Movies:ServiceApiKey` tajné klíče jsou mapovány `MovieSettings`na příslušné vlastnosti v :</span><span class="sxs-lookup"><span data-stu-id="286e6-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="286e6-186">Nahrazení řetězce tajnými kódy</span><span class="sxs-lookup"><span data-stu-id="286e6-186">String replacement with secrets</span></span>

<span data-ttu-id="286e6-187">Ukládání hesel ve formátu prostého textu je nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="286e6-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="286e6-188">Například připojovací řetězec databáze uložený v *souboru appsettings.json* může obsahovat heslo pro zadaného uživatele:</span><span class="sxs-lookup"><span data-stu-id="286e6-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="286e6-189">Bezpečnější přístup je uložení hesla jako tajný klíč.</span><span class="sxs-lookup"><span data-stu-id="286e6-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="286e6-190">Příklad:</span><span class="sxs-lookup"><span data-stu-id="286e6-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="286e6-191">Odeberte dvojici `Password` klíč-hodnota z připojovacího řetězce v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="286e6-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="286e6-192">Příklad:</span><span class="sxs-lookup"><span data-stu-id="286e6-192">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="286e6-193">Hodnotu tajného klíče lze <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nastavit na <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu k dokončení připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="286e6-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="286e6-194">Seznam tajemství</span><span class="sxs-lookup"><span data-stu-id="286e6-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="286e6-195">Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="286e6-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="286e6-196">Zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="286e6-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="286e6-197">V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *souboru secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="286e6-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="286e6-198">Odebrání jednoho tajného klíče</span><span class="sxs-lookup"><span data-stu-id="286e6-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="286e6-199">Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="286e6-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="286e6-200">Soubor *secrets.json* aplikace byl upraven tak, aby odebral `MoviesConnectionString` dvojici klíč-hodnota přidruženou ke klíči:</span><span class="sxs-lookup"><span data-stu-id="286e6-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="286e6-201">`dotnet user-secrets list`zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="286e6-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="286e6-202">Odebrání všech tajných kódů</span><span class="sxs-lookup"><span data-stu-id="286e6-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="286e6-203">Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="286e6-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="286e6-204">Ze souboru *secrets.json* byly odstraněny všechny tajné klíče uživatelů aplikace:</span><span class="sxs-lookup"><span data-stu-id="286e6-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="286e6-205">Spuštěno `dotnet user-secrets list` zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="286e6-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="286e6-206">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="286e6-206">Additional resources</span></span>

* <span data-ttu-id="286e6-207">Informace o přístupu správce tajných barev ze služby IIS naleznete v [tomto problému.](https://github.com/dotnet/AspNetCore.Docs/issues/16328)</span><span class="sxs-lookup"><span data-stu-id="286e6-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="286e6-208">[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="286e6-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="286e6-209">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="286e6-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="286e6-210">Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje aplikace ASP.NET Core na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="286e6-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="286e6-211">Nikdy neuklápějte hesla nebo jiná citlivá data do zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="286e6-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="286e6-212">Produkční tajné kódy by neměly být používány pro vývoj nebo testování.</span><span class="sxs-lookup"><span data-stu-id="286e6-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="286e6-213">Tajné klíče by neměly být nasazeny s aplikací.</span><span class="sxs-lookup"><span data-stu-id="286e6-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="286e6-214">Místo toho tajné klíče by měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Tajné klíče azure test a produkční verze můžete ukládat a chránit pomocí [zprostředkovatele konfigurace azure key vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="286e6-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="286e6-215">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="286e6-215">Environment variables</span></span>

<span data-ttu-id="286e6-216">Proměnné prostředí se používají k zabránění ukládání tajných kódů aplikací v kódu nebo v místních konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="286e6-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="286e6-217">Proměnné prostředí přepíší hodnoty konfigurace pro všechny dříve určené zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="286e6-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="286e6-218">Zvažte ASP.NET základní webovou aplikaci, ve které je povoleno zabezpečení **jednotlivých uživatelských účtů.**</span><span class="sxs-lookup"><span data-stu-id="286e6-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="286e6-219">Výchozí připojovací řetězec databáze je součástí souboru *appsettings.json* projektu s klíčem `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="286e6-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="286e6-220">Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo.</span><span class="sxs-lookup"><span data-stu-id="286e6-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="286e6-221">Během nasazení aplikace `DefaultConnection` může být hodnota klíče přepsána hodnotou proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="286e6-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="286e6-222">Proměnná prostředí může uložit úplný připojovací řetězec s citlivými pověřeními.</span><span class="sxs-lookup"><span data-stu-id="286e6-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="286e6-223">Proměnné prostředí jsou obvykle uloženy v prostém, nešifrovaném textu.</span><span class="sxs-lookup"><span data-stu-id="286e6-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="286e6-224">Pokud je počítač nebo proces ohrožen, proměnné prostředí mohou přistupovat nedůvěryhodné strany.</span><span class="sxs-lookup"><span data-stu-id="286e6-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="286e6-225">Mohou být vyžadována další opatření, která zabrání zveřejnění tajných tajemství uživatelů.</span><span class="sxs-lookup"><span data-stu-id="286e6-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="286e6-226">Tajný správce</span><span class="sxs-lookup"><span data-stu-id="286e6-226">Secret Manager</span></span>

<span data-ttu-id="286e6-227">Nástroj Správce tajných kódů ukládá citlivá data během vývoje projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="286e6-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="286e6-228">V tomto kontextu část citlivá data je tajný klíč aplikace.</span><span class="sxs-lookup"><span data-stu-id="286e6-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="286e6-229">Tajné klíče aplikací jsou uloženy na jiném místě ze stromu projektu.</span><span class="sxs-lookup"><span data-stu-id="286e6-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="286e6-230">Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny v několika projektech.</span><span class="sxs-lookup"><span data-stu-id="286e6-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="286e6-231">Tajné klíče aplikace nejsou zaškrtnuté do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="286e6-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="286e6-232">Nástroj Správce tajných barev nešifruje uložené tajné klíče a neměl by být považován za důvěryhodný obchod.</span><span class="sxs-lookup"><span data-stu-id="286e6-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="286e6-233">Je to jen pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="286e6-233">It's for development purposes only.</span></span> <span data-ttu-id="286e6-234">Klíče a hodnoty jsou uloženy v konfiguračním souboru JSON v adresáři profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="286e6-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="286e6-235">Jak nástroj Správce tajných služeb funguje</span><span class="sxs-lookup"><span data-stu-id="286e6-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="286e6-236">Nástroj Správce tajných služeb abstrahuje podrobnosti implementace, například kde a jak jsou uloženy hodnoty.</span><span class="sxs-lookup"><span data-stu-id="286e6-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="286e6-237">Nástroj můžete použít bez znalosti těchto podrobností implementace.</span><span class="sxs-lookup"><span data-stu-id="286e6-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="286e6-238">Hodnoty jsou uloženy v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:</span><span class="sxs-lookup"><span data-stu-id="286e6-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="286e6-239">Windows</span><span class="sxs-lookup"><span data-stu-id="286e6-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="286e6-240">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="286e6-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="286e6-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="286e6-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="286e6-242">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="286e6-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="286e6-243">V předchozích cestách souborů `<user_secrets_id>` nahraďte hodnotou `UserSecretsId` zadanou v souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="286e6-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="286e6-244">Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných barev.</span><span class="sxs-lookup"><span data-stu-id="286e6-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="286e6-245">Tyto podrobnosti implementace se mohou změnit.</span><span class="sxs-lookup"><span data-stu-id="286e6-245">These implementation details may change.</span></span> <span data-ttu-id="286e6-246">Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="286e6-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="286e6-247">Povolení tajného úložiště</span><span class="sxs-lookup"><span data-stu-id="286e6-247">Enable secret storage</span></span>

<span data-ttu-id="286e6-248">Nástroj Správce tajných barev pracuje s nastavením konfigurace specifickým pro projekt uloženým ve vašem uživatelském profilu.</span><span class="sxs-lookup"><span data-stu-id="286e6-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="286e6-249">Chcete-li použít tajné `UserSecretsId` klíče `PropertyGroup` uživatelů, definujte prvek v rámci souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="286e6-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="286e6-250">Vnitřní text `UserSecretsId` je libovolný, ale je jedinečný pro projekt.</span><span class="sxs-lookup"><span data-stu-id="286e6-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="286e6-251">Vývojáři obvykle generují `UserSecretsId`identifikátor GUID pro rozhraní .</span><span class="sxs-lookup"><span data-stu-id="286e6-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="286e6-252">V sadě Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumníkovi řešení a v místní nabídce vyberte **Spravovat tajné kódy uživatelů.**</span><span class="sxs-lookup"><span data-stu-id="286e6-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="286e6-253">Toto gesto `UserSecretsId` přidá prvek naplněný identifikátorem GUID do souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="286e6-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="286e6-254">Nastavení tajného klíče</span><span class="sxs-lookup"><span data-stu-id="286e6-254">Set a secret</span></span>

<span data-ttu-id="286e6-255">Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty.</span><span class="sxs-lookup"><span data-stu-id="286e6-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="286e6-256">Tajný klíč je spojen s `UserSecretsId` hodnotou projektu.</span><span class="sxs-lookup"><span data-stu-id="286e6-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="286e6-257">Spusťte například následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="286e6-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="286e6-258">V předchozím příkladu dvojtečka `Movies` označuje, že je `ServiceApiKey` literál objektu s vlastností.</span><span class="sxs-lookup"><span data-stu-id="286e6-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="286e6-259">Nástroj Správce tajných barev lze použít také z jiných adresářů.</span><span class="sxs-lookup"><span data-stu-id="286e6-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="286e6-260">Pomocí `--project` možnosti můžete zadat cestu k systému souborů, ve které existuje soubor *CSProJ.*</span><span class="sxs-lookup"><span data-stu-id="286e6-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="286e6-261">Příklad:</span><span class="sxs-lookup"><span data-stu-id="286e6-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="286e6-262">Sloučení struktury JSON v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="286e6-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="286e6-263">Gesto Spravovat **tajné klíče uživatelů** sady Visual Studio otevře soubor *secrets.json* v textovém editoru.</span><span class="sxs-lookup"><span data-stu-id="286e6-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="286e6-264">Nahraďte obsah *souboru secrets.json* dvojicemi klíč-hodnota, které mají být uloženy.</span><span class="sxs-lookup"><span data-stu-id="286e6-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="286e6-265">Příklad:</span><span class="sxs-lookup"><span data-stu-id="286e6-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="286e6-266">Struktura JSON je po úpravách `dotnet user-secrets remove` zploštělá pomocí nebo `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="286e6-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="286e6-267">Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí literál objektu. `Movies`</span><span class="sxs-lookup"><span data-stu-id="286e6-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="286e6-268">Změněný soubor se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="286e6-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="286e6-269">Nastavení více tajných kódů</span><span class="sxs-lookup"><span data-stu-id="286e6-269">Set multiple secrets</span></span>

<span data-ttu-id="286e6-270">Dávku tajných kódů lze nastavit potrubím `set` JSON do příkazu.</span><span class="sxs-lookup"><span data-stu-id="286e6-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="286e6-271">V následujícím příkladu jsou obsah souboru *input.json* `set` uveden do příkazu.</span><span class="sxs-lookup"><span data-stu-id="286e6-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="286e6-272">Windows</span><span class="sxs-lookup"><span data-stu-id="286e6-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="286e6-273">Otevřete příkazové prostředí a proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="286e6-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="286e6-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="286e6-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="286e6-275">Otevřete příkazové prostředí a proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="286e6-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="286e6-276">Přístup k tajnému klíči</span><span class="sxs-lookup"><span data-stu-id="286e6-276">Access a secret</span></span>

<span data-ttu-id="286e6-277">Rozhraní [ASP.NET jádrové konfigurace rozhraní API](xref:fundamentals/configuration/index) poskytuje přístup k tajným klíčům správce tajných barev.</span><span class="sxs-lookup"><span data-stu-id="286e6-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="286e6-278">Pokud váš projekt cílí na rozhraní .NET Framework, nainstalujte balíček [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.</span><span class="sxs-lookup"><span data-stu-id="286e6-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>


<span data-ttu-id="286e6-279">V ASP.NET Core 2.0 nebo novější, zdroj konfigurace uživatelských tajných <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> kódů je automaticky přidán do režimu vývoje, když projekt volá inicializovat novou instanci hostitele s předkonfigurovanými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="286e6-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="286e6-280">`CreateDefaultBuilder`volání, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> když <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>je :</span><span class="sxs-lookup"><span data-stu-id="286e6-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


<span data-ttu-id="286e6-281">Když `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> uživatelských `Startup` tajných kódů explicitně voláním v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="286e6-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="286e6-282">Volat `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="286e6-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="286e6-283">Uživatelské tajné klíče lze `Configuration` načíst prostřednictvím rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="286e6-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="286e6-284">Mapovat tajné kódy na POCO</span><span class="sxs-lookup"><span data-stu-id="286e6-284">Map secrets to a POCO</span></span>

<span data-ttu-id="286e6-285">Mapování literálu celého objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečné pro agregaci souvisejících vlastností.</span><span class="sxs-lookup"><span data-stu-id="286e6-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="286e6-286">Chcete-li mapovat předchozí tajné klíče `Configuration` na POCO, použijte funkci [vazby vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="286e6-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="286e6-287">Následující kód se váže `MovieSettings` na vlastní POCO `ServiceApiKey` a přistupuje k hodnotě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="286e6-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="286e6-288">A `Movies:ConnectionString` `Movies:ServiceApiKey` tajné klíče jsou mapovány `MovieSettings`na příslušné vlastnosti v :</span><span class="sxs-lookup"><span data-stu-id="286e6-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="286e6-289">Nahrazení řetězce tajnými kódy</span><span class="sxs-lookup"><span data-stu-id="286e6-289">String replacement with secrets</span></span>

<span data-ttu-id="286e6-290">Ukládání hesel ve formátu prostého textu je nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="286e6-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="286e6-291">Například připojovací řetězec databáze uložený v *souboru appsettings.json* může obsahovat heslo pro zadaného uživatele:</span><span class="sxs-lookup"><span data-stu-id="286e6-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="286e6-292">Bezpečnější přístup je uložení hesla jako tajný klíč.</span><span class="sxs-lookup"><span data-stu-id="286e6-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="286e6-293">Příklad:</span><span class="sxs-lookup"><span data-stu-id="286e6-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="286e6-294">Odeberte dvojici `Password` klíč-hodnota z připojovacího řetězce v *souboru appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="286e6-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="286e6-295">Příklad:</span><span class="sxs-lookup"><span data-stu-id="286e6-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="286e6-296">Hodnotu tajného klíče lze <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nastavit na <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu k dokončení připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="286e6-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="286e6-297">Seznam tajemství</span><span class="sxs-lookup"><span data-stu-id="286e6-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="286e6-298">Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="286e6-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="286e6-299">Zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="286e6-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="286e6-300">V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *souboru secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="286e6-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="286e6-301">Odebrání jednoho tajného klíče</span><span class="sxs-lookup"><span data-stu-id="286e6-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="286e6-302">Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="286e6-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="286e6-303">Soubor *secrets.json* aplikace byl upraven tak, aby odebral `MoviesConnectionString` dvojici klíč-hodnota přidruženou ke klíči:</span><span class="sxs-lookup"><span data-stu-id="286e6-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="286e6-304">Spuštěno `dotnet user-secrets list` zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="286e6-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="286e6-305">Odebrání všech tajných kódů</span><span class="sxs-lookup"><span data-stu-id="286e6-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="286e6-306">Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*</span><span class="sxs-lookup"><span data-stu-id="286e6-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="286e6-307">Ze souboru *secrets.json* byly odstraněny všechny tajné klíče uživatelů aplikace:</span><span class="sxs-lookup"><span data-stu-id="286e6-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="286e6-308">Spuštěno `dotnet user-secrets list` zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="286e6-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="286e6-309">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="286e6-309">Additional resources</span></span>

* <span data-ttu-id="286e6-310">Informace o přístupu správce tajných barev ze služby IIS naleznete v [tomto problému.](https://github.com/dotnet/AspNetCore.Docs/issues/16328)</span><span class="sxs-lookup"><span data-stu-id="286e6-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
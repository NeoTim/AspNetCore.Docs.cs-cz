---
title: Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core
author: rick-anderson
description: Naučte se ukládat a načítat citlivé informace jako tajné klíče aplikace při vývoji aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 917e698d34a5d4b6c2c3f4737c08f1a590f5df1a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017945"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="d5568-103">Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5568-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5568-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5), [Daniel Skořepa](https://github.com/danroth27)a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="d5568-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="d5568-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5568-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d5568-106">Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje ASP.NET Core aplikace na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="d5568-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="d5568-107">Nikdy neukládejte hesla nebo další citlivá data ve zdrojovém kódu.</span><span class="sxs-lookup"><span data-stu-id="d5568-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="d5568-108">Provozní tajemství by se neměla používat pro vývoj nebo testování.</span><span class="sxs-lookup"><span data-stu-id="d5568-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="d5568-109">Tajné klíče by se neměly nasazovat spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d5568-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="d5568-110">Místo toho by tajné klíče měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="d5568-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="d5568-111">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="d5568-111">Environment variables</span></span>

<span data-ttu-id="d5568-112">Proměnné prostředí se používají k tomu, aby nedocházelo k ukládání tajných klíčů k aplikacím v kódu nebo v místních konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="d5568-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="d5568-113">Proměnné prostředí přepíšou konfigurační hodnoty pro všechny dříve zadané zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d5568-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="d5568-114">Vezměte v úvahu ASP.NET Core webovou aplikaci, ve které je povolené zabezpečení **individuálních uživatelských účtů** .</span><span class="sxs-lookup"><span data-stu-id="d5568-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="d5568-115">Výchozí připojovací řetězec databáze je součástí *appsettings.js* projektu se souborem s klíčem `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="d5568-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="d5568-116">Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo.</span><span class="sxs-lookup"><span data-stu-id="d5568-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="d5568-117">Během nasazování aplikace se `DefaultConnection` hodnota klíče dá přepsat hodnotou proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d5568-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="d5568-118">Proměnná prostředí může ukládat úplný připojovací řetězec s citlivými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="d5568-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="d5568-119">Proměnné prostředí se většinou ukládají v prostém, nešifrovaném textu.</span><span class="sxs-lookup"><span data-stu-id="d5568-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="d5568-120">Pokud dojde k ohrožení bezpečnosti počítače nebo procesu, jsou proměnné prostředí dostupné nedůvěryhodnými stranami.</span><span class="sxs-lookup"><span data-stu-id="d5568-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="d5568-121">Další opatření, která zabraňují odhalení tajných kódů uživateli, mohou být povinná.</span><span class="sxs-lookup"><span data-stu-id="d5568-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="d5568-122">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="d5568-122">Secret Manager</span></span>

<span data-ttu-id="d5568-123">Nástroj Správce tajných klíčů ukládá citlivé údaje během vývoje ASP.NET Core projektu.</span><span class="sxs-lookup"><span data-stu-id="d5568-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="d5568-124">V tomto kontextu je část citlivých dat tajný klíč aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5568-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="d5568-125">Tajné klíče aplikace se ukládají v odděleném umístění ze stromu projektu.</span><span class="sxs-lookup"><span data-stu-id="d5568-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="d5568-126">Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny napříč několika projekty.</span><span class="sxs-lookup"><span data-stu-id="d5568-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="d5568-127">Tajné kódy aplikace nejsou zaregistrovány do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="d5568-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="d5568-128">Nástroj Správce tajných klíčů nešifruje uložené tajné klíče a neměl by být považován za důvěryhodné úložiště.</span><span class="sxs-lookup"><span data-stu-id="d5568-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="d5568-129">Pouze pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="d5568-129">It's for development purposes only.</span></span> <span data-ttu-id="d5568-130">Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="d5568-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="d5568-131">Jak funguje nástroj Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="d5568-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="d5568-132">Nástroj Správce tajných klíčů vyříznout podrobnosti implementace, například WHERE a způsob uložení hodnot.</span><span class="sxs-lookup"><span data-stu-id="d5568-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="d5568-133">Tento nástroj můžete použít bez znalosti těchto podrobností implementace.</span><span class="sxs-lookup"><span data-stu-id="d5568-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="d5568-134">Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:</span><span class="sxs-lookup"><span data-stu-id="d5568-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="d5568-135">Windows</span><span class="sxs-lookup"><span data-stu-id="d5568-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="d5568-136">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="d5568-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="d5568-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="d5568-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="d5568-138">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="d5568-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="d5568-139">V předchozích cestách souborů nahraďte `<user_secrets_id>` `UserSecretsId` hodnotou zadanou v souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5568-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="d5568-140">Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="d5568-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="d5568-141">Tyto podrobnosti implementace se můžou změnit.</span><span class="sxs-lookup"><span data-stu-id="d5568-141">These implementation details may change.</span></span> <span data-ttu-id="d5568-142">Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="d5568-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="d5568-143">Povolit tajné úložiště</span><span class="sxs-lookup"><span data-stu-id="d5568-143">Enable secret storage</span></span>

<span data-ttu-id="d5568-144">Nástroj Správce tajných klíčů pracuje na nastavení konfigurace specifické pro projekt uložené v profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="d5568-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="d5568-145">Nástroj Správce tajných klíčů obsahuje `init` příkaz v .NET Core SDK 3.0.100 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="d5568-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="d5568-146">Chcete-li použít tajné klíče uživatele, spusťte následující příkaz v adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="d5568-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="d5568-147">Předchozí příkaz přidá `UserSecretsId` prvek v rámci `PropertyGroup` souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5568-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="d5568-148">Ve výchozím nastavení je vnitřní text pro `UserSecretsId` identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="d5568-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="d5568-149">Vnitřní text je libovolný, ale je pro projekt jedinečný.</span><span class="sxs-lookup"><span data-stu-id="d5568-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="d5568-150">V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumník řešení a v místní nabídce vyberte možnost **spravovat tajné klíče uživatele** .</span><span class="sxs-lookup"><span data-stu-id="d5568-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="d5568-151">Tento gesto přidá `UserSecretsId` prvek, který je vyplněn identifikátorem GUID, do souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5568-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="d5568-152">Nastavení tajného klíče</span><span class="sxs-lookup"><span data-stu-id="d5568-152">Set a secret</span></span>

<span data-ttu-id="d5568-153">Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d5568-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="d5568-154">Tajný kód je přidružený k `UserSecretsId` hodnotě projektu.</span><span class="sxs-lookup"><span data-stu-id="d5568-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="d5568-155">Například spusťte následující příkaz z adresáře, ve kterém existuje soubor *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="d5568-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="d5568-156">V předchozím příkladu dvojtečka označuje `Movies` literál objektu s `ServiceApiKey` vlastností.</span><span class="sxs-lookup"><span data-stu-id="d5568-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="d5568-157">Nástroj Správce tajných klíčů je možné použít i v jiných adresářích.</span><span class="sxs-lookup"><span data-stu-id="d5568-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="d5568-158">Pomocí `--project` Možnosti zadejte cestu k systému souborů, na které existuje soubor *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5568-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="d5568-159">Například:</span><span class="sxs-lookup"><span data-stu-id="d5568-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="d5568-160">Sloučení struktury JSON v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5568-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="d5568-161">Gesto **správy uživatelských tajných** kódů v aplikaci Visual Studio otevře *secrets.js* v souboru v textovém editoru.</span><span class="sxs-lookup"><span data-stu-id="d5568-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="d5568-162">Nahraďte obsah *secrets.js* s páry klíč-hodnota, které se mají uložit.</span><span class="sxs-lookup"><span data-stu-id="d5568-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="d5568-163">Například:</span><span class="sxs-lookup"><span data-stu-id="d5568-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="d5568-164">Struktura JSON je po úpravách prostřednictvím `dotnet user-secrets remove` nebo. shrnuta `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="d5568-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="d5568-165">Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí `Movies` literál objektu.</span><span class="sxs-lookup"><span data-stu-id="d5568-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="d5568-166">Změněný soubor se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="d5568-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="d5568-167">Nastavení více tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="d5568-167">Set multiple secrets</span></span>

<span data-ttu-id="d5568-168">Dávku tajných kódů lze nastavit pomocí `set` příkazuového řádku JSON pro příkaz.</span><span class="sxs-lookup"><span data-stu-id="d5568-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="d5568-169">V následujícím příkladu je *input.js* obsahu souboru kanálem do `set` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d5568-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="d5568-170">Windows</span><span class="sxs-lookup"><span data-stu-id="d5568-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="d5568-171">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="d5568-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="d5568-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="d5568-173">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="d5568-174">Přístup ke tajnému kódu</span><span class="sxs-lookup"><span data-stu-id="d5568-174">Access a secret</span></span>

<span data-ttu-id="d5568-175">[Rozhraní API pro konfiguraci ASP.NET Core](xref:fundamentals/configuration/index) poskytuje přístup ke tajným tajným klíčům správce.</span><span class="sxs-lookup"><span data-stu-id="d5568-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="d5568-176">Zdroj konfigurace uživatelských tajných klíčů je automaticky přidán do režimu vývoje, pokud projekt volá <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> inicializaci nové instance hostitele s předem konfigurovanými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="d5568-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="d5568-177">`CreateDefaultBuilder`volá <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> se, když <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> je <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="d5568-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="d5568-178">Pokud `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace tajných klíčů uživatele explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="d5568-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="d5568-179">Volejte `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d5568-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="d5568-180">Uživatelské tajné kódy lze načíst prostřednictvím `Configuration` rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="d5568-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="d5568-181">Mapování tajných kódů na POCO</span><span class="sxs-lookup"><span data-stu-id="d5568-181">Map secrets to a POCO</span></span>

<span data-ttu-id="d5568-182">Mapování celého literálu objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečná pro agregaci souvisejících vlastností.</span><span class="sxs-lookup"><span data-stu-id="d5568-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d5568-183">K namapování předchozích tajných kódů na POCO použijte `Configuration` funkci [vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d5568-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="d5568-184">Následující kód se váže k vlastnímu `MovieSettings` POCO a přistupuje k `ServiceApiKey` hodnotě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="d5568-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="d5568-185">`Movies:ConnectionString`Tajné kódy a `Movies:ServiceApiKey` jsou mapovány na příslušné vlastnosti v `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="d5568-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="d5568-186">Výměna řetězců s tajnými kódy</span><span class="sxs-lookup"><span data-stu-id="d5568-186">String replacement with secrets</span></span>

<span data-ttu-id="d5568-187">Ukládání hesel v prostém textu je nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="d5568-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="d5568-188">Připojovací řetězec databáze uložený v *appsettings.js* může například zahrnovat heslo pro zadaného uživatele:</span><span class="sxs-lookup"><span data-stu-id="d5568-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="d5568-189">Bezpečnější je ukládání hesla jako tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="d5568-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="d5568-190">Například:</span><span class="sxs-lookup"><span data-stu-id="d5568-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="d5568-191">Odeberte `Password` dvojici klíč-hodnota z připojovacího řetězce v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="d5568-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="d5568-192">Například:</span><span class="sxs-lookup"><span data-stu-id="d5568-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="d5568-193">Hodnota tajného klíče může být nastavena na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu pro dokončení připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="d5568-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="d5568-194">Výpis tajných kódů</span><span class="sxs-lookup"><span data-stu-id="d5568-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d5568-195">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="d5568-196">Objeví se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d5568-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="d5568-197">V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="d5568-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="d5568-198">Odebrání jednoho tajného klíče</span><span class="sxs-lookup"><span data-stu-id="d5568-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d5568-199">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="d5568-200">*secrets.jsaplikace v* souboru se změnila tak, aby se odebral pár klíč-hodnota přidružený ke `MoviesConnectionString` klíči:</span><span class="sxs-lookup"><span data-stu-id="d5568-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="d5568-201">`dotnet user-secrets list`zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="d5568-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="d5568-202">Odebrat všechna tajná klíčová</span><span class="sxs-lookup"><span data-stu-id="d5568-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d5568-203">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="d5568-204">Všechny tajné klíče uživatele pro aplikaci byly z *secrets.jsv* souboru odstraněny:</span><span class="sxs-lookup"><span data-stu-id="d5568-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="d5568-205">`dotnet user-secrets list`Při spuštění se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="d5568-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="d5568-206">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5568-206">Additional resources</span></span>

* <span data-ttu-id="d5568-207">Informace o přístupu ke Správci tajných kódů ze služby IIS najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="d5568-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5568-208">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Skořepa](https://github.com/danroth27)a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="d5568-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="d5568-209">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5568-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d5568-210">Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje ASP.NET Core aplikace na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="d5568-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="d5568-211">Nikdy neukládejte hesla nebo další citlivá data ve zdrojovém kódu.</span><span class="sxs-lookup"><span data-stu-id="d5568-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="d5568-212">Provozní tajemství by se neměla používat pro vývoj nebo testování.</span><span class="sxs-lookup"><span data-stu-id="d5568-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="d5568-213">Tajné klíče by se neměly nasazovat spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d5568-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="d5568-214">Místo toho by tajné klíče měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="d5568-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="d5568-215">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="d5568-215">Environment variables</span></span>

<span data-ttu-id="d5568-216">Proměnné prostředí se používají k tomu, aby nedocházelo k ukládání tajných klíčů k aplikacím v kódu nebo v místních konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="d5568-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="d5568-217">Proměnné prostředí přepíšou konfigurační hodnoty pro všechny dříve zadané zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d5568-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="d5568-218">Vezměte v úvahu ASP.NET Core webovou aplikaci, ve které je povolené zabezpečení **individuálních uživatelských účtů** .</span><span class="sxs-lookup"><span data-stu-id="d5568-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="d5568-219">Výchozí připojovací řetězec databáze je součástí *appsettings.js* projektu se souborem s klíčem `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="d5568-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="d5568-220">Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo.</span><span class="sxs-lookup"><span data-stu-id="d5568-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="d5568-221">Během nasazování aplikace se `DefaultConnection` hodnota klíče dá přepsat hodnotou proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d5568-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="d5568-222">Proměnná prostředí může ukládat úplný připojovací řetězec s citlivými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="d5568-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="d5568-223">Proměnné prostředí se většinou ukládají v prostém, nešifrovaném textu.</span><span class="sxs-lookup"><span data-stu-id="d5568-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="d5568-224">Pokud dojde k ohrožení bezpečnosti počítače nebo procesu, jsou proměnné prostředí dostupné nedůvěryhodnými stranami.</span><span class="sxs-lookup"><span data-stu-id="d5568-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="d5568-225">Další opatření, která zabraňují odhalení tajných kódů uživateli, mohou být povinná.</span><span class="sxs-lookup"><span data-stu-id="d5568-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="d5568-226">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="d5568-226">Secret Manager</span></span>

<span data-ttu-id="d5568-227">Nástroj Správce tajných klíčů ukládá citlivé údaje během vývoje ASP.NET Core projektu.</span><span class="sxs-lookup"><span data-stu-id="d5568-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="d5568-228">V tomto kontextu je část citlivých dat tajný klíč aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5568-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="d5568-229">Tajné klíče aplikace se ukládají v odděleném umístění ze stromu projektu.</span><span class="sxs-lookup"><span data-stu-id="d5568-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="d5568-230">Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny napříč několika projekty.</span><span class="sxs-lookup"><span data-stu-id="d5568-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="d5568-231">Tajné kódy aplikace nejsou zaregistrovány do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="d5568-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="d5568-232">Nástroj Správce tajných klíčů nešifruje uložené tajné klíče a neměl by být považován za důvěryhodné úložiště.</span><span class="sxs-lookup"><span data-stu-id="d5568-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="d5568-233">Pouze pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="d5568-233">It's for development purposes only.</span></span> <span data-ttu-id="d5568-234">Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="d5568-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="d5568-235">Jak funguje nástroj Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="d5568-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="d5568-236">Nástroj Správce tajných klíčů vyříznout podrobnosti implementace, například WHERE a způsob uložení hodnot.</span><span class="sxs-lookup"><span data-stu-id="d5568-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="d5568-237">Tento nástroj můžete použít bez znalosti těchto podrobností implementace.</span><span class="sxs-lookup"><span data-stu-id="d5568-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="d5568-238">Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:</span><span class="sxs-lookup"><span data-stu-id="d5568-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="d5568-239">Windows</span><span class="sxs-lookup"><span data-stu-id="d5568-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="d5568-240">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="d5568-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="d5568-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="d5568-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="d5568-242">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="d5568-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="d5568-243">V předchozích cestách souborů nahraďte `<user_secrets_id>` `UserSecretsId` hodnotou zadanou v souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5568-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="d5568-244">Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="d5568-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="d5568-245">Tyto podrobnosti implementace se můžou změnit.</span><span class="sxs-lookup"><span data-stu-id="d5568-245">These implementation details may change.</span></span> <span data-ttu-id="d5568-246">Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="d5568-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="d5568-247">Povolit tajné úložiště</span><span class="sxs-lookup"><span data-stu-id="d5568-247">Enable secret storage</span></span>

<span data-ttu-id="d5568-248">Nástroj Správce tajných klíčů pracuje na nastavení konfigurace specifické pro projekt uložené v profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="d5568-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="d5568-249">Pro použití uživatelských tajných klíčů definujte `UserSecretsId` prvek v rámci `PropertyGroup` souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5568-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="d5568-250">Vnitřní text `UserSecretsId` je libovolný, ale je pro projekt jedinečný.</span><span class="sxs-lookup"><span data-stu-id="d5568-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="d5568-251">Vývojáři obvykle generují identifikátor GUID pro `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="d5568-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="d5568-252">V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumník řešení a v místní nabídce vyberte možnost **spravovat tajné klíče uživatele** .</span><span class="sxs-lookup"><span data-stu-id="d5568-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="d5568-253">Tento gesto přidá `UserSecretsId` prvek, který je vyplněn identifikátorem GUID, do souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5568-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="d5568-254">Nastavení tajného klíče</span><span class="sxs-lookup"><span data-stu-id="d5568-254">Set a secret</span></span>

<span data-ttu-id="d5568-255">Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d5568-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="d5568-256">Tajný kód je přidružený k `UserSecretsId` hodnotě projektu.</span><span class="sxs-lookup"><span data-stu-id="d5568-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="d5568-257">Například spusťte následující příkaz z adresáře, ve kterém existuje soubor *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="d5568-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="d5568-258">V předchozím příkladu dvojtečka označuje `Movies` literál objektu s `ServiceApiKey` vlastností.</span><span class="sxs-lookup"><span data-stu-id="d5568-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="d5568-259">Nástroj Správce tajných klíčů je možné použít i v jiných adresářích.</span><span class="sxs-lookup"><span data-stu-id="d5568-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="d5568-260">Pomocí `--project` Možnosti zadejte cestu k systému souborů, na které existuje soubor *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="d5568-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="d5568-261">Například:</span><span class="sxs-lookup"><span data-stu-id="d5568-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="d5568-262">Sloučení struktury JSON v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5568-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="d5568-263">Gesto **správy uživatelských tajných** kódů v aplikaci Visual Studio otevře *secrets.js* v souboru v textovém editoru.</span><span class="sxs-lookup"><span data-stu-id="d5568-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="d5568-264">Nahraďte obsah *secrets.js* s páry klíč-hodnota, které se mají uložit.</span><span class="sxs-lookup"><span data-stu-id="d5568-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="d5568-265">Například:</span><span class="sxs-lookup"><span data-stu-id="d5568-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="d5568-266">Struktura JSON je po úpravách prostřednictvím `dotnet user-secrets remove` nebo. shrnuta `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="d5568-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="d5568-267">Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí `Movies` literál objektu.</span><span class="sxs-lookup"><span data-stu-id="d5568-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="d5568-268">Změněný soubor se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="d5568-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="d5568-269">Nastavení více tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="d5568-269">Set multiple secrets</span></span>

<span data-ttu-id="d5568-270">Dávku tajných kódů lze nastavit pomocí `set` příkazuového řádku JSON pro příkaz.</span><span class="sxs-lookup"><span data-stu-id="d5568-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="d5568-271">V následujícím příkladu je *input.js* obsahu souboru kanálem do `set` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d5568-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="d5568-272">Windows</span><span class="sxs-lookup"><span data-stu-id="d5568-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="d5568-273">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="d5568-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="d5568-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="d5568-275">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="d5568-276">Přístup ke tajnému kódu</span><span class="sxs-lookup"><span data-stu-id="d5568-276">Access a secret</span></span>

<span data-ttu-id="d5568-277">[Rozhraní API pro konfiguraci ASP.NET Core](xref:fundamentals/configuration/index) poskytuje přístup ke tajným tajným klíčům správce.</span><span class="sxs-lookup"><span data-stu-id="d5568-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="d5568-278">Pokud je projekt cílen .NET Framework, nainstalujte [Microsoft.Extensions.Configuration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)Balíček NuGet UserSecrets</span><span class="sxs-lookup"><span data-stu-id="d5568-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="d5568-279">V ASP.NET Core 2,0 nebo novějším je zdroj konfigurace tajných kódů uživatelských tajných kódů automaticky přidán do režimu vývoje, pokud projekt volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> inicializaci nové instance hostitele s předem konfigurovanými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="d5568-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="d5568-280">`CreateDefaultBuilder`volá <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> se, když <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> je <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="d5568-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="d5568-281">Pokud `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace tajných klíčů uživatele explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> v `Startup` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="d5568-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="d5568-282">Volejte `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d5568-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="d5568-283">Uživatelské tajné kódy lze načíst prostřednictvím `Configuration` rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="d5568-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="d5568-284">Mapování tajných kódů na POCO</span><span class="sxs-lookup"><span data-stu-id="d5568-284">Map secrets to a POCO</span></span>

<span data-ttu-id="d5568-285">Mapování celého literálu objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečná pro agregaci souvisejících vlastností.</span><span class="sxs-lookup"><span data-stu-id="d5568-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d5568-286">K namapování předchozích tajných kódů na POCO použijte `Configuration` funkci [vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d5568-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="d5568-287">Následující kód se váže k vlastnímu `MovieSettings` POCO a přistupuje k `ServiceApiKey` hodnotě vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="d5568-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="d5568-288">`Movies:ConnectionString`Tajné kódy a `Movies:ServiceApiKey` jsou mapovány na příslušné vlastnosti v `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="d5568-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="d5568-289">Výměna řetězců s tajnými kódy</span><span class="sxs-lookup"><span data-stu-id="d5568-289">String replacement with secrets</span></span>

<span data-ttu-id="d5568-290">Ukládání hesel v prostém textu je nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="d5568-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="d5568-291">Připojovací řetězec databáze uložený v *appsettings.js* může například zahrnovat heslo pro zadaného uživatele:</span><span class="sxs-lookup"><span data-stu-id="d5568-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="d5568-292">Bezpečnější je ukládání hesla jako tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="d5568-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="d5568-293">Například:</span><span class="sxs-lookup"><span data-stu-id="d5568-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="d5568-294">Odeberte `Password` dvojici klíč-hodnota z připojovacího řetězce v *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="d5568-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="d5568-295">Například:</span><span class="sxs-lookup"><span data-stu-id="d5568-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="d5568-296">Hodnota tajného klíče může být nastavena na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu pro dokončení připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="d5568-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="d5568-297">Výpis tajných kódů</span><span class="sxs-lookup"><span data-stu-id="d5568-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d5568-298">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="d5568-299">Objeví se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d5568-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="d5568-300">V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="d5568-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="d5568-301">Odebrání jednoho tajného klíče</span><span class="sxs-lookup"><span data-stu-id="d5568-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d5568-302">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="d5568-303">*secrets.jsaplikace v* souboru se změnila tak, aby se odebral pár klíč-hodnota přidružený ke `MoviesConnectionString` klíči:</span><span class="sxs-lookup"><span data-stu-id="d5568-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="d5568-304">`dotnet user-secrets list`Při spuštění se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="d5568-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="d5568-305">Odebrat všechna tajná klíčová</span><span class="sxs-lookup"><span data-stu-id="d5568-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d5568-306">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5568-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="d5568-307">Všechny tajné klíče uživatele pro aplikaci byly z *secrets.jsv* souboru odstraněny:</span><span class="sxs-lookup"><span data-stu-id="d5568-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="d5568-308">`dotnet user-secrets list`Při spuštění se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="d5568-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="d5568-309">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5568-309">Additional resources</span></span>

* <span data-ttu-id="d5568-310">Informace o přístupu ke Správci tajných kódů ze služby IIS najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="d5568-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
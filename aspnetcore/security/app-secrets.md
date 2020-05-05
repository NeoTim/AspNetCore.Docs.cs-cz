---
title: Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core
author: rick-anderson
description: Naučte se ukládat a načítat citlivé informace jako tajné klíče aplikace při vývoji aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 7508aebcda4e14812140f13ece635428908a4abb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776679"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="11319-103">Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11319-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="11319-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5), [Daniel Skořepa](https://github.com/danroth27)a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="11319-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="11319-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11319-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="11319-106">Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje ASP.NET Core aplikace na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="11319-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="11319-107">Nikdy neukládejte hesla nebo další citlivá data ve zdrojovém kódu.</span><span class="sxs-lookup"><span data-stu-id="11319-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="11319-108">Provozní tajemství by se neměla používat pro vývoj nebo testování.</span><span class="sxs-lookup"><span data-stu-id="11319-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="11319-109">Tajné klíče by se neměly nasazovat spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="11319-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="11319-110">Místo toho by tajné klíče měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="11319-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="11319-111">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="11319-111">Environment variables</span></span>

<span data-ttu-id="11319-112">Proměnné prostředí se používají k tomu, aby nedocházelo k ukládání tajných klíčů k aplikacím v kódu nebo v místních konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="11319-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="11319-113">Proměnné prostředí přepíšou konfigurační hodnoty pro všechny dříve zadané zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="11319-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="11319-114">Vezměte v úvahu ASP.NET Core webovou aplikaci, ve které je povolené zabezpečení **individuálních uživatelských účtů** .</span><span class="sxs-lookup"><span data-stu-id="11319-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="11319-115">Výchozí připojovací řetězec databáze je obsažen v souboru *appSettings. JSON* projektu s klíčem `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="11319-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="11319-116">Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo.</span><span class="sxs-lookup"><span data-stu-id="11319-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="11319-117">Během nasazování aplikace se `DefaultConnection` hodnota klíče dá přepsat hodnotou proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="11319-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="11319-118">Proměnná prostředí může ukládat úplný připojovací řetězec s citlivými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="11319-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="11319-119">Proměnné prostředí se většinou ukládají v prostém, nešifrovaném textu.</span><span class="sxs-lookup"><span data-stu-id="11319-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="11319-120">Pokud dojde k ohrožení bezpečnosti počítače nebo procesu, jsou proměnné prostředí dostupné nedůvěryhodnými stranami.</span><span class="sxs-lookup"><span data-stu-id="11319-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="11319-121">Další opatření, která zabraňují odhalení tajných kódů uživateli, mohou být povinná.</span><span class="sxs-lookup"><span data-stu-id="11319-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="11319-122">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="11319-122">Secret Manager</span></span>

<span data-ttu-id="11319-123">Nástroj Správce tajných klíčů ukládá citlivé údaje během vývoje ASP.NET Core projektu.</span><span class="sxs-lookup"><span data-stu-id="11319-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="11319-124">V tomto kontextu je část citlivých dat tajný klíč aplikace.</span><span class="sxs-lookup"><span data-stu-id="11319-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="11319-125">Tajné klíče aplikace se ukládají v odděleném umístění ze stromu projektu.</span><span class="sxs-lookup"><span data-stu-id="11319-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="11319-126">Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny napříč několika projekty.</span><span class="sxs-lookup"><span data-stu-id="11319-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="11319-127">Tajné kódy aplikace nejsou zaregistrovány do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="11319-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="11319-128">Nástroj Správce tajných klíčů nešifruje uložené tajné klíče a neměl by být považován za důvěryhodné úložiště.</span><span class="sxs-lookup"><span data-stu-id="11319-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="11319-129">Pouze pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="11319-129">It's for development purposes only.</span></span> <span data-ttu-id="11319-130">Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="11319-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="11319-131">Jak funguje nástroj Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="11319-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="11319-132">Nástroj Správce tajných klíčů vyříznout podrobnosti implementace, například WHERE a způsob uložení hodnot.</span><span class="sxs-lookup"><span data-stu-id="11319-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="11319-133">Tento nástroj můžete použít bez znalosti těchto podrobností implementace.</span><span class="sxs-lookup"><span data-stu-id="11319-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="11319-134">Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:</span><span class="sxs-lookup"><span data-stu-id="11319-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="11319-135">Windows</span><span class="sxs-lookup"><span data-stu-id="11319-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="11319-136">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="11319-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="11319-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="11319-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="11319-138">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="11319-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="11319-139">V předchozích cestách souborů nahraďte `<user_secrets_id>` `UserSecretsId` hodnotou zadanou v souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="11319-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="11319-140">Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="11319-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="11319-141">Tyto podrobnosti implementace se můžou změnit.</span><span class="sxs-lookup"><span data-stu-id="11319-141">These implementation details may change.</span></span> <span data-ttu-id="11319-142">Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="11319-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="11319-143">Povolit tajné úložiště</span><span class="sxs-lookup"><span data-stu-id="11319-143">Enable secret storage</span></span>

<span data-ttu-id="11319-144">Nástroj Správce tajných klíčů pracuje na nastavení konfigurace specifické pro projekt uložené v profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="11319-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="11319-145">Nástroj Správce tajných klíčů obsahuje `init` příkaz v .NET Core SDK 3.0.100 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="11319-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="11319-146">Chcete-li použít tajné klíče uživatele, spusťte následující příkaz v adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="11319-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="11319-147">Předchozí příkaz přidá `UserSecretsId` prvek v rámci `PropertyGroup` souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="11319-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="11319-148">Ve výchozím nastavení je vnitřní text pro `UserSecretsId` identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="11319-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="11319-149">Vnitřní text je libovolný, ale je pro projekt jedinečný.</span><span class="sxs-lookup"><span data-stu-id="11319-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="11319-150">V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumník řešení a v místní nabídce vyberte možnost **spravovat tajné klíče uživatele** .</span><span class="sxs-lookup"><span data-stu-id="11319-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="11319-151">Tento gesto přidá `UserSecretsId` prvek, který je vyplněn identifikátorem GUID, do souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="11319-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="11319-152">Nastavení tajného klíče</span><span class="sxs-lookup"><span data-stu-id="11319-152">Set a secret</span></span>

<span data-ttu-id="11319-153">Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty.</span><span class="sxs-lookup"><span data-stu-id="11319-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="11319-154">Tajný kód je přidružený k `UserSecretsId` hodnotě projektu.</span><span class="sxs-lookup"><span data-stu-id="11319-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="11319-155">Například spusťte následující příkaz z adresáře, ve kterém existuje soubor *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="11319-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="11319-156">V předchozím příkladu dvojtečka označuje `Movies` literál objektu s `ServiceApiKey` vlastností.</span><span class="sxs-lookup"><span data-stu-id="11319-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="11319-157">Nástroj Správce tajných klíčů je možné použít i v jiných adresářích.</span><span class="sxs-lookup"><span data-stu-id="11319-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="11319-158">Pomocí `--project` možnosti zadejte cestu k systému souborů, na které existuje soubor *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="11319-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="11319-159">Příklad:</span><span class="sxs-lookup"><span data-stu-id="11319-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="11319-160">Sloučení struktury JSON v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11319-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="11319-161">Gesto **Spravovat uživatelské tajemství** v aplikaci Visual Studio otevře v textovém editoru soubor *tajných kódů. JSON* .</span><span class="sxs-lookup"><span data-stu-id="11319-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="11319-162">Nahraďte obsah *tajných klíčů. JSON* páry klíč-hodnota, které se mají uložit.</span><span class="sxs-lookup"><span data-stu-id="11319-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="11319-163">Příklad:</span><span class="sxs-lookup"><span data-stu-id="11319-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="11319-164">Struktura JSON je po úpravách prostřednictvím `dotnet user-secrets remove` nebo `dotnet user-secrets set`. shrnuta.</span><span class="sxs-lookup"><span data-stu-id="11319-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="11319-165">Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí literál `Movies` objektu.</span><span class="sxs-lookup"><span data-stu-id="11319-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="11319-166">Změněný soubor se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="11319-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="11319-167">Nastavení více tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="11319-167">Set multiple secrets</span></span>

<span data-ttu-id="11319-168">Dávku tajných kódů lze nastavit pomocí `set` příkazuového řádku JSON pro příkaz.</span><span class="sxs-lookup"><span data-stu-id="11319-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="11319-169">V následujícím příkladu je obsah souboru *input. JSON* v kanálu do `set` příkazu.</span><span class="sxs-lookup"><span data-stu-id="11319-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="11319-170">Windows</span><span class="sxs-lookup"><span data-stu-id="11319-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="11319-171">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="11319-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="11319-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="11319-173">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="11319-174">Přístup ke tajnému kódu</span><span class="sxs-lookup"><span data-stu-id="11319-174">Access a secret</span></span>

<span data-ttu-id="11319-175">[Rozhraní API pro konfiguraci ASP.NET Core](xref:fundamentals/configuration/index) poskytuje přístup ke tajným tajným klíčům správce.</span><span class="sxs-lookup"><span data-stu-id="11319-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="11319-176">Zdroj konfigurace uživatelských tajných klíčů je automaticky přidán do režimu vývoje, pokud projekt <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> volá inicializaci nové instance hostitele s předem konfigurovanými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="11319-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="11319-177">`CreateDefaultBuilder`volá <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> se, <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> když <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>je:</span><span class="sxs-lookup"><span data-stu-id="11319-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="11319-178">Pokud `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace tajných klíčů uživatele explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span><span class="sxs-lookup"><span data-stu-id="11319-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="11319-179">Volejte `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="11319-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="11319-180">Uživatelské tajné kódy lze načíst prostřednictvím `Configuration` rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="11319-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="11319-181">Mapování tajných kódů na POCO</span><span class="sxs-lookup"><span data-stu-id="11319-181">Map secrets to a POCO</span></span>

<span data-ttu-id="11319-182">Mapování celého literálu objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečná pro agregaci souvisejících vlastností.</span><span class="sxs-lookup"><span data-stu-id="11319-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="11319-183">K namapování předchozích tajných kódů na POCO použijte funkci `Configuration` [vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="11319-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="11319-184">Následující kód se váže k vlastnímu `MovieSettings` POCO a přistupuje k hodnotě `ServiceApiKey` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="11319-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="11319-185">Tajné `Movies:ConnectionString` kódy `Movies:ServiceApiKey` a jsou mapovány na příslušné vlastnosti v `MovieSettings`:</span><span class="sxs-lookup"><span data-stu-id="11319-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="11319-186">Výměna řetězců s tajnými kódy</span><span class="sxs-lookup"><span data-stu-id="11319-186">String replacement with secrets</span></span>

<span data-ttu-id="11319-187">Ukládání hesel v prostém textu je nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="11319-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="11319-188">Připojovací řetězec databáze uložený v souboru *appSettings. JSON* může například zahrnovat heslo pro zadaného uživatele:</span><span class="sxs-lookup"><span data-stu-id="11319-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="11319-189">Bezpečnější je ukládání hesla jako tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="11319-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="11319-190">Příklad:</span><span class="sxs-lookup"><span data-stu-id="11319-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="11319-191">Odeberte dvojici `Password` klíč-hodnota z připojovacího řetězce v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="11319-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="11319-192">Příklad:</span><span class="sxs-lookup"><span data-stu-id="11319-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="11319-193">Hodnota tajného klíče může být nastavena na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu pro dokončení připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="11319-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="11319-194">Výpis tajných kódů</span><span class="sxs-lookup"><span data-stu-id="11319-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="11319-195">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="11319-196">Zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="11319-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="11319-197">V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *tajných kódů. JSON*.</span><span class="sxs-lookup"><span data-stu-id="11319-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="11319-198">Odebrání jednoho tajného klíče</span><span class="sxs-lookup"><span data-stu-id="11319-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="11319-199">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="11319-200">Soubor *tajných kódů. JSON* aplikace byl změněn tak, aby odstranil dvojici klíč-hodnota přidruženou ke `MoviesConnectionString` klíči:</span><span class="sxs-lookup"><span data-stu-id="11319-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="11319-201">`dotnet user-secrets list`zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="11319-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="11319-202">Odebrat všechna tajná klíčová</span><span class="sxs-lookup"><span data-stu-id="11319-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="11319-203">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="11319-204">Všechny tajné klíče uživatele pro aplikaci byly odstraněny ze souboru *tajných kódů. JSON* :</span><span class="sxs-lookup"><span data-stu-id="11319-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="11319-205">Při `dotnet user-secrets list` spuštění se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="11319-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="11319-206">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="11319-206">Additional resources</span></span>

* <span data-ttu-id="11319-207">Informace o přístupu ke Správci tajných kódů ze služby IIS najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="11319-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="11319-208">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Skořepa](https://github.com/danroth27)a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="11319-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="11319-209">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11319-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="11319-210">Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje ASP.NET Core aplikace na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="11319-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="11319-211">Nikdy neukládejte hesla nebo další citlivá data ve zdrojovém kódu.</span><span class="sxs-lookup"><span data-stu-id="11319-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="11319-212">Provozní tajemství by se neměla používat pro vývoj nebo testování.</span><span class="sxs-lookup"><span data-stu-id="11319-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="11319-213">Tajné klíče by se neměly nasazovat spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="11319-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="11319-214">Místo toho by tajné klíče měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="11319-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="11319-215">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="11319-215">Environment variables</span></span>

<span data-ttu-id="11319-216">Proměnné prostředí se používají k tomu, aby nedocházelo k ukládání tajných klíčů k aplikacím v kódu nebo v místních konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="11319-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="11319-217">Proměnné prostředí přepíšou konfigurační hodnoty pro všechny dříve zadané zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="11319-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="11319-218">Vezměte v úvahu ASP.NET Core webovou aplikaci, ve které je povolené zabezpečení **individuálních uživatelských účtů** .</span><span class="sxs-lookup"><span data-stu-id="11319-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="11319-219">Výchozí připojovací řetězec databáze je obsažen v souboru *appSettings. JSON* projektu s klíčem `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="11319-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="11319-220">Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo.</span><span class="sxs-lookup"><span data-stu-id="11319-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="11319-221">Během nasazování aplikace se `DefaultConnection` hodnota klíče dá přepsat hodnotou proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="11319-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="11319-222">Proměnná prostředí může ukládat úplný připojovací řetězec s citlivými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="11319-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="11319-223">Proměnné prostředí se většinou ukládají v prostém, nešifrovaném textu.</span><span class="sxs-lookup"><span data-stu-id="11319-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="11319-224">Pokud dojde k ohrožení bezpečnosti počítače nebo procesu, jsou proměnné prostředí dostupné nedůvěryhodnými stranami.</span><span class="sxs-lookup"><span data-stu-id="11319-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="11319-225">Další opatření, která zabraňují odhalení tajných kódů uživateli, mohou být povinná.</span><span class="sxs-lookup"><span data-stu-id="11319-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="11319-226">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="11319-226">Secret Manager</span></span>

<span data-ttu-id="11319-227">Nástroj Správce tajných klíčů ukládá citlivé údaje během vývoje ASP.NET Core projektu.</span><span class="sxs-lookup"><span data-stu-id="11319-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="11319-228">V tomto kontextu je část citlivých dat tajný klíč aplikace.</span><span class="sxs-lookup"><span data-stu-id="11319-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="11319-229">Tajné klíče aplikace se ukládají v odděleném umístění ze stromu projektu.</span><span class="sxs-lookup"><span data-stu-id="11319-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="11319-230">Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny napříč několika projekty.</span><span class="sxs-lookup"><span data-stu-id="11319-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="11319-231">Tajné kódy aplikace nejsou zaregistrovány do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="11319-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="11319-232">Nástroj Správce tajných klíčů nešifruje uložené tajné klíče a neměl by být považován za důvěryhodné úložiště.</span><span class="sxs-lookup"><span data-stu-id="11319-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="11319-233">Pouze pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="11319-233">It's for development purposes only.</span></span> <span data-ttu-id="11319-234">Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="11319-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="11319-235">Jak funguje nástroj Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="11319-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="11319-236">Nástroj Správce tajných klíčů vyříznout podrobnosti implementace, například WHERE a způsob uložení hodnot.</span><span class="sxs-lookup"><span data-stu-id="11319-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="11319-237">Tento nástroj můžete použít bez znalosti těchto podrobností implementace.</span><span class="sxs-lookup"><span data-stu-id="11319-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="11319-238">Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:</span><span class="sxs-lookup"><span data-stu-id="11319-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="11319-239">Windows</span><span class="sxs-lookup"><span data-stu-id="11319-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="11319-240">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="11319-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="11319-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="11319-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="11319-242">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="11319-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="11319-243">V předchozích cestách souborů nahraďte `<user_secrets_id>` `UserSecretsId` hodnotou zadanou v souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="11319-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="11319-244">Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="11319-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="11319-245">Tyto podrobnosti implementace se můžou změnit.</span><span class="sxs-lookup"><span data-stu-id="11319-245">These implementation details may change.</span></span> <span data-ttu-id="11319-246">Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="11319-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="11319-247">Povolit tajné úložiště</span><span class="sxs-lookup"><span data-stu-id="11319-247">Enable secret storage</span></span>

<span data-ttu-id="11319-248">Nástroj Správce tajných klíčů pracuje na nastavení konfigurace specifické pro projekt uložené v profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="11319-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="11319-249">Pro použití uživatelských tajných klíčů definujte `UserSecretsId` prvek v rámci `PropertyGroup` souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="11319-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="11319-250">Vnitřní text `UserSecretsId` je libovolný, ale je pro projekt jedinečný.</span><span class="sxs-lookup"><span data-stu-id="11319-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="11319-251">Vývojáři obvykle generují identifikátor GUID pro `UserSecretsId`.</span><span class="sxs-lookup"><span data-stu-id="11319-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="11319-252">V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumník řešení a v místní nabídce vyberte možnost **spravovat tajné klíče uživatele** .</span><span class="sxs-lookup"><span data-stu-id="11319-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="11319-253">Tento gesto přidá `UserSecretsId` prvek, který je vyplněn identifikátorem GUID, do souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="11319-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="11319-254">Nastavení tajného klíče</span><span class="sxs-lookup"><span data-stu-id="11319-254">Set a secret</span></span>

<span data-ttu-id="11319-255">Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty.</span><span class="sxs-lookup"><span data-stu-id="11319-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="11319-256">Tajný kód je přidružený k `UserSecretsId` hodnotě projektu.</span><span class="sxs-lookup"><span data-stu-id="11319-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="11319-257">Například spusťte následující příkaz z adresáře, ve kterém existuje soubor *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="11319-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="11319-258">V předchozím příkladu dvojtečka označuje `Movies` literál objektu s `ServiceApiKey` vlastností.</span><span class="sxs-lookup"><span data-stu-id="11319-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="11319-259">Nástroj Správce tajných klíčů je možné použít i v jiných adresářích.</span><span class="sxs-lookup"><span data-stu-id="11319-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="11319-260">Pomocí `--project` možnosti zadejte cestu k systému souborů, na které existuje soubor *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="11319-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="11319-261">Příklad:</span><span class="sxs-lookup"><span data-stu-id="11319-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="11319-262">Sloučení struktury JSON v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11319-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="11319-263">Gesto **Spravovat uživatelské tajemství** v aplikaci Visual Studio otevře v textovém editoru soubor *tajných kódů. JSON* .</span><span class="sxs-lookup"><span data-stu-id="11319-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="11319-264">Nahraďte obsah *tajných klíčů. JSON* páry klíč-hodnota, které se mají uložit.</span><span class="sxs-lookup"><span data-stu-id="11319-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="11319-265">Příklad:</span><span class="sxs-lookup"><span data-stu-id="11319-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="11319-266">Struktura JSON je po úpravách prostřednictvím `dotnet user-secrets remove` nebo `dotnet user-secrets set`. shrnuta.</span><span class="sxs-lookup"><span data-stu-id="11319-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="11319-267">Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí literál `Movies` objektu.</span><span class="sxs-lookup"><span data-stu-id="11319-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="11319-268">Změněný soubor se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="11319-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="11319-269">Nastavení více tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="11319-269">Set multiple secrets</span></span>

<span data-ttu-id="11319-270">Dávku tajných kódů lze nastavit pomocí `set` příkazuového řádku JSON pro příkaz.</span><span class="sxs-lookup"><span data-stu-id="11319-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="11319-271">V následujícím příkladu je obsah souboru *input. JSON* v kanálu do `set` příkazu.</span><span class="sxs-lookup"><span data-stu-id="11319-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="11319-272">Windows</span><span class="sxs-lookup"><span data-stu-id="11319-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="11319-273">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="11319-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="11319-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="11319-275">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="11319-276">Přístup ke tajnému kódu</span><span class="sxs-lookup"><span data-stu-id="11319-276">Access a secret</span></span>

<span data-ttu-id="11319-277">[Rozhraní API pro konfiguraci ASP.NET Core](xref:fundamentals/configuration/index) poskytuje přístup ke tajným tajným klíčům správce.</span><span class="sxs-lookup"><span data-stu-id="11319-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="11319-278">Pokud je projekt cílen .NET Framework, nainstalujte balíček NuGet [Microsoft. Extensions. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="11319-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="11319-279">V ASP.NET Core 2,0 nebo novějším je zdroj konfigurace tajných kódů uživatelských tajných kódů automaticky přidán do režimu vývoje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , pokud projekt volá inicializaci nové instance hostitele s předem konfigurovanými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="11319-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="11319-280">`CreateDefaultBuilder`volá <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> se, <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> když <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>je:</span><span class="sxs-lookup"><span data-stu-id="11319-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="11319-281">Pokud `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace tajných klíčů uživatele explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> v `Startup` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="11319-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="11319-282">Volejte `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="11319-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="11319-283">Uživatelské tajné kódy lze načíst prostřednictvím `Configuration` rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="11319-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="11319-284">Mapování tajných kódů na POCO</span><span class="sxs-lookup"><span data-stu-id="11319-284">Map secrets to a POCO</span></span>

<span data-ttu-id="11319-285">Mapování celého literálu objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečná pro agregaci souvisejících vlastností.</span><span class="sxs-lookup"><span data-stu-id="11319-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="11319-286">K namapování předchozích tajných kódů na POCO použijte funkci `Configuration` [vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="11319-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="11319-287">Následující kód se váže k vlastnímu `MovieSettings` POCO a přistupuje k hodnotě `ServiceApiKey` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="11319-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="11319-288">Tajné `Movies:ConnectionString` kódy `Movies:ServiceApiKey` a jsou mapovány na příslušné vlastnosti v `MovieSettings`:</span><span class="sxs-lookup"><span data-stu-id="11319-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="11319-289">Výměna řetězců s tajnými kódy</span><span class="sxs-lookup"><span data-stu-id="11319-289">String replacement with secrets</span></span>

<span data-ttu-id="11319-290">Ukládání hesel v prostém textu je nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="11319-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="11319-291">Připojovací řetězec databáze uložený v souboru *appSettings. JSON* může například zahrnovat heslo pro zadaného uživatele:</span><span class="sxs-lookup"><span data-stu-id="11319-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="11319-292">Bezpečnější je ukládání hesla jako tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="11319-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="11319-293">Příklad:</span><span class="sxs-lookup"><span data-stu-id="11319-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="11319-294">Odeberte dvojici `Password` klíč-hodnota z připojovacího řetězce v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="11319-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="11319-295">Příklad:</span><span class="sxs-lookup"><span data-stu-id="11319-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="11319-296">Hodnota tajného klíče může být nastavena na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu pro dokončení připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="11319-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="11319-297">Výpis tajných kódů</span><span class="sxs-lookup"><span data-stu-id="11319-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="11319-298">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="11319-299">Zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="11319-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="11319-300">V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *tajných kódů. JSON*.</span><span class="sxs-lookup"><span data-stu-id="11319-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="11319-301">Odebrání jednoho tajného klíče</span><span class="sxs-lookup"><span data-stu-id="11319-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="11319-302">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="11319-303">Soubor *tajných kódů. JSON* aplikace byl změněn tak, aby odstranil dvojici klíč-hodnota přidruženou ke `MoviesConnectionString` klíči:</span><span class="sxs-lookup"><span data-stu-id="11319-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="11319-304">Při `dotnet user-secrets list` spuštění se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="11319-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="11319-305">Odebrat všechna tajná klíčová</span><span class="sxs-lookup"><span data-stu-id="11319-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="11319-306">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="11319-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="11319-307">Všechny tajné klíče uživatele pro aplikaci byly odstraněny ze souboru *tajných kódů. JSON* :</span><span class="sxs-lookup"><span data-stu-id="11319-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="11319-308">Při `dotnet user-secrets list` spuštění se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="11319-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="11319-309">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="11319-309">Additional resources</span></span>

* <span data-ttu-id="11319-310">Informace o přístupu ke Správci tajných kódů ze služby IIS najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="11319-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end
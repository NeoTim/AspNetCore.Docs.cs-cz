---
title: Bezpečné ukládání tajných kódů aplikace při vývoji v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ukládat a načítat citlivých informací jako tajných kódů aplikace během vývoje aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/31/2019
uid: security/app-secrets
ms.openlocfilehash: eaa2e9d1ba98d391a29a9ff55872d062df016b87
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667775"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="30c1e-103">Bezpečné ukládání tajných kódů aplikace při vývoji v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="30c1e-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="30c1e-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="30c1e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="30c1e-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/app-secrets/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="30c1e-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="30c1e-106">Tento dokument popisuje postupy pro ukládání a načítání citlivých dat během vývoje aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30c1e-106">This document explains techniques for storing and retrieving sensitive data during the development of an ASP.NET Core app.</span></span> <span data-ttu-id="30c1e-107">Nikdy ukládání hesel nebo jiných citlivých dat. ve zdrojovém kódu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="30c1e-108">Tajné klíče v produkčním prostředí by se neměly pro vývoj nebo testování.</span><span class="sxs-lookup"><span data-stu-id="30c1e-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="30c1e-109">Můžete ukládat a chránit Azure testovací a produkční tajných kódů pomocí [poskytovatele konfigurace služby Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="30c1e-109">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="30c1e-110">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="30c1e-110">Environment variables</span></span>

<span data-ttu-id="30c1e-111">Proměnné prostředí je použit k ukládání tajných kódů aplikace v kódu nebo v místních konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="30c1e-111">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="30c1e-112">Proměnné prostředí přepsat hodnoty konfigurace pro všechny zdroje dříve zadanou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="30c1e-112">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="30c1e-113">Konfigurace čtení hodnot proměnných prostředí pomocí volání [AddEnvironmentVariables](/dotnet/api/microsoft.extensions.configuration.environmentvariablesextensions.addenvironmentvariables) v `Startup` konstruktor:</span><span class="sxs-lookup"><span data-stu-id="30c1e-113">Configure the reading of environment variable values by calling [AddEnvironmentVariables](/dotnet/api/microsoft.extensions.configuration.environmentvariablesextensions.addenvironmentvariables) in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="30c1e-114">Vezměte v úvahu webovou aplikaci ASP.NET Core, ve kterém **jednotlivé uživatelské účty** je zabezpečená.</span><span class="sxs-lookup"><span data-stu-id="30c1e-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="30c1e-115">Výchozí připojovací řetězec databáze je součástí projektu *appsettings.json* soubor s klíčem `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="30c1e-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="30c1e-116">Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje, aby heslo.</span><span class="sxs-lookup"><span data-stu-id="30c1e-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="30c1e-117">Během nasazování aplikací `DefaultConnection` klíče hodnota se dá přepsat hodnotou proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="30c1e-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="30c1e-118">Proměnná prostředí může ukládat úplný připojovací řetězec s citlivé přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="30c1e-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="30c1e-119">Proměnné prostředí jsou obecně uložené v nezašifrované prostého textu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="30c1e-120">Pokud počítači nebo procesu dojde k ohrožení, proměnné prostředí je přístupný nedůvěryhodní.</span><span class="sxs-lookup"><span data-stu-id="30c1e-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="30c1e-121">Může vyžadovat další opatření k zamezení zveřejňování těchto tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="30c1e-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

## <a name="secret-manager"></a><span data-ttu-id="30c1e-122">Tajný klíč správce</span><span class="sxs-lookup"><span data-stu-id="30c1e-122">Secret Manager</span></span>

<span data-ttu-id="30c1e-123">Tajný klíč správce nástroj ukládá citlivých dat během vývoje projektu aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30c1e-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="30c1e-124">V tomto kontextu je část citlivá data tajný kód aplikace.</span><span class="sxs-lookup"><span data-stu-id="30c1e-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="30c1e-125">Tajné kódy aplikace jsou uložené v samostatném umístění ve stromu projektu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="30c1e-126">Tajných kódů aplikace jsou přidružené k určitému projektu nebo sdílet mezi více projekty.</span><span class="sxs-lookup"><span data-stu-id="30c1e-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="30c1e-127">Tajných kódů aplikace se změnami do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="30c1e-128">Nástroj manažera tajných nešifruje uložené tajných kódů a by neměla být zpracována jako důvěryhodné úložiště.</span><span class="sxs-lookup"><span data-stu-id="30c1e-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="30c1e-129">Je jenom pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="30c1e-129">It's for development purposes only.</span></span> <span data-ttu-id="30c1e-130">Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="30c1e-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="30c1e-131">Jak funguje nástroj tajný klíč správce</span><span class="sxs-lookup"><span data-stu-id="30c1e-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="30c1e-132">Nástroj manažera tajných vyčleňuje podrobnosti implementace, například jak a kde jsou uložené hodnoty.</span><span class="sxs-lookup"><span data-stu-id="30c1e-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="30c1e-133">Nástroj můžete použít bez znalosti tyto podrobnosti implementace.</span><span class="sxs-lookup"><span data-stu-id="30c1e-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="30c1e-134">Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu systému protected Users na místním počítači:</span><span class="sxs-lookup"><span data-stu-id="30c1e-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="30c1e-135">Windows</span><span class="sxs-lookup"><span data-stu-id="30c1e-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="30c1e-136">Cesta systému souborů:</span><span class="sxs-lookup"><span data-stu-id="30c1e-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="macostabmacos"></a>[<span data-ttu-id="30c1e-137">macOS</span><span class="sxs-lookup"><span data-stu-id="30c1e-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="30c1e-138">Cesta systému souborů:</span><span class="sxs-lookup"><span data-stu-id="30c1e-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

# <a name="linuxtablinux"></a>[<span data-ttu-id="30c1e-139">Linux</span><span class="sxs-lookup"><span data-stu-id="30c1e-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="30c1e-140">Cesta systému souborů:</span><span class="sxs-lookup"><span data-stu-id="30c1e-140">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="30c1e-141">V předchozím cesty k souborům, nahraďte `<user_secrets_id>` s `UserSecretsId` hodnotu zadanou v *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="30c1e-141">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="30c1e-142">Nemusíte psát kód, který závisí na umístění nebo formátu data uložená pomocí nástroje Správce tajný klíč.</span><span class="sxs-lookup"><span data-stu-id="30c1e-142">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="30c1e-143">Tyto podrobnosti implementace se může změnit.</span><span class="sxs-lookup"><span data-stu-id="30c1e-143">These implementation details may change.</span></span> <span data-ttu-id="30c1e-144">Například hodnoty tajných kódů nejsou zašifrované, ale může být v budoucnosti.</span><span class="sxs-lookup"><span data-stu-id="30c1e-144">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="30c1e-145">Nainstalujte nástroj tajný klíč správce</span><span class="sxs-lookup"><span data-stu-id="30c1e-145">Install the Secret Manager tool</span></span>

<span data-ttu-id="30c1e-146">Tajný klíč správce je jako součást balíčku s rozhraní příkazového řádku .NET Core v rozhraní .NET Core SDK 2.1.300 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="30c1e-146">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="30c1e-147">Pro .NET Core SDK verze před 2.1.300 je nutné instalaci nástroje.</span><span class="sxs-lookup"><span data-stu-id="30c1e-147">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="30c1e-148">Spustit `dotnet --version` z příkazové okno, chcete-li zobrazit číslo nainstalované verze .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="30c1e-148">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="30c1e-149">Pokud se používají sadu .NET Core SDK obsahuje nástroje, zobrazí se upozornění:</span><span class="sxs-lookup"><span data-stu-id="30c1e-149">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="30c1e-150">Nainstalujte [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) balíčku NuGet ve vašem projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30c1e-150">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="30c1e-151">Příklad:</span><span class="sxs-lookup"><span data-stu-id="30c1e-151">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="30c1e-152">Spuštěním následujícího příkazu v příkazovém prostředí se ověřit instalaci nástroje:</span><span class="sxs-lookup"><span data-stu-id="30c1e-152">Execute the following command in a command shell to validate the tool installation:</span></span>

```console
dotnet user-secrets -h
```

<span data-ttu-id="30c1e-153">Tajný klíč správce nástroj zobrazí ukázkové využití, možnosti a nápovědy k příkazům:</span><span class="sxs-lookup"><span data-stu-id="30c1e-153">The Secret Manager tool displays sample usage, options, and command help:</span></span>

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> <span data-ttu-id="30c1e-154">Musí být ve stejném adresáři jako *.csproj* spuštění nástroje, které jsou definovány v souboru *.csproj* souboru `DotNetCliToolReference` elementy.</span><span class="sxs-lookup"><span data-stu-id="30c1e-154">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="set-a-secret"></a><span data-ttu-id="30c1e-155">Nastavte tajného kódu</span><span class="sxs-lookup"><span data-stu-id="30c1e-155">Set a secret</span></span>

<span data-ttu-id="30c1e-156">Tajný klíč správce nástroj funguje v nastavení konfigurace specifické pro projekt uložené v profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="30c1e-156">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span> <span data-ttu-id="30c1e-157">Pro použití tajných kódů uživatelů, definovat `UserSecretsId` v elementu `PropertyGroup` z *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="30c1e-157">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="30c1e-158">Hodnota `UserSecretsId` je volitelný, ale jsou jedinečná pro projekt.</span><span class="sxs-lookup"><span data-stu-id="30c1e-158">The value of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="30c1e-159">Vývojáři obvykle generování identifikátoru GUID pro `UserSecretsId`.</span><span class="sxs-lookup"><span data-stu-id="30c1e-159">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="30c1e-160">V sadě Visual Studio, klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat tajné klíče uživatelů** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="30c1e-160">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="30c1e-161">Přidá tento gesta `UserSecretsId` prvek vyplní identifikátor GUID položky *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="30c1e-161">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span> <span data-ttu-id="30c1e-162">Visual Studio otevře *secrets.json* souboru v textovém editoru.</span><span class="sxs-lookup"><span data-stu-id="30c1e-162">Visual Studio opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="30c1e-163">Nahraďte obsah *secrets.json* s páry klíč hodnota, které mají být uloženy.</span><span class="sxs-lookup"><span data-stu-id="30c1e-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="30c1e-164">Příklad:</span><span class="sxs-lookup"><span data-stu-id="30c1e-164">For example:</span></span>
> ```json
> {
>   "Movies": {
>     "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
>     "ServiceApiKey": "12345"
>   }
> }
> ```
> <span data-ttu-id="30c1e-165">Struktura JSON se sloučí po změny prostřednictvím `dotnet user-secrets remove` nebo `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="30c1e-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="30c1e-166">Například systém `dotnet user-secrets remove "Movies:ConnectionString"` sbalí `Movies` literálu objektu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="30c1e-167">Upravený soubor vypadá přibližně takto:</span><span class="sxs-lookup"><span data-stu-id="30c1e-167">The modified file resembles the following:</span></span>
> ```json
> {
>   "Movies:ServiceApiKey": "12345"
> }
> ```

<span data-ttu-id="30c1e-168">Definujte tajný kód aplikace, který se skládá z klíče a jeho hodnotu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-168">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="30c1e-169">Tajný kód je přiřazena k projektu `UserSecretsId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-169">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="30c1e-170">Například spusťte následující příkaz z adresáře, ve kterém *.csproj* soubor existuje:</span><span class="sxs-lookup"><span data-stu-id="30c1e-170">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="30c1e-171">V předchozím příkladu, který označuje dvojtečka `Movies` je literál s objektu `ServiceApiKey` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="30c1e-171">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="30c1e-172">Nástroj tajný klíč správce je možné z jiných adresářů příliš.</span><span class="sxs-lookup"><span data-stu-id="30c1e-172">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="30c1e-173">Použití `--project` možnost zadat cestu systému souborů, ve kterém *.csproj* soubor existuje.</span><span class="sxs-lookup"><span data-stu-id="30c1e-173">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="30c1e-174">Příklad:</span><span class="sxs-lookup"><span data-stu-id="30c1e-174">For example:</span></span>

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="30c1e-175">Nastavit víc tajných kódů</span><span class="sxs-lookup"><span data-stu-id="30c1e-175">Set multiple secrets</span></span>

<span data-ttu-id="30c1e-176">Dávku tajné kódy je možné nastavit přesměrujete JSON na `set` příkazu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-176">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="30c1e-177">V následujícím příkladu *Input.JSON vypadá* obsah souboru jsou směrované do `set` příkazu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-177">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="30c1e-178">Windows</span><span class="sxs-lookup"><span data-stu-id="30c1e-178">Windows</span></span>](#tab/windows)

<span data-ttu-id="30c1e-179">Otevřete příkazové okno a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="30c1e-179">Open a command shell, and execute the following command:</span></span>

  ```console
  type .\input.json | dotnet user-secrets set
  ```

# <a name="macostabmacos"></a>[<span data-ttu-id="30c1e-180">macOS</span><span class="sxs-lookup"><span data-stu-id="30c1e-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="30c1e-181">Otevřete příkazové okno a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="30c1e-181">Open a command shell, and execute the following command:</span></span>

  ```console
  cat ./input.json | dotnet user-secrets set
  ```

# <a name="linuxtablinux"></a>[<span data-ttu-id="30c1e-182">Linux</span><span class="sxs-lookup"><span data-stu-id="30c1e-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="30c1e-183">Otevřete příkazové okno a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="30c1e-183">Open a command shell, and execute the following command:</span></span>

  ```console
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="30c1e-184">Přístup k tajným kódem</span><span class="sxs-lookup"><span data-stu-id="30c1e-184">Access a secret</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="30c1e-185">[Rozhraní API pro ASP.NET Core konfigurace](xref:fundamentals/configuration/index) poskytuje přístup k tajným kódům tajný klíč správce.</span><span class="sxs-lookup"><span data-stu-id="30c1e-185">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span> <span data-ttu-id="30c1e-186">Pokud váš projekt cílí na .NET Framework, nainstalujte [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="30c1e-186">If your project targets the .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="30c1e-187">V technologii ASP.NET Core 2.0 nebo novější, uživatelský zdroj konfigurace tajných kódů se automaticky přidá ve vývojovém režimu při volání projektu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> k inicializaci nové instance hostitele s předem nakonfigurované výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="30c1e-187">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="30c1e-188">`CreateDefaultBuilder` volání <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> při <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> je <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span><span class="sxs-lookup"><span data-stu-id="30c1e-188">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="30c1e-189">Když `CreateDefaultBuilder` není volána, přidat zdroj konfigurace tajných kódů uživatelů explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> v `Startup` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="30c1e-189">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor.</span></span> <span data-ttu-id="30c1e-190">Volání `AddUserSecrets` pouze při spuštění aplikace ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="30c1e-190">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="30c1e-191">[Rozhraní API pro ASP.NET Core konfigurace](xref:fundamentals/configuration/index) poskytuje přístup k tajným kódům tajný klíč správce.</span><span class="sxs-lookup"><span data-stu-id="30c1e-191">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span> <span data-ttu-id="30c1e-192">Nainstalujte [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="30c1e-192">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="30c1e-193">Přidat zdroj konfigurace tajných kódů uživatelů pomocí volání [AddUserSecrets](/dotnet/api/microsoft.extensions.configuration.usersecretsconfigurationextensions.addusersecrets) v `Startup` konstruktor:</span><span class="sxs-lookup"><span data-stu-id="30c1e-193">Add the user secrets configuration source with a call to [AddUserSecrets](/dotnet/api/microsoft.extensions.configuration.usersecretsconfigurationextensions.addusersecrets) in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="30c1e-194">Tajné klíče uživatelů se dá načíst pomocí `Configuration` rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="30c1e-194">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="30c1e-195">Mapování tajných kódů POCO</span><span class="sxs-lookup"><span data-stu-id="30c1e-195">Map secrets to a POCO</span></span>

<span data-ttu-id="30c1e-196">Mapování literál celého objektu POCO (jednoduchá třída .NET s vlastnostmi) je užitečné pro agregaci související vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="30c1e-196">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c1e-197">Chcete-li namapovat předchozí tajných kódů POCO, použijte `Configuration` rozhraní API [objektu vazby grafu](xref:fundamentals/configuration/index#bind-to-an-object-graph) funkce.</span><span class="sxs-lookup"><span data-stu-id="30c1e-197">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="30c1e-198">Následující kód vytvoří vazbu k vlastní `MovieSettings` POCO a přístupy `ServiceApiKey` hodnota vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="30c1e-198">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="30c1e-199">`Movies:ConnectionString` a `Movies:ServiceApiKey` tajných kódů se mapují na odpovídající vlastnosti v `MovieSettings`:</span><span class="sxs-lookup"><span data-stu-id="30c1e-199">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="30c1e-200">Náhradní řetězec s tajnými kódy</span><span class="sxs-lookup"><span data-stu-id="30c1e-200">String replacement with secrets</span></span>

<span data-ttu-id="30c1e-201">Není bezpečné ukládání hesel ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="30c1e-201">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="30c1e-202">Příklad připojovacího řetězce databáze uloženého v *appsettings.json* může zahrnovat heslo pro zadaného uživatele:</span><span class="sxs-lookup"><span data-stu-id="30c1e-202">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="30c1e-203">Je lépe zabezpečit přístup k uložení hesla jako tajný kód.</span><span class="sxs-lookup"><span data-stu-id="30c1e-203">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="30c1e-204">Příklad:</span><span class="sxs-lookup"><span data-stu-id="30c1e-204">For example:</span></span>

```console
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="30c1e-205">Odeberte `Password` páru klíč hodnota z připojovacího řetězce v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="30c1e-205">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="30c1e-206">Příklad:</span><span class="sxs-lookup"><span data-stu-id="30c1e-206">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="30c1e-207">Hodnota tajného klíče se dá nastavit na [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder) objektu [heslo](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.password) vlastnost dokončete připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="30c1e-207">The secret's value can be set on a [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder) object's [Password](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.password) property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="30c1e-208">Uvádí tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="30c1e-208">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c1e-209">Spusťte následující příkaz z adresáře, ve kterém *.csproj* soubor existuje:</span><span class="sxs-lookup"><span data-stu-id="30c1e-209">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```console
dotnet user-secrets list
```

<span data-ttu-id="30c1e-210">Zobrazí se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="30c1e-210">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="30c1e-211">V předchozím příkladu, dvojtečka v názvu klíče označuje hierarchie objektů v rámci *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="30c1e-211">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="30c1e-212">Odebrat tajný kód jednotného</span><span class="sxs-lookup"><span data-stu-id="30c1e-212">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c1e-213">Spusťte následující příkaz z adresáře, ve kterém *.csproj* soubor existuje:</span><span class="sxs-lookup"><span data-stu-id="30c1e-213">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```console
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="30c1e-214">Aplikace *secrets.json* změny odebrat páru klíč hodnota, které jsou přidružené k souboru `MoviesConnectionString` klíč:</span><span class="sxs-lookup"><span data-stu-id="30c1e-214">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="30c1e-215">Spuštění `dotnet user-secrets list` zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="30c1e-215">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="30c1e-216">Odebrání všech tajných kódů</span><span class="sxs-lookup"><span data-stu-id="30c1e-216">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="30c1e-217">Spusťte následující příkaz z adresáře, ve kterém *.csproj* soubor existuje:</span><span class="sxs-lookup"><span data-stu-id="30c1e-217">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```console
dotnet user-secrets clear
```

<span data-ttu-id="30c1e-218">Všechny tajné klíče uživatelů pro aplikaci se odstranily z *secrets.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="30c1e-218">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="30c1e-219">Spuštění `dotnet user-secrets list` zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="30c1e-219">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="30c1e-220">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="30c1e-220">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

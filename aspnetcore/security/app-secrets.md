---
title: Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core
author: rick-anderson
description: Naučte se ukládat a načítat citlivé informace jako tajné klíče aplikace při vývoji aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: ef5cb120c15d349be744c401bd518e026ddf11e9
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880773"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="0e5d9-103">Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e5d9-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="0e5d9-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Skořepa](https://github.com/danroth27)a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0e5d9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="0e5d9-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0e5d9-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0e5d9-106">Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-106">This document explains techniques for storing and retrieving sensitive data during the development of an ASP.NET Core app.</span></span> <span data-ttu-id="0e5d9-107">Nikdy neukládejte hesla nebo další citlivá data ve zdrojovém kódu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="0e5d9-108">Provozní tajemství by se neměla používat pro vývoj nebo testování.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="0e5d9-109">Tajné klíče by se neměly nasazovat spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="0e5d9-110">Místo toho by tajné klíče měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="0e5d9-111">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="0e5d9-111">Environment variables</span></span>

<span data-ttu-id="0e5d9-112">Proměnné prostředí se používají k tomu, aby nedocházelo k ukládání tajných klíčů k aplikacím v kódu nebo v místních konfiguračních souborech.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="0e5d9-113">Proměnné prostředí přepíšou konfigurační hodnoty pro všechny dříve zadané zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="0e5d9-114">Nakonfigurujte čtení hodnot proměnných prostředí voláním <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> v konstruktoru `Startup`:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-114">Configure the reading of environment variable values by calling <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="0e5d9-115">Vezměte v úvahu ASP.NET Core webovou aplikaci, ve které je povolené zabezpečení **individuálních uživatelských účtů** .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="0e5d9-116">Výchozí připojovací řetězec databáze je součástí souboru *appSettings. JSON* projektu s klíčem `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="0e5d9-117">Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="0e5d9-118">Během nasazování aplikace se hodnota `DefaultConnection` klíče dá přepsat hodnotou proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="0e5d9-119">Proměnná prostředí může ukládat úplný připojovací řetězec s citlivými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="0e5d9-120">Proměnné prostředí se většinou ukládají v prostém, nešifrovaném textu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="0e5d9-121">Pokud dojde k ohrožení bezpečnosti počítače nebo procesu, jsou proměnné prostředí dostupné nedůvěryhodnými stranami.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="0e5d9-122">Další opatření, která zabraňují odhalení tajných kódů uživateli, mohou být povinná.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="0e5d9-123">Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="0e5d9-123">Secret Manager</span></span>

<span data-ttu-id="0e5d9-124">Nástroj Správce tajných klíčů ukládá citlivé údaje během vývoje ASP.NET Core projektu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="0e5d9-125">V tomto kontextu je část citlivých dat tajný klíč aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="0e5d9-126">Tajné klíče aplikace se ukládají v odděleném umístění ze stromu projektu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="0e5d9-127">Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny napříč několika projekty.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="0e5d9-128">Tajné kódy aplikace nejsou zaregistrovány do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="0e5d9-129">Nástroj Správce tajných klíčů nešifruje uložené tajné klíče a neměl by být považován za důvěryhodné úložiště.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="0e5d9-130">Pouze pro účely vývoje.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-130">It's for development purposes only.</span></span> <span data-ttu-id="0e5d9-131">Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="0e5d9-132">Jak funguje nástroj Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="0e5d9-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="0e5d9-133">Nástroj Správce tajných klíčů vyříznout podrobnosti implementace, například WHERE a způsob uložení hodnot.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-133">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="0e5d9-134">Tento nástroj můžete použít bez znalosti těchto podrobností implementace.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="0e5d9-135">Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-135">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0e5d9-136">Windows</span><span class="sxs-lookup"><span data-stu-id="0e5d9-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="0e5d9-137">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="0e5d9-138">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="0e5d9-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="0e5d9-139">Cesta k systému souborů:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="0e5d9-140">V předchozích cestách souborů nahraďte `<user_secrets_id>` hodnotou `UserSecretsId` zadanou v souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="0e5d9-141">Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="0e5d9-142">Tyto podrobnosti implementace se můžou změnit.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-142">These implementation details may change.</span></span> <span data-ttu-id="0e5d9-143">Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="0e5d9-144">Instalace nástroje Správce tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="0e5d9-144">Install the Secret Manager tool</span></span>

<span data-ttu-id="0e5d9-145">Nástroj Správce tajných klíčů je součástí .NET Core CLI v .NET Core SDK 2.1.300 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-145">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="0e5d9-146">Pro .NET Core SDK verze před 2.1.300 je instalace nástroje nutná.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-146">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="0e5d9-147">Pokud chcete zobrazit nainstalované číslo verze .NET Core SDK, spusťte `dotnet --version` z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-147">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="0e5d9-148">Zobrazí se upozornění, pokud .NET Core SDK používá nástroj:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-148">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="0e5d9-149">Do projektu ASP.NET Core nainstalujte balíček NuGet [Microsoft. Extensions. SecretManager. Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-149">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="0e5d9-150">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-150">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="0e5d9-151">Spuštěním následujícího příkazu v příkazovém prostředí Ověřte instalaci nástroje:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-151">Execute the following command in a command shell to validate the tool installation:</span></span>

```dotnetcli
dotnet user-secrets -h
```

<span data-ttu-id="0e5d9-152">Nástroj Správce tajných klíčů zobrazuje ukázkové použití, možnosti a nápovědu k příkazům:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-152">The Secret Manager tool displays sample usage, options, and command help:</span></span>

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
> <span data-ttu-id="0e5d9-153">Musíte být ve stejném adresáři jako soubor *. csproj* ke spuštění nástrojů definovaných v `DotNetCliToolReference` prvky souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-153">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="enable-secret-storage"></a><span data-ttu-id="0e5d9-154">Povolit tajné úložiště</span><span class="sxs-lookup"><span data-stu-id="0e5d9-154">Enable secret storage</span></span>

<span data-ttu-id="0e5d9-155">Nástroj Správce tajných klíčů pracuje na nastavení konfigurace specifické pro projekt uložené v profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-155">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0e5d9-156">Nástroj Správce tajných klíčů obsahuje příkaz `init` v .NET Core SDK 3.0.100 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-156">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="0e5d9-157">Chcete-li použít tajné klíče uživatele, spusťte následující příkaz v adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-157">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="0e5d9-158">Předchozí příkaz přidá prvek `UserSecretsId` v rámci `PropertyGroup` souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-158">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="0e5d9-159">Ve výchozím nastavení je vnitřní text `UserSecretsId` identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-159">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="0e5d9-160">Vnitřní text je libovolný, ale je pro projekt jedinečný.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-160">The inner text is arbitrary, but is unique to the project.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="0e5d9-161">Pro použití uživatelských tajných klíčů definujte `UserSecretsId` element v `PropertyGroup` souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-161">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="0e5d9-162">Vnitřní text `UserSecretsId` je libovolný, ale je pro projekt jedinečný.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-162">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="0e5d9-163">Vývojáři obvykle generují identifikátor GUID pro `UserSecretsId`.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-163">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="0e5d9-164">V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumník řešení a v místní nabídce vyberte možnost **spravovat tajné klíče uživatele** .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-164">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="0e5d9-165">Tento gesto Přidá element `UserSecretsId`, který je vyplněný identifikátorem GUID, do souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-165">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="0e5d9-166">Nastavení tajného klíče</span><span class="sxs-lookup"><span data-stu-id="0e5d9-166">Set a secret</span></span>

<span data-ttu-id="0e5d9-167">Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-167">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="0e5d9-168">Tajný kód je přidružený k hodnotě `UserSecretsId` projektu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-168">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="0e5d9-169">Například spusťte následující příkaz z adresáře, ve kterém existuje soubor *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="0e5d9-169">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="0e5d9-170">V předchozím příkladu dvojtečka označuje, že `Movies` je literál objektu s vlastností `ServiceApiKey`.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-170">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="0e5d9-171">Nástroj Správce tajných klíčů je možné použít i v jiných adresářích.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-171">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="0e5d9-172">Pomocí možnosti `--project` zadejte cestu k systému souborů, na které existuje soubor *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-172">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="0e5d9-173">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-173">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="0e5d9-174">Sloučení struktury JSON v aplikaci Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e5d9-174">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="0e5d9-175">Gesto **Spravovat uživatelské tajemství** v aplikaci Visual Studio otevře v textovém editoru soubor *tajných kódů. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-175">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="0e5d9-176">Nahraďte obsah *tajných klíčů. JSON* páry klíč-hodnota, které se mají uložit.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-176">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="0e5d9-177">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-177">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="0e5d9-178">Po úpravách prostřednictvím `dotnet user-secrets remove` nebo `dotnet user-secrets set`se struktura JSON sloučí.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-178">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="0e5d9-179">Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí literál `Movies` objektu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-179">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="0e5d9-180">Změněný soubor se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-180">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="0e5d9-181">Nastavení více tajných klíčů</span><span class="sxs-lookup"><span data-stu-id="0e5d9-181">Set multiple secrets</span></span>

<span data-ttu-id="0e5d9-182">Dávku tajných kódů lze nastavit pomocí příkazu pro vytvoření kódu JSON na příkaz `set`.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-182">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="0e5d9-183">V následujícím příkladu je obsah souboru *input. JSON* v kanálu `set` příkazu.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-183">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0e5d9-184">Windows</span><span class="sxs-lookup"><span data-stu-id="0e5d9-184">Windows</span></span>](#tab/windows)

<span data-ttu-id="0e5d9-185">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-185">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="0e5d9-186">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="0e5d9-186">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="0e5d9-187">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-187">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="0e5d9-188">Přístup ke tajnému kódu</span><span class="sxs-lookup"><span data-stu-id="0e5d9-188">Access a secret</span></span>

<span data-ttu-id="0e5d9-189">[Rozhraní API pro konfiguraci ASP.NET Core](xref:fundamentals/configuration/index) poskytuje přístup ke tajným tajným klíčům správce.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-189">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="0e5d9-190">Pokud je projekt cílen .NET Framework, nainstalujte balíček NuGet [Microsoft. Extensions. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-190">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0e5d9-191">V ASP.NET Core 2,0 nebo novějším je zdroj konfigurace uživatelských tajných kódů automaticky přidán do režimu vývoje, pokud projekt volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> k inicializaci nové instance hostitele s předem konfigurovanými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-191">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="0e5d9-192">`CreateDefaultBuilder` volá <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>, když je <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-192">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0e5d9-193">Pokud `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace uživatelských tajných klíčů explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> v konstruktoru `Startup`.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-193">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="0e5d9-194">Volejte `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-194">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="0e5d9-195">Nainstalujte balíček NuGet [Microsoft. Extensions. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="0e5d9-195">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="0e5d9-196">Přidejte zdroj konfigurace uživatelských tajných klíčů s voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> v konstruktoru `Startup`:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-196">Add the user secrets configuration source with a call to <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="0e5d9-197">Tajné klíče uživatele lze načíst prostřednictvím rozhraní `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-197">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="0e5d9-198">Mapování tajných kódů na POCO</span><span class="sxs-lookup"><span data-stu-id="0e5d9-198">Map secrets to a POCO</span></span>

<span data-ttu-id="0e5d9-199">Mapování celého literálu objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečná pro agregaci souvisejících vlastností.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-199">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="0e5d9-200">K namapování předchozích tajných kódů na POCO použijte funkci [vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-200">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="0e5d9-201">Následující kód se váže k vlastnímu `MovieSettings` POCO a přistupuje k hodnotě vlastnosti `ServiceApiKey`:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-201">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="0e5d9-202">Tajné kódy `Movies:ConnectionString` a `Movies:ServiceApiKey` jsou mapovány na příslušné vlastnosti v `MovieSettings`:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-202">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="0e5d9-203">Výměna řetězců s tajnými kódy</span><span class="sxs-lookup"><span data-stu-id="0e5d9-203">String replacement with secrets</span></span>

<span data-ttu-id="0e5d9-204">Ukládání hesel v prostém textu je nezabezpečené.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-204">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="0e5d9-205">Připojovací řetězec databáze uložený v souboru *appSettings. JSON* může například zahrnovat heslo pro zadaného uživatele:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-205">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="0e5d9-206">Bezpečnější je ukládání hesla jako tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-206">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="0e5d9-207">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-207">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="0e5d9-208">Odeberte dvojici klíč-hodnota `Password` z připojovacího řetězce v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-208">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="0e5d9-209">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-209">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="0e5d9-210">Hodnota tajného klíče může být nastavena na vlastnost <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> objektu <xref:System.Data.SqlClient.SqlConnectionStringBuilder> k dokončení připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-210">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="0e5d9-211">Výpis tajných kódů</span><span class="sxs-lookup"><span data-stu-id="0e5d9-211">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="0e5d9-212">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-212">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="0e5d9-213">Zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-213">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="0e5d9-214">V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *tajných kódů. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0e5d9-214">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="0e5d9-215">Odebrání jednoho tajného klíče</span><span class="sxs-lookup"><span data-stu-id="0e5d9-215">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="0e5d9-216">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-216">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="0e5d9-217">Soubor *tajných kódů. JSON* aplikace byl změněn tak, aby odstranil dvojici klíč-hodnota přidruženou ke klíči `MoviesConnectionString`:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-217">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="0e5d9-218">Spuštění `dotnet user-secrets list` zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-218">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="0e5d9-219">Odebrat všechna tajná klíčová</span><span class="sxs-lookup"><span data-stu-id="0e5d9-219">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="0e5d9-220">Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-220">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="0e5d9-221">Všechny tajné klíče uživatele pro aplikaci byly odstraněny ze souboru *tajných kódů. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0e5d9-221">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="0e5d9-222">Spuštění `dotnet user-secrets list` zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="0e5d9-222">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="0e5d9-223">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="0e5d9-223">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

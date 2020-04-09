---
title: Použití cli LibMan s ASP.NET jádrem
author: scottaddie
description: Přečtěte si, jak používat cli LibMan v projektu ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664631"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="3f86d-103">Použití cli LibMan s ASP.NET jádrem</span><span class="sxs-lookup"><span data-stu-id="3f86d-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="3f86d-104">Podle [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3f86d-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="3f86d-105">[LibMan](xref:client-side/libman/index) CLI je nástroj pro různé platformy, který je podporován všude .</span><span class="sxs-lookup"><span data-stu-id="3f86d-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3f86d-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3f86d-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="3f86d-107">Instalace</span><span class="sxs-lookup"><span data-stu-id="3f86d-107">Installation</span></span>

<span data-ttu-id="3f86d-108">Instalace cli LibMan:</span><span class="sxs-lookup"><span data-stu-id="3f86d-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="3f86d-109">[Globální nástroj .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) je nainstalován z balíčku [Microsoft.Web.LibraryManagerManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="3f86d-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="3f86d-110">Instalace cli LibMan z konkrétního zdroje balíčku NuGet:</span><span class="sxs-lookup"><span data-stu-id="3f86d-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="3f86d-111">V předchozím příkladu je globální nástroj .NET Core nainstalován ze souboru *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg.*</span><span class="sxs-lookup"><span data-stu-id="3f86d-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="3f86d-112">Využití</span><span class="sxs-lookup"><span data-stu-id="3f86d-112">Usage</span></span>

<span data-ttu-id="3f86d-113">Po úspěšné instalaci příkazového příkazu lze použít následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3f86d-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="3f86d-114">Zobrazení nainstalované verze cli:</span><span class="sxs-lookup"><span data-stu-id="3f86d-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="3f86d-115">Zobrazení dostupných příkazů rozhraní příkazového příkazu:</span><span class="sxs-lookup"><span data-stu-id="3f86d-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="3f86d-116">Předchozí příkaz zobrazí výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3f86d-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="3f86d-117">V následujících částech jsou uvedeny dostupné příkazy příkazu příkazu příkazu k příkazu.</span><span class="sxs-lookup"><span data-stu-id="3f86d-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="3f86d-118">Inicializovat LibManv rámci projektu</span><span class="sxs-lookup"><span data-stu-id="3f86d-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="3f86d-119">Příkaz `libman init` vytvoří soubor *libman.json,* pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="3f86d-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="3f86d-120">Soubor je vytvořen s výchozím obsahem šablony položky.</span><span class="sxs-lookup"><span data-stu-id="3f86d-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3f86d-121">Synopse</span><span class="sxs-lookup"><span data-stu-id="3f86d-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3f86d-122">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3f86d-122">Options</span></span>

<span data-ttu-id="3f86d-123">Pro příkaz jsou k `libman init` dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3f86d-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="3f86d-124">Cesta vzhledem k aktuální složce.</span><span class="sxs-lookup"><span data-stu-id="3f86d-124">A path relative to the current folder.</span></span> <span data-ttu-id="3f86d-125">Soubory knihovny jsou nainstalovány `destination` v tomto umístění, pokud není definována žádná vlastnost pro knihovnu v *souboru libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3f86d-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="3f86d-126">Hodnota `<PATH>` je zapsána `defaultDestination` do vlastnosti *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3f86d-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="3f86d-127">Zprostředkovatel použít, pokud žádný zprostředkovatel je definován pro danou knihovnu.</span><span class="sxs-lookup"><span data-stu-id="3f86d-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="3f86d-128">Hodnota `<PROVIDER>` je zapsána `defaultProvider` do vlastnosti *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3f86d-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="3f86d-129">Nahraďte `<PROVIDER>` se jednou z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="3f86d-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3f86d-130">Příklady</span><span class="sxs-lookup"><span data-stu-id="3f86d-130">Examples</span></span>

<span data-ttu-id="3f86d-131">Vytvoření souboru *libman.json* v projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3f86d-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="3f86d-132">Přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="3f86d-132">Navigate to the project root.</span></span>
* <span data-ttu-id="3f86d-133">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3f86d-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="3f86d-134">Zadejte název výchozího zprostředkovatele `Enter` nebo stisknutím klávesy použijte výchozího zprostředkovatele CDNJS.</span><span class="sxs-lookup"><span data-stu-id="3f86d-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="3f86d-135">Mezi platné hodnoty patří:</span><span class="sxs-lookup"><span data-stu-id="3f86d-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init, příkaz - výchozí zprostředkovatel](_static/libman-init-provider.png)

<span data-ttu-id="3f86d-137">Do kořenového adresáře projektu je přidán soubor *libman.json* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="3f86d-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="3f86d-138">Přidání souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="3f86d-138">Add library files</span></span>

<span data-ttu-id="3f86d-139">Příkaz `libman install` stáhne a nainstaluje soubory knihovny do projektu.</span><span class="sxs-lookup"><span data-stu-id="3f86d-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="3f86d-140">Pokud soubor libman.json neexistuje, je přidán soubor *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="3f86d-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="3f86d-141">Soubor *libman.json* je upraven tak, aby ukládaly podrobnosti konfigurace pro soubory knihovny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3f86d-142">Synopse</span><span class="sxs-lookup"><span data-stu-id="3f86d-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3f86d-143">Argumenty</span><span class="sxs-lookup"><span data-stu-id="3f86d-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3f86d-144">Název knihovny k instalaci.</span><span class="sxs-lookup"><span data-stu-id="3f86d-144">The name of the library to install.</span></span> <span data-ttu-id="3f86d-145">Tento název může obsahovat zápis čísla `@1.2.0`verze (například ).</span><span class="sxs-lookup"><span data-stu-id="3f86d-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3f86d-146">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3f86d-146">Options</span></span>

<span data-ttu-id="3f86d-147">Pro příkaz jsou k `libman install` dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3f86d-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="3f86d-148">Umístění pro instalaci knihovny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-148">The location to install the library.</span></span> <span data-ttu-id="3f86d-149">Pokud není zadán, použije se výchozí umístění.</span><span class="sxs-lookup"><span data-stu-id="3f86d-149">If not specified, the default location is used.</span></span> <span data-ttu-id="3f86d-150">Pokud `defaultDestination` v *souboru libman.json*není zadána žádná vlastnost , je tato možnost vyžadována.</span><span class="sxs-lookup"><span data-stu-id="3f86d-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="3f86d-151">Zadejte název souboru, který chcete nainstalovat z knihovny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="3f86d-152">Pokud není zadán, jsou nainstalovány všechny soubory z knihovny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="3f86d-153">Zadejte `--files` jednu možnost pro každý soubor, který má být nainstalován.</span><span class="sxs-lookup"><span data-stu-id="3f86d-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="3f86d-154">Relativní cesty jsou také podporovány.</span><span class="sxs-lookup"><span data-stu-id="3f86d-154">Relative paths are supported too.</span></span> <span data-ttu-id="3f86d-155">Například: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="3f86d-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="3f86d-156">Název zprostředkovatele, který má být používán pro získání knihovny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="3f86d-157">Nahraďte `<PROVIDER>` se jednou z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="3f86d-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="3f86d-158">Pokud není zadán, `defaultProvider` vlastnost v *libman.json* se používá.</span><span class="sxs-lookup"><span data-stu-id="3f86d-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="3f86d-159">Pokud `defaultProvider` v *souboru libman.json*není zadána žádná vlastnost , je tato možnost vyžadována.</span><span class="sxs-lookup"><span data-stu-id="3f86d-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3f86d-160">Příklady</span><span class="sxs-lookup"><span data-stu-id="3f86d-160">Examples</span></span>

<span data-ttu-id="3f86d-161">Zvažte následující soubor *libman.json:*</span><span class="sxs-lookup"><span data-stu-id="3f86d-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="3f86d-162">Chcete-li nainstalovat soubor jQuery verze 3.2.1 *jquery.min.js* do složky *wwwroot/scripts/jquery* pomocí zprostředkovatele CDNJS:</span><span class="sxs-lookup"><span data-stu-id="3f86d-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="3f86d-163">Soubor *libman.json* se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3f86d-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="3f86d-164">Instalace souborů *calendar.js* a *calendar.css* z \*c:\\temp\\contosoCalendar\\ \* pomocí zprostředkovatele systému souborů:</span><span class="sxs-lookup"><span data-stu-id="3f86d-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="3f86d-165">Následující výzva se zobrazí ze dvou důvodů:</span><span class="sxs-lookup"><span data-stu-id="3f86d-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="3f86d-166">Soubor *libman.json* neobsahuje `defaultDestination` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="3f86d-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="3f86d-167">Příkaz `libman install` tuto `-d|--destination` možnost neobsahuje.</span><span class="sxs-lookup"><span data-stu-id="3f86d-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman nainstalovat příkaz - cíl](_static/libman-install-destination.png)

<span data-ttu-id="3f86d-169">Po přijetí výchozího cíle se soubor *libman.json* podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3f86d-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="3f86d-170">Obnovení souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="3f86d-170">Restore library files</span></span>

<span data-ttu-id="3f86d-171">Příkaz `libman restore` nainstaluje soubory knihovny definované v *souboru libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3f86d-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="3f86d-172">Platí následující pravidla:</span><span class="sxs-lookup"><span data-stu-id="3f86d-172">The following rules apply:</span></span>

* <span data-ttu-id="3f86d-173">Pokud v kořenovém adresáři projektu neexistuje žádný soubor *libman.json,* je vrácena chyba.</span><span class="sxs-lookup"><span data-stu-id="3f86d-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="3f86d-174">Pokud knihovna určuje zprostředkovatele, vlastnost v `defaultProvider` *souboru libman.json* je ignorována.</span><span class="sxs-lookup"><span data-stu-id="3f86d-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="3f86d-175">Pokud knihovna určuje cíl, `defaultDestination` vlastnost v *souboru libman.json* je ignorována.</span><span class="sxs-lookup"><span data-stu-id="3f86d-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3f86d-176">Synopse</span><span class="sxs-lookup"><span data-stu-id="3f86d-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3f86d-177">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3f86d-177">Options</span></span>

<span data-ttu-id="3f86d-178">Pro příkaz jsou k `libman restore` dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3f86d-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3f86d-179">Příklady</span><span class="sxs-lookup"><span data-stu-id="3f86d-179">Examples</span></span>

<span data-ttu-id="3f86d-180">Obnovení souborů knihovny definovaných v *souboru libman.json*:</span><span class="sxs-lookup"><span data-stu-id="3f86d-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="3f86d-181">Odstranění souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="3f86d-181">Delete library files</span></span>

<span data-ttu-id="3f86d-182">Příkaz `libman clean` odstraní soubory knihovny dříve obnovené přes LibMan.</span><span class="sxs-lookup"><span data-stu-id="3f86d-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="3f86d-183">Složky, které se po této operaci vyprázdní, budou odstraněny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="3f86d-184">Přidružené konfigurace souborů knihovny `libraries` ve vlastnosti *libman.json nebudou odebrány.*</span><span class="sxs-lookup"><span data-stu-id="3f86d-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3f86d-185">Synopse</span><span class="sxs-lookup"><span data-stu-id="3f86d-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3f86d-186">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3f86d-186">Options</span></span>

<span data-ttu-id="3f86d-187">Pro příkaz jsou k `libman clean` dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3f86d-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3f86d-188">Příklady</span><span class="sxs-lookup"><span data-stu-id="3f86d-188">Examples</span></span>

<span data-ttu-id="3f86d-189">Odstranění souborů knihovny nainstalovaných přes LibMan:</span><span class="sxs-lookup"><span data-stu-id="3f86d-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="3f86d-190">Odinstalace souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="3f86d-190">Uninstall library files</span></span>

<span data-ttu-id="3f86d-191">Příkaz `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="3f86d-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="3f86d-192">Odstraní všechny soubory přidružené k zadané knihovně z cíle v *souboru libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3f86d-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="3f86d-193">Odebere přidruženou konfiguraci knihovny z *souboru libman.json*.</span><span class="sxs-lookup"><span data-stu-id="3f86d-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="3f86d-194">K chybě dochází, když:</span><span class="sxs-lookup"><span data-stu-id="3f86d-194">An error occurs when:</span></span>

* <span data-ttu-id="3f86d-195">V kořenovém adresáři projektu neexistuje žádný soubor *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="3f86d-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3f86d-196">Zadaná knihovna neexistuje.</span><span class="sxs-lookup"><span data-stu-id="3f86d-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="3f86d-197">Pokud je nainstalována více než jedna knihovna se stejným názvem, budete vyzváni k výběru jedné.</span><span class="sxs-lookup"><span data-stu-id="3f86d-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3f86d-198">Synopse</span><span class="sxs-lookup"><span data-stu-id="3f86d-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3f86d-199">Argumenty</span><span class="sxs-lookup"><span data-stu-id="3f86d-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3f86d-200">Název knihovny, kterou chcete odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="3f86d-200">The name of the library to uninstall.</span></span> <span data-ttu-id="3f86d-201">Tento název může obsahovat zápis čísla `@1.2.0`verze (například ).</span><span class="sxs-lookup"><span data-stu-id="3f86d-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3f86d-202">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3f86d-202">Options</span></span>

<span data-ttu-id="3f86d-203">Pro příkaz jsou k `libman uninstall` dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3f86d-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3f86d-204">Příklady</span><span class="sxs-lookup"><span data-stu-id="3f86d-204">Examples</span></span>

<span data-ttu-id="3f86d-205">Zvažte následující soubor *libman.json:*</span><span class="sxs-lookup"><span data-stu-id="3f86d-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="3f86d-206">Chcete-li odinstalovat jQuery, jeden z následujících příkazů úspěšné:</span><span class="sxs-lookup"><span data-stu-id="3f86d-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="3f86d-207">Chcete-li odinstalovat soubory Lodash nainstalované prostřednictvím `filesystem` poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="3f86d-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="3f86d-208">Aktualizovat verzi knihovny</span><span class="sxs-lookup"><span data-stu-id="3f86d-208">Update library version</span></span>

<span data-ttu-id="3f86d-209">Příkaz `libman update` aktualizuje knihovnu nainstalovanou přes LibMan na zadanou verzi.</span><span class="sxs-lookup"><span data-stu-id="3f86d-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="3f86d-210">K chybě dochází, když:</span><span class="sxs-lookup"><span data-stu-id="3f86d-210">An error occurs when:</span></span>

* <span data-ttu-id="3f86d-211">V kořenovém adresáři projektu neexistuje žádný soubor *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="3f86d-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3f86d-212">Zadaná knihovna neexistuje.</span><span class="sxs-lookup"><span data-stu-id="3f86d-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="3f86d-213">Pokud je nainstalována více než jedna knihovna se stejným názvem, budete vyzváni k výběru jedné.</span><span class="sxs-lookup"><span data-stu-id="3f86d-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3f86d-214">Synopse</span><span class="sxs-lookup"><span data-stu-id="3f86d-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3f86d-215">Argumenty</span><span class="sxs-lookup"><span data-stu-id="3f86d-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3f86d-216">Název knihovny aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="3f86d-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="3f86d-217">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3f86d-217">Options</span></span>

<span data-ttu-id="3f86d-218">Pro příkaz jsou k `libman update` dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3f86d-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="3f86d-219">Získejte nejnovější předběžnou verzi knihovny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="3f86d-220">Získejte konkrétní verzi knihovny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3f86d-221">Příklady</span><span class="sxs-lookup"><span data-stu-id="3f86d-221">Examples</span></span>

* <span data-ttu-id="3f86d-222">Aktualizace aplikace jQuery na nejnovější verzi:</span><span class="sxs-lookup"><span data-stu-id="3f86d-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="3f86d-223">Aktualizace aplikace jQuery na verzi 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="3f86d-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="3f86d-224">Aktualizace aplikace jQuery na nejnovější předběžnou verzi:</span><span class="sxs-lookup"><span data-stu-id="3f86d-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="3f86d-225">Správa mezipaměti knihovny</span><span class="sxs-lookup"><span data-stu-id="3f86d-225">Manage library cache</span></span>

<span data-ttu-id="3f86d-226">Příkaz `libman cache` spravuje mezipaměť knihovny LibMan.</span><span class="sxs-lookup"><span data-stu-id="3f86d-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="3f86d-227">Zprostředkovatel `filesystem` nepoužívá mezipaměť knihovny.</span><span class="sxs-lookup"><span data-stu-id="3f86d-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3f86d-228">Synopse</span><span class="sxs-lookup"><span data-stu-id="3f86d-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3f86d-229">Argumenty</span><span class="sxs-lookup"><span data-stu-id="3f86d-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="3f86d-230">Používá se `clean` pouze s příkazem.</span><span class="sxs-lookup"><span data-stu-id="3f86d-230">Only used with the `clean` command.</span></span> <span data-ttu-id="3f86d-231">Určuje mezipaměť zprostředkovatele, která má být vyčištěna.</span><span class="sxs-lookup"><span data-stu-id="3f86d-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="3f86d-232">Mezi platné hodnoty patří:</span><span class="sxs-lookup"><span data-stu-id="3f86d-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="3f86d-233">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3f86d-233">Options</span></span>

<span data-ttu-id="3f86d-234">Pro příkaz jsou k `libman cache` dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="3f86d-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="3f86d-235">Seznam názvů souborů, které jsou uloženy do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="3f86d-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="3f86d-236">Seznam názvů knihoven, které jsou uloženy v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="3f86d-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3f86d-237">Příklady</span><span class="sxs-lookup"><span data-stu-id="3f86d-237">Examples</span></span>

* <span data-ttu-id="3f86d-238">Chcete-li zobrazit názvy knihoven uložených v mezipaměti podle zprostředkovatele, použijte jeden z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="3f86d-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="3f86d-239">Zobrazí se výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3f86d-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="3f86d-240">Chcete-li zobrazit názvy souborů knihovny uložených v mezipaměti podle zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="3f86d-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="3f86d-241">Zobrazí se výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3f86d-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="3f86d-242">Všimněte si, že předchozí výstup ukazuje, že jQuery verze 3.2.1 a 3.3.1 jsou uloženy do mezipaměti pod zprostředkovatelem CDNJS.</span><span class="sxs-lookup"><span data-stu-id="3f86d-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="3f86d-243">Vyprázdnění mezipaměti knihovny pro zprostředkovatele CDNJS:</span><span class="sxs-lookup"><span data-stu-id="3f86d-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="3f86d-244">Po vyprázdnění mezipaměti `libman cache list` zprostředkovatele CDNJS se v příkazu zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="3f86d-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="3f86d-245">Vyprázdnění mezipaměti pro všechny podporované zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="3f86d-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="3f86d-246">Po vyprázdnění všech `libman cache list` mezipamětí zprostředkovatele se v příkazu zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="3f86d-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="3f86d-247">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3f86d-247">Additional resources</span></span>

* [<span data-ttu-id="3f86d-248">Instalace globálního nástroje</span><span class="sxs-lookup"><span data-stu-id="3f86d-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="3f86d-249">Úložiště LibMan GitHub</span><span class="sxs-lookup"><span data-stu-id="3f86d-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)

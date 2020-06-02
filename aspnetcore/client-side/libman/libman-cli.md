---
title: Použití rozhraní příkazového řádku LibMan s ASP.NET Core
author: scottaddie
description: Naučte se používat rozhraní příkazového řádku LibMan v projektu ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 1a42d162e28d4bb4cce284b8b5e37f1be6ff64c6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770549"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="3d8b3-103">Použití rozhraní příkazového řádku LibMan s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d8b3-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="3d8b3-104">[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3d8b3-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="3d8b3-105">[LibMan](xref:client-side/libman/index) CLI je nástroj pro různé platformy, který podporuje všude, kde je podporováno rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3d8b3-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3d8b3-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="3d8b3-107">Instalace</span><span class="sxs-lookup"><span data-stu-id="3d8b3-107">Installation</span></span>

<span data-ttu-id="3d8b3-108">Instalace rozhraní příkazového řádku LibMan:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="3d8b3-109">[Globální nástroj .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) je nainstalovaný z balíčku NuGet [Microsoft. Web. LibraryManager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .</span><span class="sxs-lookup"><span data-stu-id="3d8b3-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="3d8b3-110">Instalace rozhraní příkazového řádku LibMan z konkrétního zdroje balíčku NuGet:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="3d8b3-111">V předchozím příkladu je globální nástroj .NET Core nainstalovaný ze souboru *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* místního počítače s Windows.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="3d8b3-112">Využití</span><span class="sxs-lookup"><span data-stu-id="3d8b3-112">Usage</span></span>

<span data-ttu-id="3d8b3-113">Po úspěšné instalaci rozhraní příkazového řádku je možné použít následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="3d8b3-114">Chcete-li zobrazit nainstalovanou verzi rozhraní příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="3d8b3-115">Postup zobrazení dostupných příkazů rozhraní příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="3d8b3-116">Předchozí příkaz zobrazí výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="3d8b3-117">Následující části popisují dostupné příkazy rozhraní příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="3d8b3-118">Inicializovat LibMan v projektu</span><span class="sxs-lookup"><span data-stu-id="3d8b3-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="3d8b3-119">`libman init`Příkaz vytvoří soubor *Libman. JSON* , pokud jeden neexistuje.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="3d8b3-120">Vytvoří se soubor s výchozím obsahem šablony položky.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3d8b3-121">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="3d8b3-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3d8b3-122">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3d8b3-122">Options</span></span>

<span data-ttu-id="3d8b3-123">Pro příkaz jsou k dispozici následující možnosti `libman init` :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="3d8b3-124">Cesta relativní k aktuální složce</span><span class="sxs-lookup"><span data-stu-id="3d8b3-124">A path relative to the current folder.</span></span> <span data-ttu-id="3d8b3-125">Soubory knihovny jsou nainstalovány v tomto umístění, pokud `destination` není definována žádná vlastnost pro knihovnu v souboru *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="3d8b3-126">`<PATH>`Hodnota je zapsána do `defaultDestination` vlastnosti *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="3d8b3-127">Poskytovatel, který má být použit, pokud pro danou knihovnu není definován žádný zprostředkovatel.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="3d8b3-128">`<PROVIDER>`Hodnota je zapsána do `defaultProvider` vlastnosti *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="3d8b3-129">Nahraďte `<PROVIDER>` některou z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3d8b3-130">Příklady</span><span class="sxs-lookup"><span data-stu-id="3d8b3-130">Examples</span></span>

<span data-ttu-id="3d8b3-131">Vytvoření souboru *Libman. JSON* v projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="3d8b3-132">Přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-132">Navigate to the project root.</span></span>
* <span data-ttu-id="3d8b3-133">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="3d8b3-134">Zadejte název výchozího poskytovatele, nebo stiskněte klávesu `Enter` a použijte výchozího poskytovatele CDNJS.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="3d8b3-135">Platné hodnoty zahrnují:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![příkaz Libman init – výchozí zprostředkovatel](_static/libman-init-provider.png)

<span data-ttu-id="3d8b3-137">Do kořenu projektu se přidá soubor *Libman. JSON* s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="3d8b3-138">Přidat soubory knihovny</span><span class="sxs-lookup"><span data-stu-id="3d8b3-138">Add library files</span></span>

<span data-ttu-id="3d8b3-139">`libman install`Příkaz stáhne a nainstaluje soubory knihovny do projektu.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="3d8b3-140">Pokud jeden soubor *Libman. JSON* neexistuje, přidá se.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="3d8b3-141">Soubor *Libman. JSON* se upraví tak, aby se uložily podrobnosti o konfiguraci souborů knihovny.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3d8b3-142">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="3d8b3-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3d8b3-143">Arguments</span><span class="sxs-lookup"><span data-stu-id="3d8b3-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3d8b3-144">Název knihovny, která se má nainstalovat</span><span class="sxs-lookup"><span data-stu-id="3d8b3-144">The name of the library to install.</span></span> <span data-ttu-id="3d8b3-145">Tento název může obsahovat zápis čísla verze (například `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="3d8b3-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3d8b3-146">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3d8b3-146">Options</span></span>

<span data-ttu-id="3d8b3-147">Pro příkaz jsou k dispozici následující možnosti `libman install` :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="3d8b3-148">Umístění pro instalaci knihovny.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-148">The location to install the library.</span></span> <span data-ttu-id="3d8b3-149">Pokud není zadaný, použije se výchozí umístění.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-149">If not specified, the default location is used.</span></span> <span data-ttu-id="3d8b3-150">Pokud `defaultDestination` v *Libman. JSON*není zadaná žádná vlastnost, je tato možnost povinná.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="3d8b3-151">Zadejte název souboru, který se má nainstalovat z knihovny.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="3d8b3-152">Pokud tento parametr nezadáte, nainstalují se všechny soubory z knihovny.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="3d8b3-153">Zadejte jednu `--files` možnost pro soubor, který se má nainstalovat.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="3d8b3-154">Jsou podporovány i relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-154">Relative paths are supported too.</span></span> <span data-ttu-id="3d8b3-155">Příklad: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="3d8b3-156">Název zprostředkovatele, který se má použít pro získání knihovny.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="3d8b3-157">Nahraďte `<PROVIDER>` některou z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="3d8b3-158">Pokud není zadaný, `defaultProvider` použije se vlastnost v *Libman. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3d8b3-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="3d8b3-159">Pokud `defaultProvider` v *Libman. JSON*není zadaná žádná vlastnost, je tato možnost povinná.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3d8b3-160">Příklady</span><span class="sxs-lookup"><span data-stu-id="3d8b3-160">Examples</span></span>

<span data-ttu-id="3d8b3-161">Vezměte v úvahu následující soubor *Libman. JSON* :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="3d8b3-162">Chcete-li nainstalovat soubor jQuery verze 3.2.1 *jQuery. js* do složky *wwwroot/Scripts/JQUERY* pomocí poskytovatele CDNJS:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="3d8b3-163">Soubor *Libman. JSON* se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="3d8b3-164">Instalace souborů *Calendar. js* a *Calendar. CSS* z \*C: \\ TEMP \\ contosoCalendar \\ \* pomocí zprostředkovatele systému souborů:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="3d8b3-165">Následující výzva se zobrazí ze dvou důvodů:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="3d8b3-166">Soubor *Libman. JSON* neobsahuje `defaultDestination` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="3d8b3-167">`libman install`Příkaz neobsahuje `-d|--destination` možnost.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![Libman – příkaz instalace – cíl](_static/libman-install-destination.png)

<span data-ttu-id="3d8b3-169">Po přijetí výchozího cíle se soubor *Libman. JSON* podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="3d8b3-170">Obnovit soubory knihovny</span><span class="sxs-lookup"><span data-stu-id="3d8b3-170">Restore library files</span></span>

<span data-ttu-id="3d8b3-171">`libman restore`Příkaz nainstaluje soubory knihovny definované v souboru *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="3d8b3-172">Platí následující pravidla:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-172">The following rules apply:</span></span>

* <span data-ttu-id="3d8b3-173">Pokud v kořenovém adresáři projektu neexistuje žádný soubor *Libman. JSON* , je vrácena chyba.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="3d8b3-174">Pokud knihovna Určuje poskytovatele, `defaultProvider` vlastnost v *Libman. JSON* se ignoruje.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="3d8b3-175">Pokud knihovna určuje cíl, `defaultDestination` vlastnost v *Libman. JSON* se ignoruje.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3d8b3-176">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="3d8b3-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3d8b3-177">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3d8b3-177">Options</span></span>

<span data-ttu-id="3d8b3-178">Pro příkaz jsou k dispozici následující možnosti `libman restore` :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3d8b3-179">Příklady</span><span class="sxs-lookup"><span data-stu-id="3d8b3-179">Examples</span></span>

<span data-ttu-id="3d8b3-180">Postup obnovení souborů knihovny definovaných v souboru *Libman. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="3d8b3-181">Odstranit soubory knihovny</span><span class="sxs-lookup"><span data-stu-id="3d8b3-181">Delete library files</span></span>

<span data-ttu-id="3d8b3-182">`libman clean`Příkaz odstraní soubory knihovny, které byly dříve obnoveny prostřednictvím LibMan.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="3d8b3-183">Složky, které se po odstranění této operace stanou prázdné</span><span class="sxs-lookup"><span data-stu-id="3d8b3-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="3d8b3-184">Soubory knihovny přidružené konfigurace ve `libraries` vlastnosti *Libman. JSON* se neodeberou.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3d8b3-185">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="3d8b3-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3d8b3-186">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3d8b3-186">Options</span></span>

<span data-ttu-id="3d8b3-187">Pro příkaz jsou k dispozici následující možnosti `libman clean` :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3d8b3-188">Příklady</span><span class="sxs-lookup"><span data-stu-id="3d8b3-188">Examples</span></span>

<span data-ttu-id="3d8b3-189">Postup odstranění souborů knihovny nainstalované prostřednictvím LibMan:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="3d8b3-190">Odinstalace souborů knihovny</span><span class="sxs-lookup"><span data-stu-id="3d8b3-190">Uninstall library files</span></span>

<span data-ttu-id="3d8b3-191">Příkaz `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="3d8b3-192">Odstraní všechny soubory přidružené k zadané knihovně z cíle v souboru *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="3d8b3-193">Odebere přidruženou konfiguraci knihovny z *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="3d8b3-194">K chybě dojde v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-194">An error occurs when:</span></span>

* <span data-ttu-id="3d8b3-195">V kořenu projektu neexistuje žádný soubor *Libman. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3d8b3-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3d8b3-196">Zadaná knihovna neexistuje.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="3d8b3-197">Pokud je nainstalovaná více než jedna knihovna se stejným názvem, budete vyzváni k výběru jedné z nich.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3d8b3-198">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="3d8b3-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3d8b3-199">Arguments</span><span class="sxs-lookup"><span data-stu-id="3d8b3-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3d8b3-200">Název knihovny, která se má odinstalovat.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-200">The name of the library to uninstall.</span></span> <span data-ttu-id="3d8b3-201">Tento název může obsahovat zápis čísla verze (například `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="3d8b3-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3d8b3-202">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3d8b3-202">Options</span></span>

<span data-ttu-id="3d8b3-203">Pro příkaz jsou k dispozici následující možnosti `libman uninstall` :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3d8b3-204">Příklady</span><span class="sxs-lookup"><span data-stu-id="3d8b3-204">Examples</span></span>

<span data-ttu-id="3d8b3-205">Vezměte v úvahu následující soubor *Libman. JSON* :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="3d8b3-206">Chcete-li odinstalovat jQuery, některý z následujících příkazů je úspěšný:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="3d8b3-207">Odinstalace souborů Lodash nainstalovaných prostřednictvím `filesystem` zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="3d8b3-208">Aktualizovat verzi knihovny</span><span class="sxs-lookup"><span data-stu-id="3d8b3-208">Update library version</span></span>

<span data-ttu-id="3d8b3-209">`libman update`Příkaz aktualizuje knihovnu nainstalovanou přes LibMan do zadané verze.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="3d8b3-210">K chybě dojde v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-210">An error occurs when:</span></span>

* <span data-ttu-id="3d8b3-211">V kořenu projektu neexistuje žádný soubor *Libman. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3d8b3-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3d8b3-212">Zadaná knihovna neexistuje.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="3d8b3-213">Pokud je nainstalovaná více než jedna knihovna se stejným názvem, budete vyzváni k výběru jedné z nich.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3d8b3-214">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="3d8b3-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3d8b3-215">Arguments</span><span class="sxs-lookup"><span data-stu-id="3d8b3-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3d8b3-216">Název knihovny, která se má aktualizovat</span><span class="sxs-lookup"><span data-stu-id="3d8b3-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="3d8b3-217">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3d8b3-217">Options</span></span>

<span data-ttu-id="3d8b3-218">Pro příkaz jsou k dispozici následující možnosti `libman update` :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="3d8b3-219">Získejte nejnovější předběžnou verzi knihovny.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="3d8b3-220">Získat konkrétní verzi knihovny.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3d8b3-221">Příklady</span><span class="sxs-lookup"><span data-stu-id="3d8b3-221">Examples</span></span>

* <span data-ttu-id="3d8b3-222">Aktualizace jQuery na nejnovější verzi:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="3d8b3-223">Aktualizace jQuery na verzi 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="3d8b3-224">Aktualizace jQuery na nejnovější verzi předprodejní verze:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="3d8b3-225">Spravovat mezipaměť knihovny</span><span class="sxs-lookup"><span data-stu-id="3d8b3-225">Manage library cache</span></span>

<span data-ttu-id="3d8b3-226">`libman cache`Příkaz spravuje mezipaměť knihovny LibMan.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="3d8b3-227">`filesystem`Zprostředkovatel nepoužívá mezipaměť knihovny.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3d8b3-228">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="3d8b3-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3d8b3-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="3d8b3-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="3d8b3-230">Používá se jenom s `clean` příkazem.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-230">Only used with the `clean` command.</span></span> <span data-ttu-id="3d8b3-231">Určuje mezipaměť poskytovatele, která se má vyčistit.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="3d8b3-232">Platné hodnoty zahrnují:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="3d8b3-233">Možnosti</span><span class="sxs-lookup"><span data-stu-id="3d8b3-233">Options</span></span>

<span data-ttu-id="3d8b3-234">Pro příkaz jsou k dispozici následující možnosti `libman cache` :</span><span class="sxs-lookup"><span data-stu-id="3d8b3-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="3d8b3-235">Vypíše názvy souborů, které jsou uloženy v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="3d8b3-236">Seznam názvů knihoven, které jsou uloženy v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3d8b3-237">Příklady</span><span class="sxs-lookup"><span data-stu-id="3d8b3-237">Examples</span></span>

* <span data-ttu-id="3d8b3-238">Chcete-li zobrazit názvy knihoven uložených v mezipaměti podle zprostředkovatele, použijte jeden z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="3d8b3-239">Zobrazí se výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="3d8b3-240">Zobrazení názvů souborů knihoven uložených v mezipaměti na poskytovatele:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="3d8b3-241">Zobrazí se výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="3d8b3-242">Všimněte si, že předchozí výstup ukazuje, že jsou ve zprostředkovateli CDNJS uloženy do mezipaměti jQuery verze 3.2.1 a 3.3.1.</span><span class="sxs-lookup"><span data-stu-id="3d8b3-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="3d8b3-243">Chcete-li vyprázdnit mezipaměť knihovny pro poskytovatele CDNJS:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="3d8b3-244">Po vyprázdnění mezipaměti poskytovatele CDNJS `libman cache list` zobrazí příkaz následující:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="3d8b3-245">Chcete-li vyprázdnit mezipaměť pro všechny podporované zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="3d8b3-246">Po vyprázdnění všech mezipamětí zprostředkovatele budou v `libman cache list` příkazu zobrazeny tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="3d8b3-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="3d8b3-247">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3d8b3-247">Additional resources</span></span>

* [<span data-ttu-id="3d8b3-248">Instalace globálního nástroje</span><span class="sxs-lookup"><span data-stu-id="3d8b3-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="3d8b3-249">Úložiště GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="3d8b3-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)

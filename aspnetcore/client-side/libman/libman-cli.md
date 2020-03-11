---
title: Použití rozhraní příkazového řádku LibMan s ASP.NET Core
author: scottaddie
description: Naučte se používat rozhraní příkazového řádku LibMan v projektu ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664631"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Použití rozhraní příkazového řádku LibMan s ASP.NET Core

[Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI je nástroj pro různé platformy, který podporuje všude, kde je podporováno rozhraní .NET Core.

## <a name="prerequisites"></a>Předpoklady

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalace

Instalace rozhraní příkazového řádku LibMan:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[Globální nástroj .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) je nainstalovaný z balíčku NuGet [Microsoft. Web. LibraryManager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .

Instalace rozhraní příkazového řádku LibMan z konkrétního zdroje balíčku NuGet:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

V předchozím příkladu je globální nástroj .NET Core nainstalovaný ze souboru *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* místního počítače s Windows.

## <a name="usage"></a>Využití

Po úspěšné instalaci rozhraní příkazového řádku je možné použít následující příkaz:

```console
libman
```

Chcete-li zobrazit nainstalovanou verzi rozhraní příkazového řádku:

```console
libman --version
```

Postup zobrazení dostupných příkazů rozhraní příkazového řádku:

```console
libman --help
```

Předchozí příkaz zobrazí výstup podobný následujícímu:

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

Následující části popisují dostupné příkazy rozhraní příkazového řádku.

## <a name="initialize-libman-in-the-project"></a>Inicializovat LibMan v projektu

Příkaz `libman init` vytvoří soubor *Libman. JSON* , pokud jeden neexistuje. Vytvoří se soubor s výchozím obsahem šablony položky.

### <a name="synopsis"></a>Stručný obsah

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Možnosti

Pro příkaz `libman init` jsou k dispozici následující možnosti:

* `-d|--default-destination <PATH>`

  Cesta relativní k aktuální složce Soubory knihovny jsou nainstalovány v tomto umístění, pokud není definována žádná vlastnost `destination` pro knihovnu v souboru *Libman. JSON*. Hodnota `<PATH>` je zapsána do vlastnosti `defaultDestination` *Libman. JSON*.

* `-p|--default-provider <PROVIDER>`

  Poskytovatel, který má být použit, pokud pro danou knihovnu není definován žádný zprostředkovatel. Hodnota `<PROVIDER>` je zapsána do vlastnosti `defaultProvider` *Libman. JSON*. Nahraďte `<PROVIDER>` jednou z následujících hodnot:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Vytvoření souboru *Libman. JSON* v projektu ASP.NET Core:

* Přejděte do kořenového adresáře projektu.
* Spusťte následující příkaz:

  ```console
  libman init
  ```

* Zadejte název výchozího poskytovatele nebo stiskněte `Enter` pro použití výchozího poskytovatele CDNJS. Platné hodnoty jsou:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![příkaz Libman init – výchozí zprostředkovatel](_static/libman-init-provider.png)

Do kořenu projektu se přidá soubor *Libman. JSON* s následujícím obsahem:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Přidat soubory knihovny

Příkaz `libman install` stáhne a nainstaluje soubory knihovny do projektu. Pokud jeden soubor *Libman. JSON* neexistuje, přidá se. Soubor *Libman. JSON* se upraví tak, aby se uložily podrobnosti o konfiguraci souborů knihovny.

### <a name="synopsis"></a>Stručný obsah

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Název knihovny, která se má nainstalovat Tento název může obsahovat zápis čísla verze (například `@1.2.0`).

### <a name="options"></a>Možnosti

Pro příkaz `libman install` jsou k dispozici následující možnosti:

* `-d|--destination <PATH>`

  Umístění pro instalaci knihovny. Pokud není zadaný, použije se výchozí umístění. Pokud v *Libman. JSON*není zadaná žádná vlastnost `defaultDestination`, je tato možnost povinná.

* `--files <FILE>`

  Zadejte název souboru, který se má nainstalovat z knihovny. Pokud tento parametr nezadáte, nainstalují se všechny soubory z knihovny. Zadejte jednu možnost `--files` pro každý soubor, který se má nainstalovat. Jsou podporovány i relativní cesty. Například: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Název zprostředkovatele, který se má použít pro získání knihovny. Nahraďte `<PROVIDER>` jednou z následujících hodnot:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Pokud není zadaný, použije se vlastnost `defaultProvider` v *Libman. JSON* . Pokud v *Libman. JSON*není zadaná žádná vlastnost `defaultProvider`, je tato možnost povinná.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Vezměte v úvahu následující soubor *Libman. JSON* :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Chcete-li nainstalovat soubor jQuery verze 3.2.1 *jQuery. js* do složky *wwwroot/Scripts/JQUERY* pomocí poskytovatele CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Soubor *Libman. JSON* se podobá následujícímu:

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

Chcete-li nainstalovat soubory *Calendar. js* a *Calendar. CSS* z *C:\\dočasné\\contosoCalendar\\* pomocí poskytovatele systému souborů:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Následující výzva se zobrazí ze dvou důvodů:

* Soubor *Libman. JSON* neobsahuje vlastnost `defaultDestination`.
* Příkaz `libman install` neobsahuje možnost `-d|--destination`.

![Libman – příkaz instalace – cíl](_static/libman-install-destination.png)

Po přijetí výchozího cíle se soubor *Libman. JSON* podobá následujícímu:

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

## <a name="restore-library-files"></a>Obnovit soubory knihovny

Příkaz `libman restore` nainstaluje soubory knihovny definované v souboru *Libman. JSON*. Platí následující pravidla:

* Pokud v kořenovém adresáři projektu neexistuje žádný soubor *Libman. JSON* , je vrácena chyba.
* Pokud knihovna Určuje poskytovatele, vlastnost `defaultProvider` v *Libman. JSON* se ignoruje.
* Pokud knihovna určuje cíl, vlastnost `defaultDestination` v *Libman. JSON* se ignoruje.

### <a name="synopsis"></a>Stručný obsah

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Možnosti

Pro příkaz `libman restore` jsou k dispozici následující možnosti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Postup obnovení souborů knihovny definovaných v souboru *Libman. JSON*:

```console
libman restore
```

## <a name="delete-library-files"></a>Odstranit soubory knihovny

Příkaz `libman clean` odstraní soubory knihovny, které byly dříve obnoveny prostřednictvím LibMan. Složky, které se po odstranění této operace stanou prázdné Soubory knihovny přidružené konfigurace ve vlastnosti `libraries` souboru *Libman. JSON* se neodeberou.

### <a name="synopsis"></a>Stručný obsah

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Možnosti

Pro příkaz `libman clean` jsou k dispozici následující možnosti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Postup odstranění souborů knihovny nainstalované prostřednictvím LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Odinstalace souborů knihovny

Příkaz `libman uninstall`:

* Odstraní všechny soubory přidružené k zadané knihovně z cíle v souboru *Libman. JSON*.
* Odebere přidruženou konfiguraci knihovny z *Libman. JSON*.

K chybě dojde v těchto případech:

* V kořenu projektu neexistuje žádný soubor *Libman. JSON* .
* Zadaná knihovna neexistuje.

Pokud je nainstalovaná více než jedna knihovna se stejným názvem, budete vyzváni k výběru jedné z nich.

### <a name="synopsis"></a>Stručný obsah

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Název knihovny, která se má odinstalovat. Tento název může obsahovat zápis čísla verze (například `@1.2.0`).

### <a name="options"></a>Možnosti

Pro příkaz `libman uninstall` jsou k dispozici následující možnosti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Vezměte v úvahu následující soubor *Libman. JSON* :

[!code-json[](samples/LibManSample/libman.json)]

* Chcete-li odinstalovat jQuery, některý z následujících příkazů je úspěšný:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Postup odinstalace souborů Lodash nainstalovaných prostřednictvím poskytovatele `filesystem`:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Aktualizovat verzi knihovny

Příkaz `libman update` aktualizuje knihovnu nainstalovanou přes LibMan do zadané verze.

K chybě dojde v těchto případech:

* V kořenu projektu neexistuje žádný soubor *Libman. JSON* .
* Zadaná knihovna neexistuje.

Pokud je nainstalovaná více než jedna knihovna se stejným názvem, budete vyzváni k výběru jedné z nich.

### <a name="synopsis"></a>Stručný obsah

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Název knihovny, která se má aktualizovat

### <a name="options"></a>Možnosti

Pro příkaz `libman update` jsou k dispozici následující možnosti:

* `-pre`

  Získejte nejnovější předběžnou verzi knihovny.

* `--to <VERSION>`

  Získat konkrétní verzi knihovny.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

* Aktualizace jQuery na nejnovější verzi:

  ```console
  libman update jquery
  ```

* Aktualizace jQuery na verzi 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Aktualizace jQuery na nejnovější verzi předprodejní verze:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Spravovat mezipaměť knihovny

Příkaz `libman cache` spravuje mezipaměť knihovny LibMan. Zprostředkovatel `filesystem` nepoužívá mezipaměť knihovny.

### <a name="synopsis"></a>Stručný obsah

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumenty

`PROVIDER`

Používá se jenom s příkazem `clean`. Určuje mezipaměť poskytovatele, která se má vyčistit. Platné hodnoty jsou:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Možnosti

Pro příkaz `libman cache` jsou k dispozici následující možnosti:

* `--files`

  Vypíše názvy souborů, které jsou uloženy v mezipaměti.

* `--libraries`

  Seznam názvů knihoven, které jsou uloženy v mezipaměti.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

* Chcete-li zobrazit názvy knihoven uložených v mezipaměti podle zprostředkovatele, použijte jeden z následujících příkazů:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Zobrazí se výstup podobný následujícímu:

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

* Zobrazení názvů souborů knihoven uložených v mezipaměti na poskytovatele:

  ```console
  libman cache list --files
  ```

  Zobrazí se výstup podobný následujícímu:

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

  Všimněte si, že předchozí výstup ukazuje, že jsou ve zprostředkovateli CDNJS uloženy do mezipaměti jQuery verze 3.2.1 a 3.3.1.

* Chcete-li vyprázdnit mezipaměť knihovny pro poskytovatele CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Po vyprázdnění mezipaměti poskytovatele CDNJS zobrazí příkaz `libman cache list` následující:

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

* Chcete-li vyprázdnit mezipaměť pro všechny podporované zprostředkovatele:

  ```console
  libman cache clean
  ```

  Po vyprázdnění všech mezipamětí zprostředkovatele, příkaz `libman cache list` zobrazí následující:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Další zdroje

* [Instalace globálního nástroje](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Úložiště GitHub LibMan](https://github.com/aspnet/LibraryManager)

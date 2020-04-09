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
# <a name="use-the-libman-cli-with-aspnet-core"></a>Použití cli LibMan s ASP.NET jádrem

Podle [Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI je nástroj pro různé platformy, který je podporován všude .

## <a name="prerequisites"></a>Požadavky

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalace

Instalace cli LibMan:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[Globální nástroj .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) je nainstalován z balíčku [Microsoft.Web.LibraryManagerManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet.

Instalace cli LibMan z konkrétního zdroje balíčku NuGet:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

V předchozím příkladu je globální nástroj .NET Core nainstalován ze souboru *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg.*

## <a name="usage"></a>Využití

Po úspěšné instalaci příkazového příkazu lze použít následující příkaz:

```console
libman
```

Zobrazení nainstalované verze cli:

```console
libman --version
```

Zobrazení dostupných příkazů rozhraní příkazového příkazu:

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

V následujících částech jsou uvedeny dostupné příkazy příkazu příkazu příkazu k příkazu.

## <a name="initialize-libman-in-the-project"></a>Inicializovat LibManv rámci projektu

Příkaz `libman init` vytvoří soubor *libman.json,* pokud neexistuje. Soubor je vytvořen s výchozím obsahem šablony položky.

### <a name="synopsis"></a>Synopse

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Možnosti

Pro příkaz jsou k `libman init` dispozici následující možnosti:

* `-d|--default-destination <PATH>`

  Cesta vzhledem k aktuální složce. Soubory knihovny jsou nainstalovány `destination` v tomto umístění, pokud není definována žádná vlastnost pro knihovnu v *souboru libman.json*. Hodnota `<PATH>` je zapsána `defaultDestination` do vlastnosti *libman.json*.

* `-p|--default-provider <PROVIDER>`

  Zprostředkovatel použít, pokud žádný zprostředkovatel je definován pro danou knihovnu. Hodnota `<PROVIDER>` je zapsána `defaultProvider` do vlastnosti *libman.json*. Nahraďte `<PROVIDER>` se jednou z následujících hodnot:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Vytvoření souboru *libman.json* v projektu ASP.NET Core:

* Přejděte do kořenového adresáře projektu.
* Spusťte následující příkaz:

  ```console
  libman init
  ```

* Zadejte název výchozího zprostředkovatele `Enter` nebo stisknutím klávesy použijte výchozího zprostředkovatele CDNJS. Mezi platné hodnoty patří:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init, příkaz - výchozí zprostředkovatel](_static/libman-init-provider.png)

Do kořenového adresáře projektu je přidán soubor *libman.json* s následujícím obsahem:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Přidání souborů knihovny

Příkaz `libman install` stáhne a nainstaluje soubory knihovny do projektu. Pokud soubor libman.json neexistuje, je přidán soubor *libman.json.* Soubor *libman.json* je upraven tak, aby ukládaly podrobnosti konfigurace pro soubory knihovny.

### <a name="synopsis"></a>Synopse

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Název knihovny k instalaci. Tento název může obsahovat zápis čísla `@1.2.0`verze (například ).

### <a name="options"></a>Možnosti

Pro příkaz jsou k `libman install` dispozici následující možnosti:

* `-d|--destination <PATH>`

  Umístění pro instalaci knihovny. Pokud není zadán, použije se výchozí umístění. Pokud `defaultDestination` v *souboru libman.json*není zadána žádná vlastnost , je tato možnost vyžadována.

* `--files <FILE>`

  Zadejte název souboru, který chcete nainstalovat z knihovny. Pokud není zadán, jsou nainstalovány všechny soubory z knihovny. Zadejte `--files` jednu možnost pro každý soubor, který má být nainstalován. Relativní cesty jsou také podporovány. Například: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Název zprostředkovatele, který má být používán pro získání knihovny. Nahraďte `<PROVIDER>` se jednou z následujících hodnot:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Pokud není zadán, `defaultProvider` vlastnost v *libman.json* se používá. Pokud `defaultProvider` v *souboru libman.json*není zadána žádná vlastnost , je tato možnost vyžadována.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Zvažte následující soubor *libman.json:*

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Chcete-li nainstalovat soubor jQuery verze 3.2.1 *jquery.min.js* do složky *wwwroot/scripts/jquery* pomocí zprostředkovatele CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Soubor *libman.json* se podobá následujícímu:

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

Instalace souborů *calendar.js* a *calendar.css* z *c:\\temp\\contosoCalendar\\ * pomocí zprostředkovatele systému souborů:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Následující výzva se zobrazí ze dvou důvodů:

* Soubor *libman.json* neobsahuje `defaultDestination` vlastnost.
* Příkaz `libman install` tuto `-d|--destination` možnost neobsahuje.

![libman nainstalovat příkaz - cíl](_static/libman-install-destination.png)

Po přijetí výchozího cíle se soubor *libman.json* podobá následujícímu:

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

## <a name="restore-library-files"></a>Obnovení souborů knihovny

Příkaz `libman restore` nainstaluje soubory knihovny definované v *souboru libman.json*. Platí následující pravidla:

* Pokud v kořenovém adresáři projektu neexistuje žádný soubor *libman.json,* je vrácena chyba.
* Pokud knihovna určuje zprostředkovatele, vlastnost v `defaultProvider` *souboru libman.json* je ignorována.
* Pokud knihovna určuje cíl, `defaultDestination` vlastnost v *souboru libman.json* je ignorována.

### <a name="synopsis"></a>Synopse

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Možnosti

Pro příkaz jsou k `libman restore` dispozici následující možnosti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Obnovení souborů knihovny definovaných v *souboru libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>Odstranění souborů knihovny

Příkaz `libman clean` odstraní soubory knihovny dříve obnovené přes LibMan. Složky, které se po této operaci vyprázdní, budou odstraněny. Přidružené konfigurace souborů knihovny `libraries` ve vlastnosti *libman.json nebudou odebrány.*

### <a name="synopsis"></a>Synopse

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Možnosti

Pro příkaz jsou k `libman clean` dispozici následující možnosti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Odstranění souborů knihovny nainstalovaných přes LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Odinstalace souborů knihovny

Příkaz `libman uninstall`:

* Odstraní všechny soubory přidružené k zadané knihovně z cíle v *souboru libman.json*.
* Odebere přidruženou konfiguraci knihovny z *souboru libman.json*.

K chybě dochází, když:

* V kořenovém adresáři projektu neexistuje žádný soubor *libman.json.*
* Zadaná knihovna neexistuje.

Pokud je nainstalována více než jedna knihovna se stejným názvem, budete vyzváni k výběru jedné.

### <a name="synopsis"></a>Synopse

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Název knihovny, kterou chcete odinstalovat. Tento název může obsahovat zápis čísla `@1.2.0`verze (například ).

### <a name="options"></a>Možnosti

Pro příkaz jsou k `libman uninstall` dispozici následující možnosti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Zvažte následující soubor *libman.json:*

[!code-json[](samples/LibManSample/libman.json)]

* Chcete-li odinstalovat jQuery, jeden z následujících příkazů úspěšné:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Chcete-li odinstalovat soubory Lodash nainstalované prostřednictvím `filesystem` poskytovatele:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Aktualizovat verzi knihovny

Příkaz `libman update` aktualizuje knihovnu nainstalovanou přes LibMan na zadanou verzi.

K chybě dochází, když:

* V kořenovém adresáři projektu neexistuje žádný soubor *libman.json.*
* Zadaná knihovna neexistuje.

Pokud je nainstalována více než jedna knihovna se stejným názvem, budete vyzváni k výběru jedné.

### <a name="synopsis"></a>Synopse

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumenty

`LIBRARY`

Název knihovny aktualizovat.

### <a name="options"></a>Možnosti

Pro příkaz jsou k `libman update` dispozici následující možnosti:

* `-pre`

  Získejte nejnovější předběžnou verzi knihovny.

* `--to <VERSION>`

  Získejte konkrétní verzi knihovny.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

* Aktualizace aplikace jQuery na nejnovější verzi:

  ```console
  libman update jquery
  ```

* Aktualizace aplikace jQuery na verzi 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Aktualizace aplikace jQuery na nejnovější předběžnou verzi:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Správa mezipaměti knihovny

Příkaz `libman cache` spravuje mezipaměť knihovny LibMan. Zprostředkovatel `filesystem` nepoužívá mezipaměť knihovny.

### <a name="synopsis"></a>Synopse

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumenty

`PROVIDER`

Používá se `clean` pouze s příkazem. Určuje mezipaměť zprostředkovatele, která má být vyčištěna. Mezi platné hodnoty patří:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Možnosti

Pro příkaz jsou k `libman cache` dispozici následující možnosti:

* `--files`

  Seznam názvů souborů, které jsou uloženy do mezipaměti.

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

* Chcete-li zobrazit názvy souborů knihovny uložených v mezipaměti podle zprostředkovatele:

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

  Všimněte si, že předchozí výstup ukazuje, že jQuery verze 3.2.1 a 3.3.1 jsou uloženy do mezipaměti pod zprostředkovatelem CDNJS.

* Vyprázdnění mezipaměti knihovny pro zprostředkovatele CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Po vyprázdnění mezipaměti `libman cache list` zprostředkovatele CDNJS se v příkazu zobrazí následující:

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

* Vyprázdnění mezipaměti pro všechny podporované zprostředkovatele:

  ```console
  libman cache clean
  ```

  Po vyprázdnění všech `libman cache list` mezipamětí zprostředkovatele se v příkazu zobrazí následující:

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
* [Úložiště LibMan GitHub](https://github.com/aspnet/LibraryManager)

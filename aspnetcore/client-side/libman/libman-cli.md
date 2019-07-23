---
title: Použití rozhraní příkazového řádku (CLI) LibMan s ASP.NET Core
author: scottaddie
description: Další informace o použití rozhraní příkazového řádku (CLI) LibMan v projektu aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: 5667f79648a60b8fd9496f8041ef08891ab766af
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900882"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a>Použití rozhraní příkazového řádku (CLI) LibMan s ASP.NET Core

Podle [Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI je nástroj napříč platformami, která je podporována everywhere .NET Core je podporována.

## <a name="prerequisites"></a>Požadavky

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalace

Instalace rozhraní příkazového řádku LibMan:

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

A [globální nástroje .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) se instaluje z [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) balíček NuGet.

Instalace rozhraní příkazového řádku LibMan z konkrétního zdroje balíčku NuGet:

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

V předchozím příkladu je nainstalován nástroj globální .NET Core z místního počítače Windows *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* souboru.

## <a name="usage"></a>Použití

Po úspěšné instalaci rozhraní příkazového řádku můžete pomocí následujícího příkazu:

```console
libman
```

Pokud chcete zobrazit nainstalovanou verzi rozhraní příkazového řádku:

```console
libman --version
```

Chcete-li zobrazit dostupné příkazy rozhraní příkazového řádku:

```console
libman --help
```

Ve výstupu předchozího příkazu se zobrazí výstup podobný následujícímu:

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

Následující oddíly popisují dostupné příkazy rozhraní příkazového řádku.

## <a name="initialize-libman-in-the-project"></a>Inicializovat LibMan v projektu

`libman init` Příkaz vytvoří *libman.json* souboru, pokud neexistuje. Soubor se vytvoří s výchozí obsah šablony položky.

### <a name="synopsis"></a>Souhrn

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Možnosti

Jsou k dispozici pro následující možnosti `libman init` příkaz:

* `-d|--default-destination <PATH>`

  Cestu relativní vzhledem k aktuální složky. Soubory knihovny nainstalují v tomto umístění, pokud žádné `destination` vlastnost je definována pro knihovny *libman.json*. `<PATH>` Hodnotu zapíšete do `defaultDestination` vlastnost *libman.json*.

* `-p|--default-provider <PROVIDER>`

  Zprostředkovatele, který se použijte, pokud není definován žádný poskytovatel pro danou knihovnu. `<PROVIDER>` Hodnotu zapíšete do `defaultProvider` vlastnost *libman.json*. Nahraďte `<PROVIDER>` s jedním z následujících hodnot:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Chcete-li vytvořit *libman.json* soubor v projektu aplikace ASP.NET Core:

* Přejděte do kořenového adresáře projektu.
* Spusťte následující příkaz:

  ```console
  libman init
  ```

* Zadejte název výchozího poskytovatele nebo stisknutím klávesy `Enter` poskytovatel CDNJS výchozí se použije. Platné hodnoty jsou:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![příkaz init libman – výchozího zprostředkovatele](_static/libman-init-provider.png)

A *libman.json* přidá soubor do kořenového adresáře projektu s následujícím obsahem:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Přidejte soubory knihovny

`libman install` Příkaz stáhne a nainstaluje soubory knihovny do projektu. A *libman.json* se přidá soubor, pokud neexistuje. *Libman.json* změně souboru k ukládání konfiguračních souborů knihoven.

### <a name="synopsis"></a>Souhrn

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Název knihovny určené k instalaci. Tento název může obsahovat notation číslo verze (například `@1.2.0`).

### <a name="options"></a>Možnosti

Jsou k dispozici pro následující možnosti `libman install` příkaz:

* `-d|--destination <PATH>`

  Umístění pro instalaci knihovny. Pokud není zadán, použije se výchozí umístění. Pokud ne `defaultDestination` je zadána vlastnost v *libman.json*, tato možnost je vyžadována.

* `--files <FILE>`

  Zadejte název souboru k instalaci z knihovny. Pokud není zadán, nainstalují se všechny soubory z knihovny. Zadejte jeden `--files` možnost na soubor k instalaci. Jsou podporovány příliš relativní cesty. Například: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Název zprostředkovatele, který má být použit pro získání knihovny. Nahraďte `<PROVIDER>` s jedním z následujících hodnot:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Pokud není zadán, `defaultProvider` vlastnost *libman.json* se používá. Pokud ne `defaultProvider` je zadána vlastnost v *libman.json*, tato možnost je vyžadována.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Vezměte v úvahu následující *libman.json* souboru:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

K instalaci verze jQuery 3.2.1 *jquery.min.js* do souboru *wwwroot/skripty/jquery* pomocí zprostředkovatele CDNJS složky:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

*Libman.json* soubor má následující podobu:

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

Chcete-li nainstalovat *calendar.js* a *calendar.css* souborů z *C:\\temp\\contosoCalendar\\*  pomocí systému souborů poskytovatel:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Se zobrazí následující výzva pro dva důvody:

* *Libman.json* soubor neobsahuje `defaultDestination` vlastnost.
* `libman install` Neobsahuje příkaz `-d|--destination` možnost.

![libman nainstalovat příkaz – cíl](_static/libman-install-destination.png)

Po přijetí výchozího místa určení, *libman.json* soubor má následující podobu:

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

## <a name="restore-library-files"></a>Obnovit soubory knihoven

`libman restore` Nainstaluje soubory knihoven, které jsou definovány v *libman.json*. Platí následující pravidla:

* Pokud ne *libman.json* soubor v kořenovém adresáři projektu existuje, vrátí se chyba.
* Pokud knihovny určuje zprostředkovatele, `defaultProvider` vlastnost v *libman.json* se ignoruje.
* Pokud knihovny určuje cíl, `defaultDestination` vlastnost *libman.json* se ignoruje.

### <a name="synopsis"></a>Souhrn

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Možnosti

Jsou k dispozici pro následující možnosti `libman restore` příkaz:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Pokud chcete obnovit soubory knihovny definované v *libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>Odstranit soubory knihoven

`libman clean` Příkaz odstraní soubory knihovny přes LibMan byly obnoveny. Složky, které se stanou prázdný po provedení této operace jsou odstraněny. Konfigurace v přidružené soubory knihovny `libraries` vlastnost *libman.json* se neodeberou.

### <a name="synopsis"></a>Souhrn

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Možnosti

Jsou k dispozici pro následující možnosti `libman clean` příkaz:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Chcete-li odstranit soubory knihovny k instalaci použili LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Odinstalace soubory knihoven

`libman uninstall` Příkaz:

* Odstraní všechny soubory přidružené k zadanou knihovnu z cílového umístění v *libman.json*.
* Odstraní konfiguraci přidruženou knihovnu z *libman.json*.

Dojde k chybě při:

* Ne *libman.json* existuje soubor v kořenové složce projektu.
* Zadanou knihovnu neexistuje.

Pokud je nainstalovaná více než jedna knihovna se stejným názvem, budete vyzváni, zvolte jeden.

### <a name="synopsis"></a>Souhrn

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Název knihovny určené k odinstalaci. Tento název může obsahovat notation číslo verze (například `@1.2.0`).

### <a name="options"></a>Možnosti

Jsou k dispozici pro následující možnosti `libman uninstall` příkaz:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

Vezměte v úvahu následující *libman.json* souboru:

[!code-json[](samples/LibManSample/libman.json)]

* Pokud chcete odinstalovat jQuery, některý z následujících příkazů úspěšné:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* K odinstalaci soubory Lodash instalovaných pomocí instalace `filesystem` zprostředkovatele:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Aktualizujte verzi knihovny

`libman update` Příkaz aktualizuje instalovaných pomocí instalace LibMan zadanou verzi knihovny.

Dojde k chybě při:

* Ne *libman.json* existuje soubor v kořenové složce projektu.
* Zadanou knihovnu neexistuje.

Pokud je nainstalovaná více než jedna knihovna se stejným názvem, budete vyzváni, zvolte jeden.

### <a name="synopsis"></a>Souhrn

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Název knihovny určené k aktualizaci.

### <a name="options"></a>Možnosti

Jsou k dispozici pro následující možnosti `libman update` příkaz:

* `-pre`

  Získáte nejnovější předběžnou verzi knihovny.

* `--to <VERSION>`

  Získáte konkrétní verzi knihovny.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

* JQuery aktualizovat na nejnovější verzi:

  ```console
  libman update jquery
  ```

* Chcete aktualizovat jQuery verze 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Chcete-li aktualizovat jQuery nejnovější předběžnou verzi:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Správa mezipaměti knihovny

`libman cache` Příkaz spravuje mezipaměti LibMan knihovny. `filesystem` Poskytovatele nepoužívá mezipaměť knihovny.

### <a name="synopsis"></a>Souhrn

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Arguments

`PROVIDER`

Použít pouze s `clean` příkazu. Určuje mezipaměť zprostředkovatele k vyčištění. Platné hodnoty jsou:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Možnosti

Jsou k dispozici pro následující možnosti `libman cache` příkaz:

* `--files`

  Seznam názvů souborů, které jsou uložené v mezipaměti.

* `--libraries`

  Seznam názvů knihoven, které jsou uložené v mezipaměti.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Příklady

* Chcete-li zobrazit názvy knihoven v mezipaměti na poskytovatele, použijte jednu z následujících příkazů:

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

* Chcete-li zobrazit názvy souborů v mezipaměti knihovny na zprostředkovatele:

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

  Všimněte si, že tento výstup ukazuje, že jQuery verze 3.2.1 a 3.3.1 jsou uložené v mezipaměti v rámci zprostředkovatele CDNJS.

* Vyprázdnit mezipaměť knihovny pro poskytovatele CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Po vyprazdňují se mezipaměť zprostředkovatele CDNJS, `libman cache list` příkazu se zobrazí následující:

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

  Po vyprazdňují se všechny mezipaměti zprostředkovatele `libman cache list` příkazu se zobrazí následující:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Další zdroje

* [Nainstalujte nástroj Global](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Úložiště LibMan GitHub](https://github.com/aspnet/LibraryManager)

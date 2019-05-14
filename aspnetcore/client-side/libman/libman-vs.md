---
title: LibMan pomocí ASP.NET Core v sadě Visual Studio
author: scottaddie
description: Další informace o použití LibMan v projektu aplikace ASP.NET Core pomocí sady Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: d0494b96447ad2b256acbde7748209ff430ae7c1
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610182"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>LibMan pomocí ASP.NET Core v sadě Visual Studio

Podle [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio obsahuje integrovanou podporu [LibMan](xref:client-side/libman/index) v projektech ASP.NET Core, včetně:

* Podpora pro konfiguraci a spuštění operace obnovení LibMan na sestavení.
* Položky nabídky, která aktivuje LibMan obnovení a vyčistit operace.
* Dialog pro hledání pro hledání knihoven a přidávání souborů do projektu.
* Podporu pro editaci *libman.json*&mdash;LibMan souboru manifestu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **vývoj pro ASP.NET a web** pracovního vytížení

## <a name="add-library-files"></a>Přidejte soubory knihovny

Soubory knihovny můžete přidat do projektu aplikace ASP.NET Core dvěma různými způsoby:

1. [Použijte dialogové okno Přidat knihovnu na straně klienta](#use-the-add-client-side-library-dialog)
1. [Ruční konfigurace LibMan položek souboru manifestu](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Použijte dialogové okno Přidat knihovnu na straně klienta

Postupujte podle těchto kroků nainstalujte klientské knihovny:

* V **Průzkumníka řešení**, klikněte pravým tlačítkem na složku projektu, ve kterém soubory přidaly. Zvolte **přidat** > **knihoven na straně klienta**. **Přidat knihovnu na straně klienta** se zobrazí dialogové okno:

  ![Přidat dialog knihoven na straně klienta](_static/add-library-dialog.png)

* Vyberte zprostředkovatele knihovny z **poskytovatele** rozevírací seznam. CDNJS je výchozím zprostředkovatelem.
* Zadejte název knihovny k načtení v **knihovny** textového pole. Technologie IntelliSense poskytuje seznam knihoven počínaje zadaný text.
* Vyberte knihovnu ze seznamu technologie IntelliSense. Všimněte si, že je název knihovny příponu `@` symbolů a nejnovější stabilní verze označuje pro vybraného zprostředkovatele.
* Určete soubory, které chcete zahrnout:
  * Vyberte **zahrnutí všech souborů knihovny** přepínací tlačítko, které zahrnují všechny soubory knihovně.
  * Vyberte **zvolte konkrétní soubory** přepínací tlačítko, které zahrnují podmnožinu souborů knihovny. Když je přepínač vybrán, stromu výběru souboru je povolen. Zaškrtněte políčka nalevo od názvu souboru ke stažení.
* Zadejte složku projektu pro ukládání souborů do **cílové umístění** textového pole. Jako doporučení uložte každou knihovnu do samostatné složky.

  Návrhy **cílové umístění** složky podle umístění, ze kterého se spustí dialogové okno:

  * Pokud se spustí z kořenového adresáře projektu:
    * *Wwwroot/lib* se používá v případě *wwwroot* existuje.
    * *lib* se používá v případě *wwwroot* neexistuje.
  * Pokud se spustí ze složky projektu, se používá odpovídající název složky.

  Návrh složky je přidán s názvem knihovny. Následující tabulka uvádí návrhy složky při instalaci jQuery v projektu pro stránky Razor.
  
  |Spustit umístění                           |Navrhované složky      |
  |------------------------------------------|----------------------|
  |kořen projektu (Pokud *wwwroot* existuje)        |*wwwroot/lib/jquery/* |
  |kořen projektu (Pokud *wwwroot* neexistuje) |*lib/jquery/*         |
  |*Stránky* složky v projektu                 |*Pages/jquery/*       |

* Klikněte na tlačítko **nainstalovat** tlačítko a stáhněte si soubory na konfiguraci v *libman.json*.
* Zkontrolujte **Správce knihovny** informačního kanálu **výstup** okno pro podrobné informace o instalaci. Příklad:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>Ruční konfigurace LibMan položek souboru manifestu

Všechny operace LibMan v sadě Visual Studio jsou založeny na obsah manifestu LibMan kořen projektu (*libman.json*). Můžete ručně upravit *libman.json* konfigurace soubory knihovny pro projekt. Visual Studio obnoví všechny soubory knihovny jednou *libman.json* se uloží.

Chcete-li otevřít *libman.json* pro úpravy, existují tyto možnosti:

* Dvakrát klikněte *libman.json* ve **Průzkumníka řešení**.
* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **správu knihoven na straně klienta**. **&#8224;**
* Vyberte **správu knihoven na straně klienta** ze sady Visual Studio **projektu** nabídky. **&#8224;**

**&#8224;** Pokud *libman.json* soubor v kořenové složce projektu již neexistuje, vytvoří se s obsahem výchozí položku šablony.

Visual Studio nabízí bohaté možnosti JSON úpravy podpory, jako je například zabarvení, formátování, technologie IntelliSense a ověřování schématu. Schéma JSON manifestu LibMan se nachází v umístění [ http://json.schemastore.org/libman ](http://json.schemastore.org/libman).

Pomocí následující soubor manifestu LibMan načte soubory za definované v konfiguraci `libraries` vlastnost. Vysvětlení literály objektů definovaných v rámci `libraries` následující:

* Podmnožinu [jQuery](https://jquery.com/) verze 3.3.1 je načten z CDNJS zprostředkovatele. Dílčí je definována v `files` vlastnost&mdash;*jquery.min.js*, *jquery.js*, a *jquery.min.map*. Soubory jsou umístěny v projektu *wwwroot/lib/jquery* složky.
* Podkladové [Bootstrap](https://getbootstrap.com/) verze 4.1.3 je načten a je umístěná v *wwwroot/lib/bootstrap* složky. Literál objektu `provider` vlastnosti přepsání `defaultProvider` hodnotu vlastnosti. LibMan načte Bootstrap soubory z unpkg zprostředkovatele.
* Podmnožinu [Lodash](https://lodash.com/) schválila orgán v rámci organizace. *Lodash.js* a *lodash.min.js* soubory se načítají z místního systému souborů na *C:\\temp\\lodash\\*. Soubory se zkopírují do projektu *wwwroot/lib/lodash* složky.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan podporuje pouze jednu verzi každého knihovny od každého poskytovatele. *Libman.json* souboru schématu ověřování nezdaří, pokud obsahuje dvě knihovny se stejným názvem knihovny pro daného zprostředkovatele.

## <a name="restore-library-files"></a>Obnovit soubory knihoven

Obnovení souborů knihovny ze sady Visual Studio, musí být platný *libman.json* soubor v kořenové složce projektu. Obnovené soubory jsou umístěny v projektu na umístění zadaném pro každou knihovnu.

Soubory knihovny je možné obnovit v projektu aplikace ASP.NET Core dvěma způsoby:

1. [Obnovení souborů během sestavování](#restore-files-during-build)
1. [Ruční obnovení souborů](#restore-files-manually)

### <a name="restore-files-during-build"></a>Obnovení souborů během sestavování

LibMan můžete obnovit soubory knihovny definované jako součást procesu sestavení. Ve výchozím nastavení *obnovení na build* zakázané chování.

Povolit a otestovat chování obnovení na sestavení:

* Klikněte pravým tlačítkem na *libman.json* v **Průzkumníka řešení** a vyberte **povolit obnovení na straně klienta knihovny na sestavení** v místní nabídce.
* Klikněte na tlačítko **Ano** tlačítko po zobrazení výzvy k instalaci balíčku NuGet. [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) se přidá balíček NuGet do projektu:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Sestavte projekt, abyste potvrdili, že dojde k obnovení souborů LibMan. `Microsoft.Web.LibraryManager.Build` Balíček vkládá cíl nástroje MSBuild, na kterém běží LibMan během operace sestavení projektu.
* Zkontrolujte **sestavení** informačního kanálu **výstup** okna pro protokol aktivit LibMan:

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

Pokud je povoleno chování obnovení na sestavení, *libman.json* zobrazí místní nabídka **zakázat obnovení klientské knihovny na sestavení** možnost. Tato volba odstraní `Microsoft.Web.LibraryManager.Build` balíček odkaz ze souboru projektu. V důsledku toho budou na každé sestavení už obnoveny knihoven na straně klienta.

Bez ohledu na nastavení obnovení na sestavení, můžete ručně obnovit kdykoli *libman.json* kontextové nabídky. Další informace najdete v tématu [ručně obnovit soubory](#restore-files-manually).

### <a name="restore-files-manually"></a>Ruční obnovení souborů

Chcete-li obnovit ručně soubory knihoven:

* Pro všechny projekty v řešení:
  * Klikněte pravým tlačítkem na název řešení v **Průzkumníka řešení**.
  * Vyberte **obnovení knihoven na straně klienta** možnost.
* Pro konkrétní projekt:
  * Klikněte pravým tlačítkem myši *libman.json* ve **Průzkumníka řešení**.
  * Vyberte **obnovení knihoven na straně klienta** možnost.

Během operace obnovení:

* Centrum stavu úloh (TSC) ikonu na stavovém řádku sady Visual Studio bude při animaci pohybovat a přečte *obnovení byla zahájena operace*. Kliknutím na ikonu otevře popisek výpisu úloh na pozadí známé.
* Zprávy se odešlou do stavového řádku a **Správce knihovny** informačního kanálu **výstup** okna. Příklad:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Odstranit soubory knihoven

K provedení *čisté* operaci, která odstraní soubory knihoven, které byly obnoveny v sadě Visual Studio:

* Klikněte pravým tlačítkem myši *libman.json* ve **Průzkumníka řešení**.
* Vyberte **čisté knihoven na straně klienta** možnost.

Aby se zabránilo neúmyslnému odebrání souborů mimo knihovnu, operace vyčištění nedojde k odstranění celé adresáře. Odebere pouze soubory, které byly součástí předchozích obnovení.

Během operace čištění:

* TSC ikonu na stavovém řádku sady Visual Studio bude při animaci pohybovat a přečte *klientské knihovny operaci spustit*. Kliknutím na ikonu otevře popisek výpisu úloh na pozadí známé.
* Odeslání zpráv stavového řádku a **Správce knihovny** informačního kanálu **výstup** okna. Příklad:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Operace vyčištění odstraní jenom soubory z projektu. Soubory knihoven zůstat v mezipaměti pro rychlejší načítání na budoucí obnovení operací. Ke správě knihovny soubory uložené v mezipaměti místním počítači, použijte [LibMan CLI](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Odinstalace soubory knihoven

Chcete-li odinstalovat soubory knihovny:

* Open *libman.json*.
* Pozici blikajícího kurzoru dovnitř odpovídající `libraries` literálu objektu.
* Klikněte na ikonu žárovky, která se zobrazí u levého okraje a vyberte **odinstalovat \<library_name > @\<library_version >**:

  ![Knihovna možnost místní nabídky odinstalovat](_static/uninstall-menu-option.png)

Alternativně můžete ručně upravit a uložit LibMan manifest (*libman.json*). [Operace obnovení](#restore-library-files) spustí, když je soubor uložen. Soubory knihoven, které jsou již definovány v *libman.json* jsou odebrány z projektu.

## <a name="update-library-version"></a>Aktualizujte verzi knihovny

Vyhledat aktualizovanou knihovní verze:

* Open *libman.json*.
* Pozici blikajícího kurzoru dovnitř odpovídající `libraries` literálu objektu.
* Klikněte na ikonu žárovky, která se zobrazí na levém okraji. Najeďte myší na **vyhledávat aktualizace**.

LibMan zkontroluje verzi knihovny, která je novější než verze nainstalovaná. Může dojít následujících situací:

* A **nenašly se žádné aktualizace** se zobrazí zpráva, pokud už je nainstalovaná nejnovější verze.
* Nejnovější stabilní verze je zobrazen Pokud ještě není nainstalované.

  ![Zkontrolovat aktualizace možnost místní nabídky](_static/update-menu-option.png)

* Pokud se předběžné verze novější než nainstalovaná verze je k dispozici, zobrazí se předběžné verze.

Přejít na starší verzi knihovny, na ručně upravit *libman.json* souboru. Když je soubor uložen, LibMan [operace obnovení](#restore-library-files):

* Odebere nadbytečné soubory z předchozí verze.
* Přidá nové a aktualizované soubory z nové verze.

## <a name="additional-resources"></a>Další zdroje

* <xref:client-side/libman/libman-cli>
* [Úložiště LibMan GitHub](https://github.com/aspnet/LibraryManager)

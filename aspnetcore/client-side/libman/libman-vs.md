---
title: Použití LibMan a ASP.NET jádra v sadě Visual Studio
author: scottaddie
description: Naučte se používat LibMan v projektu ASP.NET Core s Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658310"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Použití LibMan a ASP.NET jádra v sadě Visual Studio

Podle [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio má integrovanou podporu [libmana](xref:client-side/libman/index) v projektech ASP.NET Core, včetně:

* Podpora konfigurace a spuštění operací obnovení LibMan na sestavení.
* Položky nabídky pro spuštění obnovení LibMan a čištění operací.
* Dialogové okno hledání pro hledání knihoven a přidávání souborů do projektu.
* Podpora úprav pro soubor manifestu *Libman.json*&mdash;LibMan.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**

## <a name="add-library-files"></a>Přidání souborů knihovny

Soubory knihovny lze do projektu ASP.NET Core přidat dvěma různými způsoby:

1. [Použití dialogového okna Přidat knihovnu na straně klienta](#use-the-add-client-side-library-dialog)
1. [Ruční konfigurace položek souboru manifestu LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Použití dialogového okna Přidat knihovnu na straně klienta

Chcete-li nainstalovat knihovnu na straně klienta, postupujte takto:

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku projektu, do které mají být soubory přidány. Zvolte **Přidat** > **knihovnu na straně klienta**. Zobrazí se dialogové okno **Přidat knihovnu na straně klienta:**

  ![Dialogové okno Přidat knihovnu na straně klienta](_static/add-library-dialog.png)

* V rozevíracím souboru Zprostředkovatel vyberte **zprostředkovatele.** CDNJS je výchozím zprostředkovatelem.
* Do textového pole **Knihovna** zadejte název knihovny, který chcete načíst. Technologie IntelliSense poskytuje seznam knihoven začínajících na poskytnutý text.
* Vyberte knihovnu ze seznamu IntelliSense. Všimněte si, že název knihovny je opatřen `@` symbolem a nejnovější stabilní verzí známou vybranému zprostředkovateli.
* Rozhodněte, které soubory chcete zahrnout:
  * Vyberte **přepínací tlačítko Zahrnout všechny soubory knihovny,** chcete-li zahrnout všechny soubory knihovny.
  * Vyberte přepínač **Zvolit konkrétní soubory,** chcete-li zahrnout podmnožinu souborů knihovny. Je-li vybráno přepínací tlačítko, je povolen strom pro výběr souborů. Zaškrtněte políčka nalevo od názvů souborů, které chcete stáhnout.
* Určete složku projektu pro ukládání souborů do textového pole **Cílové umístění.** Jako doporučení uložte každou knihovnu do samostatné složky.

  Navrhovaná složka **Cílová poloha** je založena na umístění, ze kterého bylo dialogové okno spuštěno:

  * Pokud je spuštěn z kořenového adresáře projektu:
    * *wwwroot/lib* se používá, pokud *wwwroot* existuje.
    * *lib* se používá, pokud *wwwroot* neexistuje.
  * Pokud je spuštěn ze složky projektu, bude použit odpovídající název složky.

  Návrh složky je opatřen názvem knihovny. Následující tabulka ilustruje návrhy složek při instalaci jQuery v projektu Razor Pages.
  
  |Místo spuštění                           |Navrhovaná složka      |
  |------------------------------------------|----------------------|
  |kořen projektu (pokud *existuje wwwroot)*        |*wwwroot/lib/jquery/* |
  |kořen projektu (pokud *wwwroot* neexistuje) |*lib/jquery/*         |
  |*Složka Stránky* v projektu                 |*Stránky/jquery/*       |

* Klepnutím na tlačítko **Instalovat** stáhnete soubory podle konfigurace v *souboru libman.json*.
* Podrobnosti o instalaci naleznete v informačním kanálu **Správce knihovny** v okně **Výstup.** Příklad:

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

### <a name="manually-configure-libman-manifest-file-entries"></a>Ruční konfigurace položek souboru manifestu LibMan

Všechny operace LibMan v sadě Visual Studio jsou založeny na obsahu manifestu LibMan kořenového projektu (*libman.json*). Chcete-li nakonfigurovat soubory knihovny pro projekt, můžete ručně upravit *soubor libman.json.* Visual Studio obnoví všechny soubory knihovny po uložení *souboru libman.json.*

Chcete-li otevřít *soubor libman.json* pro úpravy, existují následující možnosti:

* Poklepejte na soubor *libman.json* v **Průzkumníku řešení**.
* Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **spravovat knihovny na straně klienta**. **&#8224;**
* Z nabídky **Aplikace Visual** Studio Project vyberte **Spravovat knihovny na straně klienta.** **&#8224;**

**&#8224;** Pokud soubor *libman.json* ještě v kořenovém adresáři projektu neexistuje, bude vytvořen s výchozím obsahem šablony položky.

Visual Studio nabízí bohatou podporu úprav JSON, jako je vybarvení, formátování, technologie IntelliSense a ověřování schématu. Schéma JSON manifestu LibMan se nachází [https://json.schemastore.org/libman](https://json.schemastore.org/libman)na adrese .

S následujícím souborem manifestu LibMan načte soubory `libraries` podle konfigurace definované ve vlastnosti. Vysvětlení literály objektu definované `libraries` v jenásledující:

* Podmnožina [jQuery](https://jquery.com/) verze 3.3.1 je načtena od zprostředkovatele CDNJS. Podmnožina je `files` definována ve vlastnostech&mdash;*jquery.min.js*, *jquery.js*a *jquery.min.map*. Soubory jsou umístěny ve složce *wwwroot/lib/jquery* projektu.
* Celá [Bootstrap](https://getbootstrap.com/) verze 4.1.3 je načtena a umístěna do složky *wwwroot/ lib / bootstrap.* `provider` Vlastnost literálu objektu přepíše hodnotu vlastnosti. `defaultProvider` LibMan načte soubory Bootstrap od poskytovatele unpkg.
* Podmnožina [Lodash](https://lodash.com/) byla schválena řídícím orgánem v rámci organizace. *Lodash.js* a *lodash.min.js* soubory jsou načteny z místního systému souborů na *C:\\temp\\lodash\\*. Soubory jsou zkopírovány do složky *wwwroot/lib/lodash* projektu.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan podporuje pouze jednu verzi každé knihovny od každého poskytovatele. Soubor *libman.json* se nezdaří ověření schématu, pokud obsahuje dvě knihovny se stejným názvem knihovny pro daného zprostředkovatele.

## <a name="restore-library-files"></a>Obnovení souborů knihovny

Chcete-li obnovit soubory knihovny z aplikace Visual Studio, musí být platný soubor *libman.json* v kořenovém adresáři projektu. Obnovené soubory jsou umístěny v projektu v umístění určeném pro každou knihovnu.

Soubory knihovny lze obnovit v projektu ASP.NET Core dvěma způsoby:

1. [Obnovení souborů během sestavení](#restore-files-during-build)
1. [Ruční obnovení souborů](#restore-files-manually)

### <a name="restore-files-during-build"></a>Obnovení souborů během sestavení

LibMan můžete obnovit definované soubory knihovny jako součást procesu sestavení. Ve výchozím nastavení je zakázáno chování *obnovení na sestavení.*

Povolení a testování chování obnovení na sestavení:

* Klepněte pravým tlačítkem myši na *soubor libman.json* v **Průzkumníku řešení** a z kontextové nabídky vyberte **Povolit obnovení knihoven na straně klienta v buildu.**
* Po zobrazení výzvy k instalaci balíčku NuGet klepněte na tlačítko **Ano.** Balíček [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet je přidán do projektu:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Vytvořte projekt pro potvrzení obnovení souboru LibMan. Balíček `Microsoft.Web.LibraryManager.Build` vloží Cíl MSBuild, který spouští LibMan během operace sestavení projektu.
* Projděte **si informační** kanál sestavení **výstupního** okna pro protokol aktivit LibMan:

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

Pokud je povoleno chování obnovení na sestavení, kontextová nabídka *libman.json* zobrazí možnost **Zakázat obnovení knihovny na straně klienta v sestavení.** Výběrem této možnosti `Microsoft.Web.LibraryManager.Build` odeberete odkaz na balíček ze souboru projektu. V důsledku toho knihovny na straně klienta již nejsou obnoveny v každém sestavení.

Bez ohledu na nastavení obnovení na sestavení můžete kdykoli ručně obnovit z kontextové nabídky *libman.json.* Další informace naleznete [v tématu Ruční obnovení souborů](#restore-files-manually).

### <a name="restore-files-manually"></a>Ruční obnovení souborů

Ruční obnovení souborů knihovny:

* Pro všechny projekty v řešení:
  * Klepněte pravým tlačítkem myši na název řešení v **Průzkumníku řešení**.
  * Vyberte možnost **Obnovit knihovny na straně klienta.**
* Pro konkrétní projekt:
  * Klepněte pravým tlačítkem myši na soubor *libman.json* v **Průzkumníku řešení**.
  * Vyberte možnost **Obnovit knihovny na straně klienta.**

Během operace obnovení je spuštěna:

* Ikona Centrum stavu úloh (TSC) na stavovém řádku sady Visual Studio bude animovaná a bude číst *zahájení operace obnovení*. Kliknutím na ikonu se otevře popisek se známými úkoly na pozadí.
* Zprávy budou odeslány na stavový řádek a informační kanál **Správce knihovny** v okně **Výstup.** Příklad:

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

## <a name="delete-library-files"></a>Odstranění souborů knihovny

Chcete-li provést *čistou* operaci, která odstraní soubory knihovny dříve obnovené v sadě Visual Studio:

* Klepněte pravým tlačítkem myši na soubor *libman.json* v **Průzkumníku řešení**.
* Vyberte možnost **Vyčistit knihovny na straně klienta.**

Aby se zabránilo nechtěnému odebrání souborů, které nejsou součástí knihovny, čistá operace neodstraní celé adresáře. Odebere pouze soubory, které byly zahrnuty v předchozím obnovení.

Během čisté operace je spuštěna:

* Ikona TSC na stavovém řádku sady Visual Studio bude animovaná a bude číst *spuštění operace klientských knihoven*. Kliknutím na ikonu se otevře popisek se známými úkoly na pozadí.
* Zprávy jsou odesílány na stavový řádek a informační kanál **Správce knihovny** v okně **Výstup.** Příklad:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Čistá operace pouze odstraní soubory z projektu. Soubory knihovny zůstávají v mezipaměti pro rychlejší načítání budoucích operací obnovení. Chcete-li spravovat soubory knihovny uložené v mezipaměti místního počítače, použijte [cli LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Odinstalace souborů knihovny

Jak odinstalovat soubory knihovny:

* Otevřete *soubor libman.json*.
* Umístěte stříšku `libraries` uvnitř odpovídajícího literálu objektu.
* Klikněte na ikonu žárovky, která se zobrazí na levém okraji, a vyberte **Odinstalovat \<library_name>@\<library_version>**:

  ![Možnost odinstalace kontextové nabídky knihovny](_static/uninstall-menu-option.png)

Případně můžete ručně upravit a uložit manifest LibMan *(libman.json).* [Operace obnovení](#restore-library-files) se spustí při uložení souboru. Soubory knihovny, které již nejsou definovány v *souboru libman.json,* budou z projektu odebrány.

## <a name="update-library-version"></a>Aktualizovat verzi knihovny

Kontrola aktualizované verze knihovny:

* Otevřete *soubor libman.json*.
* Umístěte stříšku `libraries` uvnitř odpovídajícího literálu objektu.
* Klikněte na ikonu žárovky, která se zobrazí na levém okraji. Najeďte **na: Vyhledat aktualizace**.

LibMan zkontroluje novější verzi knihovny, než je nainstalovaná verze. Mohou nastat následující výsledky:

* Pokud je již nainstalována nejnovější verze, zobrazí se zpráva **Bez nalezených aktualizací.**
* Pokud již není nainstalována, zobrazí se nejnovější stabilní verze.

  ![Vyhledat možnost kontextové nabídky aktualizací](_static/update-menu-option.png)

* Pokud je k dispozici novější verze, než je nainstalovaná verze, zobrazí se předběžná verze.

Chcete-li přejít na starší verzi knihovny, ručně upravte soubor *libman.json.* Po uložení souboru se [operace obnovení](#restore-library-files)LibMan :

* Odebere redundantní soubory z předchozí verze.
* Přidá nové a aktualizované soubory z nové verze.

## <a name="additional-resources"></a>Další zdroje

* <xref:client-side/libman/libman-cli>
* [Úložiště LibMan GitHub](https://github.com/aspnet/LibraryManager)

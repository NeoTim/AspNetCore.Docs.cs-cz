---
title: Použití LibMan s ASP.NET Core v aplikaci Visual Studio
author: scottaddie
description: Naučte se používat LibMan v projektu ASP.NET Core se sadou Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-vs
ms.openlocfilehash: 45f81cbc713e7e7c1f335aef49360992d2297a81
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770090"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Použití LibMan s ASP.NET Core v aplikaci Visual Studio

[Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio obsahuje integrovanou podporu pro [LibMan](xref:client-side/libman/index) v projektech ASP.NET Core, včetně:

* Podpora pro konfiguraci a spuštění operací obnovení LibMan při sestavení.
* Položky nabídky, které aktivují LibMan operace obnovení a vyčištění
* Dialogové okno Hledat pro hledání knihoven a přidávání souborů do projektu.
* Úprava podpory pro *Libman. JSON*&mdash;soubor manifestu Libman.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**

## <a name="add-library-files"></a>Přidat soubory knihovny

Soubory knihovny lze přidat do projektu ASP.NET Core dvěma různými způsoby:

1. [Použití dialogového okna přidat knihovnu na straně klienta](#use-the-add-client-side-library-dialog)
1. [Ruční konfigurace položek souboru manifestu LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Použití dialogového okna přidat knihovnu na straně klienta

Pomocí těchto kroků nainstalujete knihovnu na straně klienta:

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku projektu, do které by se měly přidat soubory. Vyberte možnost **Přidat** > **knihovnu na straně klienta**. Zobrazí se dialogové okno **přidat knihovnu na straně klienta** :

  ![Dialogové okno Přidat knihovnu na straně klienta](_static/add-library-dialog.png)

* V rozevíracím seznamu **zprostředkovatelů** vyberte poskytovatele knihovny. CDNJS je výchozí zprostředkovatel.
* Do textového pole **Knihovna** zadejte název knihovny, která se má načíst. IntelliSense nabízí seznam knihoven začínajících zadaným textem.
* Vyberte knihovnu ze seznamu technologie IntelliSense. Všimněte si, že název knihovny má příponu s `@` příponou a nejnovější stabilní verzí známou pro vybraného zprostředkovatele.
* Rozhodněte, které soubory se mají zahrnout:
  * Výběrem přepínače **Zahrnout všechny soubory knihovny** Zahrňte všechny soubory knihovny.
  * Vyberte přepínač **zvolte konkrétní soubory** a přidejte tak podmnožinu souborů knihovny. Je-li vybrán přepínač, je povolen strom pro výběr souboru. Zaškrtněte políčka nalevo od názvů souborů ke stažení.
* Zadejte složku projektu pro uložení souborů do textového pole **cílové umístění** . Jako doporučení uložte každou knihovnu do samostatné složky.

  Navrhovaná složka **umístění cíle** je založena na umístění, ze kterého byl dialog spuštěn:

  * Při spuštění z kořenového adresáře projektu:
    * *wwwroot/lib* se používá, pokud existuje *wwwroot* .
    * *lib* se používá, pokud neexistuje *wwwroot* .
  * Při spuštění ze složky projektu se použije odpovídající název složky.

  Návrh složky má příponu s názvem knihovny. Následující tabulka popisuje návrhy složek při instalaci jQuery do Razor Pages projektu.
  
  |Umístění pro spuštění                           |Navrhovaná složka      |
  |------------------------------------------|----------------------|
  |kořen projektu (pokud existuje *wwwroot* )        |*wwwroot/lib/jQuery/* |
  |kořen projektu (Pokud neexistuje *wwwroot* ) |*lib/jQuery/*         |
  |Složka *stránky* v projektu                 |*Stránky/jQuery/*       |

* Klikněte na tlačítko **instalovat** a Stáhněte soubory podle konfigurace v souboru *Libman. JSON*.
* Podrobnosti o instalaci najdete v informačním kanálu **Správce knihovny** v okně **výstup** . Příklad:

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

Všechny operace LibMan v aplikaci Visual Studio jsou založené na obsahu manifestu LibMan kořenu projektu (*LibMan. JSON*). Můžete ručně upravit *Libman. JSON* a nakonfigurovat soubory knihovny pro projekt. Po uložení souboru *Libman. JSON* obnoví aplikace Visual Studio všechny soubory knihoven.

Pro otevření *Libman. JSON* pro úpravy existují následující možnosti:

* Dvakrát klikněte na soubor *Libman. JSON* v **Průzkumník řešení**.
* V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **Spravovat knihovny na straně klienta**. **&#8224;**
* V nabídce **projektu** Visual Studio vyberte **Spravovat knihovny na straně klienta** . **&#8224;**

**&#8224;** Pokud soubor *Libman. JSON* již v kořenu projektu neexistuje, bude vytvořen s obsahem šablony výchozí položky.

Visual Studio nabízí bohatou podporu úprav JSON, jako je například zabarvení, formátování, IntelliSense a ověřování schématu. Schéma JSON manifestu LibMan se nachází na adrese [https://json.schemastore.org/libman](https://json.schemastore.org/libman).

Pomocí následujícího souboru manifestu LibMan načte soubory podle konfigurace definované ve `libraries` vlastnosti. Vysvětlení literálů objektu, které jsou definovány v `libraries` následujícím seznamu:

* Z poskytovatele CDNJS je načtena podmnožina [jQuery](https://jquery.com/) verze 3.3.1. Podmnožina je definována ve `files` vlastnosti&mdash;*jQuery. min. js*, *jQuery. js*a *jQuery. min. map*. Soubory jsou umístěny ve složce *wwwroot/lib/jQuery* projektu.
* Celá část [bootstrap](https://getbootstrap.com/) verze 4.1.3 se načte a umístí do složky *wwwroot/lib/Bootstrap* . `provider` Vlastnost literálu objektu Přepisuje hodnotu `defaultProvider` vlastnosti. LibMan načte spouštěcí soubory od poskytovatele unpkg.
* Podmnožina [Lodash](https://lodash.com/) byla schválena tělem pro řízení v rámci organizace. Soubory *lodash. js* a *lodash. js* jsou načteny z místního systému souborů v *C:\\\\Temp lodash\\*. Soubory jsou zkopírovány do složky *wwwroot/lib/lodash* projektu.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan podporuje pouze jednu verzi každé knihovny od každého poskytovatele. V souboru *Libman. JSON* se ověřování schématu nezdařilo, pokud obsahuje dvě knihovny se stejným názvem knihovny pro daného zprostředkovatele.

## <a name="restore-library-files"></a>Obnovit soubory knihovny

Chcete-li obnovit soubory knihovny z aplikace Visual Studio, musí být v kořenovém adresáři projektu platný soubor *Libman. JSON* . Obnovené soubory jsou umístěny v projektu v umístění určeném pro každou knihovnu.

Soubory knihovny lze obnovit v ASP.NET Core projektu dvěma způsoby:

1. [Obnovení souborů během sestavování](#restore-files-during-build)
1. [Ruční obnovení souborů](#restore-files-manually)

### <a name="restore-files-during-build"></a>Obnovení souborů během sestavování

LibMan může obnovit definované soubory knihovny jako součást procesu sestavení. Ve výchozím nastavení je chování funkce *obnovení při sestavení* zakázané.

Postup při povolení a testování chování při obnovení při sestavení:

* V **Průzkumník řešení** klikněte pravým tlačítkem na *Libman. JSON* a v místní nabídce vyberte **Povolit obnovení knihoven na straně klienta v sestavení** .
* Po zobrazení výzvy k instalaci balíčku NuGet klikněte na tlačítko **Ano** . Do projektu se přidá balíček NuGet [Microsoft. Web. LibraryManager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) :

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Sestavte projekt, abyste se ujistili, že dojde k obnovení souboru LibMan. `Microsoft.Web.LibraryManager.Build` Balíček vloží cíl nástroje MSBuild, který spouští LibMan během operace sestavení projektu.
* Přečtěte si kanál **sestavení** okna **výstup** pro protokol aktivit LibMan:

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

Když je chování funkce obnovení při sestavení povoleno, zobrazí se v místní nabídce *Libman. JSON* možnost **Zakázat obnovení knihoven na straně klienta při sestavení** . Výběrem této možnosti se `Microsoft.Web.LibraryManager.Build` odebere odkaz na balíček ze souboru projektu. V důsledku toho se knihovny na straně klienta již v každém sestavení neobnovují.

Bez ohledu na nastavení obnovení při sestavení můžete kdykoli obnovit ručně z kontextové nabídky *Libman. JSON* . Další informace najdete v tématu [Ruční obnovení souborů](#restore-files-manually).

### <a name="restore-files-manually"></a>Ruční obnovení souborů

Ruční obnovení souborů knihovny:

* Pro všechny projekty v řešení:
  * Klikněte pravým tlačítkem myši na název řešení v **Průzkumník řešení**.
  * Vyberte možnost **obnovit knihovny na straně klienta** .
* Pro určitý projekt:
  * V **Průzkumník řešení**klikněte pravým tlačítkem na soubor *Libman. JSON* .
  * Vyberte možnost **obnovit knihovny na straně klienta** .

I když je spuštěná operace obnovení:

* Ikona Centrum stavu úloh (TSC) na stavovém řádku sady Visual Studio bude animovaná a spustí se *operace obnovení*. Po kliknutí na ikonu se zobrazí popis známých úloh na pozadí.
* Zprávy se odešlou do stavového řádku a do informačního kanálu **Správce knihovny** v okně **výstup** . Příklad:

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

## <a name="delete-library-files"></a>Odstranit soubory knihovny

Chcete-li provést operaci *Vyčištění* , která odstraní soubory knihovny, které byly dříve obnoveny v aplikaci Visual Studio:

* V **Průzkumník řešení**klikněte pravým tlačítkem na soubor *Libman. JSON* .
* Vyberte možnost **vyčistit knihovny na straně klienta** .

Aby nedocházelo k neúmyslnému odebrání souborů bez knihoven, operace čištění neodstraní celé adresáře. Odstraní jenom soubory, které byly zahrnuté do předchozího obnovení.

I když je spuštěná operace čištění:

* Ikona čítače TSC na stavovém řádku sady Visual Studio bude animovaná a spustí se *operace čtení klientských knihoven*. Po kliknutí na ikonu se zobrazí popis známých úloh na pozadí.
* Zprávy jsou odesílány do stavového řádku a kanálu **Správce knihovny** v okně **výstup** . Příklad:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Operace čištění odstraní pouze soubory z projektu. Soubory knihovny zůstávají v mezipaměti, aby bylo možné rychlejší načítání budoucích operací obnovení. Chcete-li spravovat soubory knihovny uložené v mezipaměti místního počítače, použijte rozhraní příkazového [řádku LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Odinstalace souborů knihovny

Odinstalace souborů knihovny:

* Otevřete *Libman. JSON*.
* Umístit blikající kurzor uvnitř odpovídajícího `libraries` literálu objektu.
* Klikněte na ikonu žárovky, která se zobrazí na levém okraji, a vyberte **odinstalovat \<library_name\<> @ library_version>**:

  ![Možnost místní nabídky pro odinstalaci knihovny](_static/uninstall-menu-option.png)

Alternativně můžete ručně upravit a uložit manifest LibMan (*LibMan. JSON*). [Operace obnovení](#restore-library-files) se spustí při uložení souboru. Soubory knihoven, které již nejsou definovány v souboru *Libman. JSON* , jsou odebrány z projektu.

## <a name="update-library-version"></a>Aktualizovat verzi knihovny

Vyhledání aktualizované verze knihovny:

* Otevřete *Libman. JSON*.
* Umístit blikající kurzor uvnitř odpovídajícího `libraries` literálu objektu.
* Klikněte na ikonu žárovky, která se zobrazí na levém okraji. Najeďte myší na **kontrolu aktualizací**.

LibMan vyhledá verzi knihovny novější než verze nainstalovaná. Může dojít k následujícím výsledkům:

* Pokud je už nainstalovaná nejnovější verze, nezobrazí se žádná zpráva o **nalezených aktualizacích** .
* Pokud ještě není nainstalovaná, zobrazí se nejnovější stabilní verze.

  ![Možnost místní nabídky vyhledat aktualizace](_static/update-menu-option.png)

* Pokud je k dispozici předběžná verze novější než nainstalovaná verze, zobrazí se předběžná verze.

Pokud chcete downgradovat na starší verzi knihovny, ručně upravte soubor *Libman. JSON* . Po uložení souboru se LibMan [operace obnovení](#restore-library-files):

* Odstraní nadbytečné soubory z předchozí verze.
* Přidá nové a aktualizované soubory z nové verze.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:client-side/libman/libman-cli>
* [Úložiště GitHub LibMan](https://github.com/aspnet/LibraryManager)

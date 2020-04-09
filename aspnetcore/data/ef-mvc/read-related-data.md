---
title: 'Kurz: Čtení souvisejících dat - ASP.NET MVC s EF Core'
description: V tomto kurzu budete číst a zobrazovat související data -- to znamená data, která rozhraní entity načte do navigačních vlastností.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: a6e63723101ab09219db81ee9796c3938a612226
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657106"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Kurz: Čtení souvisejících dat - ASP.NET MVC s EF Core

V předchozím kurzu jste dokončili datový model školy. V tomto kurzu budete číst a zobrazovat související data -- to znamená data, která rozhraní entity načte do navigačních vlastností.

Na následujících obrázcích jsou uvedeny stránky, se kterými budete pracovat.

![Stránka Index kurzů](read-related-data/_static/courses-index.png)

![Stránka Index instruktorů](read-related-data/_static/instructors-index.png)

V tomto kurzu jste:

> [!div class="checklist"]
> * Přečtěte si, jak načíst související data
> * Vytvoření stránky Kurzů
> * Vytvoření stránky Instruktoři
> * Informace o explicitním načítání

## <a name="prerequisites"></a>Požadavky

* [Vytvoření složitého datového modelu](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Přečtěte si, jak načíst související data

Existuje několik způsobů, jak software orm (Object-Relational Mapping), jako je entity Framework, může načíst související data do navigačních vlastností entity:

* Dychtivé nakládání. Při čtení entity se spolu s ní načítají související data. To obvykle vede k dotazu na jedno spojení, který načte všechna data, která je potřeba. Můžete zadat dychtivé načítání v `Include` core `ThenInclude` entity framework pomocí a metody.

  ![Příklad dychtivénačítání](read-related-data/_static/eager-loading.png)

  Můžete načíst některá data v samostatných dotazech a EF "opravuje" navigační vlastnosti.  To znamená EF automaticky přidá samostatně načtené entity, kde patří do navigačních vlastností dříve načtených entit. Pro dotaz, který načítá související data, můžete použít metodu `Load` namísto metody, `ToList` `Single`která vrací seznam nebo objekt, například nebo .

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

* Explicitní načítání. Při prvním čtení entity se nenačtou související data. Napíšete kód, který načte související data, pokud je to potřeba. Stejně jako v případě dychtivé načítání se samostatnými dotazy, explicitní načítání výsledky ve více dotazů odeslaných do databáze. Rozdíl je, že s explicitní načítání kód určuje navigační vlastnosti, které mají být načteny. V entity Framework Core 1.1 `Load` můžete použít metodu k explicitnínačítání. Příklad:

  ![Příklad explicitního načítání](read-related-data/_static/explicit-loading.png)

* Opožděné načítání. Při prvním čtení entity se nenačtou související data. Při prvním pokusu o přístup k navigační vlastnosti jsou však automaticky načtena data požadovaná pro tuto vlastnost navigace. Dotaz je odeslán do databáze pokaždé, když se pokusíte získat data z navigační vlastnosti poprvé. Entity Framework Core 1.0 nepodporuje opožděné načítání.

### <a name="performance-considerations"></a>Otázky výkonu

Pokud víte, že potřebujete související data pro každou načtenou entitu, eager loading často nabízí nejlepší výkon, protože jeden dotaz odeslaný do databáze je obvykle efektivnější než samostatné dotazy pro každou načtenou entitu. Předpokládejme například, že každé oddělení má deset souvisejících kurzů. Dychtivé načítání všech souvisejících dat by mělo za následek pouze jeden dotaz (spojení) a jednu odezvu do databáze. Samostatný dotaz na kurzy pro každé oddělení by měl za následek jedenáct zpátečních cest do databáze. Extra zpáteční cesty do databáze jsou obzvláště škodlivé pro výkon, když je vysoká latence.

Na druhou stranu v některých scénářích samostatné dotazy je efektivnější. Eager načítání všech souvisejících dat v jednom dotazu může způsobit velmi složité spojení, které mají být generovány, které SQL Server nelze efektivně zpracovat. Nebo pokud potřebujete přístup k navigačním vlastnostem entity pouze pro podmnožinu sady entit, které zpracováváte, samostatné dotazy mohou fungovat lépe, protože dychtivé načítání všeho předem by načetlo více dat, než potřebujete. Pokud je výkon kritický, je nejlepší otestovat výkon oběma směry, aby bylo možné provést nejlepší volbu.

## <a name="create-a-courses-page"></a>Vytvoření stránky Kurzů

Entita Kurz obsahuje navigační vlastnost, která obsahuje entitu oddělení oddělení, ke kterému je kurz přiřazen. Chcete-li zobrazit název přiřazeného oddělení v seznamu kurzů, musíte získat vlastnost Name z `Course.Department` entity Oddělení, která je ve vlastnosti navigace.

Vytvořte řadič s názvem CoursesController pro typ entity kurzu, pomocí stejných možností pro **MVC řadič se zobrazeními, pomocí** entity framework scaffolder, které jste dříve pro řadič Students, jak je znázorněno na následujícím obrázku:

![Ovladač přidat kurzy](read-related-data/_static/add-courses-controller.png)

Otevřete *CoursesController.cs* `Index` a zkontrolujte metodu. Automatické generování uživatelského čípku určileager `Department` zatížení pro `Include` navigační vlastnost pomocí metody.

`Index` Nahraďte metodu následujícím kódem, který `IQueryable` používá vhodnější název`courses` pro `schoolContext`entity, které vrací entity kurzu ( místo ):

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Otevřete *zobrazení/kurzy/index.cshtml* a nahraďte kód šablony následujícím kódem. Změny jsou zvýrazněny:

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

V kódu slepeného kódu jste provedli následující změny:

* Nadpis byl změněn z rejstříku na Kurzy.

* Byl přidán sloupec **Číslo,** který zobrazuje hodnotu vlastnosti. `CourseID` Ve výchozím nastavení nejsou primární klíče skládané, protože obvykle nemají pro koncové uživatele smysl. V tomto případě je však primární klíč smysluplný a chcete jej zobrazit.

* Sloupec **Oddělení** se změnil tak, aby se zobrazil název oddělení. Kód zobrazuje `Name` vlastnost entity Oddělení, která je `Department` načtena do vlastnosti navigace:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Spusťte aplikaci a vyberte kartu **Kurzy,** abyste viděli seznam s názvy oddělení.

![Stránka Index kurzů](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Vytvoření stránky Instruktoři

V této části vytvoříte řadič a zobrazení pro entitu Instruktor, abyste zobrazili stránku Instruktoři:

![Stránka Index instruktorů](read-related-data/_static/instructors-index.png)

Tato stránka čte a zobrazuje související data následujícími způsoby:

* Seznam instruktorů zobrazuje související data z entity OfficeAssignment. Entity Instruktor a OfficeAssignment jsou ve vztahu 1:Nula nebo jedna. Budete používat dychtivé načítání pro officeassignment entity. Jak bylo vysvětleno dříve, eager načítání je obvykle efektivnější, když potřebujete související data pro všechny načtené řádky primární tabulky. V takovém případě chcete zobrazit přiřazení kanceláře pro všechny zobrazené instruktory.

* Když uživatel vybere instruktora, zobrazí se související entity kurzu. Instruktor a samozřejmě subjekty jsou ve vztahu n-to-n. Budete používat dychtivé načítání pro entity kurzu a jejich související entity oddělení. V takovém případě mohou být samostatné dotazy efektivnější, protože kurzy potřebujete pouze pro vybraného instruktora. Tento příklad však ukazuje, jak použít dychtivé načítání pro navigační vlastnosti v rámci entit, které jsou samy v navigačnívlastnosti.

* Když uživatel vybere kurz, zobrazí se související data ze sady entit Zápisy. Entity Kurz a Zápis jsou ve vztahu 1:N. Budete používat samostatné dotazy pro entity zápisu a jejich související studentské entity.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Vytvoření modelu zobrazení pro zobrazení indexu instruktora

Stránka Instruktoři zobrazuje data ze tří různých tabulek. Proto vytvoříte model zobrazení, který obsahuje tři vlastnosti, z nichž každá obsahuje data pro jednu z tabulek.

Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Vytvoření ovladače instruktora a zobrazení

Vytvořte řadič instruktorů s akcemi ef pro čtení a zápis, jak je znázorněno na následujícím obrázku:

![Přidat ovladač Instruktoři](read-related-data/_static/add-instructors-controller.png)

Otevřete *InstructorsController.cs* a přidejte příkaz using pro obor názvů ViewModels:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Nahraďte metodu Index následujícím kódem, který probíhá dychtivé načítání souvisejících dat, a vložte je do modelu zobrazení.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

Metoda přijímá volitelná data`id`trasy ( )`courseID`a parametr řetězce dotazu ( ), který poskytuje hodnoty ID vybraného instruktora a vybraného kurzu. Parametry jsou poskytovány hypertextovými odkazy **Select** na stránce.

Kód začíná vytvořením instance modelu zobrazení a vložením seznamu instruktorů. Kód určuje nedočkavé načítání vlastností `Instructor.OfficeAssignment` a `Instructor.CourseAssignments` navigace. V `CourseAssignments` rámci vlastnosti je `Course` načtena vlastnost `Enrollments` a `Department` v rámci toho `Enrollment` jsou `Student` načteny vlastnosti a v rámci každé entity je načtena vlastnost.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Vzhledem k tomu, že zobrazení vždy vyžaduje entitu OfficeAssignment, je efektivnější načíst ve stejném dotazu. Entity kurzu jsou vyžadovány, když je na webové stránce vybrán instruktor, takže jeden dotaz je lepší než více dotazů pouze v případě, že se stránka zobrazuje častěji s vybraným kurzem než bez.

Kód se `CourseAssignments` opakuje `Course` a protože potřebujete `Course`dvě vlastnosti z . První řetězec `ThenInclude` volání `CourseAssignment.Course`získá `Course.Enrollments`, `Enrollment.Student`a .

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

V tomto okamžiku v `ThenInclude` kódu jiný by `Student`pro navigační vlastnosti , které nepotřebujete. Volání `Include` však `Instructor` začíná znovu vlastnostmi, takže musíte znovu projít `Course.Department` řetězem, tentokrát zadáním místo `Course.Enrollments`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Následující kód se spustí, když byl vybrán instruktor. Vybraný instruktor je načten ze seznamu instruktorů v modelu zobrazení. `Courses` Vlastnost modelu zobrazení je pak načtena s entity course `CourseAssignments` z navigační vlastnosti tohoto instruktora.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

Metoda `Where` vrátí kolekci, ale v tomto případě kritéria předaná této metodě za následek pouze jeden instruktor entity vrácena. Metoda `Single` převede kolekci na jednu entitu Instruktor, která `CourseAssignments` umožňuje přístup k vlastnosti této entity. Vlastnost `CourseAssignments` obsahuje `CourseAssignment` entity, ze kterých chcete `Course` pouze související entity.

Metodu `Single` použijete v kolekci, když víte, že kolekce bude mít pouze jednu položku. Single Metoda vyvolá výjimku, pokud kolekce předána je prázdný nebo pokud existuje více než jednu položku. Alternativou `SingleOrDefault`je , která vrátí výchozí hodnotu (null v tomto případě), pokud je kolekce prázdná. V tomto případě by však stále vést k výjimce (z pokusu `Courses` o nalezení vlastnosti na odkaz null) a zpráva o výjimce by méně jasně uvést příčinu problému. Při volání `Single` metody můžete také předat v Where podmínku `Where` namísto volání metody samostatně:

```csharp
.Single(i => i.ID == id.Value)
```

Namísto:

```csharp
.Where(i => i.ID == id.Value).Single()
```

Dále, pokud byl kurz vybrán, vybraný kurz je načten ze seznamu kurzů v modelu pohledu. Potom `Enrollments` je vlastnost modelu zobrazení načtena s entity Zápisu `Enrollments` z navigační vlastnosti tohoto kurzu.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Změna zobrazení indexu instruktora

V *části Zobrazení/Instruktoři/Index.cshtml*nahraďte kód šablony následujícím kódem. Změny jsou zvýrazněny.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

V existujícím kódu jste provedli následující změny:

* Třídu modelu `InstructorIndexData`byla změněna na .

* Název stránky byl změněn z **Rejstříku** na **Instruktoři**.

* Přidán sloupec **Office,** který se zobrazí `item.OfficeAssignment.Location` pouze v případě, `item.OfficeAssignment` že nemá hodnotu null. (Vzhledem k tomu, že se jedná o vztah 1:Nula nebo jeden, nemusí existovat související entita OfficeAssignment.)

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Byl přidán sloupec **Kurzy,** který zobrazuje kurzy vyučované jednotlivými instruktory. Další informace naleznete v části [Explicitní přechod řádku](xref:mvc/views/razor#explicit-line-transition) v článku syntaxe razor.

* Přidán kód, který `class="success"` dynamicky přidává do `tr` prvku vybraného instruktora. Tím nastavíte barvu pozadí pro vybraný řádek pomocí třídy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Byl přidán nový hypertextový odkaz s názvem **Vybrat** bezprostředně před ostatními odkazy v každém řádku, což způsobí, že id vybraného instruktora bude odesláno metodě. `Index`

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Spusťte aplikaci a vyberte kartu **Instruktoři.** Na stránce se zobrazí vlastnost Umístění souvisejících entit OfficeAssignment a prázdná buňka tabulky, pokud neexistuje žádná související entita OfficeAssignment.

![Stránka Index instruktorů není vybrána.](read-related-data/_static/instructors-index-no-selection.png)

V souboru *Zobrazení/Instruktoři/Index.cshtml* za prvek uzavírací tabulky (na konci souboru) přidejte následující kód. Tento kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Tento kód `Courses` přečte vlastnost modelu zobrazení pro zobrazení seznamu kurzů. Poskytuje také **select** hypertextový odkaz, který odešle ID vybraného kurzu na metodu `Index` akce.

Aktualizujte stránku a vyberte instruktora. Nyní se zobrazí mřížka, která zobrazuje kurzy přiřazené vybranému instruktorovi, a pro každý kurz se zobrazí název přiřazeného oddělení.

![Vybraná stránka instruktorů instruktorů instruktorů instruktorů instruktorů instruktorů instruktorů instruktorů instruktor](read-related-data/_static/instructors-index-instructor-selected.png)

Po bloku kódu, který jste právě přidali, přidejte následující kód. Zobrazí se seznam studentů, kteří jsou zapsáni do kurzu, když je tento kurz vybrán.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Tento kód přečte Vlastnost Enrollments modelu zobrazení, aby se zobrazil seznam studentů zapsaných v kurzu.

Znovu aktualizujte stránku a vyberte instruktora. Poté vyberte kurz, který zobrazí seznam zapsaných studentů a jejich známky.

![Instruktoři Index stránky instruktor a kurz vybrán](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>O explicitním načítání

Při načtení seznamu instruktorů v *InstructorsController.cs*, jste `CourseAssignments` zadali dychtivé načítání pro navigační vlastnost.

Předpokládejme, že jste očekávali, že uživatelé budou jen zřídka chtít zobrazit zápisy do vybraného instruktora a kurzu. V takovém případě můžete chtít načíst data zápisu pouze v případě, že je požadováno. Chcete-li zobrazit příklad, jak provést `Index` explicitní načítání, nahraďte metodu následujícím kódem, který odebere dychtivé načítání pro registrace a načte tuto vlastnost explicitně. Změny kódu jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Nový kód klesne *ThenInclude* volání metody pro zápis dat z kódu, který načítá entity instruktora. To také `AsNoTracking`kapky .  Pokud je vybrán instruktor a kurz, zvýrazněný kód načte entity Zápisu pro vybraný kurz a entity studenta pro každou registraci.

Spusťte aplikaci, přejděte na stránku Index instruktorů a neuvidíte žádný rozdíl v tom, co se na stránce zobrazuje, i když jste změnili způsob načítání dat.

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Zjistili, jak načíst související data
> * Stránka Vytvoření kurzů
> * Vytvoření stránky Instruktoři
> * Informace o explicitním načítání

Přejdete k dalšímu kurzu, kde se dozvíte, jak aktualizovat související data.

> [!div class="nextstepaction"]
> [Aktualizace souvisejících dat](update-related-data.md)

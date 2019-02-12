---
title: 'Kurz: Čtení souvisejících dat – ASP.NET MVC s EF Core'
description: V tomto kurzu budete čtení a zobrazení souvisejících dat – to znamená, že data, která načte Entity Framework do navigační vlastnosti.
author: rick-anderson
ms.author: tdykstra
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 73e225c2cd6d9f88079c54115cccad48f43d7d0c
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103043"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Kurz: Čtení souvisejících dat – ASP.NET MVC s EF Core

V předchozím kurzu jste dokončili školní datového modelu. V tomto kurzu budete čtení a zobrazení souvisejících dat – to znamená, že data, která načte Entity Framework do navigační vlastnosti.

Na následujících obrázcích stránky, kterou budete pracovat.

![Kurzy indexová stránka](read-related-data/_static/courses-index.png)

![Instruktoři indexová stránka](read-related-data/_static/instructors-index.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zjistěte, jak načíst související data
> * Vytvoření stránky kurzy
> * Vytvoření stránky Instruktoři
> * Další informace o explicitní načtení

## <a name="prerequisites"></a>Požadavky

* [Vytvoření složitějšího datového modelu pro webovou aplikaci ASP.NET Core MVC s EF Core](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Zjistěte, jak načíst související data

Existuje několik způsobů objektově-relační mapování (ORM) určené softwaru, jako je rozhraní Entity Framework mohou načíst související data do navigační vlastnosti entity:

* Předběžné načítání. Při čtení entity související data načíst společně. Obvykle v důsledku jednoho spojení dotaz, který zkopíruje všechna data, který je nezbytný. Předběžné načítání zadáte v Entity Framework Core pomocí `Include` a `ThenInclude` metody.

  ![Předběžné načítání příklad](read-related-data/_static/eager-loading.png)

  Můžete načíst některá data v samostatné dotazy a EF termín "opravy" navigační vlastnosti.  To znamená EF automaticky přidá samostatně načtený entity, které patří, kde ve vlastnosti navigace entit dříve načtená. Pro dotaz, který načte související data, můžete použít `Load` metoda místo metodu, která vrátí seznam nebo objekt, jako například `ToList` nebo `Single`.

  ![Příklad samostatné dotazy](read-related-data/_static/separate-queries.png)

* Explicitní načtení. Pokud entita je nejdřív přečíst, související data nebude načten. Můžete napsat kód, který načte související data, pokud je to potřeba. Stejně jako v případě eager načítání s samostatné dotazy explicitní načítání více dotazů za následek odeslán do databáze. Rozdíl je, že s explicitní načtení, určuje kód navigačních vlastností, které mají být načteny. V Entity Framework Core 1.1 můžete použít `Load` metodu explicitní načtení. Příklad:

  ![Příklad explicitní načtení](read-related-data/_static/explicit-loading.png)

* Opožděné načtení. Pokud entita je nejdřív přečíst, související data nebude načten. Ale při prvním pokusu o přístup k vlastnosti navigace data požadovaná pro tuto navigační vlastnost je automaticky načte. Bude odeslán dotaz do databáze při každém pokusu o získání dat z navigační vlastnost poprvé. Entity Framework Core 1.0 nepodporuje opožděné načtení.

### <a name="performance-considerations"></a>Důležité informace o výkonu

Pokud víte, že budete potřebovat pro každou entitu načíst související data, nemůžou dočkat, až načítání často nabízí nejlepší výkon, protože jednoho dotazu odeslaného do databáze je obvykle mnohem efektivnější než samostatné dotazy pro každou entitu načíst. Předpokládejme například, že každé oddělení má deset související kurzy. Předběžné načítání všechna související data výsledkem budou pouze jednou (spojení) dotazu a jedinou odezvy k databázi. Samostatný dotaz kurzy pro každé oddělení způsobí jedenáct zpátečních cest k databázi. Další zpátečních cest k databázi jsou zvlášť ztráty výkonu, když má vysokou latenci.

Na druhé straně v některých případech je efektivnější samostatné dotazy. Předběžné načítání všechna související data v jednom dotazu může vést k velmi složité spojení být vytvořen, kterou SQL Server nemůže zpracovat efektivně. Nebo pokud potřebujete přístup k entity navigační vlastnosti pouze pro dílčí sadu entit, které jste zpracování, samostatné dotazy může lépe provést, protože nemůžou dočkat, až načítání všechno, co ještě před zahájením by byl načten více dat, než potřebujete. Pokud je nejdůležitější výkon, je nejvhodnější pro testování výkonu oba způsoby, aby bylo možné správně se rozhodnout.

## <a name="create-a-courses-page"></a>Vytvoření stránky kurzy

Kurz entita obsahuje navigační vlastnost obsahující tuto entitu oddělení oddělení, která je přiřazena kurzu. Chcete-li zobrazit název přiřazený oddělení v seznamu kurzů, je potřeba získat název vlastnosti z oddělení entity, která je v `Course.Department` navigační vlastnost.

Vytvořit řadič CoursesController pro typ entity kurz pomocí stejných možností pro **kontroler MVC se zobrazeními, s použitím Entity Framework** generátor, který jste provedli dříve pro studenty řadič, jak je znázorněno Následující obrázek:

![Přidat kontroler kurzy](read-related-data/_static/add-courses-controller.png)

Otevřít *CoursesController.cs* a prozkoumejte `Index` metody. Automatické generování uživatelského rozhraní má zadanou předběžné načítání pro `Department` navigační vlastnost s použitím `Include` metody.

Nahradit `Index` metodu s následujícím kódem, který používá více vhodný název pro `IQueryable` , který vrací kurz entity (`courses` místo `schoolContext`):

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Otevřít *Views/Courses/Index.cshtml* a nahraďte kód šablony následujícím kódem. Změny jsou zvýrazněny:

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Automaticky generovaný kód, které jste udělali následující změny:

* Změnit záhlaví od indexu ke kurzům.

* Přidá **číslo** sloupec, který ukazuje `CourseID` hodnotu vlastnosti. Ve výchozím nastavení nejsou automaticky generovaný primární klíče, protože jsou obvykle nemá význam pro koncové uživatele. Ale v tomto případě má smysl primární klíč a chcete zobrazit.

* Změnit **oddělení** sloupec, který se zobrazí název oddělení. Zobrazení kódu `Name` vlastnost entity oddělení, který je načten do `Department` navigační vlastnost:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Spusťte aplikaci a vyberte **kurzy** kartu pro zobrazení seznamu s názvy oddělení.

![Kurzy indexová stránka](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Vytvoření stránky Instruktoři

V této části vytvoříte kontroler a zobrazení entity kurzů vedených mohla zobrazit na stránce Instruktoři:

![Instruktoři indexová stránka](read-related-data/_static/instructors-index.png)

Tato stránka načte a zobrazí související data následujícími způsoby:

* Seznam Instruktoři zobrazí související data z OfficeAssignment entity. Entity instruktorem a OfficeAssignment jsou ve vztahu k nule nebo jednom. Předběžné načítání budete používat pro OfficeAssignment entity. Jak jsme vysvětlili výše, předběžné načítání je obvykle mnohem efektivnější, když potřebujete související data pro všechny načtené řádky v tabulce primární. V takovém případě budete chtít zobrazit přiřazení office pro všechny zobrazené Instruktoři.

* Když uživatel vybere instruktorem, se zobrazí související entity kurzu. Entity instruktorem a kurzu jsou v relaci m: m. Předběžné načítání budete používat pro kurz entit a jejich souvisejících entit oddělení. Samostatné dotazy v tomto případě může být mnohem efektivnější, protože potřebujete jenom pro vybrané kurzů vedených kurzů. Však tento příklad ukazuje způsob použití předběžné načítání pro navigační vlastnosti v rámci entity, které představují samy o sobě v navigační vlastnosti.

* Když uživatel vybere kurzu, zobrazí se související data ze sady entit registrace. Kurz a registraci entity jsou v vztah jeden mnoho. Pro registraci a jejich souvisejících entit Student použijete samostatné dotazy.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Vytvoření modelu zobrazení pro zobrazení indexu instruktorem

Na stránce Instruktoři zobrazuje data ze tří různých tabulek. Proto vytvoříte zobrazení modelu, který obsahuje tři vlastnosti každé obsahující data pro jednu z tabulek.

V *SchoolViewModels* složku, vytvořte *InstructorIndexData.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Vytvoření kontroleru instruktorem a zobrazení

Vytvoření řadiče Instruktoři s akcemi čtení/zápisu EF, jak je znázorněno na následujícím obrázku:

![Přidat kontroler Instruktoři](read-related-data/_static/add-instructors-controller.png)

Otevřít *InstructorsController.cs* a přidejte sadu pomocí příkazu pro modely ViewModels obor názvů:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Metoda Index nahraďte následující kód, který se nemůžou dočkat, až načítání souvisejících dat a vložit ho do modelu zobrazení.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

Metoda přijímá data volitelné trasy (`id`) a parametru řetězce dotazu (`courseID`), které poskytují hodnoty ID vybrané instruktorem a vybrané kurzu. Parametry jsou poskytovány **vyberte** hypertextové odkazy na stránky.

Vytváření instance zobrazení modelu a jeho uvedením seznamu instruktorů začíná kód. Předběžné načítání pro Určuje kód `Instructor.OfficeAssignment` a `Instructor.CourseAssignments` navigační vlastnosti. V rámci `CourseAssignments` vlastnost, `Course` vlastnosti je načtena a v rámci, která je `Enrollments` a `Department` vlastnosti jsou načtena a v každé `Enrollment` entity `Student` načtena vlastnost.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Protože zobrazení vždy vyžaduje OfficeAssignment entity, je efektivnější pro načtení, které ve stejném dotazu. Kurz entity jsou požadovány při výběru instruktorem na webové stránce, takže pomocí jediného dotazu je lepší než více dotazů jenom v případě, že na stránce se zobrazí častěji kurzu vybrali než bez.

Kód se opakuje `CourseAssignments` a `Course` vzhledem k tomu, že budete potřebovat dvě vlastnosti z `Course`. První řetězec `ThenInclude` volá získá `CourseAssignment.Course`, `Course.Enrollments`, a `Enrollment.Student`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

V tomto bodě v kódu jiné `ThenInclude` budou platit pro navigační vlastnosti `Student`, které nepotřebujete. Ale volání `Include` začne přehrávat znovu, s `Instructor` vlastnosti, takže budete muset projít řetězu znovu tento čas zadání `Course.Department` místo `Course.Enrollments`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Následující kód provede při instruktorem byla vybrána. Vybrané kurzů vedených je načten ze seznamu školitelů v modelu zobrazení. Model zobrazení `Courses` vlastnost je pak načten pomocí kurzu entity z této kurzů vedených `CourseAssignments` navigační vlastnost.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

`Where` Metoda vrátí kolekci, ale v tomto případě kritéria předat výsledek metody v pouze jednu entitu kurzů vedených se vrací. `Single` Metoda převede kolekci na jednu entitu instruktorem, která umožňuje přístup k dané entity `CourseAssignments` vlastnost. `CourseAssignments` Obsahuje vlastnost `CourseAssignment` entity, z nichž chcete pouze související `Course` entity.

Můžete použít `Single` metody na kolekci, když víte, kolekce budou mít pouze jednu položku. Jedinou metodu vyvolá výjimku, pokud do něho předaný kolekce je prázdná, nebo pokud existuje více než jednu položku. Alternativou je `SingleOrDefault`, která vrátí výchozí hodnoty (null v tomto případě) Pokud kolekce je prázdná. Ale v tomto případě bude stále výsledkem výjimku (z pokusu o nalezení `Courses` vlastnost na hodnotu Null), a zpráva o výjimce by méně jasně ukazovat na příčinu problému. Při volání `Single` metodu, můžete také předat Where podmínku namísto volání metody `Where` metoda samostatně:

```csharp
.Single(i => i.ID == id.Value)
```

Namísto:

```csharp
.Where(i => i.ID == id.Value).Single()
```

Dále pokud jste vybrali kurz, vybrané kurzu se načte z seznam kurzů v zobrazení modelu. Potom zobrazení modelu `Enrollments` s registrací entity z tohoto kurzu je načtena vlastnost `Enrollments` navigační vlastnost.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Úprava zobrazení indexu instruktorem

V *Views/Instructors/Index.cshtml*, nahraďte kód šablony následujícím kódem. Změny jsou zvýrazněné.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

Stávající kód, které jste udělali následující změny:

* Změnit třídu modelu `InstructorIndexData`.

* Změnil se název stránky z **Index** k **Instruktoři**.

* Přidá **Office** sloupec, který zobrazuje `item.OfficeAssignment.Location` pouze tehdy, pokud `item.OfficeAssignment` není null. (Protože je to vztah jeden: nula nebo 1, nemusí být související entita OfficeAssignment.)

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Přidá **kurzy** sloupec, který zobrazuje kurzy vedené jednotlivých kurzů vedených. Zobrazit [explicitní přechod na řádek s `@:` ](xref:mvc/views/razor#explicit-line-transition-with-) Další informace o tuto syntaxi razor.

* Přidání kódu, která dynamicky přidá `class="success"` k `tr` element vybrané instruktorem. Tím se nastaví barvu pozadí vybraného řádku pomocí Bootstrap třídy.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Přidat nový popisek hypertextový odkaz **vyberte** bezprostředně před další odkazy na každém řádku, což způsobí, že ID vybraných kurzů vedených k odeslání do `Index` metody.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Spusťte aplikaci a vyberte **Instruktoři** kartu. Na stránce zobrazí vlastnost umístění související entity OfficeAssignment a na prázdné tabulce buňku, když není žádná související entita OfficeAssignment.

![Instruktoři indexovou stránku, kterou nevybere](read-related-data/_static/instructors-index-no-selection.png)

V *Views/Instructors/Index.cshtml* soubor po zavření tabulky prvku (na konci souboru), přidejte následující kód. Tento kód zobrazí seznam kurzů související s instruktorem, pokud je vybrána instruktorem.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Tento kód čte `Courses` vlastnost model zobrazení zobrazíte seznam kurzů. Poskytuje také **vyberte** hypertextový odkaz, který odesílá ID vybrané kurz `Index` metody akce.

Aktualizujte stránku a vybrat instruktorem. Nyní uvidíte tabulku, která zobrazuje kurzy přiřazen k vybrané instruktorem a jednotlivých kurzů se zobrazí název přiřazený oddělení.

![Instruktoři Index stránky kurzů vedených vybrané](read-related-data/_static/instructors-index-instructor-selected.png)

Za blok kódu, který jste právě přidali přidejte následující kód. Zobrazí seznam studentů, kteří se zaregistrují v kurzu při výběru tohoto kurzu.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Tento kód načte vlastnost registrace modelu zobrazení zobrazíte seznam studentů zaregistrovaný do kurzu.

Znovu aktualizujte stránku a vybrat instruktorem. Vyberte kurzu zobrazíte seznam registrovaná studentů a jejich kvality.

![Kurzů vedených instruktory Index stránky a vybrali kurzu](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>O explicitní načtení

Když jste získali seznam školitelů v *InstructorsController.cs*, jste zadali pro předběžné načítání `CourseAssignments` navigační vlastnost.

Předpokládejme, že jste očekávali uživatelům jen zřídka byste rádi viděli registrací ve vybrané instruktorem a kurzu. V takovém případě můžete chtít načíst data registrací. pouze v případě, že je požadováno. Chcete-li zobrazit příklad toho, jak provést explicitní načtení, nahraďte `Index` metody následující kód, který odebere předběžné načítání pro registraci a načte vlastnosti explicitně. Změny kódu jsou zvýrazněné.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Nový kód klesne *ThenInclude* metoda se volá pro zápis dat z kódu, která načte entity instruktorem. Pokud se vybere instruktorem a kurz, zvýrazněný kód načte registrace pro vybraný kurz a Student entit pro každou registrace.

Spustit aplikaci, přejděte na stránku Instruktoři Index a zobrazí obsah zobrazený na stránce, nijak neliší, i když jste změnili, jak načíst data.

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zjistili jste, jak načíst související data
> * Vytvoření stránky kurzy
> * Vytvoří stránku Instruktoři
> * Dozvěděli jste se o explicitní načtení

Přejděte k dalším článku se naučíte, jak aktualizovat související data.
> [!div class="nextstepaction"]
> [Aktualizace souvisejících dat](update-related-data.md)

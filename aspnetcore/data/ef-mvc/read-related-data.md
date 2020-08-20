---
title: 'Kurz: čtení souvisejících dat – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která Entity Framework načíst do vlastností navigace.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: f2b4fd9fb1e328882583536b704d516955343417
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629454"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Kurz: čtení souvisejících dat – ASP.NET MVC pomocí EF Core

V předchozím kurzu jste dokončili model školních dat. V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která Entity Framework načíst do vlastností navigace.

Následující ilustrace znázorňují stránky, se kterými budete pracovat.

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

V tomto kurzu jste:

> [!div class="checklist"]
> * Naučte se načítat související data
> * Vytvoření stránky kurzů
> * Vytvoření stránky instruktory
> * Informace o explicitním načítání

## <a name="prerequisites"></a>Předpoklady

* [Vytvoření složitého datového modelu](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Naučte se načítat související data

Existuje několik způsobů, jak může software pro mapování relačních dat (ORM), jako je například Entity Framework, načítat související data do navigačních vlastností entity:

* Eager načítání. Když se entita přečte, načtou se spolu s ní související data. To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data. Eager načítání můžete zadat v Entity Framework Core pomocí `Include` `ThenInclude` metod a.

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)

  Některá data můžete načíst v samostatných dotazech a EF "opravuje" navigační vlastnosti.  To znamená, že EF automaticky přidá samostatně načtené entity, kde patří do vlastností navigace dříve načtených entit. Pro dotaz, který načte související data, můžete použít `Load` metodu namísto metody, která vrací seznam nebo objekt, například `ToList` nebo `Single` .

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

* Explicitní načítání. Při prvním načtení entity se nenačte související data. Napíšete kód, který načte související data, pokud je to potřeba. Stejně jako v případě, že se Eager načítá pomocí samostatných dotazů, explicitní načítání vede k více dotazům odesílaných do databáze. Rozdíl je v tom, že při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny. V Entity Framework Core 1,1 lze použít `Load` metodu k explicitnímu načítání. Příklad:

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* Opožděné načítání. Při prvním načtení entity se nenačte související data. Při prvním pokusu o přístup k navigační vlastnosti je však automaticky načtena data potřebná pro tuto vlastnost navigace. Dotaz se pošle do databáze pokaždé, když se pokusíte získat data z navigační vlastnosti poprvé. Entity Framework Core 1,0 nepodporuje opožděné načítání.

### <a name="performance-considerations"></a>Otázky výkonu

Pokud víte, že pro každou načtenou entitu potřebujete související data, Eager načítání často nabízí nejlepší výkon, protože jediný dotaz odeslaný do databáze je obvykle efektivnější než samostatné dotazy pro každou načtenou entitu. Předpokládejme například, že každé oddělení má deset souvisejících kurzů. Eager načtení všech souvisejících dat by způsobilo pouze jeden (JOIN) dotaz a jednu zpáteční cestu do databáze. Samostatný dotaz na kurzy pro každé oddělení by způsobil jedenácté cestování databáze. Další výměna cest k databázi je obzvláště neškodná na výkon, pokud je latence vysoká.

Naopak v některých scénářích jsou samostatné dotazy efektivnější. Eager načtení všech souvisejících dat v jednom dotazu může způsobit vygenerování velmi složitého připojení, které SQL Server nemůže efektivně zpracovat. Nebo pokud potřebujete přístup k vlastnostem navigace entity jenom pro podmnožinu sady entit, které zpracováváte, můžou se samostatné dotazy provádět lépe, protože Eager načítání všeho dopředu by načetlo víc dat, než kolik potřebujete. Pokud je výkon kritický, je nejlepší testovat výkon oběma způsoby, abyste mohli nejlépe vybrat.

## <a name="create-a-courses-page"></a>Vytvoření stránky kurzů

Entita kurzu obsahuje navigační vlastnost, která obsahuje entitu oddělení oddělení, ke které je kurz přiřazen. Pokud chcete v seznamu kurzů zobrazit název přiřazeného oddělení, musíte získat vlastnost Name z entity oddělení, která je ve `Course.Department` vlastnosti navigace.

Vytvořte řadič s názvem CoursesController pro typ entity kurzu pomocí stejných možností pro **kontroler MVC se zobrazeními, a to pomocí entity Frameworkého** uživatelského rozhraní, které jste předtím vytvořili pro řadič studenta, jak je znázorněno na následujícím obrázku:

![Přidat kontroler kurzů](read-related-data/_static/add-courses-controller.png)

Otevřete *CoursesController.cs* a prověřte `Index` metodu. Automatické generování uživatelského rozhraní pro vlastnost navigace určilo načítání Eager `Department` pomocí `Include` metody.

Nahraďte `Index` metodu následujícím kódem, který používá vhodnější název pro `IQueryable` , který vrací entity kurzu ( `courses` místo `schoolContext` ):

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Otevřete *zobrazení/kurzy/index. cshtml* a nahraďte kód šablony následujícím kódem. Změny jsou zvýrazněny:

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Provedli jste následující změny ve vygenerovaném kódu:

* Změnili jste záhlaví z indexu na kurzy.

* Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti. Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele. V tomto případě je však primární klíč smysluplný a chcete jej zobrazit.

* Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení. Kód zobrazí `Name` vlastnost entity oddělení, která je načtena do `Department` navigační vlastnosti:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Vytvoření stránky instruktory

V této části vytvoříte kontrolér a zobrazení pro entitu instruktora, aby se zobrazila stránka instruktoři:

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

Tato stránka čte a zobrazuje související data následujícími způsoby:

* Seznam instruktorů zobrazuje související data z entity OfficeAssignment. Entity instruktora a OfficeAssignment jsou v relaci typu 1:1 nebo 1:1. Pro entity OfficeAssignment budete používat Eager načítání. Jak bylo vysvětleno dříve, načítání Eager je obvykle efektivnější, pokud potřebujete související data pro všechny načtené řádky primární tabulky. V takovém případě chcete zobrazit přiřazení Office pro všechny zobrazené instruktory.

* Když uživatel vybere instruktora, zobrazí se související entity kurzu. Entity instruktor a Course jsou v relaci n:n. Pro entity kurzu a jejich související entity oddělení budete používat Eager načítání. V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete kurzy jenom pro vybraného instruktora. Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v rámci entit, které jsou samy v navigační vlastnosti.

* Když uživatel vybere kurz, zobrazí se související data ze sady entit registrace. Entity kurzu a registrace jsou v relaci 1: n. Pro entity registrace a jejich související entity studenta použijete samostatné dotazy.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Vytvoření modelu zobrazení pro zobrazení indexu instruktora

Stránka instruktoři zobrazuje data ze tří různých tabulek. Proto vytvoříte model zobrazení, který obsahuje tři vlastnosti, z nichž každý uchovává data pro jednu z tabulek.

Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Vytvoření kontroleru a zobrazení instruktora

Vytvořte kontrolora instruktorů s akcemi čtení/zápisu EF, jak je znázorněno na následujícím obrázku:

![Přidat kontroler instruktorů](read-related-data/_static/add-instructors-controller.png)

Otevřete *InstructorsController.cs* a přidejte příkaz using pro obor názvů ViewModels:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Nahraďte metodu indexu následujícím kódem pro Eager načítání souvisejících dat a umístěte je do modelu zobrazení.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

Metoda přijímá volitelná data směrování ( `id` ) a parametr řetězce dotazu ( `courseID` ), které poskytují hodnoty ID vybraného instruktora a vybraného kurzu. Parametry jsou k dispozici na stránce **vybrané** hypertextové odkazy.

Kód začíná vytvořením instance modelu zobrazení a jeho vložením do seznamu instruktorů. Kód určuje načítání Eager pro `Instructor.OfficeAssignment` a `Instructor.CourseAssignments` vlastnosti navigace. V rámci `CourseAssignments` vlastnosti `Course` je načtena vlastnost a v rámci ní `Enrollments` `Department` jsou načteny vlastnosti a a v rámci každé `Enrollment` entity `Student` je načtena vlastnost.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Vzhledem k tomu, že zobrazení vždy vyžaduje entitu OfficeAssignment, je efektivnější ho načíst ve stejném dotazu. Entity kurzu jsou požadovány, když je na webové stránce vybrán instruktor. jediný dotaz je lepší než více dotazů pouze v případě, že je stránka zobrazena častěji s kurzem vybraným než bez.

Kód se opakuje `CourseAssignments` a `Course` protože potřebujete dvě vlastnosti z `Course` . První řetězec `ThenInclude` volání získá `CourseAssignment.Course` , `Course.Enrollments` a `Enrollment.Student` .

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

V tomto okamžiku v kódu, další `ThenInclude` by byl pro navigační vlastnosti `Student` , které nepotřebujete. Ale volání `Include` začíná s `Instructor` vlastnostmi, takže musíte projít řetěz znovu, tentokrát `Course.Department` místo `Course.Enrollments` .

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Následující kód se spustí, když byl vybrán instruktor. Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení. Vlastnost modelu zobrazení `Courses` je pak načtena s entitami kurzu z vlastnosti navigace tohoto vyučujícího `CourseAssignments` .

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

`Where`Metoda vrací kolekci, ale v tomto případě kritéria předaná této metodě mají za následek vrácenou pouze jednu entitu Instructor. `Single`Metoda převede kolekci na jednu entitu instruktora, která vám umožní přístup k vlastnosti této entity `CourseAssignments` . `CourseAssignments`Vlastnost obsahuje `CourseAssignment` entity, ze kterých chcete pouze související `Course` entity.

`Single`Metodu pro kolekci použijete, když víte, že kolekce bude obsahovat pouze jednu položku. Jediná metoda vyvolá výjimku, pokud je kolekce předána prázdná, nebo pokud existuje více než jedna položka. Alternativa je `SingleOrDefault` , která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná. V tomto případě by však došlo k výjimce (při pokusu o nalezení `Courses` vlastnosti v odkazu s hodnotou null) a zpráva o výjimce by byla méně zřetelně indikovat příčinu problému. Při volání `Single` metody lze také předat podmínku WHERE namísto volání `Where` metody samostatně:

```csharp
.Single(i => i.ID == id.Value)
```

Namísto:

```csharp
.Where(i => i.ID == id.Value).Single()
```

V dalším případě se vybraný kurz načte ze seznamu kurzů v modelu zobrazení. Pak je vlastnost zobrazení modelu `Enrollments` načtena s entitami registrace z vlastnosti navigace tohoto kurzu `Enrollments` .

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Úprava zobrazení indexu instruktorů

V *zobrazeních, instruktorech/index. cshtml*nahraďte kód šablony následujícím kódem. Změny jsou zvýrazněny.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

V existujícím kódu jste provedli následující změny:

* Třída modelu se změnila na `InstructorIndexData` .

* Změnila se název stránky z **indexu** na **instruktory**.

* Přidání sloupce **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v případě, že `item.OfficeAssignment` není null. (Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.)

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Přidali jsme sloupec **kurzy** , ve kterém se zobrazují kurzy výukové každým instruktorem. Další informace najdete v části [explicitní přechod na řádku](xref:mvc/views/razor#explicit-line-transition) v Razor článku syntaxe.

* Přidaný kód, který dynamicky přidá `class="success"` do `tr` prvku vybraného instruktora. Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Přidali jsme nový hypertextový odkaz **označený těsně před** ostatní odkazy v každém řádku, což způsobí odeslání vybraného ID instruktoru do `Index` metody.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje vlastnost umístění souvisejících entit OfficeAssignment a prázdnou buňku tabulky, pokud neexistuje žádná související entita OfficeAssignment.

![Žádná vybraná stránka indexu instruktorů](read-related-data/_static/instructors-index-no-selection.png)

V souboru *views/instruktors/index. cshtml* za uzavírací element Table (na konci souboru) přidejte následující kód. Tento kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Tento kód načte `Courses` vlastnost modelu zobrazení a zobrazí seznam kurzů. Poskytuje také hypertextový odkaz pro **Výběr** , který pošle ID vybraného kurzu do `Index` metody Action.

Aktualizujte stránku a vyberte instruktora. Nyní se zobrazí mřížka zobrazující kurzy přiřazené k vybranému instruktorovi a pro každý kurz vidíte název přiřazeného oddělení.

![Vybraná instruktora stránky indexu instruktorů](read-related-data/_static/instructors-index-instructor-selected.png)

Po tom, co jste právě přidali blok kódu, přidejte následující kód. Zobrazí se seznam studentů, kteří jsou v kurzu při výběru tohoto kurzu zaregistrovaní.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Tento kód načte vlastnost registrace modelu zobrazení, aby se zobrazil seznam studentů zaregistrovaných v tomto kurzu.

Aktualizujte stránku znovu a vyberte instruktora. Pak vyberte kurz, ve kterém se zobrazí seznam zaregistrovaných studentů a jejich třídy.

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>O explicitním načítání

Když jste načetli seznam instruktorů v *InstructorsController.cs*, zadali jste Eager načítání pro `CourseAssignments` navigační vlastnost.

Předpokládejme, že jste uživatelům očekávali jenom zřídka jenom ty registrace ve vybraném instruktoru a kurzu. V takovém případě může být vhodné načíst data zápisu pouze v případě, že je požadováno. Chcete-li zobrazit příklad explicitního načítání, nahraďte `Index` metodu následujícím kódem, který odebere Eager načítání pro zápisy a explicitně načte tuto vlastnost. Změny kódu jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Nový kód zruší volání metody *ThenInclude* pro data zápisu z kódu, který načítá entity instruktora. Tím také klesne `AsNoTracking` .  Pokud je vybrán instruktor a kurz, zvýrazněný kód načte entity registrace pro vybraný kurz a entity studenta pro každou registraci.

Spusťte aplikaci, přejděte na stránku indexu instruktory nyní a uvidíte, že se na stránce zobrazí žádný rozdíl, i když jste změnili způsob, jakým se data načítají.

## <a name="get-the-code"></a>Získání kódu

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Zjistili jste, jak načíst související data
> * Stránka vytvoření kurzů
> * Stránka s instruktory se vytvořila.
> * Dozvěděli jste se o explicitním načítání.

Přejděte k dalšímu kurzu, kde se dozvíte, jak aktualizovat související data.

> [!div class="nextstepaction"]
> [Aktualizace souvisejících dat](update-related-data.md)

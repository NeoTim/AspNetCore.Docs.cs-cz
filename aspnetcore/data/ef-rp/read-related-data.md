---
title: Stránky Razor s EF Core v ASP.NET Core – čtení souvisejících dat – 6 8
author: rick-anderson
description: V tomto kurzu čtení a zobrazení souvisejících dat – to znamená, že data, která načte Entity Framework do navigační vlastnosti.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/read-related-data
ms.openlocfilehash: a264cdaf0f577be6ea2043935b485f4fd16e0229
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264946"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a>Stránky Razor s EF Core v ASP.NET Core – čtení souvisejících dat – 6 8

Podle [Petr Dykstra](https://github.com/tdykstra), [Jan Macek P](https://twitter.com/thereformedprog), a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

V tomto kurzu související data načíst a zobrazit. Související data jsou data, která načte EF Core do navigační vlastnosti.

Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

Dokončené stránky pro účely tohoto kurzu na následujících obrázcích:

![Kurzy indexová stránka](read-related-data/_static/courses-index.png)

![Instruktoři indexová stránka](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Nemůžou dočkat, až explicitní a opožděné načtení souvisejících dat

Existuje několik způsobů, EF Core můžete načíst související data do navigační vlastnosti entity:

* [Předběžné načítání](/ef/core/querying/related-data#eager-loading). Předběžné načítání je při dotazu na jeden typ entity také načtení souvisejících entit. Při čtení je entita, související data načtena. Obvykle v důsledku jednoho spojení dotaz, který zkopíruje všechna data, který je nezbytný. EF Core vydá pro některé typy nemůžou dočkat, až načítání více dotazů. Vydání více dotazů může být efektivnější než v případě u některých dotazů v EF6 tam, kde byla jeden dotaz. Předběžné načítání je zadán s `Include` a `ThenInclude` metody.

  ![Předběžné načítání příklad](read-related-data/_static/eager-loading.png)
 
  Předběžné načítání odešle více dotazů, když je součástí navigace kolekce:

  * Jeden dotaz pro hlavní dotaz 
  * Jeden dotaz pro každou kolekci "edge" ve stromové struktuře zatížení.

* Samostatné dotazy s `Load`: Data můžete obnovit v samostatné dotazy a EF Core termín "opravy" navigační vlastnosti. "opravy nahoru" znamená, že EF Core automaticky naplní navigační vlastnosti. Samostatné dotazy s `Load` se víc explicitní načtení než předběžné načítání.

  ![Příklad samostatné dotazy](read-related-data/_static/separate-queries.png)

  Poznámka: EF Core automaticky opravuje vlastnosti navigace s jinými entitami, které byly dříve načtena do instance kontextu. I v případě, že jsou data pro navigační vlastnost *není* výslovně zahrnuty, vlastnost pořád naplněný, pokud některé nebo všechny související entity byly dříve načteny.

* [Explicitní načtení](/ef/core/querying/related-data#explicit-loading). Pokud entita je nejdřív přečíst, související data nebude načten. Načíst související data, když je potřeba, musí být kód zapsán. Explicitní načtení pomocí samostatné dotazy za následek více dotazy odeslané do databáze. S explicitní načtení, určuje kód navigačních vlastností, které mají být načteny. Použití `Load` metodu explicitní načtení. Příklad:

  ![Příklad explicitní načtení](read-related-data/_static/explicit-loading.png)

* [Opožděné načtení](/ef/core/querying/related-data#lazy-loading). [Opožděné načtení byl přidán do EF Core ve verzi 2.1](/ef/core/querying/related-data#lazy-loading). Pokud entita je nejdřív přečíst, související data nebude načten. Při prvním přístupu k vlastnosti navigace se automaticky načte data požadovaná pro tuto navigační vlastnost. Bude odeslán dotaz do databáze pokaždé, když vlastnost navigace pracuje poprvé.

* `Select` Operátor načte pouze souvisejících dat, které jsou potřeba.

## <a name="create-a-course-page-that-displays-department-name"></a>Vytvoření stránky kurz, který zobrazuje název oddělení

Kurz entita obsahuje vlastnost navigace, která obsahuje `Department` entity. `Department` Oddělení, která je přiřazena kurz v entitě.

Chcete-li zobrazit název přiřazený oddělení v seznamu kurzů:

* Získejte `Name` vlastnost z `Department` entity.
* `Department` Entity pocházejí z `Course.Department` navigační vlastnost.

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Vygenerované uživatelské rozhraní modelu kurzu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Course` pro třídu modelu.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

 Spusťte následující příkaz:

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

------

Předchozí příkaz scaffold `Course` modelu. Otevřete projekt v sadě Visual Studio.

Otevřít *Pages/Courses/Index.cshtml.cs* a prozkoumejte `OnGetAsync` metody. Předběžné načítání pro zadaný modul generování uživatelského rozhraní `Department` navigační vlastnost. `Include` Metody určuje předběžné načítání.

Spusťte aplikaci a vyberte **kurzy** odkaz. Zobrazí sloupec oddělení `DepartmentID`, který není užitečné.

Aktualizace `OnGetAsync` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Předchozí kód přidá `AsNoTracking`. `AsNoTracking` zlepší výkon, protože nebudou pro účely vrácených entit. Entity, které nejsou sledovat, protože se neaktualizují v rámci aktuálního kontextu.

Aktualizace *Pages/Courses/Index.cshtml* s následující zvýrazněný kód:

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Na automaticky generovaný kód se provedly následující změny:

* Změnit záhlaví od indexu ke kurzům.
* Přidá **číslo** sloupec, který ukazuje `CourseID` hodnotu vlastnosti. Ve výchozím nastavení nejsou automaticky generovaný primární klíče, protože jsou obvykle nemá význam pro koncové uživatele. Ale v tomto případě primární klíč má smysl.
* Změnit **oddělení** sloupec, který se zobrazí název oddělení. Zobrazení kódu `Name` vlastnost `Department` entity, který je načten do `Department` navigační vlastnost:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Spusťte aplikaci a vyberte **kurzy** kartu pro zobrazení seznamu s názvy oddělení.

![Kurzy indexová stránka](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Načítají se související data s vybranými

`OnGetAsync` Metoda načte související data se `Include` metody:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select` Operátor načte pouze souvisejících dat, které jsou potřeba. Pro jednotlivé položky jako `Department.Name` používá SQL INNER JOIN. Pro kolekce, používá jiný přístup k databázi, ale postupy zločinců `Include` operátor v kolekcích.

Následující kód načte data související s `Select` metody:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Zobrazit [IndexSelect.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) kompletní příklad.

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Vytvoření stránky školitelů, který ukazuje, kurzy a registrace

V této části se vytvoří Instruktoři stránka.

<a name="IP"></a>
![Instruktoři indexová stránka](read-related-data/_static/instructors-index.png)

Tato stránka načte a zobrazí související data následujícími způsoby:

* V seznamu instruktorů zobrazí související data z `OfficeAssignment` entity (Office na předchozím obrázku). `Instructor` a `OfficeAssignment` entity jsou ve vztahu k nule nebo jednom. Předběžné načítání se používá pro `OfficeAssignment` entity. Předběžné načítání je obvykle efektivnější, pokud je potřeba zobrazí související data. V takovém případě přiřazení sady office pro Instruktoři zobrazují.
* Když uživatel vybere instruktorem (Harui na předchozím obrázku), související `Course` entity jsou zobrazeny. `Instructor` a `Course` entity jsou v relaci m: m. Předběžné načítání se používá pro `Course` entit a jejich související `Department` entity. Samostatné dotazy v tomto případě může být mnohem efektivnější, protože jsou potřeba jenom kurzů pro vybranou instruktorem. Tento příklad ukazuje způsob použití předběžné načítání pro navigační vlastnosti v entitách, které jsou v navigační vlastnosti.
* Když uživatel vybere kurzu (chemie na předchozím obrázku), související data z `Enrollments` entity se zobrazí. Na předchozím obrázku jsou zobrazeny jméno studenta a na podnikové úrovni. `Course` a `Enrollment` entity jsou v vztah jeden mnoho.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Vytvoření modelu zobrazení pro zobrazení indexu instruktorem

Na stránce Instruktoři zobrazuje data ze tří různých tabulek. Zobrazení modelu je vytvořen, která obsahuje tři entity představující tři tabulky.

V *SchoolViewModels* složku, vytvořte *InstructorIndexData.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Vygenerované uživatelské rozhraní modelu instruktorem

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Instructor` pro třídu modelu.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

 Spusťte následující příkaz:

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

------

Předchozí příkaz scaffold `Instructor` modelu. Spusťte aplikaci a přejděte na stránku Instruktoři.

Nahraďte *Pages/Instructors/Index.cshtml.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

`OnGetAsync` Metoda přijímá data volitelné trasy pro ID vybrané instruktorem.

Prozkoumat dotaz *Pages/Instructors/Index.cshtml.cs* souboru:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Dotaz má dvě zahrnuje:

* `OfficeAssignment`: Zobrazí v [Instruktoři zobrazení](#IP).
* `CourseAssignments`: Což přináší výukové kurzy.

### <a name="update-the-instructors-index-page"></a>Aktualizace Instruktoři indexovou stránku

Aktualizace *Pages/Instructors/Index.cshtml* následujícím kódem:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Předchozí kód provede následující změny:

* Aktualizace `page` direktiv z `@page` k `@page "{id:int?}"`. `"{id:int?}"` je šablonu trasy. Šablona trasy změny celé číslo řetězce dotazu v adrese URL data trasy. Například, že kliknete na **vyberte** instruktorem s pouze odkaz `@page` – direktiva vytvoří adresu URL podobnou následující:

  `http://localhost:1234/Instructors?id=2`

  Po direktivě stránky `@page "{id:int?}"`, předchozí adresa URL je:

  `http://localhost:1234/Instructors/2`

* Je název stránky **Instruktoři**.
* Přidá **Office** sloupec, který zobrazuje `item.OfficeAssignment.Location` pouze tehdy, pokud `item.OfficeAssignment` není null. Protože je to vztah jeden: nula nebo 1, nemusí být související entita OfficeAssignment.

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
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Přidat nový popisek hypertextový odkaz **vyberte**. Tento odkaz odešle vybraný instruktorem ID `Index` metoda a nastaví barvu pozadí.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Spusťte aplikaci a vyberte **Instruktoři** kartu. Na stránce se zobrazí `Location` (office) z související `OfficeAssignment` entity. Pokud OfficeAssignment' je null, se zobrazí na prázdné tabulce buňku.

![Instruktoři indexovou stránku, kterou nevybere](read-related-data/_static/instructors-index-no-selection.png)

Klikněte na **vyberte** odkaz. Změny stylu řádku.

### <a name="add-courses-taught-by-selected-instructor"></a>Přidat kurzy vedené vybrané instruktorem

Aktualizace `OnGetAsync` metoda *Pages/Instructors/Index.cshtml.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Add `public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Zkontrolujte aktualizované dotazu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Přidá předchozí dotaz `Department` entity.

Následující kód provede při výběru instruktorem (`id != null`). Vybrané kurzů vedených je načten ze seznamu školitelů v modelu zobrazení. Model zobrazení `Courses` vlastnosti je načtena s `Course` entity z této kurzů vedených `CourseAssignments` navigační vlastnost.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where` Metoda vrátí kolekci. V předchozím `Where` metody pouze jediný `Instructor` se vrátí entity. `Single` Metoda převede kolekci do jednoho `Instructor` entity. `Instructor` Entita poskytuje přístup k `CourseAssignments` vlastnost. `CourseAssignments` poskytuje přístup k související `Course` entity.

![M:M kurzů vedených kurzy](complex-data-model/_static/courseassignment.png)

`Single` Metoda se používá v kolekci, pokud kolekce obsahuje pouze jednu položku. `Single` Metoda vyvolá výjimku, pokud kolekce je prázdná, nebo pokud existuje více než jednu položku. Alternativou je `SingleOrDefault`, která vrátí výchozí hodnoty (null v tomto případě) Pokud kolekce je prázdná. Pomocí `SingleOrDefault` na prázdnou kolekci:

* Má za následek výjimku (z pokusu o nalezení `Courses` vlastnost na hodnotu Null).
* Zpráva o výjimce by méně jasně ukazovat na příčinu problému.

Následující kód naplní model zobrazení `Enrollments` vlastnost při výběru kurzu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Přidejte následující kód do konce *Pages/Instructors/Index.cshtml* stránky Razor:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Předchozí kód zobrazí seznam kurzů související s instruktorem, pokud je vybrána instruktorem.

Testování aplikace. Klikněte na **vyberte** odkaz na stránce Instruktoři.

![Instruktoři Index stránky kurzů vedených vybrané](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a>Zobrazení údajů studentů

V této části se aplikace aktualizuje a zobrazí student data pro vybrané kurzu.

Aktualizovat dotaz `OnGetAsync` metoda ve *Pages/Instructors/Index.cshtml.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Aktualizace *Pages/Instructors/Index.cshtml*. Na konec souboru přidejte následující kód:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Předchozí kód zobrazí seznam studentů, kteří se zaregistrují ve vybraných kurzů.

Aktualizujte stránku a vybrat instruktorem. Vyberte kurzu zobrazíte seznam registrovaná studentů a jejich kvality.

![Kurzů vedených instruktory Index stránky a vybrali kurzu](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Pomocí jednoho

`Single` Můžete předat metodě `Where` podmínku namísto volání metody `Where` metoda samostatně:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Předchozí `Single` přístup nabízí v porovnání s použitím žádné výhody `Where`. Někteří vývojáři raději `Single` přistupovat ke stylu.

## <a name="explicit-loading"></a>explicitní načtení

Aktuální kód určuje předběžné načítání pro `Enrollments` a `Students`:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Předpokládejme, že uživatelé chtějí zřídka naleznete v tématu registrace v kurzu. Optimalizace v takovém případě by se pouze načíst data registrací. Pokud je požadováno. V této části `OnGetAsync` je aktualizované, aby používaly explicitní načtení `Enrollments` a `Students`.

Aktualizace `OnGetAsync` následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Předchozí kód klesne *ThenInclude* metoda se volá pro registraci a studentů data. Pokud je vybrána kurz, načte zvýrazněný kód:

* `Enrollment` Entity pro vybraný kurz.
* `Student` Entit pro každou `Enrollment`.

Všimněte si, že předchozí na komentářích ke kódu `.AsNoTracking()`. Navigační vlastnosti lze explicitně načíst pouze u sledovaných entit.

Testování aplikace. Z hlediska uživatelů aplikace chová stejně jako předchozí verze.

Další kurz ukazuje, jak aktualizovat související data.

## <a name="additional-resources"></a>Další zdroje

* [YouTube verzi tohoto kurzu (část 1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [YouTube verzi tohoto kurzu (část2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Předchozí](xref:data/ef-rp/complex-data-model)
>[další](xref:data/ef-rp/update-related-data)

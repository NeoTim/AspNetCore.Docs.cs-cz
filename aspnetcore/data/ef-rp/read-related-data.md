---
title: Část 6, Razor stránky s EF Core v souvisejících datech ASP.NET Core pro čtení
author: rick-anderson
description: 6. část Razor stránek a Entity Framework řady kurzů.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 171607544bfe89fdd0a1ed9efb68f7a532f9aee1
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212655"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>Část 6, Razor stránky s EF Core v souvisejících datech ASP.NET Core pro čtení

Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se dozvíte, jak číst a zobrazovat související data. Související data jsou data, která EF Core načíst do vlastností navigace.

Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Eager, explicitní a opožděné načítání

Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:

* [Eager načítání](/ef/core/querying/related-data#eager-loading). Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity. Při čtení entity se načtou související data. To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data. EF Core bude vydávat více dotazů pro některé typy načítání Eager. Vystavení více dotazů může být efektivnější než obří jediný dotaz. Eager načítání je zadáno pomocí `Include` metod a `ThenInclude` .

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:

  * Jeden dotaz pro hlavní dotaz 
  * Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.

* Samostatné dotazy `Load` : data lze načíst v samostatných dotazech a EF Core "" řeší "navigační vlastnosti. "Opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti. Samostatné dotazy se `Load` podobají explicitnímu načítání než Eager načítání.

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  **Poznámka:** EF Core automaticky opravuje navigační vlastnosti na jakékoli další entity, které byly dříve načteny do instance kontextu. I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.

* [Explicitní načítání](/ef/core/querying/related-data#explicit-loading). Při prvním načtení entity se nenačte související data. Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód. Explicitní načítání pomocí samostatných dotazů má za následek odeslání více dotazů do databáze. Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny. `Load`K provedení explicitního načítání použijte metodu. Zde je příklad:

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* [Opožděné načítání](/ef/core/querying/related-data#lazy-loading). [Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading). Při prvním načtení entity se nenačte související data. Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace. Dotaz je odeslán do databáze při každém prvním otevření navigační vlastnosti.

## <a name="create-course-pages"></a>Vytvořit stránky kurzu

`Course`Entita obsahuje navigační vlastnost, která obsahuje související `Department` entitu.

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

Chcete-li zobrazit název přiřazeného oddělení za kurz:

* Načtěte související `Department` entitu do `Course.Department` vlastnosti navigace.
* Získá název z `Department` `Name` vlastnosti entity.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Stránky kurzu generování uživatelského rozhraní

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Postupujte podle pokynů v části [stránky pro studenty](xref:data/ef-rp/intro#scaffold-student-pages) s těmito výjimkami:

  * Vytvořte složku *stránky nebo kurzy* .
  * Použijte `Course` pro třídu modelu.
  * Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Vytvořte složku *stránky nebo kurzy* .

* Spusťte následující příkaz pro generování uživatelského rozhraní stránek kurzu.

  **Ve Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **V systému Linux nebo macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu. Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání. `Include`Metoda určuje Eager načítání.

* Spusťte aplikaci a vyberte odkaz **kurzy** . Sloupec oddělení zobrazí `DepartmentID` , což není užitečné.

### <a name="display-the-department-name"></a>Zobrazit název oddělení

Aktualizujte stránky/kurzy/index. cshtml. cs s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Předchozí kód změní `Course` vlastnost na `Courses` a přidá `AsNoTracking` . `AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány. Entity není nutné sledovat, protože nejsou aktualizovány v aktuálním kontextu.

Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího kódu.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

V generovaném kódu byly provedeny následující změny:

* Změnila se `Course` název vlastnosti na `Courses` .
* Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti. Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele. V tomto případě je však primární klíč smysluplný.
* Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení. Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do `Department` navigační vlastnosti:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Načítání souvisejících dat pomocí výběru

`OnGetAsync`Metoda načte související data pomocí `Include` metody. `Select`Metoda je alternativou, která načte pouze související data. Pro jednotlivé položky, například `Department.Name` používá vnitřní spojení SQL. U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.

Následující kód načte související data `Select` metodou:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

Předchozí kód nevrací žádné typy entit, proto žádné sledování není provedeno. Další informace o sledování EF naleznete v tématu [sledování vs. žádné dotazy sledování](/ef/core/querying/tracking).

`CourseViewModel`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .

## <a name="create-instructor-pages"></a>Vytvořit stránky instruktora

Tato část vygeneruje stránky instruktora a přidá související kurzy a registrace na stránku indexu instruktorů.

<a name="IP"></a>
![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)

Tato stránka čte a zobrazuje související data následujícími způsoby:

* Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku). `Instructor`Entity a `OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota. Pro entity se používá Eager načítání `OfficeAssignment` . Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data. V tomto případě se zobrazí přiřazení kanceláře pro instruktory.
* Když uživatel vybere instruktora, `Course` zobrazí se související entity. `Instructor`Entity a `Course` jsou v relaci m:n. Eager načítání se používá pro `Course` entity a jejich související `Department` entity. V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora. Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.
* Když uživatel vybere kurz, zobrazí se související data z `Enrollments` entity. Na předchozím obrázku se zobrazí název a stupeň studenta. `Course`Entity a `Enrollment` jsou v relaci 1: n.

### <a name="create-a-view-model"></a>Vytvoření modelu zobrazení

Stránka instruktoři zobrazuje data ze tří různých tabulek. Model zobrazení je potřeba, který obsahuje tři vlastnosti reprezentující tři tabulky.

Vytvořte *SchoolViewModels/InstructorIndexData. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Stránky instruktora uživatelského rozhraní

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Postupujte podle pokynů v [části generování uživatelského rozhraní se stránkami studenta](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:

  * Vytvořte složku *Pages/instruktory* .
  * Použijte `Instructor` pro třídu modelu.
  * Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Vytvořte složku *Pages/instruktory* .

* Spusťte následující příkaz pro generování uživatelského rozhraní stránek instruktora.

  **Ve Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **V systému Linux nebo macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Chcete-li zjistit, jaké vygenerované stránky vypadá před tím, než je aktualizujete, spusťte aplikaci a přejděte na stránku instruktoři.

Aktualizovat *stránky/instruktory/index. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

`OnGetAsync`Metoda přijímá volitelná data směrování pro ID vybraného instruktora.

Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

Kód určuje načítání Eager pro následující navigační vlastnosti:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Všimněte si opakování `Include` a `ThenInclude` metod pro `CourseAssignments` a `Course` . Toto opakování je nezbytné pro zadání Eager načítání pro dvě navigační vlastnosti `Course` entity.

Následující kód se spustí při výběru instruktora ( `id != null` ).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení. Vlastnost modelu zobrazení `Courses` je načtena s `Course` entitami z vlastnosti navigace tohoto instruktora `CourseAssignments` .

`Where`Metoda vrátí kolekci. Ale v tomto případě filtr vybere jednu entitu, takže se `Single` zavolá metoda, která kolekci převede na jedinou `Instructor` entitu. `Instructor`Entita poskytuje přístup k `CourseAssignments` Vlastnosti. `CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

`Single`Metoda se používá v kolekci, pokud má kolekce pouze jednu položku. `Single`Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka. Alternativa je `SingleOrDefault` , která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.

Následující kód naplní vlastnost modelu zobrazení, `Enrollments` když je vybraný kurz:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Aktualizace stránky indexu instruktorů

Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Předchozí kód provede následující změny:

* Aktualizuje `page` direktivu z `@page` na `@page "{id:int?}"` . `"{id:int?}"`je šablona trasy. Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat. Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze `@page` direktivu, se vytvoří adresa URL jako následující:

  `https://localhost:5001/Instructors?id=2`

  V případě direktivy stránky je `@page "{id:int?}"` Adresa URL:

  `https://localhost:5001/Instructors/2`

* Přidá sloupec **Office** , který se zobrazí pouze v případě, že `item.OfficeAssignment.Location` `item.OfficeAssignment` není null. Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Přidá sloupec **kurzů** , ve kterém se zobrazují kurzy, které každý instruktor prodlužuje. Další informace o této syntaxi Razor naleznete v tématu [explicitní přechod mezi řádky](xref:mvc/views/razor#explicit-line-transition) .

* Přidá kód, který dynamicky přidá `class="success"` do `tr` prvku vybraného instruktora a kurzu. Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Přidá nový hypertextový odkaz označený jako **vybraný**. Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Přidá tabulku kurzů pro vybraného instruktora.

* Přidá tabulku zápisů studentů pro vybraný kurz.

Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity. Pokud `OfficeAssignment` má hodnotu null, zobrazí se prázdná buňka tabulky.

Klikněte na odkaz **Vybrat** pro instruktora. Zobrazí se změna stylu řádku a kurzy přiřazené k tomuto instruktorovi.

Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Použití jednoduchých

`Single`Metoda může předat `Where` podmínku namísto volání `Where` metody samostatně:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Použití `Single` s podmínkou WHERE je záležitostí osobní preference. Neposkytuje za použití metody žádné výhody `Where` .

## <a name="explicit-loading"></a>Explicitní načítání

Aktuální kód určuje Eager načítání pro `Enrollments` a `Students` :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace. V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno. V této části `OnGetAsync` je aktualizována pro použití explicitního načítání `Enrollments` a `Students` .

Aktualizujte *stránky/instruktory/index. cshtml. cs* pomocí následujícího kódu.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi. Pokud je vybrán kurz, explicitní načítání kódu načte:

* `Enrollment`Entity pro vybraný kurz.
* `Student`Entity pro každou `Enrollment` .

Všimněte si, že předchozí komentáře k kódu byly vycházející `.AsNoTracking()` . Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.

Otestujete aplikaci. V perspektivě uživatele se aplikace chová stejně jako předchozí verze.

## <a name="next-steps"></a>Další kroky

V dalším kurzu se dozvíte, jak aktualizovat související data.

>[!div class="step-by-step"]
>[Předchozí kurz](xref:data/ef-rp/complex-data-model) 
> [Další kurz](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se budou číst a zobrazovat související data. Související data jsou data, která EF Core načíst do vlastností navigace.

Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Eager, explicitní a opožděné načítání souvisejících dat

Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:

* [Eager načítání](/ef/core/querying/related-data#eager-loading). Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity. Při čtení entity se načtou související data. To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data. EF Core bude vydávat více dotazů pro některé typy načítání Eager. Vystavení více dotazů může být efektivnější než u některých dotazů v EF6, kde existoval jeden dotaz. Eager načítání je zadáno pomocí `Include` metod a `ThenInclude` .

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:

  * Jeden dotaz pro hlavní dotaz 
  * Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.

* Samostatné dotazy `Load` : data lze načíst v samostatných dotazech a EF Core "" řeší "navigační vlastnosti. "opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti. Samostatné dotazy se `Load` podobají explicitnímu načítání než Eager načítání.

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  Poznámka: EF Core automaticky opravuje navigační vlastnosti pro všechny další entity, které byly dříve načteny do instance kontextu. I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.

* [Explicitní načítání](/ef/core/querying/related-data#explicit-loading). Při prvním načtení entity se nenačte související data. Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód. Explicitní načítání pomocí samostatných dotazů má za následek více dotazů odeslaných do databáze. Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny. `Load`K provedení explicitního načítání použijte metodu. Zde je příklad:

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* [Opožděné načítání](/ef/core/querying/related-data#lazy-loading). [Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading). Při prvním načtení entity se nenačte související data. Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace. Dotaz se pošle do databáze při prvním otevření navigační vlastnosti.

* `Select`Operátor načte pouze související požadovaná data.

## <a name="create-a-course-page-that-displays-department-name"></a>Vytvoří stránku kurzu, která zobrazuje název oddělení.

Entita kurzu obsahuje navigační vlastnost, která obsahuje `Department` entitu. `Department`Entita obsahuje oddělení, ke kterému je kurz přiřazen.

Chcete-li zobrazit název přiřazeného oddělení v seznamu kurzů:

* Získá `Name` vlastnost z `Department` entity.
* `Department`Entita pochází z `Course.Department` navigační vlastnosti.

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Generování uživatelského rozhraní modelu kurzu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-the-student-model) a použijte `Course` pro třídu modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Předchozí příkaz vygeneruje `Course` model. Otevřete projekt v sadě Visual Studio.

Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu. Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání. `Include`Metoda určuje Eager načítání.

Spusťte aplikaci a vyberte odkaz **kurzy** . Sloupec oddělení zobrazí `DepartmentID` , což není užitečné.

Aktualizujte `OnGetAsync` metodu následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Předchozí kód přidá `AsNoTracking` . `AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány. Entity nejsou sledovány, protože nejsou aktualizovány v aktuálním kontextu.

Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího zvýrazněného označení:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

V generovaném kódu byly provedeny následující změny:

* Změnili jste záhlaví z indexu na kurzy.
* Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti. Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele. V tomto případě je však primární klíč smysluplný.
* Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení. Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do `Department` navigační vlastnosti:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Načítání souvisejících dat pomocí výběru

`OnGetAsync`Metoda načte související data pomocí `Include` metody:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select`Operátor načte pouze související požadovaná data. Pro jednotlivé položky, například `Department.Name` používá vnitřní spojení SQL. U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.

Následující kód načte související data `Select` metodou:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Vytvoření stránky instruktory, která zobrazuje kurzy a registrace

V této části se vytvoří stránka instruktoři.

<a name="IP"></a>
![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

Tato stránka čte a zobrazuje související data následujícími způsoby:

* Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku). `Instructor`Entity a `OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota. Pro entity se používá Eager načítání `OfficeAssignment` . Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data. V tomto případě se zobrazí přiřazení kanceláře pro instruktory.
* Když uživatel vybere instruktora (Harui na předchozím obrázku), `Course` zobrazí se související entity. `Instructor`Entity a `Course` jsou v relaci m:n. Eager načítání se používá pro `Course` entity a jejich související `Department` entity. V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora. Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.
* Když uživatel vybere kurz (chemie na předchozím obrázku), zobrazí se související data z `Enrollments` entity. Na předchozím obrázku se zobrazí název a stupeň studenta. `Course`Entity a `Enrollment` jsou v relaci 1: n.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Vytvoření modelu zobrazení pro zobrazení indexu instruktora

Stránka instruktoři zobrazuje data ze tří různých tabulek. Vytvoří se model zobrazení, který obsahuje tři entity reprezentující tři tabulky.

Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Generování uživatelského rozhraní modelu instruktor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-the-student-model) a použijte `Instructor` pro třídu modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Předchozí příkaz vygeneruje `Instructor` model. 
Spusťte aplikaci a přejděte na stránku instruktoři.

*Stránky/instruktořis/index. cshtml. cs* nahraďte následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

`OnGetAsync`Metoda přijímá volitelná data směrování pro ID vybraného instruktora.

Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Dotaz obsahuje dvě:

* `OfficeAssignment`: Zobrazí se v [zobrazení instruktory](#IP).
* `CourseAssignments`: Přináší výuku kurzů.

### <a name="update-the-instructors-index-page"></a>Aktualizace stránky indexu instruktorů

Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Předchozí kód provede následující změny:

* Aktualizuje `page` direktivu z `@page` na `@page "{id:int?}"` . `"{id:int?}"`je šablona trasy. Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat. Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze `@page` direktivu, se vytvoří adresa URL jako následující:

  `http://localhost:1234/Instructors?id=2`

  Pokud je direktiva stránky `@page "{id:int?}"` , předchozí adresa URL:

  `http://localhost:1234/Instructors/2`

* Nadpis stránky jsou **instruktory**.
* Přidání sloupce **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v případě, že `item.OfficeAssignment` není null. Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Přidali jsme sloupec **kurzy** , ve kterém se zobrazují kurzy výukové každým instruktorem. Další informace o této syntaxi Razor naleznete v tématu [explicitní přechod mezi řádky](xref:mvc/views/razor#explicit-line-transition) .

* Přidaný kód, který dynamicky přidá `class="success"` do `tr` prvku vybraného instruktora. Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Byl přidán nový hypertextový odkaz označený jako **vybraný**. Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity. Pokud má OfficeAssignment hodnotu null, zobrazí se prázdná buňka tabulky.

Klikněte na odkaz **Vybrat** . Styl řádku se změní.

### <a name="add-courses-taught-by-selected-instructor"></a>Přidat výukové kurzy podle vybraného instruktora

Aktualizujte `OnGetAsync` metodu na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Přidávání`public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Projděte si aktualizovaný dotaz:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Předchozí dotaz přidá `Department` entity.

Následující kód se spustí při výběru instruktora ( `id != null` ). Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení. Vlastnost modelu zobrazení `Courses` je načtena s `Course` entitami z vlastnosti navigace tohoto instruktora `CourseAssignments` .

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where`Metoda vrátí kolekci. V předchozí `Where` metodě `Instructor` se vrátí jenom jedna entita. `Single`Metoda převede kolekci na jednu `Instructor` entitu. `Instructor`Entita poskytuje přístup k `CourseAssignments` Vlastnosti. `CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

`Single`Metoda se používá v kolekci, pokud má kolekce pouze jednu položku. `Single`Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka. Alternativa je `SingleOrDefault` , která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná. Použití `SingleOrDefault` v prázdné kolekci:

* Výsledkem je výjimka (při pokusu o nalezení `Courses` vlastnosti v odkazu s hodnotou null).
* Zpráva o výjimce by byla zřetelně označovat příčinu problému.

Následující kód naplní vlastnost modelu zobrazení, `Enrollments` když je vybraný kurz:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Na konec stránky *stránky/instruktory/index. cshtml* přidejte následující kód Razor :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Předchozí kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.

Otestujete aplikaci. Na stránce instruktoři klikněte na odkaz **Vybrat** .

### <a name="show-student-data"></a>Zobrazit data studenta

V této části se aplikace aktualizuje, aby zobrazovala údaje o studentech pro vybraný kurz.

Aktualizujte dotaz v `OnGetAsync` metodě na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Aktualizovat *stránky/instruktory/index. cshtml* Na konec souboru přidejte následující kód:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Předchozí kód zobrazuje seznam studentů, kteří jsou zaregistrovaní ve vybraném kurzu.

Aktualizujte stránku a vyberte instruktora. Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Použití jednoduchých

`Single`Metoda může předat `Where` podmínku namísto volání `Where` metody samostatně:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Předchozí `Single` přístup neposkytuje oproti použití žádné výhody `Where` . Někteří vývojáři upřednostňují `Single` styl přístupu.

## <a name="explicit-loading"></a>Explicitní načítání

Aktuální kód určuje Eager načítání pro `Enrollments` a `Students` :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace. V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno. V této části `OnGetAsync` je aktualizována pro použití explicitního načítání `Enrollments` a `Students` .

Aktualizujte `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi. Pokud je vybrán kurz, zvýrazněný kód načte:

* `Enrollment`Entity pro vybraný kurz.
* `Student`Entity pro každou `Enrollment` .

Všimněte si předcházejících komentářů k kódu `.AsNoTracking()` . Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.

Otestujete aplikaci. V perspektivě uživatelů se aplikace chová stejně jako předchozí verze.

V dalším kurzu se dozvíte, jak aktualizovat související data.

## <a name="additional-resources"></a>Další zdroje informací

* [Verze tohoto kurzu pro YouTube (part1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Verze tohoto kurzu pro YouTube (Část2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Předchozí](xref:data/ef-rp/complex-data-model) 
> [Další](xref:data/ef-rp/update-related-data)

::: moniker-end

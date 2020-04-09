---
title: Razor Stránky s EF core v ASP.NET Core - Číst související data - 6 z 8
author: rick-anderson
description: V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která rozhraní Entity Framework načte do navigačních vlastností.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: d244ce1527486466bcbc6557ec35869aa206bc4f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656574"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a>Razor Stránky s EF core v ASP.NET Core - Číst související data - 6 z 8

[Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), a Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Tento kurz ukazuje, jak číst a zobrazovat související data. Související data jsou data, která EF Core načte do navigačních vlastností.

Následující ilustrace znázornit dokončené stránky pro tento kurz:

![Stránka Index kurzů](read-related-data/_static/courses-index30.png)

![Stránka Index instruktorů](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Dychtivé, explicitní a opožděné načítání

Existuje několik způsobů, jak EF Core můžete načíst související data do navigačnívlastnosti entity:

* [Dychtivé načítání](/ef/core/querying/related-data#eager-loading). Eager loading je, když dotaz pro jeden typ entity také načte související entity. Při čtení entity se načtou její související data. To obvykle vede k dotazu na jedno spojení, který načte všechna data, která je potřeba. EF Core vydá více dotazů pro některé typy dychtivé načítání. Vydávání více dotazů může být efektivnější než obří jeden dotaz. Eager loading je `Include` zadán `ThenInclude` s a metody.

  ![Příklad dychtivénačítání](read-related-data/_static/eager-loading.png)
 
  Eager načítání odešle více dotazů, pokud je zahrnuta navigace kolekce:

  * Jeden dotaz pro hlavní dotaz 
  * Jeden dotaz pro každou kolekci "okraj" ve stromu zatížení.

* Samostatné dotazy `Load`s : Data lze načíst v samostatných dotazech a EF Core "opravuje" navigační vlastnosti. "Opravy" znamená, že EF Core automaticky naplní navigační vlastnosti. Samostatné dotazy `Load` s je spíše explicitní načítání než eager načítání.

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  Poznámka: EF Core automaticky opravuje navigační vlastnosti na všechny ostatní entity, které byly dříve načteny do instance kontextu. I v případě, že data pro navigační vlastnost *není* explicitně zahrnuta, vlastnost může být stále naplněna, pokud byly dříve načteny některé nebo všechny související entity.

* [Explicitní načítání](/ef/core/querying/related-data#explicit-loading). Při prvním čtení entity se nenačtou související data. Kód musí být zapsán, aby bylo možné načíst související data, když je to potřeba. Explicitní načítání se samostatnými dotazy má za následek více dotazů odeslaných do databáze. Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny. Použijte `Load` metodu k explicitnímu načítání. Příklad:

  ![Příklad explicitního načítání](read-related-data/_static/explicit-loading.png)

* [Opožděné načítání](/ef/core/querying/related-data#lazy-loading). [Opožděné načtení bylo přidáno do EF Core ve verzi 2.1](/ef/core/querying/related-data#lazy-loading). Při prvním čtení entity se nenačtou související data. Při prvním přístupu k navigační vlastnosti jsou automaticky načtena data požadovaná pro tuto vlastnost navigace. Dotaz je odeslán do databáze pokaždé, když navigační vlastnost je přístupná poprvé.

## <a name="create-course-pages"></a>Vytvořit stránky kurzu

Entita `Course` obsahuje navigační vlastnost, `Department` která obsahuje související entitu.

![Course.Department](read-related-data/_static/dep-crs.png)

Zobrazení názvu přiřazeného oddělení kurzu:

* Načtěte `Department` související `Course.Department` entitu do vlastnosti navigace.
* Získejte název `Department` z `Name` vlastnosti entity.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Lešení Stránky kurzu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Postupujte podle pokynů na [stránkách Student lešení](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:

  * Vytvořte složku *Stránky/kurzy.*
  * Slouží `Course` pro třídu modelu.
  * Místo vytvoření nové třídy kontextu použijte existující třídu kontextu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Vytvořte složku *Stránky/kurzy.*

* Spusťte následující příkaz pro zaslístvo stránek kurzu.

  **Ve Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **Na Linuxu nebo macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* Otevřete *stránky/kurzy/index.cshtml.cs* a zkontrolujte metodu. `OnGetAsync` Modul lešení zadal dychtivé `Department` zatížení pro vlastnost navigace. Metoda `Include` určuje eager načítání.

* Spusťte aplikaci a vyberte odkaz **Kurzy.** Sloupec oddělení zobrazuje `DepartmentID`, což není užitečné.

### <a name="display-the-department-name"></a>Zobrazení názvu oddělení

Aktualizujte stránky/kurzy/index.cshtml.cs pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Předchozí kód změní `Course` vlastnost `Courses` a `AsNoTracking`přidá . `AsNoTracking`zlepšuje výkon, protože vrácené entity nejsou sledovány. Entity není nutné sledovat, protože nejsou aktualizovány v aktuálním kontextu.

Aktualizujte *stránky/kurzy/index.cshtml* pomocí následujícího kódu.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

V kódu skládaným lešení byly provedeny následující změny:

* Název `Course` vlastnosti `Courses`byl změněn na .
* Byl přidán sloupec **Číslo,** který zobrazuje hodnotu vlastnosti. `CourseID` Ve výchozím nastavení nejsou primární klíče skládané, protože obvykle nemají pro koncové uživatele smysl. V tomto případě je však primární klíč smysluplný.
* Sloupec **Oddělení** se změnil tak, aby se zobrazil název oddělení. Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do vlastnosti `Department` navigace:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Spusťte aplikaci a vyberte kartu **Kurzy,** abyste viděli seznam s názvy oddělení.

![Stránka Index kurzů](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Načítání souvisejících dat pomocí funkce Vybrat

Metoda `OnGetAsync` načte související `Include` data s metodou. Metoda `Select` je alternativou, která načte pouze související data potřebná. Pro jednotlivé položky, jako `Department.Name` je sql inner join. Pro kolekce používá jiný přístup k databázi, ale stejně tak operátor na `Include` kolekce.

Následující kód načte související `Select` data s metodou:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

V: `CourseViewModel`

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Úplný příklad najdete v [tématu IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)

## <a name="create-instructor-pages"></a>Vytvořit stránky instruktora

Tato část scaffolds instruktor stránky a přidá související kurzy a zápisy do index instruktorů stránky.

<a name="IP"></a>
![Stránka Index instruktorů](read-related-data/_static/instructors-index30.png)

Tato stránka čte a zobrazuje související data následujícími způsoby:

* Seznam instruktorů zobrazuje související data `OfficeAssignment` z entity (Office na předchozím obrázku). Entity `Instructor` `OfficeAssignment` a jsou ve vztahu 1:0 nebo 1. Eager načítání se `OfficeAssignment` používá pro entity. Eager načítání je obvykle efektivnější, když je třeba zobrazit související data. V tomto případě jsou zobrazeny kancelářské úkoly pro instruktory.
* Když uživatel vybere instruktora, `Course` zobrazí se související entity. A `Instructor` `Course` entity jsou ve vztahu N:N. Eager načítání se `Course` používá pro entity `Department` a jejich související entity. V takovém případě mohou být samostatné dotazy efektivnější, protože jsou potřeba pouze kurzy pro vybraného instruktora. Tento příklad ukazuje, jak použít dychtivé načítání pro navigační vlastnosti v entitách, které jsou v navigačních vlastnostech.
* Když uživatel vybere kurz, zobrazí se `Enrollments` související data z entity. Na předchozím obrázku se zobrazí jméno a hodnocení studenta. A `Course` `Enrollment` entity jsou ve vztahu 1:N.

### <a name="create-a-view-model"></a>Vytvoření modelu zobrazení

Stránka instruktoři zobrazuje data ze tří různých tabulek. Je potřeba model zobrazení, který obsahuje tři vlastnosti představující tři tabulky.

Vytvořte *schoolviewmodels/instructorindexdata.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Stránky instruktora lešení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Postupujte podle pokynů v [Lešení student stránky](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:

  * Vytvořte složku *Stránky/instruktoři.*
  * Slouží `Instructor` pro třídu modelu.
  * Místo vytvoření nové třídy kontextu použijte existující třídu kontextu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Vytvořte složku *Stránky/instruktoři.*

* Spusťte následující příkaz pro zakládání uživatelského lístí stránek instruktora.

  **Ve Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **Na Linuxu nebo macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Chcete-li zjistit, jak vypadá stránka s lešením, než ji aktualizujete, spusťte aplikaci a přejděte na stránku Instruktoři.

Aktualizujte *stránky/instruktoři/Index.cshtml.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

Metoda `OnGetAsync` přijímá volitelná data trasy pro ID vybraného instruktora.

Zkontrolujte dotaz v souboru *Pages/Instructors/Index.cshtml.cs:*

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

Kód určuje nedočkavé načítání pro následující navigační vlastnosti:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Všimněte si `Include` opakování `ThenInclude` a `CourseAssignments` `Course`metody pro a . Toto opakování je nezbytné určit dychtivé načítání `Course` pro dvě navigační vlastnosti entity.

Následující kód se spustí, když`id != null`je vybrán instruktor ( ).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Vybraný instruktor je načten ze seznamu instruktorů v modelu zobrazení. `Courses` Vlastnost modelu zobrazení je načtena `Course` s entitami `CourseAssignments` z navigační vlastnosti tohoto instruktora.

Metoda `Where` vrátí kolekci. V tomto případě však filtr vybere jednu entitu. proto `Single` je metoda volána k převodu kolekce na jednu `Instructor` entitu. Entita `Instructor` poskytuje `CourseAssignments` přístup k vlastnosti. `CourseAssignments`poskytuje přístup k `Course` propojeným subjektům.

![Instruktor-kurzy m:M](complex-data-model/_static/courseassignment.png)

Metoda `Single` se používá v kolekci, pokud kolekce má pouze jednu položku. Metoda `Single` vyvolá výjimku, pokud je kolekce prázdná nebo pokud existuje více než jedna položka. Alternativou `SingleOrDefault`je , která vrátí výchozí hodnotu (null v tomto případě), pokud je kolekce prázdná.

Následující kód naplní `Enrollments` vlastnost modelu zobrazení, když je vybrán kurz:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Aktualizace stránky Rejstříku instruktorů

Aktualizujte *stránky/instruktoři/Index.cshtml* pomocí následujícího kódu.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Předchozí kód provede následující změny:

* Aktualizuje `page` direktivu z `@page` na . `@page "{id:int?}"` `"{id:int?}"`je šablona trasy. Šablona trasy změní řetězce dotazů celé číslo v adrese URL pro směrování dat. Například kliknutím na odkaz **Vybrat** pro instruktora s pouze `@page` direktivou se zobrazí adresa URL, jako je tato:

  `https://localhost:5001/Instructors?id=2`

  Pokud je `@page "{id:int?}"`direktiva stránky , adresa URL je:

  `https://localhost:5001/Instructors/2`

* Přidá sloupec **Office,** který se zobrazí `item.OfficeAssignment.Location` pouze v případě, `item.OfficeAssignment` že nemá hodnotu null. Vzhledem k tomu, že se jedná o vztah 1:Nula nebo jeden, nemusí existovat související entita OfficeAssignment.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Přidá sloupec **Kurzy,** který zobrazuje kurzy vyučované jednotlivými instruktory. Další informace o této syntaxi holicího strojku naleznete [v tématu Explicitní přechod řádku.](xref:mvc/views/razor#explicit-line-transition)

* Přidá kód, který `class="success"` dynamicky přidá k `tr` prvku vybraného instruktora a kurzu. Tím nastavíte barvu pozadí pro vybraný řádek pomocí třídy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Přidá nový hypertextový odkaz s názvem **Vybrat**. Tento odkaz odešle ID vybraného `Index` instruktora metodě a nastaví barvu pozadí.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Přidá tabulku kurzů pro vybraného instruktora.

* Přidá tabulku zápisů studentů pro vybraný kurz.

Spusťte aplikaci a vyberte kartu **Instruktoři.** Na stránce `Location` se zobrazí (kancelář) ze související `OfficeAssignment` entity. Pokud `OfficeAssignment` je null, zobrazí se prázdná buňka tabulky.

Klikněte na odkaz **Vybrat** pro instruktora. Zobrazí se změny stylu řádku a kurzy přiřazené tomuto instruktorovi.

Výběrem kurzu zobrazíte seznam zapsaných studentů a jejich známky.

![Instruktoři Index stránky instruktor a kurz vybrán](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Použití jednoduchého

Metoda `Single` může předat `Where` v podmínce `Where` namísto volání metody samostatně:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Použití `Single` s Podmínka Kde je záležitostí osobních preferencí. Poskytuje žádné výhody oproti `Where` použití metody.

## <a name="explicit-loading"></a>Explicitní načítání

Aktuální kód určuje nedočkavé načítání pro `Enrollments` a `Students`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Předpokládejme, že uživatelé zřídka chtějí zobrazit zápisy v kurzu. V takovém případě optimalizace by bylo načíst pouze data zápisu, pokud je požadováno. V této části `OnGetAsync` je aktualizován použít `Enrollments` explicitní `Students`načítání a .

Aktualizujte *stránky/instruktoři/Index.cshtml.cs* pomocí následujícího kódu.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Předchozí kód klesne *ThenInclude* volání metody pro zápis a data studenta. Pokud je vybrán kurz, načte se explicitní načítací kód:

* Entity `Enrollment` pro vybraný kurz.
* Entity `Student` pro každý `Enrollment`.

Všimněte si, že `.AsNoTracking()`předchozí kód komentáře ven . Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.

Otestujete aplikaci. Z pohledu uživatele se aplikace chová stejně jako předchozí verze.

## <a name="next-steps"></a>Další kroky

Další kurz ukazuje, jak aktualizovat související data.

>[!div class="step-by-step"]
>[Předchozí kurz](xref:data/ef-rp/complex-data-model)
>[Další kurz](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se číst a zobrazovat související data. Související data jsou data, která EF Core načte do navigačních vlastností.

Pokud narazíte na problémy, které nemůžete vyřešit, [stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Stáhnout pokyny](xref:index#how-to-download-a-sample).

Následující ilustrace znázornit dokončené stránky pro tento kurz:

![Stránka Index kurzů](read-related-data/_static/courses-index.png)

![Stránka Index instruktorů](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Dychtivé, explicitní a opožděné načítání souvisejících dat

Existuje několik způsobů, jak EF Core můžete načíst související data do navigačnívlastnosti entity:

* [Dychtivé načítání](/ef/core/querying/related-data#eager-loading). Eager loading je, když dotaz pro jeden typ entity také načte související entity. Při čtení entity se načtou její související data. To obvykle vede k dotazu na jedno spojení, který načte všechna data, která je potřeba. EF Core vydá více dotazů pro některé typy dychtivé načítání. Vydávání více dotazů může být efektivnější, než tomu bylo v případě některých dotazů v EF6, kde byl jeden dotaz. Eager loading je `Include` zadán `ThenInclude` s a metody.

  ![Příklad dychtivénačítání](read-related-data/_static/eager-loading.png)
 
  Eager načítání odešle více dotazů, pokud je zahrnuta navigace kolekce:

  * Jeden dotaz pro hlavní dotaz 
  * Jeden dotaz pro každou kolekci "okraj" ve stromu zatížení.

* Samostatné dotazy `Load`s : Data lze načíst v samostatných dotazech a EF Core "opravuje" navigační vlastnosti. "Opravy" znamená, že EF Core automaticky naplní navigační vlastnosti. Samostatné dotazy `Load` s je spíše explicitní načítání než eager načítání.

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  Poznámka: EF Core automaticky opravuje navigační vlastnosti na všechny ostatní entity, které byly dříve načteny do instance kontextu. I v případě, že data pro navigační vlastnost *není* explicitně zahrnuta, vlastnost může být stále naplněna, pokud byly dříve načteny některé nebo všechny související entity.

* [Explicitní načítání](/ef/core/querying/related-data#explicit-loading). Při prvním čtení entity se nenačtou související data. Kód musí být zapsán, aby bylo možné načíst související data, když je to potřeba. Explicitní načítání se samostatnými dotazy má za následek více dotazů odeslaných do db. Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny. Použijte `Load` metodu k explicitnímu načítání. Příklad:

  ![Příklad explicitního načítání](read-related-data/_static/explicit-loading.png)

* [Opožděné načítání](/ef/core/querying/related-data#lazy-loading). [Opožděné načtení bylo přidáno do EF Core ve verzi 2.1](/ef/core/querying/related-data#lazy-loading). Při prvním čtení entity se nenačtou související data. Při prvním přístupu k navigační vlastnosti jsou automaticky načtena data požadovaná pro tuto vlastnost navigace. Dotaz je odeslán do DB pokaždé, když navigační vlastnost je přístupná poprvé.

* Operátor `Select` načte pouze související potřebná data.

## <a name="create-a-course-page-that-displays-department-name"></a>Vytvoření stránky kurzu, která zobrazuje název oddělení

Entita Course obsahuje navigační `Department` vlastnost, která obsahuje entitu. Entita `Department` obsahuje oddělení, ke kterému je kurz přiřazen.

Zobrazení názvu přiřazeného oddělení v seznamu kurzů:

* Získejte `Name` vlastnost `Department` z entity.
* Entita `Department` pochází `Course.Department` z navigační vlastnosti.

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Lešení modelu kurzu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [lešení model studenta](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Course` pro model třídy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Předchozí příkaz uchylovací `Course` příkaz y model. Otevřete projekt v sadě Visual Studio.

Otevřete *stránky/kurzy/index.cshtml.cs* a zkontrolujte metodu. `OnGetAsync` Modul lešení zadal dychtivé `Department` zatížení pro vlastnost navigace. Metoda `Include` určuje eager načítání.

Spusťte aplikaci a vyberte odkaz **Kurzy.** Sloupec oddělení zobrazuje `DepartmentID`, což není užitečné.

Aktualizujte `OnGetAsync` metodu následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Předchozí kód přidá `AsNoTracking`. `AsNoTracking`zlepšuje výkon, protože vrácené entity nejsou sledovány. Entity nejsou sledovány, protože nejsou aktualizovány v aktuálním kontextu.

Aktualizujte *stránky/kurzy/index.cshtml* pomocí následujících zvýrazněných značek:

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

V kódu skládaným lešení byly provedeny následující změny:

* Nadpis byl změněn z rejstříku na Kurzy.
* Byl přidán sloupec **Číslo,** který zobrazuje hodnotu vlastnosti. `CourseID` Ve výchozím nastavení nejsou primární klíče skládané, protože obvykle nemají pro koncové uživatele smysl. V tomto případě je však primární klíč smysluplný.
* Sloupec **Oddělení** se změnil tak, aby se zobrazil název oddělení. Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do vlastnosti `Department` navigace:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Spusťte aplikaci a vyberte kartu **Kurzy,** abyste viděli seznam s názvy oddělení.

![Stránka Index kurzů](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Načítání souvisejících dat pomocí funkce Vybrat

Metoda `OnGetAsync` načte související `Include` data s metodou:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Operátor `Select` načte pouze související potřebná data. Pro jednotlivé položky, jako `Department.Name` je sql inner join. Pro kolekce používá jiný přístup k databázi, ale stejně tak operátor na `Include` kolekce.

Následující kód načte související `Select` data s metodou:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

V: `CourseViewModel`

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Úplný příklad najdete v [tématu IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Vytvoření stránky instruktoři, která zobrazuje kurzy a zápisy

V této části je vytvořena stránka Instruktoři.

<a name="IP"></a>
![Stránka Index instruktorů](read-related-data/_static/instructors-index.png)

Tato stránka čte a zobrazuje související data následujícími způsoby:

* Seznam instruktorů zobrazuje související data `OfficeAssignment` z entity (Office na předchozím obrázku). Entity `Instructor` `OfficeAssignment` a jsou ve vztahu 1:0 nebo 1. Eager načítání se `OfficeAssignment` používá pro entity. Eager načítání je obvykle efektivnější, když je třeba zobrazit související data. V tomto případě jsou zobrazeny kancelářské úkoly pro instruktory.
* Když uživatel vybere instruktora (Harui v předchozím `Course` obrázku), zobrazí se související entity. A `Instructor` `Course` entity jsou ve vztahu N:N. Eager načítání se `Course` používá pro entity `Department` a jejich související entity. V takovém případě mohou být samostatné dotazy efektivnější, protože jsou potřeba pouze kurzy pro vybraného instruktora. Tento příklad ukazuje, jak použít dychtivé načítání pro navigační vlastnosti v entitách, které jsou v navigačních vlastnostech.
* Když uživatel vybere kurz (Chemie v předchozím obrázku), `Enrollments` zobrazí se související data z entity. Na předchozím obrázku se zobrazí jméno a hodnocení studenta. A `Course` `Enrollment` entity jsou ve vztahu 1:N.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Vytvoření modelu zobrazení pro zobrazení indexu instruktora

Stránka instruktoři zobrazuje data ze tří různých tabulek. Je vytvořen model zobrazení, který zahrnuje tři entity představující tři tabulky.

Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Lešení instruktormodel

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [lešení model studenta](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Instructor` pro model třídy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Předchozí příkaz uchylovací `Instructor` příkaz y model. 
Spusťte aplikaci a přejděte na stránku instruktoři.

Nahraďte *stránky/instruktoři/index.cshtml.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

Metoda `OnGetAsync` přijímá volitelná data trasy pro ID vybraného instruktora.

Zkontrolujte dotaz v souboru *Pages/Instructors/Index.cshtml.cs:*

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Dotaz má dvě zahrnuje:

* `OfficeAssignment`: Zobrazeno v [zobrazení instruktorů](#IP).
* `CourseAssignments`: Což přináší vyučované kurzy.

### <a name="update-the-instructors-index-page"></a>Aktualizace stránky Rejstříku instruktorů

Aktualizujte *stránky/instruktoři/Index.cshtml* pomocí následujících značek:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Předchozí značky provede následující změny:

* Aktualizuje `page` direktivu z `@page` na . `@page "{id:int?}"` `"{id:int?}"`je šablona trasy. Šablona trasy změní řetězce dotazů celé číslo v adrese URL pro směrování dat. Například kliknutím na odkaz **Vybrat** pro instruktora s pouze `@page` direktivou se zobrazí adresa URL, jako je tato:

  `http://localhost:1234/Instructors?id=2`

  Pokud je `@page "{id:int?}"`direktiva stránky , předchozí adresa URL je:

  `http://localhost:1234/Instructors/2`

* Název stránky je **Instruktoři**.
* Přidán sloupec **Office,** který se zobrazí `item.OfficeAssignment.Location` pouze v případě, `item.OfficeAssignment` že nemá hodnotu null. Vzhledem k tomu, že se jedná o vztah 1:Nula nebo jeden, nemusí existovat související entita OfficeAssignment.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Byl přidán sloupec **Kurzy,** který zobrazuje kurzy vyučované jednotlivými instruktory. Další informace o této syntaxi holicího strojku naleznete [v tématu Explicitní přechod řádku.](xref:mvc/views/razor#explicit-line-transition)

* Přidán kód, který `class="success"` dynamicky přidává do `tr` prvku vybraného instruktora. Tím nastavíte barvu pozadí pro vybraný řádek pomocí třídy Bootstrap.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Byl přidán nový hypertextový odkaz s názvem **Select**. Tento odkaz odešle ID vybraného `Index` instruktora metodě a nastaví barvu pozadí.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Spusťte aplikaci a vyberte kartu **Instruktoři.** Na stránce `Location` se zobrazí (kancelář) ze související `OfficeAssignment` entity. Pokud OfficeAssignment' je null, zobrazí se prázdná buňka tabulky.

Klikněte na odkaz **Vybrat.** Styl řádku se změní.

### <a name="add-courses-taught-by-selected-instructor"></a>Přidat kurzy vyučované vybraným instruktorem

Aktualizujte `OnGetAsync` metodu na *pages/instructors/Index.cshtml.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Přidat`public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Zkontrolujte aktualizovaný dotaz:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Předchozí dotaz přidá `Department` entity.

Následující kód se spustí, když`id != null`je vybrán instruktor ( ). Vybraný instruktor je načten ze seznamu instruktorů v modelu zobrazení. `Courses` Vlastnost modelu zobrazení je načtena `Course` s entitami `CourseAssignments` z navigační vlastnosti tohoto instruktora.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

Metoda `Where` vrátí kolekci. V předchozí `Where` metodě je `Instructor` vrácena pouze jedna entita. Metoda `Single` převede kolekci na `Instructor` jednu entitu. Entita `Instructor` poskytuje `CourseAssignments` přístup k vlastnosti. `CourseAssignments`poskytuje přístup k `Course` propojeným subjektům.

![Instruktor-kurzy m:M](complex-data-model/_static/courseassignment.png)

Metoda `Single` se používá v kolekci, pokud kolekce má pouze jednu položku. Metoda `Single` vyvolá výjimku, pokud je kolekce prázdná nebo pokud existuje více než jedna položka. Alternativou `SingleOrDefault`je , která vrátí výchozí hodnotu (null v tomto případě), pokud je kolekce prázdná. Použití `SingleOrDefault` na prázdnou kolekci:

* Výsledkem je výjimka (z `Courses` pokusu o nalezení vlastnosti na nulový odkaz).
* Zpráva o výjimce by méně jasně označovat příčinu problému.

Následující kód naplní `Enrollments` vlastnost modelu zobrazení, když je vybrán kurz:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Na konec *stránky/instruktoři/index.cshtml* Holicí strojek přidejte následující značky:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Předchozí značky zobrazí seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.

Otestujete aplikaci. Klikněte na odkaz **Vybrat** na stránce instruktoři.

### <a name="show-student-data"></a>Zobrazit údaje o studentech

V této části je aplikace aktualizována tak, aby zobrazovala data studentů pro vybraný kurz.

Aktualizujte dotaz `OnGetAsync` v metodě v *pages/instructors/Index.cshtml.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Aktualizovat *stránky/instruktoři/Index.cshtml*. Na konec souboru přidejte následující značky:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Předchozí značky zobrazí seznam studentů, kteří jsou zapsáni ve vybraném kurzu.

Aktualizujte stránku a vyberte instruktora. Výběrem kurzu zobrazíte seznam zapsaných studentů a jejich známky.

![Instruktoři Index stránky instruktor a kurz vybrán](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Použití jednoduchého

Metoda `Single` může předat `Where` v podmínce `Where` namísto volání metody samostatně:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Předchozí `Single` přístup poskytuje žádné výhody `Where`nad pomocí . Někteří vývojáři `Single` dávají přednost stylu přístupu.

## <a name="explicit-loading"></a>Explicitní načítání

Aktuální kód určuje nedočkavé načítání pro `Enrollments` a `Students`:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Předpokládejme, že uživatelé zřídka chtějí zobrazit zápisy v kurzu. V takovém případě optimalizace by bylo načíst pouze data zápisu, pokud je požadováno. V této části `OnGetAsync` je aktualizován použít `Enrollments` explicitní `Students`načítání a .

`OnGetAsync` Aktualizujte následující kód:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Předchozí kód klesne *ThenInclude* volání metody pro zápis a data studenta. Pokud je vybrán kurz, zvýrazněný kód se načte:

* Entity `Enrollment` pro vybraný kurz.
* Entity `Student` pro každý `Enrollment`.

Všimněte si předchozíkód `.AsNoTracking()`komentáře ven . Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.

Otestujete aplikaci. Z pohledu uživatelů se aplikace chová stejně jako předchozí verze.

Další kurz ukazuje, jak aktualizovat související data.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto výukového programu na YouTube (část1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Verze tohoto výukového programu na YouTube (část2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Předchozí](xref:data/ef-rp/complex-data-model)
>[další](xref:data/ef-rp/update-related-data)

::: moniker-end

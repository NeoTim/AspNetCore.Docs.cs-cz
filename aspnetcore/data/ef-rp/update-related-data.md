---
title: Razor Stránky s EF core v ASP.NET Core - Aktualizace souvisejících dat - 7 z 8
author: rick-anderson
description: V tomto kurzu aktualizujete související data aktualizací polí cizího klíče a navigačních vlastností.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: fdfdb14ff8414b8bf30f9b95be7ba0a6bcbd2995
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656420"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>Razor Stránky s EF core v ASP.NET Core - Aktualizace souvisejících dat - 7 z 8

[Tom Dykstra](https://github.com/tdykstra), a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Tento kurz ukazuje, jak aktualizovat související data. Následující ilustrace znázornit některé z dokončených stránek.

![Stránka Úpravy kurzu](update-related-data/_static/course-edit30.png)
![Stránka pro úpravy instruktora](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Aktualizace stránek Pro vytvoření a úpravy kurzu

Kód scaffolded pro stránky Vytvoření a úpravy kurzu má rozevírací seznam oddělení, který zobrazuje ID oddělení (celé číslo). V rozevíracím seznamu by měl být uveden název oddělení, takže obě tyto stránky potřebují seznam názvů oddělení. Chcete-li tento seznam poskytnout, použijte základní třídu pro stránky Vytvořit a upravit.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Vytvoření základní třídy pro vytváření a úpravy kurzu

Vytvořte soubor *Pages/Courses/DepartmentNamePageModel.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) obsahovat seznam názvů oddělení. Pokud `selectedDepartment` je zadán, toto `SelectList`oddělení je vybránv .

Třídy modelu stránky Create and `DepartmentNamePageModel`Edit budou odvozeny z aplikace .

### <a name="update-the-course-create-page-model"></a>Aktualizace modelu stránky Vytvořit kurz

Kurz je přiřazen oddělení. Základní třída pro stránky Vytvořit a `SelectList` upravit poskytuje pro výběr oddělení. Rozevírací seznam, který `SelectList` používá `Course.DepartmentID` nastaví vlastnost cizího klíče (FK). EF Core `Course.DepartmentID` používá FK `Department` k načtení navigační vlastnosti.

![Vytvořit kurz](update-related-data/_static/ddl30.png)

Aktualizovat *stránky/kurzy/Create.cshtml.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Předcházející kód:

* Pochází z `DepartmentNamePageModel`.
* Používá `TryUpdateModelAsync` se k [zabránění nadměrnému zaúčtování](xref:data/ef-rp/crud#overposting).
* Odebere `ViewData["DepartmentID"]`. `DepartmentNameSL`ze základní třídy je model silného typu a bude použit na stránce Razor. Modely silného typu jsou upřednostňovány před slabě nadanými. Další informace naleznete [v tématu Slabě zadaný data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-razor-page"></a>Aktualizace stránky Vytvořit holicí strojek

Aktualizovat *stránky/kurzy/Create.cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí kód provede následující změny:

* Změní titulek z **DepartmentID** na **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` (ze `DepartmentNameSL` základní třídy).
* Přidá možnost Vybrat oddělení. Tato změna vykreslí "Vybrat oddělení" v rozevíracím souboru, pokud ještě nebylo vybráno žádné oddělení, nikoli první oddělení.
* Přidá ověřovací zprávu, když není vybráno oddělení.

Stránka Razor používá pomocníka [pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Otestujte stránku Vytvořit. Na stránce Vytvořit se zobrazí název oddělení, nikoli ID oddělení.

### <a name="update-the-course-edit-page-model"></a>Aktualizace modelu stránky Úpravy kurzu

Aktualizovat *stránky/kurzy/Edit.cshtml.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Změny jsou podobné těm, které byly provedeny v modelu vytvořit stránku. V předchozím kódu `PopulateDepartmentsDropDownList` předá id oddělení, které vybere toto oddělení v rozevíracím seznamu.

### <a name="update-the-course-edit-razor-page"></a>Aktualizace stránky Úpravy kurzu

Aktualizovat *stránky/kurzy/Edit.cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Předchozí kód provede následující změny:

* Zobrazí ID kurzu. Obecně se nezobrazuje primární klíč (PK) entity. PKs jsou obvykle bezvýznamné pro uživatele. V tomto případě pk je číslo kurzu.
* Změní popisek pro oddělení drop-down z **DepartmentID** na **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` (ze `DepartmentNameSL` základní třídy).

Stránka obsahuje skryté pole`<input type="hidden">`( ) pro číslo kurzu. Přidání `<label>` pomocníka pro `asp-for="Course.CourseID"` značky nevylučuje potřebu skrytého pole. `<input type="hidden">`je vyžadováno číslo kurzu, které má být zahrnuto do zaúčtovaných dat, když uživatel klikne na **Uložit**.

## <a name="update-the-course-details-and-delete-pages"></a>Aktualizace podrobností kurzu a odstranění stránek

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zlepšit výkon, když sledování není vyžadováno.

### <a name="update-the-course-page-models"></a>Aktualizace modelů stránky kurzu

Aktualizovat *stránky/kurzy/Delete.cshtml.cs* s následujícím `AsNoTracking`kódem pro přidání :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Proveďte stejnou změnu v souboru *Stránky/Kurzy/Details.cshtml.cs:*

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a>Aktualizace stránek Kurzu Břitva

Aktualizujte *stránky/kurzy/Delete.cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Proveďte stejné změny na stránce Podrobnosti.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Otestujte stránky kurzu

Otestujte stránky pro vytváření, úpravy, podrobnosti a odstranění.

## <a name="update-the-instructor-create-and-edit-pages"></a>Aktualizace stránek pro vytváření a úpravy instruktora

Instruktoři mohou vyučovat libovolný počet kurzů. Na následujícím obrázku je zobrazena stránka Úpravinstruktora se zaškrtávacími políčky pole kurzu.

![Stránka úprav instruktora s kurzy](update-related-data/_static/instructor-edit-courses30.png)

Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen. Pro každý kurz v databázi se zobrazí zaškrtávací políčko. Jsou vybrány kurzy, ke kterým je instruktor přiřazen. Uživatel může vybrat nebo vymazat zaškrtávací políčka pro změnu přiřazení kurzu. Pokud počet kurzů byly mnohem větší, jiné uI může fungovat lépe. Ale způsob řízení mnoha-k-n vztah zde uvedeno by se nezměnilo. Chcete-li vytvořit nebo odstranit vztahy, manipulujte s entitou spojení.

### <a name="create-a-class-for-assigned-courses-data"></a>Vytvoření kurzu pro přiřazená data kurzů

Vytvořte *SchoolViewModels/AssignedCourseData.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

Třída `AssignedCourseData` obsahuje data pro vytvoření zaškrtávacích políček pro kurzy přiřazené instruktorovi.

### <a name="create-an-instructor-page-model-base-class"></a>Vytvoření základní třídy modelu stránky instruktora

Vytvořte základní třídu *Pages/Instructors/InstructorCoursesPageModel.cs:*

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

Jedná `InstructorCoursesPageModel` se o základní třídu, kterou použijete pro modely stránek Úpravy a Vytváření. `PopulateAssignedCourseData`přečte `Course` všechny entity `AssignedCourseDataList`k naplnění . Pro každý kurz kód `CourseID`nastaví , název a zda je instruktor přiřazen ke kurzu. Sada [hashset](/dotnet/api/system.collections.generic.hashset-1) se používá pro efektivní vyhledávání.

Vzhledem k tomu, že stránka Razor nemá kolekci entit Course, `CourseAssignments` vězníní modelu nemůže automaticky aktualizovat vlastnost navigace. Namísto použití vazače `CourseAssignments` modelu k aktualizaci vlastnosti `UpdateInstructorCourses` navigace, uděláte to v nové metodě. Proto je třeba `CourseAssignments` vyloučit vlastnost z vazby modelu. To nevyžaduje žádnou změnu kódu, `TryUpdateModel` který volá, protože používáte `CourseAssignments` whitelisting přetížení a není v seznamu zahrnutí.

Pokud nebyla zaškrtnuta žádná `UpdateInstructorCourses` políčka, kód `CourseAssignments` v inicializuje vlastnost navigace s prázdnou kolekcí a vrátí:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Kód pak smyčky prostřednictvím všech kurzů v databázi a kontroluje každý kurz proti ty, které jsou aktuálně přiřazeny instruktora versus ty, které byly vybrány na stránce. Pro usnadnění efektivní vyhledávání, poslední dvě kolekce `HashSet` jsou uloženy v objektech.

Pokud bylo zaškrtnuto políčko pro kurz, ale `Instructor.CourseAssignments` kurz není v navigační vlastnosti, kurz se přidá do kolekce v navigační vlastnosti.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Pokud nebylo zaškrtnuto políčko kurzu, ale kurz je `Instructor.CourseAssignments` v navigační vlastnosti, kurz se odebere z vlastnosti navigace.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Zpracování umístění kanceláře

Jiný vztah, který má stránka pro úpravy zpracovat, je vztah 1:nula nebo jeden, který má entita Instruktor s entitou. `OfficeAssignment` Kód úprav instruktora musí zpracovávat následující scénáře: 

* Pokud uživatel vymaže přiřazení `OfficeAssignment` kanceláře, odstraňte entitu.
* Pokud uživatel zadá přiřazení kanceláře a bude prázdné, vytvořte novou `OfficeAssignment` entitu.
* Pokud uživatel změní přiřazení kanceláře, `OfficeAssignment` aktualizujte entitu.

### <a name="update-the-instructor-edit-page-model"></a>Aktualizace modelu stránky Úpravy instruktora

Aktualizujte *stránky/instruktoři/Edit.cshtml.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Předcházející kód:

* Získá aktuální `Instructor` entitu z databáze `OfficeAssignment`pomocí `CourseAssignment`eager `CourseAssignment.Course` načítání pro , a navigační vlastnosti.
* Aktualizuje načtenou `Instructor` entitu hodnotami z pořadače modelu. `TryUpdateModel`zabraňuje [přeúčtování](xref:data/ef-rp/crud#overposting).
* Pokud je umístění kanceláře `Instructor.OfficeAssignment` prázdné, nastaví hodnotu null. Pokud `Instructor.OfficeAssignment` je null, související `OfficeAssignment` řádek v tabulce je odstraněn.
* Volání `PopulateAssignedCourseData` `OnGetAsync` zaškrtávací políčka `AssignedCourseData` pomocí třídy modelu zobrazení.
* `UpdateInstructorCourses` Volá, `OnPostAsync` aby se informace z políček použít instruktor entity upravované.
* Volání `PopulateAssignedCourseData` `UpdateInstructorCourses` a `OnPostAsync` `TryUpdateModel` v případě selhání. Tato volání metod obnoví přiřazená data kurzu zadaná na stránce při opětovném zobrazení s chybovou zprávou.

### <a name="update-the-instructor-edit-razor-page"></a>Aktualizace stránky Úpravy břitvy instruktora

Aktualizujte *stránky/instruktoři/Edit.cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Předchozí kód vytvoří tabulku HTML, která má tři sloupce. Každý sloupec má zaškrtávací políčko a titulek obsahující číslo kurzu a název. Všechna zaškrtávací políčka mají stejný název ("vybrané kurzy"). Použití stejného názvu informuje pořadač modelu, aby s nimi zacházel jako se skupinou. Atribut hodnoty každého zaškrtávacího políčka je nastaven na . `CourseID` Po zaúčtování stránky předává pořadač modelu pole, `CourseID` které se skládá z hodnot pouze pro vybraná zaškrtávací políčka.

Při počátečním vykreslení zaškrtávacích políček jsou vybrány kurzy přiřazené instruktorovi.

Poznámka: Přístup, který zde byl přijat k úpravě dat instruktorského kurzu, funguje dobře, když je omezený počet kurzů. Pro kolekce, které jsou mnohem větší, jiné ui a různé metody aktualizace by bylo více použitelné a efektivní.

Spusťte aplikaci a otestujte aktualizovanou stránku Úpravinstruktorů. Změňte některé úkoly kurzu. Změny se projeví na stránce Index.

### <a name="update-the-instructor-create-page"></a>Aktualizace stránky Vytvoření instruktora

Aktualizujte model stránky Vytvoření instruktora a stránku Holicí strojek s kódem podobným stránce Úpravy:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Otestujte stránku Vytvoření instruktora.

## <a name="update-the-instructor-delete-page"></a>Aktualizace stránky Odstranění instruktora

Aktualizujte *stránky/instruktoři/Delete.cshtml.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Předchozí kód provede následující změny:

* Používá eager načítání `CourseAssignments` pro navigační vlastnost. `CourseAssignments`musí být zahrnuty, nebo nejsou odstraněny, když je instruktor odstraněn. Chcete-li se vyhnout nutnosti jejich čtení, nakonfigurujte kaskádové odstranění v databázi.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce všech oddělení, odebere přiřazení instruktora z těchto oddělení.

Spusťte aplikaci a otestujte stránku Odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/read-related-data)
> [Další kurz](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz ukazuje aktualizaci souvisejících dat. Pokud narazíte na problémy, které nemůžete vyřešit, [stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Stáhnout pokyny](xref:index#how-to-download-a-sample).

Následující ilustrace znázorňuje některé dokončené stránky.

![Stránka Úpravy kurzu](update-related-data/_static/course-edit.png)
![Stránka pro úpravy instruktora](update-related-data/_static/instructor-edit-courses.png)

Prohlédněte si a otestujte stránky kurzu Vytvořit a upravit. Vytvořte nový kurz. Oddělení je vybráno jeho primárním klíčem (celé číslo), nikoli jeho názvem. Upravte nový kurz. Po dokončení testování odstraňte nový kurz.

## <a name="create-a-base-class-to-share-common-code"></a>Vytvoření základní třídy pro sdílení společného kódu

Kurzy/ Vytvořit a kurzy / Upravit stránky každý potřebovat seznam názvů oddělení. Vytvořte základní třídu *Pages/Courses/DepartmentNamePageModel.cshtml.cs* pro stránky Vytvořit a upravit:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) obsahovat seznam názvů oddělení. Pokud `selectedDepartment` je zadán, toto `SelectList`oddělení je vybránv .

Třídy modelu stránky Create and `DepartmentNamePageModel`Edit budou odvozeny z aplikace .

## <a name="customize-the-courses-pages"></a>Přizpůsobení stránek kurzů

Když je vytvořena nová entita kurzu, musí mít vztah k existujícímu oddělení. Chcete-li přidat oddělení při vytváření kurzu, základní třída pro vytvořit a upravit obsahuje rozevírací seznam pro výběr oddělení. Rozevírací seznam nastaví vlastnost cizího `Course.DepartmentID` klíče (FK). EF Core `Course.DepartmentID` používá FK `Department` k načtení navigační vlastnosti.

![Vytvořit kurz](update-related-data/_static/ddl.png)

Aktualizujte model stránky Vytvořit následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Předcházející kód:

* Pochází z `DepartmentNamePageModel`.
* Používá `TryUpdateModelAsync` se k [zabránění nadměrnému zaúčtování](xref:data/ef-rp/crud#overposting).
* Nahradí `ViewData["DepartmentID"]` (ze `DepartmentNameSL` základní třídy).

`ViewData["DepartmentID"]`je nahrazen silným typem `DepartmentNameSL`. Modely silného typu jsou upřednostňovány před slabě nadanými. Další informace naleznete [v tématu Slabě zadaný data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualizace stránky Vytvořit kurzy

Aktualizovat *stránky/kurzy/Create.cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí značky provede následující změny:

* Změní titulek z **DepartmentID** na **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` (ze `DepartmentNameSL` základní třídy).
* Přidá možnost Vybrat oddělení. Tato změna vykreslí "Vybrat oddělení" spíše než první oddělení.
* Přidá ověřovací zprávu, když není vybráno oddělení.

Stránka Razor používá pomocníka [pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Otestujte stránku Vytvořit. Na stránce Vytvořit se zobrazí název oddělení, nikoli ID oddělení.

### <a name="update-the-courses-edit-page"></a>Aktualizujte stránku Úpravy kurzů.

Nahraďte kód v *Pages/Courses/Edit.cshtml.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Změny jsou podobné těm, které byly provedeny v modelu vytvořit stránku. V předchozím kódu `PopulateDepartmentsDropDownList` předá v ID oddělení, které vybere oddělení zadané v rozevíracím seznamu.

Aktualizovat *stránky/kurzy/Edit.cshtml* pomocí následujících značek:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Předchozí značky provede následující změny:

* Zobrazí ID kurzu. Obecně se nezobrazuje primární klíč (PK) entity. PKs jsou obvykle bezvýznamné pro uživatele. V tomto případě pk je číslo kurzu.
* Změní titulek z **DepartmentID** na **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` (ze `DepartmentNameSL` základní třídy).

Stránka obsahuje skryté pole`<input type="hidden">`( ) pro číslo kurzu. Přidání `<label>` pomocníka pro `asp-for="Course.CourseID"` značky nevylučuje potřebu skrytého pole. `<input type="hidden">`je vyžadováno číslo kurzu, které má být zahrnuto do zaúčtovaných dat, když uživatel klikne na **Uložit**.

Otestujte aktualizovaný kód. Vytvořte, upravte a odstraňte kurz.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Přidat AsNoTracking do modelů stránek Podrobnosti a Odstranit

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zlepšit výkon, když sledování není vyžadováno. Přidat `AsNoTracking` do modelu stránky Odstranit a Podrobnosti. Následující kód zobrazuje aktualizovaný model stránky Delete:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Aktualizujte `OnGetAsync` metodu v souboru *Pages/Courses/Details.cshtml.cs:*

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Změna stránek Odstranit a Podrobnosti

Aktualizujte stránku Odstranit holicí strojek pomocí následujících značek:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Proveďte stejné změny na stránce Podrobnosti.

### <a name="test-the-course-pages"></a>Otestujte stránky kurzu

Vyzkoušejte vytvářet, upravovat, detaily a odstraňovat.

## <a name="update-the-instructor-pages"></a>Aktualizace stránek instruktora

V následujících částech aktualizovat stránky instruktora.

### <a name="add-office-location"></a>Přidání umístění kanceláře

Při úpravách záznamu instruktora můžete aktualizovat přiřazení kanceláře instruktora. Entita `Instructor` má vztah 1:0 nebo jedna `OfficeAssignment` s entitou. Kód instruktora musí zpracovávat:

* Pokud uživatel vymaže přiřazení `OfficeAssignment` kanceláře, odstraňte entitu.
* Pokud uživatel zadá přiřazení kanceláře a bude prázdné, vytvořte novou `OfficeAssignment` entitu.
* Pokud uživatel změní přiřazení kanceláře, `OfficeAssignment` aktualizujte entitu.

Aktualizujte instruktory Upravit model stránky pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Předcházející kód:

* Získá aktuální `Instructor` entitu z databáze `OfficeAssignment` pomocí eager načítání pro navigační vlastnost.
* Aktualizuje načtenou `Instructor` entitu hodnotami z pořadače modelu. `TryUpdateModel`zabraňuje [přeúčtování](xref:data/ef-rp/crud#overposting).
* Pokud je umístění kanceláře `Instructor.OfficeAssignment` prázdné, nastaví hodnotu null. Pokud `Instructor.OfficeAssignment` je null, související `OfficeAssignment` řádek v tabulce je odstraněn.

### <a name="update-the-instructor-edit-page"></a>Aktualizace stránky Úprav instruktora

Aktualizovat *stránky/instruktoři/Edit.cshtml* s umístěním kanceláře:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Ověřte, zda můžete změnit umístění instruktoři kanceláře.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Přidání úkolů kurzu na stránku Úpravy instruktora

Instruktoři mohou vyučovat libovolný počet kurzů. V této části přidáte možnost změnit přiřazení kurzu. Následující obrázek znázorňuje aktualizovanou stránku úprav instruktora:

![Stránka úprav instruktora s kurzy](update-related-data/_static/instructor-edit-courses.png)

`Course`a `Instructor` má vztah n:N. Chcete-li přidat a odebrat vztahy, `CourseAssignments` přidejte a odeberte entity ze sady entit spojení.

Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen. Pro každý kurz v databázi se zobrazí zaškrtávací políčko. Kurzy, ke kterým je instruktor přiřazen, jsou kontrolovány. Uživatel může zaškrtnout nebo zrušit zaškrtnutí políček pro změnu přiřazení kurzu. Pokud by počet kurzů byl mnohem větší:

* Pravděpodobně byste k zobrazení kurzů použili jiné uživatelské rozhraní.
* Způsob manipulace s entitou spojení za účelem vytvoření nebo odstranění relací by se nezměnil.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Přidání kurzů pro podporu vytváření a úprav stránek instruktora

Vytvořte *SchoolViewModels/AssignedCourseData.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Třída `AssignedCourseData` obsahuje data pro vytvoření zaškrtávacích políček pro přiřazené kurzy instruktorem.

Vytvořte základní třídu *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs:*

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

Jedná `InstructorCoursesPageModel` se o základní třídu, kterou použijete pro modely stránek Úpravy a Vytváření. `PopulateAssignedCourseData`přečte `Course` všechny entity `AssignedCourseDataList`k naplnění . Pro každý kurz kód `CourseID`nastaví , název a zda je instruktor přiřazen ke kurzu. Sada [hashset](/dotnet/api/system.collections.generic.hashset-1) se používá k vytvoření efektivní vyhledávání.

### <a name="instructors-edit-page-model"></a>Instruktoři Upravit model stránky

Aktualizujte model stránky pro úpravy instruktora pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Předchozí kód zpracovává změny přiřazení kanceláře.

Aktualizace zobrazení instruktora Razor:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Při vložení kódu v sadě Visual Studio se konce řádků změní způsobem, který přeruší kód. Jedním stisknutím kombinace kláves Ctrl+Z můžete automatické formátování vrátit zpět. Ctrl+Z opravuje konce řádků tak, aby vypadaly tak, jak vidíte zde. Odsazení nemusí být dokonalé, ale `@:</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` a řádky musí být na jednom řádku, jak je znázorněno. Pokud je vybrán blok nového kódu, třikrát stiskněte klávesu Tab, abyste nový kód zařazovali s existujícím kódem. Hlasujte o stavu této chyby nebo jej zkontrolujte [pomocí tohoto odkazu](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Předchozí kód vytvoří tabulku HTML, která má tři sloupce. Každý sloupec má zaškrtávací políčko a titulek obsahující číslo kurzu a název. Všechna zaškrtávací políčka mají stejný název ("vybrané kurzy"). Použití stejného názvu informuje pořadač modelu, aby s nimi zacházel jako se skupinou. Atribut hodnoty každého zaškrtávacího políčka je nastaven na hodnotu `CourseID`. Po zaúčtování stránky předává pořadač modelu pole, `CourseID` které se skládá z hodnot pouze pro vybraná zaškrtávací políčka.

Při počátečním vykreslení zaškrtávacích políček byly kurzy přiřazené instruktorovi zkontrolovány atributy.

Spusťte aplikaci a otestujte stránku pro úpravy aktualizovaných instruktorů. Změňte některé úkoly kurzu. Změny se projeví na stránce Index.

Poznámka: Přístup, který zde byl přijat k úpravě dat instruktorského kurzu, funguje dobře, když je omezený počet kurzů. Pro kolekce, které jsou mnohem větší, jiné ui a různé metody aktualizace by bylo více použitelné a efektivní.

### <a name="update-the-instructors-create-page"></a>Aktualizace stránky Vytvořit instruktory

Aktualizace instruktora Vytvořit model stránky s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Předchozí kód je podobný kódu *Pages/Instructors/Edit.cshtml.cs.*

Aktualizujte stránku Vytvoření břitvy instruktora pomocí následujících značek:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Otestujte stránku Vytvoření instruktora.

## <a name="update-the-delete-page"></a>Aktualizace stránky Odstranit

Aktualizujte model stránky Odstranit pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Předchozí kód provede následující změny:

* Používá eager načítání `CourseAssignments` pro navigační vlastnost. `CourseAssignments`musí být zahrnuty, nebo nejsou odstraněny, když je instruktor odstraněn. Chcete-li se vyhnout nutnosti jejich čtení, nakonfigurujte kaskádové odstranění v databázi.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce všech oddělení, odebere přiřazení instruktora z těchto oddělení.

## <a name="additional-resources"></a>Další zdroje

* [YouTube verze tohoto výukového programu (část 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [YouTube verze tohoto výukového programu (část 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/read-related-data)
> [další](xref:data/ef-rp/concurrency)

::: moniker-end

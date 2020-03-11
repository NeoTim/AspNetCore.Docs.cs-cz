---
title: Razor Pages s EF Core ve službě ASP.NET Core – data související s aktualizací – 7 z 8
author: rick-anderson
description: V tomto kurzu aktualizujete související data aktualizací polí cizího klíče a vlastností navigace.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: fdfdb14ff8414b8bf30f9b95be7ba0a6bcbd2995
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656420"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>Razor Pages s EF Core ve službě ASP.NET Core – data související s aktualizací – 7 z 8

[Dykstra](https://github.com/tdykstra)a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se dozvíte, jak aktualizovat související data. Následující ilustrace znázorňují některé z dokončených stránek.

Stránka pro úpravy kurzu ![](update-related-data/_static/course-edit30.png)
![stránka pro úpravy instruktora](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Aktualizovat stránky pro vytvoření a úpravu kurzu

Vygenerovaný kód pro stránky pro vytvoření a úpravu kurzu má rozevírací seznam oddělení, který zobrazuje ID oddělení (celé číslo). Rozevírací seznam by měl zobrazovat název oddělení, takže obě tyto stránky budou potřebovat seznam názvů oddělení. Chcete-li poskytnout tento seznam, použijte pro stránky vytvořit a upravit základní třídu.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Vytvoří základní třídu pro vytvoření a úpravu kurzu.

Vytvořte soubor *Pages/kurzys/DepartmentNamePageModel. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) , který bude obsahovat seznam názvů oddělení. Je-li zadán `selectedDepartment`, je toto oddělení vybráno v `SelectList`.

Třídy modelu stránky pro vytváření a úpravy budou odvozeny z `DepartmentNamePageModel`.

### <a name="update-the-course-create-page-model"></a>Aktualizace modelu stránky vytvoření kurzu

Do oddělení se přiřadí kurz. Základní třída pro stránky pro vytváření a úpravy poskytuje `SelectList` pro výběr oddělení. Rozevírací seznam, který používá `SelectList` nastaví vlastnost `Course.DepartmentID` cizího klíče (FK). EF Core používá `Course.DepartmentID` FK k načtení vlastnosti `Department` navigace.

![Vytvořit kurz](update-related-data/_static/ddl30.png)

Aktualizovat *stránky/kurzy/vytvořit. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Předchozí kód:

* Je odvozen z `DepartmentNamePageModel`.
* Nástroj používá `TryUpdateModelAsync` k zabránění [přestavení](xref:data/ef-rp/crud#overposting).
* Odebere `ViewData["DepartmentID"]`. `DepartmentNameSL` ze základní třídy je model silného typu a bude použit stránkou Razor. Modely silného typu jsou upřednostňovány přes slabě zadaná. Další informace najdete v tématu [slabě zadaná data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-razor-page"></a>Aktualizace stránky s vytvořením kurzu – vytvořit Razor

Aktualizovat *stránky/kurzy/vytvořit. cshtml* s následujícím kódem:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí kód provede následující změny:

* Změní titulek z **DepartmentID** na **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).
* Přidá možnost vybrat oddělení. Tato změna vykreslí "vybrat oddělení" v rozevíracím seznamu, pokud zatím nebylo vybráno žádné oddělení, nikoli první oddělení.
* Přidá ověřovací zprávu, když není vybrané oddělení.

Stránka Razor používá [Pomocníka pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Otestujte stránku vytvořit. Na stránce vytvořit se zobrazuje název oddělení, nikoli ID oddělení.

### <a name="update-the-course-edit-page-model"></a>Aktualizace modelu stránky pro úpravu kurzu

Aktualizovat *stránky/kurzy/upravit. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Změny jsou podobné těm, které byly provedeny v modelu vytvoření stránky. V předchozím kódu `PopulateDepartmentsDropDownList` předá do ID oddělení, které v rozevíracím seznamu vybere toto oddělení.

### <a name="update-the-course-edit-razor-page"></a>Aktualizace stránky kurz úpravy Razor

Aktualizovat *stránky/kurzy/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Předchozí kód provede následující změny:

* Zobrazí ID kurzu. Obecně se nezobrazuje primární klíč (PK) entity. PKs mají pro uživatele obvykle význam. V tomto případě je PK číslo kurzu.
* Změní titulek rozevíracího seznamu oddělení z **DepartmentID** na **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).

Stránka obsahuje skryté pole (`<input type="hidden">`) pro číslo kurzu. Přidání pomocné rutiny značky `<label>` s `asp-for="Course.CourseID"` eliminuje nutnost skrytého pole. `<input type="hidden">` se vyžaduje, aby číslo kurzu bylo zahrnuto do publikovaných dat, když uživatel klikne na **Uložit**.

## <a name="update-the-course-details-and-delete-pages"></a>Aktualizujte podrobnosti kurzu a odstraňte stránky.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zvýšit výkon, když sledování není vyžadováno.

### <a name="update-the-course-page-models"></a>Aktualizace modelů stránek kurzu

Aktualizujte *stránky/kurzy/odstraňte. cshtml. cs* s následujícím kódem pro přidání `AsNoTracking`:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Udělejte stejnou změnu v souboru *Pages/kurzy/details. cshtml. cs* :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a>Aktualizace stránek kurzu Razor

Aktualizovat *stránky/kurzy/odstranit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Udělejte stejné změny na stránce s podrobnostmi.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Testování stránek kurzu

Otestujte stránky vytvořit, upravit, podrobnosti a odstranit.

## <a name="update-the-instructor-create-and-edit-pages"></a>Aktualizace stránek a vytváření a úpravy instruktora

Instruktoři můžou učit libovolný počet kurzů. Na následujícím obrázku vidíte stránku pro úpravu instruktora s poli pro pole kurzů.

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses30.png)

Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen. Pro každý kurz v databázi se zobrazí zaškrtávací políčko. Jsou vybrány kurzy, ke kterým je instruktor přiřazen. Uživatel může zaškrtnout nebo zrušit zaškrtnutí políček pro změnu přiřazení kurzů. Pokud byl počet kurzů mnohem větší, může být lepší pracovat s jiným uživatelským rozhraním. Nicméně, metoda správy vztahu n:n se tady nemění. Chcete-li vytvořit nebo odstranit relace, budete pracovat s entitou JOIN.

### <a name="create-a-class-for-assigned-courses-data"></a>Vytvoření třídy pro přiřazená data kurzů

Vytvořte *SchoolViewModels/AssignedCourseData. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

Třída `AssignedCourseData` obsahuje data pro vytvoření zaškrtávacích políček pro kurzy přiřazené instruktorovi.

### <a name="create-an-instructor-page-model-base-class"></a>Vytvoření základní třídy modelu stránky instruktora

Vytvořte základní třídu *Pages/instruktors/InstructorCoursesPageModel. cs* :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

`InstructorCoursesPageModel` je základní třída, kterou použijete pro modely stránek upravit a vytvořit. `PopulateAssignedCourseData` přečte všechny `Course` entity k naplnění `AssignedCourseDataList`. Pro každý kurz kód nastaví `CourseID`, název a bez ohledu na to, jestli je instruktor přiřazený k kurzu. [HashSet –](/dotnet/api/system.collections.generic.hashset-1) se používá pro efektivní vyhledávání.

Vzhledem k tomu, že stránka Razor nemá kolekci entit kurzu, nemůže pořadač modelů automaticky aktualizovat `CourseAssignments` navigační vlastnost. Namísto použití pořadače modelů k aktualizaci `CourseAssignments` navigační vlastnosti to uděláte v nové metodě `UpdateInstructorCourses`. Proto je nutné vyloučit vlastnost `CourseAssignments` z vazby modelu. To nevyžaduje žádné změny kódu, který volá `TryUpdateModel`, protože používáte přetížení seznamu povolených položek a `CourseAssignments` není v seznamu zahrnutí.

Pokud nebyla vybrána žádná zaškrtávací políčka, kód v `UpdateInstructorCourses` inicializuje vlastnost `CourseAssignments` navigace s prázdnou kolekcí a vrátí:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Kód pak projde všemi kurzy v databázi a zkontroluje každý kurz s těmi, které jsou aktuálně přiřazeny k instruktorovi, oproti těm, které byly vybrány na stránce. Aby bylo možné efektivně vyhledávat, jsou tyto dvě kolekce uloženy v `HashSet`ch objektech.

Pokud je vybráno zaškrtávací políčko pro kurz, ale kurz není v navigační vlastnosti `Instructor.CourseAssignments`, kurz se přidá do kolekce v navigační vlastnosti.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Pokud není vybrané zaškrtávací políčko pro kurz, ale kurz je v navigační vlastnosti `Instructor.CourseAssignments`, kurz se odebere z navigační vlastnosti.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Obsluha umístění kanceláře

Další relaci, kterou musí stránka pro úpravy zpracovat, je relace 1:1, kterou má entita instruktora s entitou `OfficeAssignment`. Kód pro úpravy instruktora musí zpracovat následující scénáře: 

* Pokud uživatel zruší přiřazení sady Office, odstraňte entitu `OfficeAssignment`.
* Pokud uživatel zadá přiřazení pro Office a byl prázdný, vytvořte novou entitu `OfficeAssignment`.
* Pokud uživatel změní přiřazení sady Office, aktualizujte entitu `OfficeAssignment`.

### <a name="update-the-instructor-edit-page-model"></a>Aktualizace modelu stránky pro úpravu instruktorů

Aktualizovat *stránky/instruktory/upravit. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Předchozí kód:

* Získá aktuální entitu `Instructor` z databáze pomocí Eager načítání pro vlastnosti `OfficeAssignment`, `CourseAssignment``CourseAssignment.Course` a navigace.
* Aktualizuje načtenou entitu `Instructor` hodnotami z pořadače modelů. `TryUpdateModel` brání [přestavení](xref:data/ef-rp/crud#overposting).
* Pokud je umístění kanceláře prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null. Pokud je `Instructor.OfficeAssignment` null, související řádek v tabulce `OfficeAssignment` se odstraní.
* Volá `PopulateAssignedCourseData` v `OnGetAsync` k poskytnutí informací pro zaškrtávací políčka pomocí třídy modelu zobrazení `AssignedCourseData`.
* Volá `UpdateInstructorCourses` v `OnPostAsync` pro použití informací z zaškrtávacích políček na upravované entitě instruktor.
* Pokud `TryUpdateModel` selžou, volání `PopulateAssignedCourseData` a `UpdateInstructorCourses` v `OnPostAsync`. Tato volání metody obnoví přiřazená data kurzu zadaná na stránce, když se znovu zobrazí s chybovou zprávou.

### <a name="update-the-instructor-edit-razor-page"></a>Aktualizace stránky instruktor Edit Razor

Aktualizovat *stránky/instruktory/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Předchozí kód vytvoří tabulku HTML, která má tři sloupce. Každý sloupec má zaškrtávací políčko a popisek obsahující číslo a název kurzu. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"). Použití stejného názvu informuje pořadač modelů, aby je považoval za skupinu. Atribut Value každé zaškrtávací políčko je nastaven na `CourseID`. Po zveřejnění stránky předává pořadač modelu pole, které obsahuje `CourseID` hodnoty pouze pro zaškrtávací políčka, která jsou vybrána.

Při počátečním vykreslení zaškrtávacích políček se vybere kurz přiřazený instruktorovi.

Poznámka: přístup, který je zde povedený pro úpravu dat kurzu instruktora funguje dobře, pokud existuje omezený počet kurzů. Pro kolekce, které jsou mnohem větší, jiné uživatelské rozhraní a odlišnou metodu aktualizace by byly efektivnější a efektivnější.

Spusťte aplikaci a otestujte stránku pro úpravu aktualizovaných instruktorů. Změna některých přiřazení kurzu. Změny se projeví na stránce indexu.

### <a name="update-the-instructor-create-page"></a>Aktualizace stránky pro vytvoření instruktora

Aktualizujte model stránky pro vytvoření a stránku Razor s podobným kódem jako na stránce pro úpravy:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Otestujte stránku pro vytvoření instruktoru.

## <a name="update-the-instructor-delete-page"></a>Aktualizace stránky pro odstranění instruktorů

Aktualizujte *stránky/instruktory/odstraňte. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Předchozí kód provede následující změny:

* Používá načítání Eager pro navigační vlastnost `CourseAssignments`. `CourseAssignments` musí být zahrnuté, nebo nejsou po odstranění instruktora smazány. Abyste se vyhnuli nutnosti jejich čtení, nakonfigurujte v databázi kaskádové odstranění.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.

Spusťte aplikaci a otestujte stránku odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/read-related-data)
> [Další kurz](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz ukazuje, jak aktualizovat související data. Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

Na následujících obrázcích vidíte některé z dokončených stránek.

Stránka pro úpravy kurzu ![](update-related-data/_static/course-edit.png)
![stránka pro úpravy instruktora](update-related-data/_static/instructor-edit-courses.png)

Projděte a otestujte stránky pro vytvoření a úpravu kurzu. Vytvořte nový kurz. Oddělení je vybráno podle jeho primárního klíče (celé číslo), nikoli podle názvu. Upravte nový kurz. Po dokončení testování odstraňte nový kurz.

## <a name="create-a-base-class-to-share-common-code"></a>Vytvoření základní třídy pro sdílení společného kódu

Každý z nich potřebuje seznam názvů oddělení, kurzy a vytváření a kurzy a stránky pro úpravy. Vytvořte základní třídu *Pages/kurzy/DepartmentNamePageModel. cshtml. cs* pro stránky pro vytváření a úpravy:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) , který bude obsahovat seznam názvů oddělení. Je-li zadán `selectedDepartment`, je toto oddělení vybráno v `SelectList`.

Třídy modelu stránky pro vytváření a úpravy budou odvozeny z `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Přizpůsobení stránek kurzů

Když se vytvoří nová entita kurzu, musí mít relaci s existujícím oddělením. Chcete-li přidat oddělení při vytváření kurzu, základní třída pro vytváření a úpravy obsahuje rozevírací seznam pro výběr oddělení. Rozevírací seznam nastaví vlastnost `Course.DepartmentID` cizí klíč (FK). EF Core používá `Course.DepartmentID` FK k načtení vlastnosti `Department` navigace.

![Vytvořit kurz](update-related-data/_static/ddl.png)

Aktualizujte model vytvoření stránky pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Předchozí kód:

* Je odvozen z `DepartmentNamePageModel`.
* Nástroj používá `TryUpdateModelAsync` k zabránění [přestavení](xref:data/ef-rp/crud#overposting).
* Nahradí `ViewData["DepartmentID"]` `DepartmentNameSL` (ze základní třídy).

`ViewData["DepartmentID"]` se nahrazuje `DepartmentNameSL`silného typu. Modely silného typu jsou upřednostňovány přes slabě zadaná. Další informace najdete v tématu [slabě zadaná data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualizace stránky pro vytvoření kurzů

Aktualizovat *stránky/kurzy/vytvořit. cshtml* s následujícím kódem:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí kód provede následující změny:

* Změní titulek z **DepartmentID** na **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).
* Přidá možnost vybrat oddělení. Tato změna vykreslí místo prvního oddělení možnost "vybrat oddělení".
* Přidá ověřovací zprávu, když není vybrané oddělení.

Stránka Razor používá [Pomocníka pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Otestujte stránku vytvořit. Na stránce vytvořit se zobrazuje název oddělení, nikoli ID oddělení.

### <a name="update-the-courses-edit-page"></a>Aktualizujte stránku pro úpravu kurzů.

Nahraďte kód na *stránce/kurzy/upravte. cshtml. cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Změny jsou podobné těm, které byly provedeny v modelu vytvoření stránky. V předchozím kódu `PopulateDepartmentsDropDownList` předá do ID oddělení, které vybere oddělení určené v rozevíracím seznamu.

Aktualizovat *stránky/kurzy/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Předchozí kód provede následující změny:

* Zobrazí ID kurzu. Obecně se nezobrazuje primární klíč (PK) entity. PKs mají pro uživatele obvykle význam. V tomto případě je PK číslo kurzu.
* Změní titulek z **DepartmentID** na **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).

Stránka obsahuje skryté pole (`<input type="hidden">`) pro číslo kurzu. Přidání pomocné rutiny značky `<label>` s `asp-for="Course.CourseID"` eliminuje nutnost skrytého pole. `<input type="hidden">` se vyžaduje, aby číslo kurzu bylo zahrnuto do publikovaných dat, když uživatel klikne na **Uložit**.

Otestujte aktualizovaný kód. Vytvořit, upravit a odstranit kurz.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Přidání AsNoTracking k podrobnostem a odstranění modelů stránek

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zvýšit výkon, když sledování není vyžadováno. Přidejte `AsNoTracking` k modelu stránky odstranění a podrobnosti. Následující kód ukazuje aktualizovaný model stránky pro odstranění:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Aktualizujte metodu `OnGetAsync` v souboru *Pages/kurzy/details. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Úprava stránek odstranění a podrobností

Aktualizujte stránku odstranění Razor pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Udělejte stejné změny na stránce s podrobnostmi.

### <a name="test-the-course-pages"></a>Testování stránek kurzu

Test vytvořit, upravit, podrobnosti a odstranit.

## <a name="update-the-instructor-pages"></a>Aktualizovat stránky instruktora

V následujících částech se aktualizují stránky instruktora.

### <a name="add-office-location"></a>Přidat umístění kanceláře

Při úpravách záznamu instruktora můžete chtít aktualizovat přiřazení kanceláře instruktora. Entita `Instructor` má relaci typu 1:1 s entitou `OfficeAssignment`. Kód instruktora musí zpracovat:

* Pokud uživatel zruší přiřazení sady Office, odstraňte entitu `OfficeAssignment`.
* Pokud uživatel zadá přiřazení pro Office a byl prázdný, vytvořte novou entitu `OfficeAssignment`.
* Pokud uživatel změní přiřazení sady Office, aktualizujte entitu `OfficeAssignment`.

Aktualizujte model stránky pro instruktory pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Předchozí kód:

* Načte aktuální entitu `Instructor` z databáze pomocí načítání Eager pro navigační vlastnost `OfficeAssignment`.
* Aktualizuje načtenou entitu `Instructor` hodnotami z pořadače modelů. `TryUpdateModel` brání [přestavení](xref:data/ef-rp/crud#overposting).
* Pokud je umístění kanceláře prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null. Pokud je `Instructor.OfficeAssignment` null, související řádek v tabulce `OfficeAssignment` se odstraní.

### <a name="update-the-instructor-edit-page"></a>Aktualizace stránky pro úpravu instruktorů

Aktualizovat *stránky/instruktory/upravit. cshtml* s umístěním kanceláře:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Ověřte, že je možné změnit umístění kanceláře instruktorů.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Přidání přiřazení kurzů do stránky pro úpravu instruktorů

Instruktoři můžou učit libovolný počet kurzů. V této části přidáte možnost změnit přiřazení kurzů. Na následujícím obrázku je vidět aktualizovaná stránka pro úpravu instruktorů:

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses.png)

`Course` a `Instructor` má relaci m:n. Chcete-li přidat a odebrat relace, přidejte a odeberte entity ze sady entit služby `CourseAssignments` JOIN.

Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen. Pro každý kurz v databázi se zobrazí zaškrtávací políčko. Budou zkontrolovány kurzy, ke kterým je instruktor přiřazen. Uživatel může zaškrtnutím nebo zrušením zaškrtnutí políček změnit přiřazení kurzů. Pokud byl počet kurzů mnohem větší:

* Pro zobrazení kurzů byste pravděpodobně použili jiné uživatelské rozhraní.
* Metoda manipulace s entitou JOIN k vytvoření nebo odstranění relace se nezměnila.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Přidat třídy pro podporu vytváření a úprav stránek instruktora

Vytvořte *SchoolViewModels/AssignedCourseData. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Třída `AssignedCourseData` obsahuje data pro vytvoření zaškrtávacích políček pro přiřazené kurzy od instruktora.

Vytvořte základní třídu *Pages/instruktors/InstructorCoursesPageModel. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel` je základní třída, kterou použijete pro modely stránek upravit a vytvořit. `PopulateAssignedCourseData` přečte všechny `Course` entity k naplnění `AssignedCourseDataList`. Pro každý kurz kód nastaví `CourseID`, název a bez ohledu na to, jestli je instruktor přiřazený k kurzu. [HashSet –](/dotnet/api/system.collections.generic.hashset-1) se používá k vytváření efektivních hledání.

### <a name="instructors-edit-page-model"></a>Instruktoři – upravit model stránky

Aktualizujte model stránky instruktory pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Předchozí kód zpracovává změny přiřazení Office.

Aktualizace zobrazení instruktor Razor:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Při vložení kódu v aplikaci Visual Studio se konce řádků mění způsobem, který kód přerušuje. Stisknutím kombinace kláves CTRL + Z pokaždé vraťte automatické formátování zpět. CTRL + Z opravuje konce řádků, aby vypadaly jako v tomto příkladu. Odsazení nemusí být dokonalé, ale `@:</tr><tr>`, `@:<td>`, `@:</td>`a `@:</tr>` řádky musí být na jednom řádku, jak je znázorněno na obrázku. Po vybrání bloku nového kódu stiskněte klávesu Tabulátor třikrát, aby se nový kód pořádek nastavil s existujícím kódem. Hlasujte nebo zkontrolujte stav této chyby [pomocí tohoto odkazu](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Předchozí kód vytvoří tabulku HTML, která má tři sloupce. Každý sloupec má zaškrtávací políčko a popisek obsahující číslo a název kurzu. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"). Použití stejného názvu informuje pořadač modelů, aby je považoval za skupinu. Atribut Value každé zaškrtávací políčko je nastaven na `CourseID`. Po zveřejnění stránky předává pořadač modelu pole, které se skládá z `CourseID` hodnoty pouze pro zaškrtnutá políčka.

Když jsou zaškrtávací políčka zpočátku vykreslena, kurzy přiřazené instruktorovi mají zaškrtnuté atributy.

Spusťte aplikaci a otestujte stránku pro úpravu aktualizovaných instruktorů. Změna některých přiřazení kurzu. Změny se projeví na stránce indexu.

Poznámka: přístup, který je zde povedený pro úpravu dat kurzu instruktora funguje dobře, pokud existuje omezený počet kurzů. Pro kolekce, které jsou mnohem větší, jiné uživatelské rozhraní a odlišnou metodu aktualizace by byly efektivnější a efektivnější.

### <a name="update-the-instructors-create-page"></a>Aktualizace stránky pro vytvoření instruktorů

Aktualizujte model stránky pro vytvoření instruktoru pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Předchozí kód je podobný jako u kódu *stránky, instruktory/upravit. cshtml. cs* .

Aktualizujte stránku Instructor Create Razor pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Otestujte stránku pro vytvoření instruktoru.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Aktualizace modelu odstranění stránky s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Předchozí kód provede následující změny:

* Používá načítání Eager pro navigační vlastnost `CourseAssignments`. `CourseAssignments` musí být zahrnuté, nebo nejsou po odstranění instruktora smazány. Abyste se vyhnuli nutnosti jejich čtení, nakonfigurujte v databázi kaskádové odstranění.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube (část 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Verze tohoto kurzu pro YouTube (část 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/read-related-data)
> [Další](xref:data/ef-rp/concurrency)

::: moniker-end

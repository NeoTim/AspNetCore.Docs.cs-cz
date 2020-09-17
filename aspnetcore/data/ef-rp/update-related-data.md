---
title: Část 7, Razor stránky s EF Core v ASP.NET Core aktualizace dat souvisejících s aktualizací
author: rick-anderson
description: 7. část Razor stránek a Entity Framework řady kurzů.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 17b200f0ba90035c417c96689798263af16551de
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722816"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a>Část 7, Razor stránky s EF Core v ASP.NET Core aktualizace dat souvisejících s aktualizací

[Dykstra](https://github.com/tdykstra)a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se dozvíte, jak aktualizovat související data. Následující ilustrace znázorňují některé z dokončených stránek.

![Stránka pro úpravu ](update-related-data/_static/course-edit30.png)
 instruktora stránky pro úpravy kurzu ![](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Aktualizovat stránky pro vytvoření a úpravu kurzu

Vygenerovaný kód pro stránky pro vytvoření a úpravu kurzu má rozevírací seznam oddělení, který zobrazuje ID oddělení (celé číslo). Rozevírací seznam by měl zobrazovat název oddělení, takže obě tyto stránky budou potřebovat seznam názvů oddělení. Chcete-li poskytnout tento seznam, použijte pro stránky vytvořit a upravit základní třídu.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Vytvoří základní třídu pro vytvoření a úpravu kurzu.

Vytvořte soubor *Pages/kurzys/DepartmentNamePageModel. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) , který bude obsahovat seznam názvů oddělení. Je `selectedDepartment` -li parametr zadán, je toto oddělení vybráno v `SelectList` .

Třídy modelu stránky pro vytváření a úpravy budou odvozeny z `DepartmentNamePageModel` .

### <a name="update-the-course-create-page-model"></a>Aktualizace modelu stránky vytvoření kurzu

Do oddělení se přiřadí kurz. Základní třída pro stránky pro vytváření a úpravy poskytuje `SelectList` pro výběr oddělení. Rozevírací seznam, který používá `SelectList` vlastnost, nastavuje `Course.DepartmentID` vlastnost cizího klíče (FK). EF Core používá `Course.DepartmentID` FK k načtení `Department` vlastnosti navigace.

![Vytvořit kurz](update-related-data/_static/ddl30.png)

Aktualizovat *stránky/kurzy/vytvořit. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Předcházející kód:

* Je odvozen z `DepartmentNamePageModel` .
* Nástroj používá `TryUpdateModelAsync` k zabránění [přestavení](xref:data/ef-rp/crud#overposting).
* Odebere `ViewData["DepartmentID"]` . `DepartmentNameSL` ze základní třídy je model silného typu a bude použit Razor stránkou. Modely silného typu jsou upřednostňovány přes slabě zadaná. Další informace najdete v tématu [slabě zadaná data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-no-locrazor-page"></a>Aktualizace stránky pro vytvoření kurzu Razor

Aktualizovat *stránky/kurzy/vytvořit. cshtml* s následujícím kódem:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí kód provede následující změny:

* Změní titulek z **DepartmentID** na **oddělení**.
* Nahrazuje `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).
* Přidá možnost vybrat oddělení. Tato změna vykreslí "vybrat oddělení" v rozevíracím seznamu, pokud zatím nebylo vybráno žádné oddělení, nikoli první oddělení.
* Přidá ověřovací zprávu, když není vybrané oddělení.

RazorStránka používá [Pomocníka pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Otestujte stránku vytvořit. Na stránce vytvořit se zobrazuje název oddělení, nikoli ID oddělení.

### <a name="update-the-course-edit-page-model"></a>Aktualizace modelu stránky pro úpravu kurzu

Aktualizovat *stránky/kurzy/upravit. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Změny jsou podobné těm, které byly provedeny v modelu vytvoření stránky. V předchozím kódu `PopulateDepartmentsDropDownList` předává ID oddělení, které v rozevíracím seznamu vybere toto oddělení.

### <a name="update-the-course-edit-no-locrazor-page"></a>Aktualizace stránky pro úpravu kurzu Razor

Aktualizovat *stránky/kurzy/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Předchozí kód provede následující změny:

* Zobrazí ID kurzu. Obecně se nezobrazuje primární klíč (PK) entity. PKs mají pro uživatele obvykle význam. V tomto případě je PK číslo kurzu.
* Změní titulek rozevíracího seznamu oddělení z **DepartmentID** na **oddělení**.
* Nahrazuje `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).

Stránka obsahuje skryté pole ( `<input type="hidden">` ) pro číslo kurzu. Přidáním `<label>` pomocné rutiny značky `asp-for="Course.CourseID"` neodstraníte potřebu skrytého pole. `<input type="hidden">` je vyžadováno, aby číslo kurzu bylo zahrnuto do publikovaných dat, když uživatel klikne na tlačítko **Uložit**.

## <a name="update-the-course-details-and-delete-pages"></a>Aktualizujte podrobnosti kurzu a odstraňte stránky.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zvýšit výkon, když sledování není vyžadováno.

### <a name="update-the-course-page-models"></a>Aktualizace modelů stránek kurzu

Aktualizujte *stránky/kurzy/odstraňte. cshtml. cs* s následujícím kódem, který chcete přidat `AsNoTracking` :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Udělejte stejnou změnu v souboru *Pages/kurzy/details. cshtml. cs* :

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a>Aktualizace stránek kurzu Razor

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

`AssignedCourseData`Třída obsahuje data pro vytvoření zaškrtávacích políček pro kurzy přiřazené instruktorovi.

### <a name="create-an-instructor-page-model-base-class"></a>Vytvoření základní třídy modelu stránky instruktora

Vytvořte základní třídu *Pages/instruktors/InstructorCoursesPageModel. cs* :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

`InstructorCoursesPageModel`Je základní třída, kterou použijete pro modely stránky pro úpravy a vytváření. `PopulateAssignedCourseData` přečte všechny `Course` entity, které se naplní `AssignedCourseDataList` . Pro každý kurz kód nastaví `CourseID` název, název a bez ohledu na to, zda je instruktor přiřazen ke kurzu. [HashSet –](/dotnet/api/system.collections.generic.hashset-1) se používá pro efektivní vyhledávání.

Vzhledem k tomu Razor , že stránka nemá kolekci entit kurzu, nemůže pořadač modelů automaticky aktualizovat `CourseAssignments` navigační vlastnost. Namísto použití pořadače modelů k aktualizaci `CourseAssignments` navigační vlastnosti to uděláte v nové `UpdateInstructorCourses` metodě. Proto je nutné vyloučit `CourseAssignments` vlastnost z vazby modelu. To nevyžaduje žádné změny kódu, který volá, `TryUpdateModel` protože používáte přetížení s deklarovanými vlastnostmi a `CourseAssignments` není v seznamu zahrnutí.

Pokud nebyla vybrána žádná zaškrtávací políčka, kód v rámci `UpdateInstructorCourses` inicializuje `CourseAssignments` navigační vlastnost s prázdnou kolekcí a vrátí:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Kód pak projde všemi kurzy v databázi a zkontroluje každý kurz s těmi, které jsou aktuálně přiřazeny k instruktorovi, oproti těm, které byly vybrány na stránce. Aby bylo možné efektivně vyhledávat, jsou tyto dvě kolekce uloženy v `HashSet` objektech.

Pokud je vybráno zaškrtávací políčko pro kurz, ale kurz není v `Instructor.CourseAssignments` navigační vlastnosti, kurz se přidá do kolekce v navigační vlastnosti.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Pokud není vybrané zaškrtávací políčko pro kurz, ale kurz je v `Instructor.CourseAssignments` navigační vlastnosti, kurz se odebere z navigační vlastnosti.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Obsluha umístění kanceláře

Další relaci, kterou musí stránka pro úpravy zpracovat, je vztah 1:1, který entita instruktora má s `OfficeAssignment` entitou. Kód pro úpravy instruktora musí zpracovat následující scénáře: 

* Pokud uživatel zruší přiřazení sady Office, odstraňte `OfficeAssignment` entitu.
* Pokud uživatel zadá přiřazení pro Office a byl prázdný, vytvořte novou `OfficeAssignment` entitu.
* Pokud uživatel změní přiřazení kanceláře, aktualizujte `OfficeAssignment` entitu.

### <a name="update-the-instructor-edit-page-model"></a>Aktualizace modelu stránky pro úpravu instruktorů

Aktualizovat *stránky/instruktory/upravit. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Předcházející kód:

* Načte aktuální `Instructor` entitu z databáze pomocí Eager načítání pro `OfficeAssignment` `CourseAssignment` `CourseAssignment.Course` navigační vlastnosti, a.
* Aktualizuje načtenou `Instructor` entitu hodnotami z pořadače modelů. `TryUpdateModel` brání [přestavení](xref:data/ef-rp/crud#overposting).
* Pokud je umístění kanceláře prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null. Pokud `Instructor.OfficeAssignment` je hodnota null, související řádek v `OfficeAssignment` tabulce je odstraněn.
* Volání `PopulateAssignedCourseData` , `OnGetAsync` která poskytují informace pro zaškrtávací políčka pomocí `AssignedCourseData` třídy zobrazení modelu.
* Volání `UpdateInstructorCourses` v aplikaci `OnPostAsync` aplikují informace z zaškrtávacích políček na upravované entity instruktorů.
* Volání `PopulateAssignedCourseData` a `UpdateInstructorCourses` v `OnPostAsync` případě `TryUpdateModel` neúspěchu. Tato volání metody obnoví přiřazená data kurzu zadaná na stránce, když se znovu zobrazí s chybovou zprávou.

### <a name="update-the-instructor-edit-no-locrazor-page"></a>Aktualizace stránky pro úpravu instruktorů Razor

Aktualizovat *stránky/instruktory/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Předchozí kód vytvoří tabulku HTML, která má tři sloupce. Každý sloupec má zaškrtávací políčko a popisek obsahující číslo a název kurzu. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"). Použití stejného názvu informuje pořadač modelů, aby je považoval za skupinu. Atribut value pro každé zaškrtávací políčko je nastaven na hodnotu `CourseID` . Při publikování stránky předává pořadač modelu pole, které obsahuje `CourseID` hodnoty pouze pro zaškrtávací políčka, která jsou vybrána.

Při počátečním vykreslení zaškrtávacích políček se vybere kurz přiřazený instruktorovi.

Poznámka: přístup, který je zde povedený pro úpravu dat kurzu instruktora funguje dobře, pokud existuje omezený počet kurzů. Pro kolekce, které jsou mnohem větší, jiné uživatelské rozhraní a odlišnou metodu aktualizace by byly efektivnější a efektivnější.

Spusťte aplikaci a otestujte stránku pro úpravu aktualizovaných instruktorů. Změna některých přiřazení kurzu. Změny se projeví na stránce indexu.

### <a name="update-the-instructor-create-page"></a>Aktualizace stránky pro vytvoření instruktora

Aktualizujte svůj model a stránku vytvoření a Razor stránku s podobným kódem jako na stránce pro úpravy:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Otestujte stránku pro vytvoření instruktoru.

## <a name="update-the-instructor-delete-page"></a>Aktualizace stránky pro odstranění instruktorů

Aktualizujte *stránky/instruktory/odstraňte. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Předchozí kód provede následující změny:

* Používá načítání Eager pro `CourseAssignments` navigační vlastnost. `CourseAssignments` musí být zahrnuté nebo se při odstranění instruktoru neodstraní. Abyste se vyhnuli nutnosti jejich čtení, nakonfigurujte v databázi kaskádové odstranění.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.

Spusťte aplikaci a otestujte stránku odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/read-related-data) 
>  [Další kurz](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz ukazuje, jak aktualizovat související data. Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

Na následujících obrázcích vidíte některé z dokončených stránek.

![Stránka pro úpravu ](update-related-data/_static/course-edit.png)
 instruktora stránky pro úpravy kurzu ![](update-related-data/_static/instructor-edit-courses.png)

Projděte a otestujte stránky pro vytvoření a úpravu kurzu. Vytvořte nový kurz. Oddělení je vybráno podle jeho primárního klíče (celé číslo), nikoli podle názvu. Upravte nový kurz. Po dokončení testování odstraňte nový kurz.

## <a name="create-a-base-class-to-share-common-code"></a>Vytvoření základní třídy pro sdílení společného kódu

Každý z nich potřebuje seznam názvů oddělení, kurzy a vytváření a kurzy a stránky pro úpravy. Vytvořte základní třídu *Pages/kurzy/DepartmentNamePageModel. cshtml. cs* pro stránky pro vytváření a úpravy:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) , který bude obsahovat seznam názvů oddělení. Je `selectedDepartment` -li parametr zadán, je toto oddělení vybráno v `SelectList` .

Třídy modelu stránky pro vytváření a úpravy budou odvozeny z `DepartmentNamePageModel` .

## <a name="customize-the-courses-pages"></a>Přizpůsobení stránek kurzů

Když se vytvoří nová entita kurzu, musí mít relaci s existujícím oddělením. Chcete-li přidat oddělení při vytváření kurzu, základní třída pro vytváření a úpravy obsahuje rozevírací seznam pro výběr oddělení. Rozevírací seznam nastaví `Course.DepartmentID` vlastnost cizího klíče (FK). EF Core používá `Course.DepartmentID` FK k načtení `Department` vlastnosti navigace.

![Vytvořit kurz](update-related-data/_static/ddl.png)

Aktualizujte model vytvoření stránky pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Předcházející kód:

* Je odvozen z `DepartmentNamePageModel` .
* Nástroj používá `TryUpdateModelAsync` k zabránění [přestavení](xref:data/ef-rp/crud#overposting).
* Nahrazuje `ViewData["DepartmentID"]` `DepartmentNameSL` (ze základní třídy).

`ViewData["DepartmentID"]` je nahrazen silným typem `DepartmentNameSL` . Modely silného typu jsou upřednostňovány přes slabě zadaná. Další informace najdete v tématu [slabě zadaná data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualizace stránky pro vytvoření kurzů

Aktualizovat *stránky/kurzy/vytvořit. cshtml* s následujícím kódem:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí kód provede následující změny:

* Změní titulek z **DepartmentID** na **oddělení**.
* Nahrazuje `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).
* Přidá možnost vybrat oddělení. Tato změna vykreslí místo prvního oddělení možnost "vybrat oddělení".
* Přidá ověřovací zprávu, když není vybrané oddělení.

RazorStránka používá [Pomocníka pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Otestujte stránku vytvořit. Na stránce vytvořit se zobrazuje název oddělení, nikoli ID oddělení.

### <a name="update-the-courses-edit-page"></a>Aktualizujte stránku pro úpravu kurzů.

Nahraďte kód na *stránce/kurzy/upravte. cshtml. cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Změny jsou podobné těm, které byly provedeny v modelu vytvoření stránky. V předchozím kódu `PopulateDepartmentsDropDownList` předává ID oddělení, které vybere oddělení určené v rozevíracím seznamu.

Aktualizovat *stránky/kurzy/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Předchozí kód provede následující změny:

* Zobrazí ID kurzu. Obecně se nezobrazuje primární klíč (PK) entity. PKs mají pro uživatele obvykle význam. V tomto případě je PK číslo kurzu.
* Změní titulek z **DepartmentID** na **oddělení**.
* Nahrazuje `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).

Stránka obsahuje skryté pole ( `<input type="hidden">` ) pro číslo kurzu. Přidáním `<label>` pomocné rutiny značky `asp-for="Course.CourseID"` neodstraníte potřebu skrytého pole. `<input type="hidden">` je vyžadováno, aby číslo kurzu bylo zahrnuto do publikovaných dat, když uživatel klikne na tlačítko **Uložit**.

Otestujte aktualizovaný kód. Vytvořit, upravit a odstranit kurz.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Přidání AsNoTracking k podrobnostem a odstranění modelů stránek

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zvýšit výkon, když sledování není vyžadováno. Přidejte `AsNoTracking` do modelu stránky odstranění a podrobnosti. Následující kód ukazuje aktualizovaný model stránky pro odstranění:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Aktualizujte `OnGetAsync` metodu v souboru *Pages/kurzy/details. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Úprava stránek odstranění a podrobností

Aktualizujte Razor stránku odstranit pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Udělejte stejné změny na stránce s podrobnostmi.

### <a name="test-the-course-pages"></a>Testování stránek kurzu

Test vytvořit, upravit, podrobnosti a odstranit.

## <a name="update-the-instructor-pages"></a>Aktualizovat stránky instruktora

V následujících částech se aktualizují stránky instruktora.

### <a name="add-office-location"></a>Přidat umístění kanceláře

Při úpravách záznamu instruktora můžete chtít aktualizovat přiřazení kanceláře instruktora. `Instructor`Entita má s entitou relaci 1:1 nebo 1:1 `OfficeAssignment` . Kód instruktora musí zpracovat:

* Pokud uživatel zruší přiřazení sady Office, odstraňte `OfficeAssignment` entitu.
* Pokud uživatel zadá přiřazení pro Office a byl prázdný, vytvořte novou `OfficeAssignment` entitu.
* Pokud uživatel změní přiřazení kanceláře, aktualizujte `OfficeAssignment` entitu.

Aktualizujte model stránky pro instruktory pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Předcházející kód:

* Načte aktuální `Instructor` entitu z databáze pomocí Eager načítání pro `OfficeAssignment` navigační vlastnost.
* Aktualizuje načtenou `Instructor` entitu hodnotami z pořadače modelů. `TryUpdateModel` brání [přestavení](xref:data/ef-rp/crud#overposting).
* Pokud je umístění kanceláře prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null. Pokud `Instructor.OfficeAssignment` je hodnota null, související řádek v `OfficeAssignment` tabulce je odstraněn.

### <a name="update-the-instructor-edit-page"></a>Aktualizace stránky pro úpravu instruktorů

Aktualizovat *stránky/instruktory/upravit. cshtml* s umístěním kanceláře:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Ověřte, že je možné změnit umístění kanceláře instruktorů.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Přidání přiřazení kurzů do stránky pro úpravu instruktorů

Instruktoři můžou učit libovolný počet kurzů. V této části přidáte možnost změnit přiřazení kurzů. Na následujícím obrázku je vidět aktualizovaná stránka pro úpravu instruktorů:

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses.png)

`Course` a `Instructor` má relaci n:n. Chcete-li přidat a odebrat relace, přidejte a odeberte entity ze `CourseAssignments` sady entit JOIN.

Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen. Pro každý kurz v databázi se zobrazí zaškrtávací políčko. Budou zkontrolovány kurzy, ke kterým je instruktor přiřazen. Uživatel může zaškrtnutím nebo zrušením zaškrtnutí políček změnit přiřazení kurzů. Pokud byl počet kurzů mnohem větší:

* Pro zobrazení kurzů byste pravděpodobně použili jiné uživatelské rozhraní.
* Metoda manipulace s entitou JOIN k vytvoření nebo odstranění relace se nezměnila.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Přidat třídy pro podporu vytváření a úprav stránek instruktora

Vytvořte *SchoolViewModels/AssignedCourseData. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData`Třída obsahuje data pro vytvoření zaškrtávacích políček pro přiřazené kurzy od instruktora.

Vytvořte základní třídu *Pages/instruktors/InstructorCoursesPageModel. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel`Je základní třída, kterou použijete pro modely stránky pro úpravy a vytváření. `PopulateAssignedCourseData` přečte všechny `Course` entity, které se naplní `AssignedCourseDataList` . Pro každý kurz kód nastaví `CourseID` název, název a bez ohledu na to, zda je instruktor přiřazen ke kurzu. [HashSet –](/dotnet/api/system.collections.generic.hashset-1) se používá k vytváření efektivních hledání.

### <a name="instructors-edit-page-model"></a>Instruktoři – upravit model stránky

Aktualizujte model stránky instruktory pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Předchozí kód zpracovává změny přiřazení Office.

Aktualizujte zobrazení instruktora Razor :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Při vložení kódu v aplikaci Visual Studio se konce řádků mění způsobem, který kód přerušuje. Stisknutím kombinace kláves CTRL + Z pokaždé vraťte automatické formátování zpět. CTRL + Z opravuje konce řádků, aby vypadaly jako v tomto příkladu. Odsazení nemusí být dokonalé, ale `@:</tr><tr>` řádky,, a `@:<td>` `@:</td>` `@:</tr>` musí být na jednom řádku, jak je znázorněno na obrázku. Po vybrání bloku nového kódu stiskněte klávesu Tabulátor třikrát, aby se nový kód pořádek nastavil s existujícím kódem. Hlasujte nebo zkontrolujte stav této chyby [pomocí tohoto odkazu](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Předchozí kód vytvoří tabulku HTML, která má tři sloupce. Každý sloupec má zaškrtávací políčko a popisek obsahující číslo a název kurzu. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"). Použití stejného názvu informuje pořadač modelů, aby je považoval za skupinu. Atribut Value každé zaškrtávací políčko je nastaven na hodnotu `CourseID` . Po zveřejnění stránky předává pořadač modelu pole, které se skládá z `CourseID` hodnot pouze u zaškrtnutých políček.

Když jsou zaškrtávací políčka zpočátku vykreslena, kurzy přiřazené instruktorovi mají zaškrtnuté atributy.

Spusťte aplikaci a otestujte stránku pro úpravu aktualizovaných instruktorů. Změna některých přiřazení kurzu. Změny se projeví na stránce indexu.

Poznámka: přístup, který je zde povedený pro úpravu dat kurzu instruktora funguje dobře, pokud existuje omezený počet kurzů. Pro kolekce, které jsou mnohem větší, jiné uživatelské rozhraní a odlišnou metodu aktualizace by byly efektivnější a efektivnější.

### <a name="update-the-instructors-create-page"></a>Aktualizace stránky pro vytvoření instruktorů

Aktualizujte model stránky pro vytvoření instruktoru pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Předchozí kód je podobný jako u kódu *stránky, instruktory/upravit. cshtml. cs* .

Aktualizujte stránku pro vytvoření instruktoru Razor pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Otestujte stránku pro vytvoření instruktoru.

## <a name="update-the-delete-page"></a>Aktualizace stránky pro odstranění

Aktualizujte odstranit model stránky pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Předchozí kód provede následující změny:

* Používá načítání Eager pro `CourseAssignments` navigační vlastnost. `CourseAssignments` musí být zahrnuté nebo se při odstranění instruktoru neodstraní. Abyste se vyhnuli nutnosti jejich čtení, nakonfigurujte v databázi kaskádové odstranění.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube (část 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Verze tohoto kurzu pro YouTube (část 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/read-related-data) 
>  [Další](xref:data/ef-rp/concurrency)

::: moniker-end

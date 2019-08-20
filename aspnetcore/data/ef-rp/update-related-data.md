---
title: Razor Pages s EF Core ve službě ASP.NET Core – data související s aktualizací – 7 z 8
author: tdykstra
description: V tomto kurzu aktualizujete související data aktualizací polí cizího klíče a vlastností navigace.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: bc237cf928d852b92c5c1984527129404f88018d
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583494"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>Razor Pages s EF Core ve službě ASP.NET Core – data související s aktualizací – 7 z 8

[Dykstra](https://github.com/tdykstra)a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se dozvíte, jak aktualizovat související data. Následující ilustrace znázorňují některé z dokončených stránek.

![Stránka pro úpravu](update-related-data/_static/course-edit30.png)
instruktora stránky![pro úpravy kurzu](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Aktualizovat stránky pro vytvoření a úpravu kurzu

Vygenerovaný kód pro stránky pro vytvoření a úpravu kurzu má rozevírací seznam oddělení, který zobrazuje ID oddělení (celé číslo). Rozevírací seznam by měl zobrazovat název oddělení, takže obě tyto stránky budou potřebovat seznam názvů oddělení. Chcete-li poskytnout tento seznam, použijte pro stránky vytvořit a upravit základní třídu.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Vytvoří základní třídu pro vytvoření a úpravu kurzu.

Vytvořte soubor *Pages/kurzys/DepartmentNamePageModel. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) , který bude obsahovat seznam názvů oddělení. Je `selectedDepartment` -li parametr zadán, je toto oddělení vybráno `SelectList`v.

Třídy modelu stránky pro vytváření a úpravy budou odvozeny `DepartmentNamePageModel`z.

### <a name="update-the-course-create-page-model"></a>Aktualizace modelu stránky vytvoření kurzu

Do oddělení se přiřadí kurz. Základní třída pro stránky pro vytváření a úpravy poskytuje `SelectList` pro výběr oddělení. Rozevírací seznam, který používá `SelectList` vlastnost, `Course.DepartmentID` nastavuje vlastnost cizího klíče (FK). EF Core používá `Course.DepartmentID` FK k `Department` načtení vlastnosti navigace.

![Vytvořit kurz](update-related-data/_static/ddl30.png)

Aktualizovat *stránky/kurzy/vytvořit. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

Předchozí kód:

* Je odvozen z `DepartmentNamePageModel`.
* Nástroj `TryUpdateModelAsync` používá k [](xref:data/ef-rp/crud#overposting)zabránění přestavení.
* Odebere `ViewData["DepartmentID"]`. `DepartmentNameSL`ze základní třídy je model silného typu a bude použit stránkou Razor. Modely silného typu jsou upřednostňovány přes slabě zadaná. Další informace najdete v tématu [slabě zadaná data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-course-create-razor-page"></a>Aktualizace stránky s vytvořením kurzu – vytvořit Razor

Aktualizovat *stránky/kurzy/vytvořit. cshtml* s následujícím kódem:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí kód provede následující změny:

* Změní titulek z **DepartmentID** na **oddělení**.
* Nahrazuje `"ViewBag.DepartmentID"`(zezákladní třídy).`DepartmentNameSL`
* Přidá možnost vybrat oddělení. Tato změna vykreslí "vybrat oddělení" v rozevíracím seznamu, pokud zatím nebylo vybráno žádné oddělení, nikoli první oddělení.
* Přidá ověřovací zprávu, když není vybrané oddělení.

Stránka Razor používá pomocníka pro [výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Otestujte stránku vytvořit. Na stránce vytvořit se zobrazuje název oddělení, nikoli ID oddělení.

### <a name="update-the-course-edit-page-model"></a>Aktualizace modelu stránky pro úpravu kurzu

Aktualizovat *stránky/kurzy/upravit. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Změny jsou podobné těm, které byly provedeny v modelu vytvoření stránky. V předchozím kódu `PopulateDepartmentsDropDownList` předává ID oddělení, které v rozevíracím seznamu vybere toto oddělení.

### <a name="update-the-course-edit-razor-page"></a>Aktualizace stránky kurz úpravy Razor

Aktualizovat *stránky/kurzy/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Předchozí kód provede následující změny:

* Zobrazí ID kurzu. Obecně se nezobrazuje primární klíč (PK) entity. PKs mají pro uživatele obvykle význam. V tomto případě je PK číslo kurzu.
* Změní titulek rozevíracího seznamu oddělení z **DepartmentID** na **oddělení**.
* Nahrazuje `"ViewBag.DepartmentID"`(zezákladní třídy).`DepartmentNameSL`

Stránka obsahuje skryté pole (`<input type="hidden">`) pro číslo kurzu. Přidáním pomocné rutiny `<label>` značkyneodstranítepotřebu`asp-for="Course.CourseID"` skrytého pole. `<input type="hidden">`je vyžadováno, aby číslo kurzu bylo zahrnuto do publikovaných dat, když uživatel klikne na tlačítko **Uložit**.

## <a name="update-the-course-details-and-delete-pages"></a>Aktualizujte podrobnosti kurzu a odstraňte stránky.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zvýšit výkon, když sledování není vyžadováno.

### <a name="update-the-course-page-models"></a>Aktualizace modelů stránek kurzu

Aktualizujte *stránky/kurzy/odstraňte. cshtml. cs* s následujícím kódem, který `AsNoTracking`chcete přidat:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

Udělejte stejnou změnu v souboru Pages/ *kurzy/details. cshtml. cs* :

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

`AssignedCourseData` Třída obsahuje data pro vytvoření zaškrtávacích políček pro kurzy přiřazené instruktorovi.

### <a name="create-an-instructor-page-model-base-class"></a>Vytvoření základní třídy modelu stránky instruktora

Vytvořte základní třídu *Pages/instruktors/InstructorCoursesPageModel. cs* :

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

`InstructorCoursesPageModel` Je základní třída, kterou použijete pro modely stránky pro úpravy a vytváření. `PopulateAssignedCourseData`přečte `Course` všechny entity, `AssignedCourseDataList`které se naplní. Pro každý kurz kód nastaví `CourseID`název, název a bez ohledu na to, zda je instruktor přiřazen ke kurzu. [HashSet –](/dotnet/api/system.collections.generic.hashset-1) se používá pro efektivní vyhledávání.

Vzhledem k tomu, že stránka Razor nemá kolekci entit kurzu, nemůže pořadač modelů automaticky aktualizovat `CourseAssignments` navigační vlastnost. Namísto použití pořadače modelů k aktualizaci `CourseAssignments` navigační vlastnosti to uděláte v nové `UpdateInstructorCourses` metodě. Proto je nutné vyloučit `CourseAssignments` vlastnost z vazby modelu. To nevyžaduje žádné změny kódu, který volá `TryUpdateModel` , protože používáte přetížení na seznamu povolených a `CourseAssignments` není v seznamu zahrnutí.

Pokud nebyla vybrána žádná zaškrtávací políčka, kód v `UpdateInstructorCourses` rámci `CourseAssignments` inicializuje navigační vlastnost s prázdnou kolekcí a vrátí:

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

Předchozí kód:

* Načte aktuální `Instructor` entitu z databáze pomocí Eager načítání `OfficeAssignment`pro navigační vlastnosti, `CourseAssignment`a `CourseAssignment.Course` .
* Aktualizuje načtenou `Instructor` entitu hodnotami z pořadače modelů. `TryUpdateModel`brání [](xref:data/ef-rp/crud#overposting)přestavení.
* Pokud je umístění kanceláře prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null. Pokud `Instructor.OfficeAssignment` je hodnota null, související řádek `OfficeAssignment` v tabulce je odstraněn.
* Volání `PopulateAssignedCourseData` , `AssignedCourseData` která poskytují informace pro zaškrtávací políčka pomocí třídy zobrazení modelu. `OnGetAsync`
* Volání `UpdateInstructorCourses` v `OnPostAsync` aplikaci aplikují informace z zaškrtávacích políček na upravované entity instruktorů.
* Volání `PopulateAssignedCourseData` a `UpdateInstructorCourses` v `OnPostAsync` případě neúspěchu.`TryUpdateModel` Tato volání metody obnoví přiřazená data kurzu zadaná na stránce, když se znovu zobrazí s chybovou zprávou.

### <a name="update-the-instructor-edit-razor-page"></a>Aktualizace stránky instruktor Edit Razor

Aktualizovat *stránky/instruktory/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Předchozí kód vytvoří tabulku HTML, která má tři sloupce. Každý sloupec má zaškrtávací políčko a popisek obsahující číslo a název kurzu. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"). Použití stejného názvu informuje pořadač modelů, aby je považoval za skupinu. Atribut value pro každé zaškrtávací políčko je nastaven na `CourseID`hodnotu. Při publikování stránky předává pořadač modelu pole, které obsahuje `CourseID` hodnoty pouze pro zaškrtávací políčka, která jsou vybrána.

Při počátečním vykreslení zaškrtávacích políček se vybere kurz přiřazený instruktorovi.

Poznámka: Postup, který je zde k dispozici pro úpravu dat kurzu instruktora, funguje dobře, pokud existuje omezený počet kurzů. Pro kolekce, které jsou mnohem větší, jiné uživatelské rozhraní a odlišnou metodu aktualizace by byly efektivnější a efektivnější.

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

* Používá načítání Eager pro `CourseAssignments` navigační vlastnost. `CourseAssignments`musí být zahrnuté nebo se při odstranění instruktoru neodstraní. Abyste se vyhnuli nutnosti jejich čtení, nakonfigurujte v databázi kaskádové odstranění.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.

Spusťte aplikaci a otestujte stránku odstranit.

## <a name="next-steps"></a>Další postup

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/read-related-data)
> –[Další kurz](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz ukazuje, jak aktualizovat související data. Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

Na následujících obrázcích vidíte některé z dokončených stránek.

![Stránka pro úpravu](update-related-data/_static/course-edit.png)
instruktora stránky![pro úpravy kurzu](update-related-data/_static/instructor-edit-courses.png)

Projděte a otestujte stránky pro vytvoření a úpravu kurzu. Vytvořte nový kurz. Oddělení je vybráno podle jeho primárního klíče (celé číslo), nikoli podle názvu. Upravte nový kurz. Po dokončení testování odstraňte nový kurz.

## <a name="create-a-base-class-to-share-common-code"></a>Vytvoření základní třídy pro sdílení společného kódu

Každý z nich potřebuje seznam názvů oddělení, kurzy a vytváření a kurzy a stránky pro úpravy. Vytvořte základní třídu *Pages/kurzy/DepartmentNamePageModel. cshtml. cs* pro stránky pro vytváření a úpravy:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) , který bude obsahovat seznam názvů oddělení. Je `selectedDepartment` -li parametr zadán, je toto oddělení vybráno `SelectList`v.

Třídy modelu stránky pro vytváření a úpravy budou odvozeny `DepartmentNamePageModel`z.

## <a name="customize-the-courses-pages"></a>Přizpůsobení stránek kurzů

Když se vytvoří nová entita kurzu, musí mít relaci s existujícím oddělením. Chcete-li přidat oddělení při vytváření kurzu, základní třída pro vytváření a úpravy obsahuje rozevírací seznam pro výběr oddělení. Rozevírací seznam nastaví `Course.DepartmentID` vlastnost cizího klíče (FK). EF Core používá `Course.DepartmentID` FK k `Department` načtení vlastnosti navigace.

![Vytvořit kurz](update-related-data/_static/ddl.png)

Aktualizujte model vytvoření stránky pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Předchozí kód:

* Je odvozen z `DepartmentNamePageModel`.
* Nástroj `TryUpdateModelAsync` používá k [](xref:data/ef-rp/crud#overposting)zabránění přestavení.
* Nahrazuje `ViewData["DepartmentID"]`(zezákladní třídy).`DepartmentNameSL`

`ViewData["DepartmentID"]`je nahrazen silným typem `DepartmentNameSL`. Modely silného typu jsou upřednostňovány přes slabě zadaná. Další informace najdete v tématu [slabě zadaná data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualizace stránky pro vytvoření kurzů

Aktualizovat *stránky/kurzy/vytvořit. cshtml* s následujícím kódem:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí kód provede následující změny:

* Změní titulek z **DepartmentID** na **oddělení**.
* Nahrazuje `"ViewBag.DepartmentID"`(zezákladní třídy).`DepartmentNameSL`
* Přidá možnost vybrat oddělení. Tato změna vykreslí místo prvního oddělení možnost "vybrat oddělení".
* Přidá ověřovací zprávu, když není vybrané oddělení.

Stránka Razor používá pomocníka pro [výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

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
* Nahrazuje `"ViewBag.DepartmentID"`(zezákladní třídy).`DepartmentNameSL`

Stránka obsahuje skryté pole (`<input type="hidden">`) pro číslo kurzu. Přidáním pomocné rutiny `<label>` značkyneodstranítepotřebu`asp-for="Course.CourseID"` skrytého pole. `<input type="hidden">`je vyžadováno, aby číslo kurzu bylo zahrnuto do publikovaných dat, když uživatel klikne na tlačítko **Uložit**.

Otestujte aktualizovaný kód. Vytvořit, upravit a odstranit kurz.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Přidání AsNoTracking k podrobnostem a odstranění modelů stránek

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zvýšit výkon, když sledování není vyžadováno. Přidejte `AsNoTracking` do modelu stránky odstranění a podrobnosti. Následující kód ukazuje aktualizovaný model stránky pro odstranění:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Aktualizujte metodu v souboru *Pages/kurzy/details. cshtml. cs:* `OnGetAsync`

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

Při úpravách záznamu instruktora můžete chtít aktualizovat přiřazení kanceláře instruktora. `Instructor` Entita má `OfficeAssignment` s entitou relaci 1:1 nebo 1:1. Kód instruktora musí zpracovat:

* Pokud uživatel zruší přiřazení sady Office, odstraňte `OfficeAssignment` entitu.
* Pokud uživatel zadá přiřazení pro Office a byl prázdný, vytvořte novou `OfficeAssignment` entitu.
* Pokud uživatel změní přiřazení kanceláře, aktualizujte `OfficeAssignment` entitu.

Aktualizujte model stránky pro instruktory pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Předchozí kód:

* Načte aktuální `Instructor` entitu z databáze pomocí Eager načítání `OfficeAssignment` pro navigační vlastnost.
* Aktualizuje načtenou `Instructor` entitu hodnotami z pořadače modelů. `TryUpdateModel`brání [](xref:data/ef-rp/crud#overposting)přestavení.
* Pokud je umístění kanceláře prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null. Pokud `Instructor.OfficeAssignment` je hodnota null, související řádek `OfficeAssignment` v tabulce je odstraněn.

### <a name="update-the-instructor-edit-page"></a>Aktualizace stránky pro úpravu instruktorů

Aktualizovat *stránky/instruktory/upravit. cshtml* s umístěním kanceláře:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Ověřte, že je možné změnit umístění kanceláře instruktorů.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Přidání přiřazení kurzů do stránky pro úpravu instruktorů

Instruktoři můžou učit libovolný počet kurzů. V této části přidáte možnost změnit přiřazení kurzů. Na následujícím obrázku je vidět aktualizovaná stránka pro úpravu instruktorů:

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses.png)

`Course`a `Instructor` má relaci n:n. Chcete-li přidat a odebrat relace, přidejte a odeberte entity ze `CourseAssignments` sady entit JOIN.

Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen. Pro každý kurz v databázi se zobrazí zaškrtávací políčko. Budou zkontrolovány kurzy, ke kterým je instruktor přiřazen. Uživatel může zaškrtnutím nebo zrušením zaškrtnutí políček změnit přiřazení kurzů. Pokud byl počet kurzů mnohem větší:

* Pro zobrazení kurzů byste pravděpodobně použili jiné uživatelské rozhraní.
* Metoda manipulace s entitou JOIN k vytvoření nebo odstranění relace se nezměnila.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Přidat třídy pro podporu vytváření a úprav stránek instruktora

Vytvořte *SchoolViewModels/AssignedCourseData. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData` Třída obsahuje data pro vytvoření zaškrtávacích políček pro přiřazené kurzy od instruktora.

Vytvořte základní třídu *Pages/instruktors/InstructorCoursesPageModel. cshtml. cs* :

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel` Je základní třída, kterou použijete pro modely stránky pro úpravy a vytváření. `PopulateAssignedCourseData`přečte `Course` všechny entity, `AssignedCourseDataList`které se naplní. Pro každý kurz kód nastaví `CourseID`název, název a bez ohledu na to, zda je instruktor přiřazen ke kurzu. [HashSet –](/dotnet/api/system.collections.generic.hashset-1) se používá k vytváření efektivních hledání.

### <a name="instructors-edit-page-model"></a>Instruktoři – upravit model stránky

Aktualizujte model stránky instruktory pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Předchozí kód zpracovává změny přiřazení Office.

Aktualizace zobrazení instruktor Razor:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Při vložení kódu v aplikaci Visual Studio se konce řádků mění způsobem, který kód přerušuje. Stisknutím kombinace kláves CTRL + Z pokaždé vraťte automatické formátování zpět. CTRL + Z opravuje konce řádků, aby vypadaly jako v tomto příkladu. Odsazení nemusí být dokonalé `@:</tr><tr>`, ale `@:</tr>` řádky `@:</td>`, `@:<td>`, a musí být na jednom řádku, jak je znázorněno na obrázku. Po vybrání bloku nového kódu stiskněte klávesu Tabulátor třikrát, aby se nový kód pořádek nastavil s existujícím kódem. Hlasujte nebo zkontrolujte stav této chyby [pomocí tohoto odkazu](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Předchozí kód vytvoří tabulku HTML, která má tři sloupce. Každý sloupec má zaškrtávací políčko a popisek obsahující číslo a název kurzu. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"). Použití stejného názvu informuje pořadač modelů, aby je považoval za skupinu. Atribut Value každé zaškrtávací políčko je nastaven na `CourseID`hodnotu. Po zveřejnění stránky předává pořadač modelu pole, které se skládá z `CourseID` hodnot pouze u zaškrtnutých políček.

Když jsou zaškrtávací políčka zpočátku vykreslena, kurzy přiřazené instruktorovi mají zaškrtnuté atributy.

Spusťte aplikaci a otestujte stránku pro úpravu aktualizovaných instruktorů. Změna některých přiřazení kurzu. Změny se projeví na stránce indexu.

Poznámka: Postup, který je zde k dispozici pro úpravu dat kurzu instruktora, funguje dobře, pokud existuje omezený počet kurzů. Pro kolekce, které jsou mnohem větší, jiné uživatelské rozhraní a odlišnou metodu aktualizace by byly efektivnější a efektivnější.

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

* Používá načítání Eager pro `CourseAssignments` navigační vlastnost. `CourseAssignments`musí být zahrnuté nebo se při odstranění instruktoru neodstraní. Abyste se vyhnuli nutnosti jejich čtení, nakonfigurujte v databázi kaskádové odstranění.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube (část 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Verze tohoto kurzu pro YouTube (část 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/read-related-data)Další
> [](xref:data/ef-rp/concurrency)

::: moniker-end

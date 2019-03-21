---
title: Stránky Razor s EF Core v ASP.NET Core – aktualizace souvisejících dat – 7 8
author: rick-anderson
description: V tomto kurzu budete aktualizovat souvisejících dat prostřednictvím aktualizace pole cizích klíčů a navigační vlastnosti.
ms.author: riande
ms.date: 11/15/2017
uid: data/ef-rp/update-related-data
ms.openlocfilehash: e1407048157fccdbfdd854071a6fbf9785356026
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208606"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>Stránky Razor s EF Core v ASP.NET Core – aktualizace souvisejících dat – 7 8

Podle [Petr Dykstra](https://github.com/tdykstra), a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

Tento kurz ukazuje, aktualizuje se související data. Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

Následující ilustrace ukazuje některé dokončených stránek.

![Stránky pro úpravu kurzu](update-related-data/_static/course-edit.png)
![kurzů vedených upravit stránku](update-related-data/_static/instructor-edit-courses.png)

Zkontrolujte a otestujte kurzu stránky vytvořit a upravit. Vytvořte nový kurz. Oddělení se vybírá podle jeho primárního klíče (celé číslo), nikoli jeho název. Upravte nový kurz. Po dokončení testování, odstranění nový kurz.

## <a name="create-a-base-class-to-share-common-code"></a>Vytvořte základní třídu sdílet společný kód

Stránky kurzy/Create a kurzy nebo upravit třeba seznam názvů oddělení. Vytvořit *Pages/Courses/DepartmentNamePageModel.cshtml.cs* základní třída pro vytvoření a úprava stránky:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) tak, aby obsahovala seznam názvů oddělení. Pokud `selectedDepartment` není zadána, oddělení je vybraný v `SelectList`.

Vytvoření a úprava tříd modelu stránky odvodí z `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Přizpůsobení stránek kurzy

Při vytvoření nové entity kurzu musí mít relaci k existující oddělení. Chcete-li přidat při vytváření kurz oddělení, obsahuje základní třída pro vytvoření a úprava rozevíracího seznamu pro výběr z oddělení. Nastaví rozevíracího seznamu `Course.DepartmentID` vlastnosti cizího klíče (Cizíklíč). EF Core používá `Course.DepartmentID` FK načíst `Department` navigační vlastnost.

![Vytvořit kurz](update-related-data/_static/ddl.png)

Aktualizace modelu stránka vytvořit s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Předchozí kód:

* Je odvozen od `DepartmentNamePageModel`.
* Používá `TryUpdateModelAsync` zabránit [overposting](xref:data/ef-rp/crud#overposting).
* Nahradí `ViewData["DepartmentID"]` s `DepartmentNameSL` (ze základní třídy).

`ViewData["DepartmentID"]` nahrazuje se silnými typy `DepartmentNameSL`. Silného typu modely se dává přednost před slabě typované. Další informace najdete v tématu [slabě typované data (ViewData a objekt ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualizace kurzů vytvořit stránku

Aktualizace *Pages/Courses/Create.cshtml* následujícím kódem:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Předchozí kód provede následující změny:

* Změní titulek z **DepartmentID** k **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` s `DepartmentNameSL` (ze základní třídy).
* Přidá možnost "Vybrat oddělení". Tato změna vykreslí "Vyberte oddělení" místo první oddělení.
* Přidá ověřovací zprávu, pokud není vybrána oddělení.

Stránka Razor používá [vyberte pomocné rutiny značky](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Testovací stránka vytvořit. Zobrazí se stránka vytvořit název oddělení, místo ID oddělení.

### <a name="update-the-courses-edit-page"></a>Aktualizace kurzů upravit stránku.

Aktualizace modelu upravit stránku s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Změny jsou podobné těm, které jsou provedeny v modelu vytvořit stránku. V předchozím kódu `PopulateDepartmentsDropDownList` průchody v ID oddělení, které vyberte oddělení uvedli v seznamu, rozevíracího seznamu.

Aktualizace *Pages/Courses/Edit.cshtml* následujícím kódem:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Předchozí kód provede následující změny:

* Zobrazuje ID kurzu. Obecně se nezobrazí primární klíč (PK) entity. PKs jsou obvykle nemá význam pro uživatele. V tomto případě primárnímu Klíči je číslo kurzu.
* Změní titulek z **DepartmentID** k **oddělení**.
* Nahradí `"ViewBag.DepartmentID"` s `DepartmentNameSL` (ze základní třídy).

Tato stránka obsahuje skryté pole (`<input type="hidden">`) pro číslo kurzu. Přidávání `<label>` pomocná rutina značky `asp-for="Course.CourseID"` nebude eliminuje nutnost skryté pole. `<input type="hidden">` je třeba číslo kurzu mají být zahrnuty v odeslaných dat, když uživatel klikne **Uložit**.

Otestujte aktualizovaný kód. Vytvářet, upravovat a odstraňovat kurzu.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Podrobné informace o přidání AsNoTracking a odstraňovat modely stránky

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zlepšit výkon při sledování není povinné. Přidat `AsNoTracking` modelu stránku odstranit a podrobnosti. Následující kód ukazuje aktualizovaného modelu odstranit stránky:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Aktualizace `OnGetAsync` metodu *Pages/Courses/Details.cshtml.cs* souboru:

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Úprava stránky Delete a podrobnosti

Aktualizace stránky Razor odstranit následujícím kódem:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Proveďte stejné změny na stránku podrobností.

### <a name="test-the-course-pages"></a>Testování stránek kurzu

Test vytvořit, upravit, podrobnosti a odstranění.

## <a name="update-the-instructor-pages"></a>Aktualizace stránek instruktorem

V dalších částech aktualizace kurzů vedených stránek.

### <a name="add-office-location"></a>Přidat umístění pro office

Při úpravě záznamu instruktorem, můžete aktualizovat přiřazení kanceláře instruktorem. `Instructor` Entita má vztah k nule nebo jednom s `OfficeAssignment` entity. Kód kurzů vedených musí zpracovat:

* Pokud uživatel vymaže přiřazení kanceláře, odstraňte `OfficeAssignment` entity.
* Pokud uživatel zadá přiřazení kanceláře a byla prázdná, vytvořte nový `OfficeAssignment` entity.
* Pokud uživatel změní přiřazení kanceláře, aktualizujte `OfficeAssignment` entity.

Aktualizace modelu Instruktoři upravit stránku s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Předchozí kód:

* Získá aktuální `Instructor` entit z databáze pomocí předběžné načítání pro `OfficeAssignment` navigační vlastnost.
* Aktualizuje načtený `Instructor` entity s hodnotami z vazače modelu. `TryUpdateModel` zabraňuje [overposting](xref:data/ef-rp/crud#overposting).
* Pokud office umístění je prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null. Když `Instructor.OfficeAssignment` je null, použije příslušného řádku v `OfficeAssignment` tabulce se odstraní.

### <a name="update-the-instructor-edit-page"></a>Aktualizace stránky pro úpravu instruktorem

Aktualizace *Pages/Instructors/Edit.cshtml* umístěním office:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Ověřte, že můžete změnit umístění služby Instruktoři office.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Přidat přiřazení kurzu stránky pro úpravu instruktorem

Instruktoři může představuje libovolný počet kurzů. V této části přidáte změnit přiřazení kurzu. Následující obrázek ukazuje aktualizované kurzů vedených stránky pro úpravu:

![Stránky pro úpravu instruktorem s kurzy](update-related-data/_static/instructor-edit-courses.png)

`Course` a `Instructor` má vztah mnoho mnoho. Přidávat a odebírat vztahy, přidávat a odebírat entit na základě `CourseAssignments` připojit k sadě entit.

Zaškrtávací políčka Povolit změny kurzy, které je přiřazeno instruktorem. Zaškrtávací políčko se zobrazí na každé kurz v databázi. Kurzy, které je přiřazeno kurzů vedených nebyly zvoleny. Uživatel může vyberte nebo zrušte zaškrtnutí políček, chcete-li změnit přiřazení kurzu. Kdyby mnohem větší počet kurzů:

* Pravděpodobně použijete jiné uživatelské rozhraní zobrazit kurzy.
* Metoda manipulace s spojení entity k vytvoření nebo odstranění vztahů nezměnila.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Přidání tříd pro podporu vytvářet a upravovat stránky instruktorem

Vytvoření *SchoolViewModels/AssignedCourseData.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData` Třída obsahuje data k vytvoření zaškrtnutí políček u přiřazené kurzy podle instruktorem.

Vytvořte *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* základní třídy:

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel` Je základní třídou budete používat pro úpravy a vytváření modelů stránky. `PopulateAssignedCourseData` přečte všechny `Course` entity k naplnění `AssignedCourseDataList`. Pro každý kurz kód nastaví `CourseID`, název a jestli se kurzů vedených přiřazen ke kurzu. A [HashSet](/dotnet/api/system.collections.generic.hashset-1) slouží k vytvoření efektivního vyhledávání.

### <a name="instructors-edit-page-model"></a>Model stránky Instruktoři úpravy

Aktualizace modelu kurzů vedených upravit stránku s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Předchozí kód zpracovává změny přiřazení sady office.

Aktualizace kurzů vedených zobrazení Razor:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Při vkládání kódu v sadě Visual Studio konce řádků se mění tak, aby kód přestane fungovat. Stisknutím klávesy Ctrl + Z jednou vrátit zpět, automatického formátování. CTRL + Z opravy konce řádků, tak, aby vypadají se zobrazí tady. Odsazení nemusí být dokonalý, ale `@</tr><tr>`, `@:<td>`, `@:</td>`, a `@:</tr>` řádky musí být na jednom řádku jak je znázorněno. Blok vybrali nový kód a stisknutím klávesy Tab třikrát na řádek nahoru nový kód existující kód. Hlasujte nebo zkontrolujte stav tuto chybu [s tímto odkazem](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Předchozí kód vytvoří tabulku HTML, který obsahuje tři sloupce. Zaškrtávací políčko a popisek obsahující číslo kurzu a název má každý sloupec. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"). Použití stejného názvu informuje vazač modelu pro s nimi zacházet jako se skupinou. Atribut hodnotu každého zaškrtávacího políčka je nastaven na `CourseID`. Na stránce, když se pošle vazače modelu předává pole, které se skládá z `CourseID` hodnoty pro pouze pole, které jsou vybrány.

Když zaškrtávací políčka jsou zpočátku vykresleno, kurzy, které jsou přiřazeny kurzů vedených kontrole atributů.

Spusťte aplikaci a otestovat aktualizované Instruktoři stránky pro úpravu. Změňte některá přiřazení kurzu. Změny se projeví na indexovou stránku.

Poznámka: Přístup provádět upravovat data kurzu kurzů vedených funguje dobře, když je omezený počet kurzů. Pro kolekce, které jsou mnohem větší různé uživatelské rozhraní a jinou metodu aktualizace by spotřebovat a efektivní.

### <a name="update-the-instructors-create-page"></a>Stránka pro vytvoření Instruktoři aktualizace

Aktualizace kurzů vedených vytvořit stránku model s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Předchozí kód je podobný *Pages/Instructors/Edit.cshtml.cs* kódu.

Aktualizace stránky Razor vytvořit kurzů vedených následujícím kódem:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Testovací stránka vytvořit instruktorem.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Aktualizace modelu odstranění stránky s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Předchozí kód provede následující změny:

* Používá předběžné načítání pro `CourseAssignments` navigační vlastnost. `CourseAssignments` musí být zahrnut nebo se neodstranily při odstranění instruktorem. Abyste nemuseli přečtěte si je, nakonfigurujte kaskádové odstranění v databázi.

* Pokud instruktorem, která se má odstranit je přiřazen jako správce z jakékoli oddělení, odebere z těchto oddělení přiřazení instruktorem.

## <a name="additional-resources"></a>Další zdroje

* [YouTube verzi tohoto kurzu (část 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [YouTube verzi tohoto kurzu (část 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/read-related-data)
> [další](xref:data/ef-rp/concurrency)

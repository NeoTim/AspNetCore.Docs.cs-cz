---
title: 'Kurz: Aktualizace souvisejících dat – ASP.NET MVC s EF Core'
description: V tomto kurzu budete aktualizovat souvisejících dat prostřednictvím aktualizace pole cizích klíčů a navigační vlastnosti.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: ac94f2e2876c2d8d571a451e4641787ffe37b3d2
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103030"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Kurz: Aktualizace souvisejících dat – ASP.NET MVC s EF Core

V předchozím kurzu zobrazí související data; v tomto kurzu budete aktualizovat souvisejících dat prostřednictvím aktualizace pole cizích klíčů a navigační vlastnosti.

Následující ilustrace znázorňují některé stránky, které budete pracovat.

![Kurz stránky pro úpravu](update-related-data/_static/course-edit.png)

![Stránky pro úpravu instruktorem](update-related-data/_static/instructor-edit-courses.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobení stránek kurzy
> * Přidat Instruktoři upravit stránku
> * Přidejte do stránky pro úpravu kurzy
> * Stránka pro aktualizaci Delete
> * Přidat stránku vytvořit pobočky a kurzy

## <a name="prerequisites"></a>Požadavky

* [Čtení souvisejících dat s EF Core pro webovou aplikaci ASP.NET Core MVC](read-related-data.md)

## <a name="customize-courses-pages"></a>Přizpůsobení stránek kurzy

Při vytvoření nové entity kurzu musí mít relaci k existující oddělení. K provedení této obsahuje automaticky generovaný kód metody kontroleru a vytvořit a upravit zobrazení, které zahrnují rozevíracího seznamu pro výběr oddělení. Sady rozevíracího seznamu `Course.DepartmentID` vlastnost cizího klíče, a to je všechny Entity Framework, které potřebujete-li načíst `Department` navigační vlastnost s odpovídající entita oddělení. Budete používat automaticky generovaný kód, ale mírně se přidání zpracování chyb a rozevírací seznam seřadit změnit.

V *CoursesController.cs*, odstraňte čtyři metody vytvoření a úprava a nahraďte následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Po `Edit` metoda HttpPost vytvoření nové metody, který načítá informace o oddělení pro rozevíracího seznamu.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

`PopulateDepartmentsDropDownList` Metoda načte seznam všech oddělení seřazené podle názvu, vytvoří `SelectList` kolekce rozevírací seznam a předá kolekce na zobrazení `ViewBag`. Metoda přijímá volitelný `selectedDepartment` parametr, který umožňuje volajícímu kódu k určení, které se při vykreslení rozevíracího seznamu vybrat položku. Zobrazení předá název "DepartmentID" `<select>` pomocné rutiny značky a pomocné rutiny pak mohl podívejte se `ViewBag` objekt pro `SelectList` s názvem "DepartmentID".

Třídy MetadataExchangeClientMode `Create` volání metod `PopulateDepartmentsDropDownList` metody bez nastavení vybrané položky, protože pro nový kurz oddělení nedojde k ještě:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

Třídy MetadataExchangeClientMode `Edit` metoda nastaví vybrané položky na základě ID oddělení, které je již přiřazen ke kurzu, který právě upravujete:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Pro obě metody HttpPost `Create` a `Edit` také obsahovat kód, který nastaví vybrané položky při jejich opětovné zobrazení na stránce po chybě. Tím se zajistí, že při stránce se zobrazí znovu, chcete-li zobrazit chybová zpráva, ať oddělení byla vybrána pořád vybraný.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Přidáte. AsNoTracking podrobností a metod Delete

Chcete-li optimalizovat výkon podrobnosti o kurzu a odstranění stránek, přidejte `AsNoTracking` volání `Details` a HttpGet `Delete` metody.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Upravit zobrazení kurzů

V *Views/Courses/Create.cshtml*, přidat možnost "Vybrat oddělení" k **oddělení** rozevíracího seznamu, změnit popisek z **DepartmentID** k  **Oddělení**a přidat ověřovací zprávu.

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

V *Views/Courses/Edit.cshtml*, provést stejnou změnu pro pole oddělení, který jste právě provedli v *Create.cshtml*.

Také v *Views/Courses/Edit.cshtml*, přidejte pole číslo kurzu před **název** pole. Vzhledem k tomu, že číslo kurzu je primárním klíčem, se zobrazí, ale nelze změnit.

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Již existuje skrytá pole (`<input type="hidden">`) pro číslo kurzu v zobrazení pro úpravy. Přidávání `<label>` pomocné rutiny značky není eliminuje nutnost skryté pole, protože nezpůsobí kurzu číslo, které má být součástí odeslaných dat, když uživatel klikne **Uložit** na **upravit** stránky.

V *Views/Courses/Delete.cshtml*kurzu číselného pole v horní části a přidejte změnit ID oddělení název oddělení.

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

V *Views/Courses/Details.cshtml*, provést stejnou změnu, který jste právě provedli pro *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Testování stránek kurzu

Spusťte aplikaci, vyberte **kurzy** klikněte na tlačítko **vytvořit nový**a zadejte data pro nový kurzu:

![Stránka pro vytvoření kurzu](update-related-data/_static/course-create.png)

Klikněte na možnost **Vytvořit**. Kurzy indexovou stránku se zobrazí nové kurzu přidat do seznamu. Název oddělení v seznamu Index stránky pochází z navigační vlastnosti zobrazující, že byla správně vytvoří vztah.

Klikněte na tlačítko **upravit** na kurz v kurzy indexovou stránku.

![Kurz stránky pro úpravu](update-related-data/_static/course-edit.png)

Data na stránce a klikněte na tlačítko **Uložit**. S daty aktualizovaný kurz se zobrazí stránka kurzy indexu.

## <a name="add-instructors-edit-page"></a>Přidat Instruktoři upravit stránku

Při úpravě záznamu instruktorem, budete chtít být schopen aktualizovat přiřazení kanceláře instruktorem. Entita kurzů vedených má jeden nula nebo m relaci s entitou OfficeAssignment, což znamená, že má váš kód pro zpracování těchto situacích:

* Pokud uživatel vymaže přiřazení kanceláře a původně hodnotu, odstraňte OfficeAssignment entity.

* Pokud uživatel zadá hodnotu přiřazení office a ho původně byla prázdná, vytvořte novou entitu OfficeAssignment.

* Pokud uživatel změní hodnotu přiřazení sady office, změňte hodnotu v existující OfficeAssignment entity.

### <a name="update-the-instructors-controller"></a>Aktualizace kontroleru Instruktoři

V *InstructorsController.cs*, změňte kód třídy MetadataExchangeClientMode `Edit` metodu tak, že načte entity kurzů vedených `OfficeAssignment` navigační vlastnost a volání `AsNoTracking`:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

Nahraďte HttpPost `Edit` metody následující kód pro zpracování aktualizací přiřazení sady office:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Kód provede následující akce:

-  Změní název metody k `EditPost` protože podpis je teď stejně jako třídy MetadataExchangeClientMode `Edit` – metoda ( `ActionName` atribut určuje, že `/Edit/` adresa URL se stále používá).

-  Získá aktuální kurzů vedených entit z databáze pomocí nemůžou dočkat, až načítání pro `OfficeAssignment` navigační vlastnost. To je stejný jako u třídy MetadataExchangeClientMode `Edit` metody.

-  Načtená entita kurzů vedených aktualizuje s hodnotami z vazače modelu. `TryUpdateModel` Přetížení vám umožní přidat na seznam povolených vlastnosti, které chcete zahrnout. To zabraňuje typu over-pass-the účtování, jak je vysvětleno v [druhé části kurzu](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

-   Pokud office umístění je prázdné, nastaví vlastnost Instructor.OfficeAssignment na hodnotu null, tak, aby související řádek v tabulce OfficeAssignment se odstraní.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- Uloží změny do databáze.

### <a name="update-the-instructor-edit-view"></a>Aktualizace kurzů vedených upravit zobrazení

V *Views/Instructors/Edit.cshtml*, přidat nové pole pro úpravy pobočce, na konci před **Uložit** tlačítka:

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Spusťte aplikaci, vyberte **Instruktoři** kartu a potom klikněte na tlačítko **upravit** na instruktorem. Změnit **pobočce** a klikněte na tlačítko **Uložit**.

![Stránky pro úpravu instruktorem](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Přidejte do stránky pro úpravu kurzy

Instruktoři může představuje libovolný počet kurzů. Teď budete vylepšit kurzů vedených upravit stránku tak, že přidáte změnit přiřazení kurz pomocí skupiny zaškrtávacích políček, jak je znázorněno na následujícím snímku obrazovky:

![Stránky pro úpravu instruktorem s kurzy](update-related-data/_static/instructor-edit-courses.png)

Vztah mezi entitami kurzu a kurzů vedených je many-to-many. Pokud chcete přidat a odebrat relace, přidávat a odebírat entity do a ze sady entit CourseAssignments spojení.

Rozhraní, které vám umožní měnit které kurzy instruktorem je přiřazená je skupina zaškrtávacích políček. Zaškrtávací políčko pro každý kurz v databázi se zobrazí a jsou ty, které se kurzů vedených je aktuálně přiřazená k vybrané. Uživatel může vyberte nebo zrušte zaškrtnutí políček, chcete-li změnit přiřazení kurzu. Kdyby mnohem větší počet kurzů, by pravděpodobně chtít použít jinou metodu prezentace dat v zobrazení, ale stejnou metodu manipulace s spojení entity byste použili k vytvoření nebo odstranění vztahů.

### <a name="update-the-instructors-controller"></a>Aktualizace kontroleru Instruktoři

Pro zadání dat pro zobrazení seznamu zaškrtávacích políček, použijete třídu modelu zobrazení.

Vytvoření *AssignedCourseData.cs* v *SchoolViewModels* složky a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

V *InstructorsController.cs*, nahraďte třídy MetadataExchangeClientMode `Edit` metodu s následujícím kódem. Změny jsou zvýrazněné.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Kód přidá předběžné načítání pro `Courses` navigační vlastnost a volá novou `PopulateAssignedCourseData` metodu k dispozici informaci pro použití pole zaškrtávací políčko `AssignedCourseData` zobrazení třídy modelu.

Kód v `PopulateAssignedCourseData` metoda přečte všechny entity kurzu-li načíst seznam kurzů horizontálních oddílů pomocí třídy modelu zobrazení. Jednotlivých kurzů, kód kontroluje, zda existuje kurz v kurzů vedených `Courses` navigační vlastnost. Chcete-li vytvořit efektivní vyhledávání při kontrole, zda je přiřazen kurzů vedených kurz, kurzy přiřazená kurzů vedených jsou vloženy do `HashSet` kolekce. `Assigned` Je nastavena na hodnotu true pro kurzy kurzů vedených přiřazen. Zobrazení bude tuto vlastnost použít k určení, které Kontrola polí musí být zobrazen jako vybraný. Nakonec je předán zobrazení v seznamu `ViewData`.

Dál přidejte kód, který se spouští, když uživatel klikne **Uložit**. Nahraďte `EditPost` metoda s tímto kódem a přidejte novou metodu, která aktualizuje `Courses` navigační vlastnost entity instruktorem.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Podpis metody je nyní liší od třídy MetadataExchangeClientMode `Edit` metodu, tak název metody se změní z `EditPost` zpět `Edit`.

Protože zobrazení neobsahuje kolekci entit kurz, vazače modelu se nedají automaticky aktualizovat `CourseAssignments` navigační vlastnost. Namísto použití vazače modelu k aktualizaci `CourseAssignments` navigační vlastnost, která učiníte v novém `UpdateInstructorCourses` metoda. Proto je třeba vyloučit `CourseAssignments` vlastnost z vazby modelu. To nevyžaduje změny kódu, který volá `TryUpdateModel` vzhledem k tomu, že používáte přetížení přidávání na seznam povolených a `CourseAssignments` není v seznamu pro zahrnutí.

Pokud není žádná kontrola se zaškrtnuta, kód v `UpdateInstructorCourses` inicializuje `CourseAssignments` navigační vlastnost s prázdnou kolekci a vrátí:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Kód pak projde všechny kurzy v databázi a zkontroluje každý kurz oproti těm, které jsou přiřazeny k instruktorem a ty, které byly vybrány v zobrazení. K usnadnění efektivnější vyhledávání, druhé dvě kolekce jsou uloženy v `HashSet` objekty.

Pokud zaškrtli políčko pro kurz ale kurzu není v `Instructor.CourseAssignments` navigační vlastnost, kurz je přidán do kolekce ve vlastnosti navigace.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Pokud není zaškrtnuté políčko kurzům, ale celé probíhá `Instructor.CourseAssignments` navigační vlastnost, kurz je odebrána z navigační vlastnost.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Aktualizace zobrazení instruktorem

V *Views/Instructors/Edit.cshtml*, přidat **kurzy** pole s polem zaškrtávacích políček přidáním následujícího kódu ihned po `div` prvky pro **Office**  pole a před `div` – element pro **Uložit** tlačítko.

<a id="notepad"></a>
> [!NOTE]
> Při vkládání kódu v sadě Visual Studio konce řádků se změní tak, aby kód přestane fungovat.  Stisknutím klávesy Ctrl + Z jednou vrátit zpět, automatického formátování.  To tak, aby vypadají se zobrazí zde opraví konce řádků. Odsazení nemusí být dokonalý, ale `@</tr><tr>`, `@:<td>`, `@:</td>`, a `@:</tr>` řádky musí být na jednom řádku znázorněno nebo zobrazí se chyba za běhu. Blok vybrali nový kód a stisknutím klávesy Tab třikrát na řádek nahoru nový kód existující kód. Stav tohoto problému zjistíte [tady](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Tento kód vytvoří tabulku HTML, který obsahuje tři sloupce. V každém sloupci, představuje zaškrtávací políčko, za nímž následuje, který se skládá z kurzu počet a názvy popisků. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"), která informuje o vazač modelu jsou považovány za skupinu. Hodnota atributu každé zaškrtávací políčko je nastavena na hodnotu `CourseID`. Na stránce, když se pošle vazače modelu předá kontroler, který se skládá z pole `CourseID` pouze zaškrtávací políčka, které jsou vybrané hodnoty.

Když zaškrtávací políčka jsou zpočátku vykresleno, ty, které jsou pro kurzy, které jsou přiřazeny kurzů vedených kontrole atributů, který vybere je (zobrazí se, které je zaškrtnuté políčko).

Spusťte aplikaci, vyberte **Instruktoři** kartu a klikněte na tlačítko **upravit** na instruktorem zobrazíte **upravit** stránky.

![Stránky pro úpravu instruktorem s kurzy](update-related-data/_static/instructor-edit-courses.png)

Změňte některá přiřazení kurzu a klikněte na Uložit. Provedené změny se projeví na indexovou stránku.

> [!NOTE]
> Přístup provádět upravovat data kurzu kurzů vedených funguje dobře, když je omezený počet kurzů. Pro kolekce, které jsou mnohem větší různé uživatelské rozhraní a jinou metodu aktualizace by vyžaduje.

## <a name="update-delete-page"></a>Stránka pro aktualizaci Delete

V *InstructorsController.cs*, odstranit `DeleteConfirmed` metoda a vložte následující kód na příslušné místo.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Tento kód provede následující změny:

* Fakturuje se u eager načítání pro `CourseAssignments` navigační vlastnost.  Je nutné zahrnout to nebo EF nebude vědět o související `CourseAssignment` entity a nedojde k jejich odstranění.  Abyste nemuseli přečtěte si je tady můžete nakonfigurovat kaskádové odstranění v databázi.

* Pokud instruktorem, která se má odstranit je přiřazen jako správce z jakékoli oddělení, odebere z těchto oddělení přiřazení instruktorem.

## <a name="add-office-location-and-courses-to-create-page"></a>Přidat stránku vytvořit pobočky a kurzy

V *InstructorsController.cs*, odstraňte HttpGet a HttpPost `Create` metody a místo nich přidejte následující kód:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Tento kód se podobá viděli pro `Edit` jsou vybrané metody s výjimkou případů, které žádné kurzy. Třídy MetadataExchangeClientMode `Create` volání metod `PopulateAssignedCourseData` metoda není, protože může být ale vybrané kurzy účelem zadejte prázdnou kolekci pro `foreach` smyčky v zobrazení (jinak zobrazit kód by výjimku výjimky odkaz s hodnotou null).

HttpPost `Create` metoda přidá každé vybrané kurz `CourseAssignments` navigační vlastnost před kontroluje výskyt chyb ověření a přidá nový kurzů vedených do databáze. Kurzy jsou přidány, i když nejsou chyby modelu, takže když existují chyby modelu (pro příklad, uživatel s klíčem, což je neplatné datum) a na stránce se zobrazí znovu, zobrazí se chybová zpráva, se automaticky obnoví zvolené kurzů, které byly provedeny.

Všimněte si, aby bylo možné přidat kurzy, které `CourseAssignments` navigační vlastnost, je třeba inicializovat vlastnost jako prázdnou kolekci:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Jako alternativu k to v kódu kontroleru je může provést v modelu kurzů vedených změnou metoda getter vlastnosti pro vytvoření kolekce Pokud neexistuje, jak je znázorněno v následujícím příkladu:

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

Pokud změníte `CourseAssignments` vlastnost tímto způsobem můžete odebrat explicitní vlastnost inicializační kód v kontroleru.

V *Views/Instructor/Create.cshtml*, přidejte textové pole umístění office a zaškrtněte políčka pro kurzy před tlačítka pro odeslání. Třeba v případě stránky pro úpravu [opravte formátování Pokud Visual Studio přeformátuje kód při vkládání](#notepad).

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Testování tak, že aplikaci spustíte a vytváření instruktorem.

## <a name="handling-transactions"></a>Zpracování transakcí

Jak je vysvětleno v [CRUD kurzu](crud.md), Entity Framework implementuje implicitně transakce. Pro scénáře, kde můžete potřebovat mít lepší kontrolu – například pokud budete chtít zahrnout operace provedené mimo rozhraní Entity Framework v rámci transakce – viz [transakce](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vlastní stránky kurzy
> * Přidání Instruktoři upravit stránku
> * Přidání kurzy, které stránky pro úpravu
> * Aktualizovaná stránka Delete
> * Přidání pobočky a kurzy, které stránka pro vytvoření

Přejděte k dalším článku se naučíte, jak zpracování konfliktů souběžnosti.
> [!div class="nextstepaction"]
> [Zpracování konfliktů souběžnosti](concurrency.md)

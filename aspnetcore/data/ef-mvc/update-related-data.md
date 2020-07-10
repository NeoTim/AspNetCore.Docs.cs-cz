---
title: 'Kurz: Aktualizace souvisejících dat – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu aktualizujete související data aktualizací polí cizího klíče a vlastností navigace.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 754ca2f94b1abde30ae650c9c3bcf00499520383
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212577"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Kurz: Aktualizace souvisejících dat – ASP.NET MVC pomocí EF Core

V předchozím kurzu jste zobrazili související data. v tomto kurzu aktualizujete související data aktualizací polí cizího klíče a vlastností navigace.

Následující ilustrace znázorňují některé stránky, se kterými budete pracovat.

![Stránka pro úpravu kurzu](update-related-data/_static/course-edit.png)

![Stránka pro úpravu instruktora](update-related-data/_static/instructor-edit-courses.png)

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobení stránek kurzů
> * Přidat stránku pro úpravu instruktorů
> * Přidání kurzů pro úpravu stránky
> * Aktualizace stránky pro odstranění
> * Přidání umístění Office a kurzů k vytvoření stránky

## <a name="prerequisites"></a>Požadavky

* [Čtení souvisejících dat](read-related-data.md)

## <a name="customize-courses-pages"></a>Přizpůsobení stránek kurzů

Když se vytvoří nová entita kurzu, musí mít relaci s existujícím oddělením. Pro usnadnění tohoto kódu se vygenerovaný kód skládá z metod kontroleru a vytváření a upravování zobrazení, která obsahují rozevírací seznam pro výběr oddělení. Rozevírací seznam nastaví `Course.DepartmentID` vlastnost cizího klíče a to je vše, co Entity Framework potřebuje, aby se vlastnost navigace načetla `Department` s příslušnou entitou oddělení. Použijete generovaný kód, ale mírně ho změníte a přidáte tak zpracování chyb a seřadíte rozevírací seznam.

V *CoursesController.cs*odstraňte čtyři metody Create a Edit a nahraďte je následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Po `Edit` metodě HTTPPOST vytvořte novou metodu, která načte informace o oddělení v rozevíracím seznamu.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

`PopulateDepartmentsDropDownList`Metoda získá seznam všech oddělení seřazených podle názvu, vytvoří `SelectList` kolekci pro rozevírací seznam a předá kolekci do zobrazení v `ViewBag` . Metoda přijímá volitelný `selectedDepartment` parametr, který umožňuje volajícímu kódu určit položku, která bude vybrána při vykreslení rozevíracího seznamu. Zobrazení pojmenuje název "DepartmentID" do `<select>` pomocné rutiny značky a pomoc pak ví, aby hledal `ViewBag` objekt pro `SelectList` pojmenování "DepartmentID".

Metoda HttpGet `Create` volá `PopulateDepartmentsDropDownList` metodu bez nastavení vybrané položky, protože pro nový kurz se oddělení ještě nevytvořilo:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

Metoda HttpGet `Edit` Nastaví vybranou položku na základě ID oddělení, které je už přiřazené k upravovanému kurzu:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Metody HttpPost pro obojí `Create` a `Edit` také obsahují kód, který nastaví vybranou položku, když znovu zobrazí stránku po chybě. Tím se zajistí, že se při zobrazení stránky zobrazí chybová zpráva bez ohledu na vybrané oddělení zůstane vybraná možnost.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Přidávání. AsNoTracking metody Details a DELETE

Pokud chcete optimalizovat výkon pro podrobnosti kurzu a odstranit stránky, přidejte `AsNoTracking` volání `Details` metod a HttpGet `Delete` .

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Úprava zobrazení kurzu

V okně *zobrazení/kurzy/vytvořit. cshtml*přidejte možnost "vybrat oddělení" do rozevíracího seznamu **oddělení** , změňte titulek z **DepartmentID** na **oddělení**a přidejte ověřovací zprávu.

[!code-cshtml[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

V *zobrazeních, kurzech/úpravách. cshtml*udělejte stejnou změnu pro pole oddělení, které jste právě vytvořili v části *vytvoření. cshtml*.

Také v *zobrazeních/kurzech/upravit. cshtml*přidejte pole číslo kurzu před pole **název** . Vzhledem k tomu, že číslo kurzu je primární klíč, zobrazuje se, ale nedá se změnit.

[!code-cshtml[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

V zobrazení pro úpravy již existuje skryté pole ( `<input type="hidden">` ) pro číslo kurzu. Přidáním `<label>` pomocníka značek se eliminuje nutnost skrytého pole, protože nezpůsobí, že se číslo kurzu zahrne do publikovaných dat, když uživatel klikne na **Uložit** na stránce **Upravit** .

V *zobrazení/kurzy/odstranit. cshtml*přidejte do horní části pole číslo kurzu a změňte ID oddělení na název oddělení.

[!code-cshtml[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

V *zobrazeních/kurzech/details. cshtml*udělejte stejnou změnu, kterou jste právě provedli pro *odstranění. cshtml*.

### <a name="test-the-course-pages"></a>Testování stránek kurzu

Spusťte aplikaci, vyberte kartu **kurzy** , klikněte na **vytvořit novou**a zadejte data nového kurzu:

![Stránka vytvoření kurzu](update-related-data/_static/course-create.png)

Klikněte na **Vytvořit**. Stránka s rejstříkem kurzů se zobrazí spolu s novým kurzem přidaným do seznamu. Název oddělení v seznamu stránek indexu pochází z navigační vlastnosti, která ukazuje, že relace byla správně vytvořena.

V kurzu na stránce s rejstříkem kurzů klikněte na **Upravit** .

![Stránka pro úpravu kurzu](update-related-data/_static/course-edit.png)

Změňte data na stránce a klikněte na **Uložit**. Stránka s rejstříkem kurzů se zobrazuje s aktualizovanými daty kurzu.

## <a name="add-instructors-edit-page"></a>Přidat stránku pro úpravu instruktorů

Když upravíte záznam instruktora, chcete mít schopnost aktualizovat přiřazení kanceláře instruktora. Entita Instructor má relaci 1:1 s entitou OfficeAssignment, což znamená, že váš kód musí zpracovávat následující situace:

* Pokud uživatel zruší přiřazení Office a původně měl hodnotu, odstraňte entitu OfficeAssignment.

* Pokud uživatel zadá hodnotu přiřazení Office, která byla původně prázdná, vytvořte novou entitu OfficeAssignment.

* Pokud uživatel změní hodnotu přiřazení kanceláře, změňte hodnotu v existující entitě OfficeAssignment.

### <a name="update-the-instructors-controller"></a>Aktualizace kontroleru instruktorů

V *InstructorsController.cs*změňte kód v `Edit` metodě HttpGet tak, aby byla načtena `OfficeAssignment` vlastnost navigace a volání entity instruktora `AsNoTracking` :

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

`Edit`Chcete-li zpracovat aktualizace přiřazení Office, nahraďte metodu HTTPPOST následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Kód provede následující:

* Změní název metody na, `EditPost` protože signatura je nyní stejná jako `Edit` Metoda HttpGet ( `ActionName` atribut určuje, že se `/Edit/` Adresa URL stále používá).

* Získá aktuální entitu instruktor z databáze pomocí Eager načítání pro `OfficeAssignment` navigační vlastnost. To se shoduje s tím, co jste provedli v `Edit` metodě HttpGet.

* Aktualizuje načtenou entitu Instructor hodnotami z pořadače modelů. `TryUpdateModel`Přetížení umožňuje deklarovat vlastnosti, které chcete zahrnout. Tím zabráníte převzetí služeb při selhání, jak je vysvětleno v [druhém kurzu](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Pokud je umístění kanceláře prázdné, vlastnost Instructor. OfficeAssignment nastaví na hodnotu null, aby se související řádek v tabulce OfficeAssignment odstranil.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Uloží změny do databáze.

### <a name="update-the-instructor-edit-view"></a>Aktualizace zobrazení pro úpravy instruktora

V *zobrazeních/instruktorech/upravit. cshtml*přidejte nové pole pro úpravy umístění kanceláře, a to na konci před tlačítkem **Uložit** :

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Spusťte aplikaci, vyberte kartu **instruktoři** a potom klikněte na tlačítko **Upravit** v instruktorovi. Změňte **umístění kanceláře** a klikněte na **Uložit**.

![Stránka pro úpravu instruktora](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Přidání kurzů pro úpravu stránky

Instruktoři můžou učit libovolný počet kurzů. Nyní vylepšíte stránku pro úpravu instruktoru přidáním možnosti změnit přiřazení kurzů pomocí skupiny zaškrtávacích políček, jak je znázorněno na následujícím snímku obrazovky:

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses.png)

Vztah mezi entitami Course a instruktorem je mnoho až mnoho. Chcete-li přidat a odebrat relace, přidejte a odeberte entity do a ze sady entit CourseAssignments JOIN.

Uživatelské rozhraní, které umožňuje změnit, ke kterým kurzům je instruktor přiřazen, je skupina zaškrtávacích políček. Zobrazí se zaškrtávací políčko pro každý kurz v databázi a jsou vybrány ty, ke kterým je instruktor aktuálně přiřazen. Uživatel může zaškrtnutím nebo zrušením zaškrtnutí políček změnit přiřazení kurzů. Pokud byl počet kurzů mnohem větší, pravděpodobně budete chtít použít jinou metodu, která prezentují data v zobrazení, ale použijte stejnou metodu manipulace s entitou JOIN k vytváření nebo odstraňování relací.

### <a name="update-the-instructors-controller"></a>Aktualizace kontroleru instruktorů

Chcete-li poskytnout data do zobrazení pro seznam zaškrtávacích políček, použijte třídu zobrazení modelu.

Vytvořte *AssignedCourseData.cs* ve složce *SchoolViewModels* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

V *InstructorsController.cs*nahraďte metodu HttpGet `Edit` následujícím kódem. Změny jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Kód přidá načtení Eager pro `Courses` navigační vlastnost a zavolá `PopulateAssignedCourseData` metodu New k poskytnutí informací pro pole zaškrtávacího políčka pomocí `AssignedCourseData` třídy zobrazení modelu.

Kód v `PopulateAssignedCourseData` metodě čte prostřednictvím všech entit kurzu, aby bylo možné načíst seznam kurzů pomocí třídy zobrazení modelu. Pro každý kurz kód kontroluje, zda v navigační vlastnosti instruktora existuje kurz `Courses` . K vytvoření efektivního vyhledávání při kontrole, jestli je kurz přiřazen instruktorovi, jsou kurzy přiřazené k instruktorovi vloženy do `HashSet` kolekce. `Assigned`Vlastnost je nastavena na hodnotu true pro kurzy, ke kterým je instruktor přiřazen. Zobrazení použije tuto vlastnost k určení, která zaškrtávací políčka se musí zobrazit jako vybraná. Nakonec se seznam předává do zobrazení v `ViewData` .

Dále přidejte kód, který se spustí, když uživatel klikne na **Uložit**. Nahraďte `EditPost` metodu následujícím kódem a přidejte novou metodu, která aktualizuje `Courses` navigační vlastnost entity Instructor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Signatura metody se teď liší od metody HttpGet `Edit` , takže se název metody změní z `EditPost` back na `Edit` .

Vzhledem k tomu, že zobrazení nemá kolekci entit kurzu, nemůže pořadač modelů automaticky aktualizovat `CourseAssignments` navigační vlastnost. Namísto použití pořadače modelů k aktualizaci `CourseAssignments` navigační vlastnosti to uděláte v nové `UpdateInstructorCourses` metodě. Proto je nutné vyloučit `CourseAssignments` vlastnost z vazby modelu. To nevyžaduje žádné změny kódu, který volá, `TryUpdateModel` protože používáte přetížení, které vyžaduje explicitní schválení a `CourseAssignments` není v seznamu zahrnutí.

Pokud nebyla vybrána žádná zaškrtávací políčka, kód v rámci `UpdateInstructorCourses` inicializuje `CourseAssignments` navigační vlastnost s prázdnou kolekcí a vrátí:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Kód pak projde všemi kurzy v databázi a zkontroluje každý kurz s těmi, které jsou aktuálně přiřazeny k instruktorovi, a k těm, které byly vybrány v zobrazení. Aby bylo možné efektivně vyhledávat, jsou tyto dvě kolekce uloženy v `HashSet` objektech.

Pokud je vybráno zaškrtávací políčko pro kurz, ale kurz není v `Instructor.CourseAssignments` navigační vlastnosti, kurz se přidá do kolekce v navigační vlastnosti.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Pokud není vybrané zaškrtávací políčko pro kurz, ale kurz je v `Instructor.CourseAssignments` navigační vlastnosti, kurz se odebere z navigační vlastnosti.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Aktualizace zobrazení instruktora

V *zobrazeních/instruktorech/upravit. cshtml*přidejte pole **kurzů** s polem zaškrtávacích políček přidáním následujícího kódu hned za `div` prvky pro pole **Office** a před `div` prvkem pro tlačítko **Uložit** .

<a id="notepad"></a>
> [!NOTE]
> Když kód vložíte v aplikaci Visual Studio, mohou být zalomení řádků změněny způsobem, který kód přerušuje. Pokud kód po vložení vypadá jinak, stiskněte klávesovou zkratku CTRL + Z, aby bylo automatické formátování vráceno zpět. Tím dojde k odstranění konců řádků, aby vypadaly jako v tomto příkladu. Odsazení nemusí být dokonalé, ale `@:</tr><tr>` řádky,, a `@:<td>` `@:</td>` `@:</tr>` musí být na jednom řádku, jak je znázorněno, nebo se zobrazí chyba za běhu. Po vybrání bloku nového kódu stiskněte klávesu Tabulátor třikrát, aby se nový kód pořádek nastavil s existujícím kódem. Tento problém je opravený v aplikaci Visual Studio 2019.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Tento kód vytvoří tabulku HTML, která má tři sloupce. V každém sloupci je zaškrtávací políčko následované titulkem, který se skládá z čísla a názvu kurzu. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"), který informuje pořadač modelů o tom, že se mají považovat za skupinu. Atribut Value každé zaškrtávací políčko je nastaven na hodnotu `CourseID` . Po zveřejnění stránky předává pořadač modelu pole do kontroleru, který se skládá z `CourseID` hodnot pouze u zaškrtávacích políček, která jsou vybrána.

Když jsou tato zaškrtávací políčka zpočátku vykreslena, jsou pro kurzy přiřazené instruktorem zkontrolovány atributy, které je vyberou (zobrazí zaškrtnutí).

Spusťte aplikaci, vyberte kartu **instruktory** a klikněte na tlačítko **Upravit** na instruktorovi, aby se zobrazila stránka pro **Úpravy** .

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses.png)

Změňte některá přiřazení kurzů a klikněte na Uložit. Změny, které provedete, se projeví na stránce indexu.

> [!NOTE]
> Postup, který je zde k dispozici pro úpravu dat kurzu instruktora, funguje dobře, pokud existuje omezený počet kurzů. Pro kolekce, které jsou mnohem větší, by se vyžadovalo jiné uživatelské rozhraní a odlišná metoda aktualizace.

## <a name="update-delete-page"></a>Aktualizace stránky pro odstranění

V *InstructorsController.cs*odstraňte `DeleteConfirmed` metodu a vložte následující kód na místo.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Tento kód provede následující změny:

* Načítá Eager pro `CourseAssignments` navigační vlastnost. Je nutné zahrnout tento, nebo EF neznáte související `CourseAssignment` entity a nebude je odstraňovat. Abyste se vyhnuli nutnosti jejich čtení, můžete v databázi nakonfigurovat kaskádové odstranění.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.

## <a name="add-office-location-and-courses-to-create-page"></a>Přidání umístění Office a kurzů k vytvoření stránky

V *InstructorsController.cs*odstraňte metody HttpGet a HTTPPOST `Create` a pak na jejich místo přidejte následující kód:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Tento kód se podobá tomu, co jste viděli pro `Edit` metody s tím rozdílem, že se nevybraly žádné kurzy. Metoda HttpGet `Create` volá metodu, `PopulateAssignedCourseData` protože by mohly být vybrány kurzy, ale za účelem poskytnutí prázdné kolekce pro `foreach` smyčku v zobrazení (jinak kód zobrazení by vyvolal výjimku s hodnotou null).

Metoda HttpPost `Create` přidá každý vybraný kurz do `CourseAssignments` vlastnosti navigace před tím, než zkontroluje chyby ověřování a přidá nového instruktora do databáze. Kurzy se přidávají i v případě, že dojde k chybám modelu, takže když dojde k chybám modelu (například uživatel zaznamená neplatné datum) a stránka se znovu zobrazí s chybovou zprávou, všechny vybrané kurzy se automaticky obnoví.

Všimněte si, že aby bylo možné do navigační vlastnosti přidat kurzy, `CourseAssignments` musíte tuto vlastnost inicializovat jako prázdnou kolekci:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Jako alternativu k tomu, že se jedná o kód kontroleru, to můžete provést v modelu instruktoru změnou vlastnosti getter na automaticky vytvořit kolekci, pokud neexistuje, jak je znázorněno v následujícím příkladu:

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

Pokud `CourseAssignments` tuto vlastnost upravíte tímto způsobem, můžete v řadiči odebrat explicitní inicializační kód vlastnosti.

V *zobrazení/instruktor/vytvořit. cshtml*, přidejte textové pole umístění kanceláře a zaškrtávací políčka pro kurzy před tlačítkem Odeslat. Jako v případě stránky pro úpravy [opravte formátování, pokud aplikace Visual Studio přeformátuje kód při jeho vložení](#notepad).

[!code-cshtml[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Otestujte spuštěním aplikace a vytvořením instruktora.

## <a name="handling-transactions"></a>Zpracování transakcí

Jak je vysvětleno v [kurzu CRUD](crud.md), Entity Framework implicitně implementuje transakce. V případě scénářů, kde potřebujete větší kontrolu – například pokud chcete zahrnout operace provedené mimo Entity Framework v transakci – viz [transakce](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Získání kódu

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Stránky přizpůsobených kurzů
> * Stránka pro úpravu přidaných instruktorů
> * Přidání kurzů pro úpravu stránky
> * Aktualizace stránky pro odstranění
> * Přidání umístění Office a kurzů k vytvoření stránky

Přejděte k dalšímu kurzu, kde se dozvíte, jak zpracovávat konflikty souběžnosti.

> [!div class="nextstepaction"]
> [Zpracování konfliktů souběžnosti](concurrency.md)

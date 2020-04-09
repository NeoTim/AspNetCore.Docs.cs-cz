---
title: 'Kurz: Aktualizace souvisejících dat - ASP.NET MVC s EF Core'
description: V tomto kurzu budete aktualizovat související data aktualizací polí cizího klíče a navigačních vlastností.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 83d662659fb4bc7a2867be563e4e36927d2adafe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657141"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Kurz: Aktualizace souvisejících dat - ASP.NET MVC s EF Core

V předchozím kurzu jste zobrazili související data; v tomto kurzu budete aktualizovat související data aktualizací polí cizího klíče a navigačních vlastností.

Na následujících obrázcích jsou uvedeny některé stránky, se kterými budete pracovat.

![Stránka Úpravy kurzu](update-related-data/_static/course-edit.png)

![Stránka Úpravy instruktora](update-related-data/_static/instructor-edit-courses.png)

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobit stránky kurzů
> * Stránka Přidat instruktory K úpravám
> * Přidání kurzů na stránku Úpravy
> * Aktualizovat stránku Odstranění
> * Přidání umístění kanceláře a kurzů na stránku Vytvořit

## <a name="prerequisites"></a>Požadavky

* [Čtení souvisejících dat](read-related-data.md)

## <a name="customize-courses-pages"></a>Přizpůsobit stránky kurzů

Když je vytvořena nová entita kurzu, musí mít vztah k existujícímu oddělení. Pro usnadnění tohoto, šástavlo kód obsahuje metody kontroleru a vytvořit a upravit zobrazení, které obsahují rozevírací seznam pro výběr oddělení. Rozevírací seznam nastaví vlastnost cizího `Course.DepartmentID` klíče a to je vše, `Department` co rozhraní entity framework potřebuje k načtení vlastnosti navigace s příslušnou entitou Department. Budete používat kód skládaný šálecím schématem, ale mírně jej změňte, abyste přidali zpracování chyb a seřadili rozevírací seznam.

V *CoursesController.cs*odstraňte čtyři metody Vytvořit a upravit a nahraďte je následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Po `Edit` metodě HttpPost vytvořte novou metodu, která načte informace o oddělení pro rozevírací seznam.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

Metoda `PopulateDepartmentsDropDownList` získá seznam všech oddělení seřazených `SelectList` podle názvu, vytvoří kolekci pro rozevírací `ViewBag`seznam a předá kolekci zobrazení v . Metoda přijímá volitelný `selectedDepartment` parametr, který umožňuje volající kód určit položku, která bude vybrána při vykreslení rozevíracího seznamu. Zobrazení předá název "DepartmentID" `<select>` pomocné značce a pomocník pak ví, `ViewBag` že `SelectList` má v objektu vyhledat pojmenovaný "DepartmentID".

Metoda HttpGet `Create` volá `PopulateDepartmentsDropDownList` metodu bez nastavení vybrané položky, protože pro nový kurz oddělení ještě není vytvořeno:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

Metoda HttpGet `Edit` nastaví vybranou položku na základě ID oddělení, které je již přiřazeno k upravovanému kurzu:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Metody HttpPost pro `Create` `Edit` oba a také kód, který nastaví vybranou položku při opětovném zobrazení stránky po chybě. Tím je zajištěno, že při opětovném zobrazení stránky, aby se zobrazila chybová zpráva, zůstane vybráno jakékoli oddělení.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Přidat. AsNoTracking na podrobnosti a odstranit metody

Chcete-li optimalizovat výkon podrobností kurzu `AsNoTracking` a `Details` odstranit stránky, přidejte volání v a HttpGet `Delete` metody.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Změna zobrazení kurzu

V *části Zobrazení/Kurzy/Create.cshtml*přidejte do rozevíracího seznamu **Oddělení** možnost Vybrat oddělení **Department**, změňte titulek z **ID oddělení** a přidejte zprávu o ověření.

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

V *části Zobrazení/Kurzy/Edit.cshtml*proveďte stejnou změnu pro pole Oddělení, které jste právě provedli v *souboru Create.cshtml*.

Také v *zobrazení / kurzy / Edit.cshtml*, přidejte číslo kurzu pole před **pole název.** Vzhledem k tomu, že číslo kurzu je primární klíč, zobrazí se, ale nelze jej změnit.

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Pro číslo kurzu v`<input type="hidden">`zobrazení úprav již existuje skryté pole ( ). Přidání `<label>` pomocníka značky nevylučuje potřebu skrytého pole, protože nezpůsobí, že číslo kurzu bude zahrnuto do zaúčtovaných dat, když uživatel klikne na **Uložit** na stránce **Úpravy.**

V *části Zobrazení/Kurzy/Delete.cshtml*přidejte nahoře pole s číslem kurzu a změňte ID oddělení na název oddělení.

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

V *zobrazení / Kurzy / Details.cshtml*, aby stejnou změnu, kterou jste právě udělal pro *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Otestujte stránky kurzu

Spusťte aplikaci, vyberte kartu **Kurzy,** klikněte na **Vytvořit nový**a zadejte data pro nový kurz:

![Stránka Vytvořit kurz](update-related-data/_static/course-create.png)

Klikněte na **Vytvořit**. Zobrazí se stránka Index kurzů s novým kurzem přidaným do seznamu. Název oddělení v seznamu stránek Index pochází z navigační vlastnosti, která ukazuje, že vztah byl navázán správně.

Na stránce Index kurzů klikněte na **Upravit** na kurz.

![Stránka Úpravy kurzu](update-related-data/_static/course-edit.png)

Změňte data na stránce a klepněte na **uložit**. Zobrazí se stránka Index kurzů s aktualizovanými údaji o kurzu.

## <a name="add-instructors-edit-page"></a>Stránka Přidat instruktory K úpravám

Při úpravě záznamu instruktora chcete mít možnost aktualizovat přiřazení kanceláře instruktora. Entita Instruktor má vztah 1:Nula nebo jeden s entitou OfficeAssignment, což znamená, že váš kód musí zpracovat následující situace:

* Pokud uživatel vymaže přiřazení kanceláře a původně měl hodnotu, odstraňte entitu OfficeAssignment.

* Pokud uživatel zadá hodnotu přiřazení kanceláře a původně byla prázdná, vytvořte novou entitu OfficeAssignment.

* Pokud uživatel změní hodnotu přiřazení kanceláře, změňte hodnotu v existující entitě OfficeAssignment.

### <a name="update-the-instructors-controller"></a>Aktualizace ovladače Instruktoři

V *InstructorsController.cs*změňte kód v `Edit` metodě HttpGet tak, `OfficeAssignment` aby načmáral navigační vlastnost a volání `AsNoTracking`entity Instruktor :

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

Nahraďte `Edit` metodu HttpPost následujícím kódem pro zpracování aktualizací přiřazení sady Office:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Kód provádí následující akce:

* Změní název metody `EditPost` na, protože podpis je nyní `Edit` stejný `ActionName` jako Metoda HttpGet (atribut určuje, že `/Edit/` adresa URL je stále používána).

* Získá aktuální entity instruktora z databáze `OfficeAssignment` pomocí eager načítání pro navigační vlastnost. To je stejné jako to, co `Edit` jste udělali v HttpGet metody.

* Aktualizuje načtenou entitu instruktora s hodnotami z pořadače modelu. Přetížení `TryUpdateModel` umožňuje whitelist vlastnosti, které chcete zahrnout. Tím se zabrání přeúčtování, jak je vysvětleno v [druhém tutoriálu](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Pokud je umístění kanceláře prázdné, nastaví vlastnost Instructor.OfficeAssignment na hodnotu null, aby byl odstraněn související řádek v tabulce OfficeAssignment.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Uloží změny do databáze.

### <a name="update-the-instructor-edit-view"></a>Aktualizace zobrazení Úprav instruktora

V *části Zobrazení/Instruktoři/Edit.cshtml*přidejte nové pole pro úpravy umístění kanceláře na konec před tlačítkem **Uložit:**

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Spusťte aplikaci, vyberte kartu **Instruktoři** a klikněte na **Upravit** u instruktora. Změňte **umístění Office** a klepněte na tlačítko **Uložit**.

![Stránka Úpravy instruktora](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Přidání kurzů na stránku Úpravy

Instruktoři mohou vyučovat libovolný počet kurzů. Nyní vylepšíte stránku Úpravy instruktora přidáním možnosti změnit přiřazení kurzu pomocí skupiny zaškrtávacích políček, jak je znázorněno na následujícím snímku obrazovky:

![Stránka úprav instruktora s kurzy](update-related-data/_static/instructor-edit-courses.png)

Vztah mezi subjekty kurzu a instruktora je n:N. Chcete-li přidat a odebrat vztahy, přidejte a odeberte entity do sady entit Spojení courseassignments a z ní.

UI, které umožňuje změnit kurzy instruktor je přiřazen a je skupina zaškrtávacích políček. Zobrazí se zaškrtávací políčko pro každý kurz v databázi a jsou vybrány ty, ke kterým je instruktor aktuálně přiřazen. Uživatel může zaškrtnout nebo zrušit zaškrtnutí políček pro změnu přiřazení kurzu. Pokud počet kurzů byly mnohem větší, pravděpodobně budete chtít použít jinou metodu prezentace dat v zobrazení, ale použili byste stejnou metodu manipulace s entitou spojení k vytvoření nebo odstranění vztahů.

### <a name="update-the-instructors-controller"></a>Aktualizace ovladače Instruktoři

Chcete-li poskytnout data do zobrazení pro seznam zaškrtávacích políček, použijete třídu modelu zobrazení.

Vytvořte *AssignedCourseData.cs* ve složce *SchoolViewModels* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

V *InstructorsController.cs*nahraďte `Edit` metodu HttpGet následujícím kódem. Změny jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Kód přidá dychtivé `Courses` načítání vlastnosti navigace `PopulateAssignedCourseData` a zavolá novou metodu, `AssignedCourseData` která poskytne informace pro pole zaškrtávacího políčka pomocí třídy modelu zobrazení.

Kód v `PopulateAssignedCourseData` metodě čte všechny entity kurzu, aby bylo možné načíst seznam kurzů pomocí třídy modelu zobrazení. Pro každý kurz kód kontroluje, zda kurz existuje `Courses` v navigační vlastnosti instruktora. Chcete-li vytvořit efektivní vyhledávání při kontrole, zda je kurz přiřazen instruktorovi, `HashSet` kurzy přiřazené instruktorovi jsou zařazeny do kolekce. Ubytovací `Assigned` zařízení je nastaveno na hodnotu true pro kurzy, ke kterým je instruktor přiřazen. Zobrazení použije tuto vlastnost k určení, která zaškrtávací políčka musí být zobrazena jako vybraná. Nakonec je seznam předán zobrazení `ViewData`v .

Dále přidejte kód, který je spuštěn, když uživatel klepne na tlačítko **Uložit**. Nahraďte metodu `EditPost` následujícím kódem a přidejte `Courses` novou metodu, která aktualizuje vlastnost navigace entity Instruktor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Podpis metody se nyní liší `Edit` od metody HttpGet, `EditPost` takže `Edit`název metody se změní z back na .

Vzhledem k tomu, že zobrazení nemá kolekci entit Course, vázé rvaček modelu nemůže automaticky aktualizovat vlastnost `CourseAssignments` navigace. Namísto použití vazače `CourseAssignments` modelu k aktualizaci vlastnosti `UpdateInstructorCourses` navigace, uděláte to v nové metodě. Proto je třeba `CourseAssignments` vyloučit vlastnost z vazby modelu. To nevyžaduje žádnou změnu kódu, `TryUpdateModel` který volá, protože používáte `CourseAssignments` whitelisting přetížení a není v seznamu zahrnutí.

Pokud nebyla zaškrtnuta žádná `UpdateInstructorCourses` políčka, kód `CourseAssignments` v inicializuje vlastnost navigace s prázdnou kolekcí a vrátí:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Kód pak prochází všechny kurzy v databázi a kontroluje každý kurz proti ty, které jsou aktuálně přiřazeny instruktorovi, oproti tomu, které byly vybrány v zobrazení. Pro usnadnění efektivní vyhledávání, poslední dvě kolekce `HashSet` jsou uloženy v objektech.

Pokud bylo zaškrtnuto políčko pro kurz, ale `Instructor.CourseAssignments` kurz není v navigační vlastnosti, kurz se přidá do kolekce v navigační vlastnosti.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Pokud nebylo zaškrtnuto políčko kurzu, ale kurz je `Instructor.CourseAssignments` v navigační vlastnosti, kurz se odebere z vlastnosti navigace.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Aktualizace zobrazení instruktora

V *části Zobrazení/Instruktoři/Edit.cshtml*přidejte pole **Kurzy** s polem `div` zaškrtávacích políček přidáním následujícího kódu bezprostředně za prvky pro pole **Office** a před `div` prvek tlačítka **Uložit.**

<a id="notepad"></a>
> [!NOTE]
> Při vložení kódu v sadě Visual Studio mohou být konce řádků změněny způsobem, který přeruší kód. Pokud kód po vložení vypadá jinak, stiskněte kombinaci kláves Ctrl+Z jednou, abyste automatické formátování znovu zučinili. Tím se opraví zalomení řádků tak, aby vypadaly tak, jak vidíte zde. Odsazení nemusí být dokonalé, ale `@</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` a řádky musí být na jednom řádku, jak je znázorněno, nebo se zobrazí chyba za běhu. Pokud je vybrán blok nového kódu, třikrát stiskněte klávesu Tab, abyste nový kód zařazovali s existujícím kódem. Tento problém je vyřešen v sadě Visual Studio 2019.

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Tento kód vytvoří tabulku HTML, která má tři sloupce. V každém sloupci je zaškrtávací políčko následované titulkem, který se skládá z čísla kurzu a názvu. Všechna zaškrtávací políčka mají stejný název ("vybrané kurzy"), který informuje pořadač modelu, že mají být považovány za skupinu. Atribut hodnoty každého zaškrtávacího `CourseID`políčka je nastaven na hodnotu . Po zaúčtování stránky předá pořadač modelu kontroleru pole, `CourseID` které se skládá z hodnot pouze pro vybraná zaškrtávací políčka.

Při počátečním vykreslení zaškrtávacích políček zkontrolovaly atributy, které je vyberou (zobrazí je zaškrtnuto).

Spusťte aplikaci, vyberte kartu **Instruktoři** a kliknutím na **Upravit** u instruktora zobrazte stránku **Úpravy.**

![Stránka úprav instruktora s kurzy](update-related-data/_static/instructor-edit-courses.png)

Změňte některá přiřazení kurzu a klikněte na Uložit. Provedené změny se projeví na stránce Rejstřík.

> [!NOTE]
> Přístup přijatý zde upravit instruktor a kurz data funguje dobře, když je omezený počet kurzů. Pro kolekce, které jsou mnohem větší, by bylo vyžadováno jiné ui a jinou metodu aktualizace.

## <a name="update-delete-page"></a>Aktualizovat stránku Odstranění

V *InstructorsController.cs*odstraňte metodu `DeleteConfirmed` a na její místo vložte následující kód.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Tento kód provede následující změny:

* Má dychtivé `CourseAssignments` načítání pro vlastnost navigace. Musíte zahrnout tento nebo EF nebude vědět `CourseAssignment` o souvisejících entit a nebude je odstranit. Chcete-li se vyhnout nutnosti číst zde můžete nakonfigurovat kaskádové odstranění v databázi.

* Pokud je instruktor, který má být odstraněn, přiřazen jako správce všech oddělení, odebere přiřazení instruktora z těchto oddělení.

## <a name="add-office-location-and-courses-to-create-page"></a>Přidání umístění kanceláře a kurzů na stránku Vytvořit

V *InstructorsController.cs*odstraňte metody HttpGet a HttpPost `Create` a pak na své místo přidejte následující kód:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Tento kód je podobný tomu, `Edit` co jste viděli pro metody s tím rozdílem, že zpočátku nejsou vybrány žádné kurzy. Metoda HttpGet `Create` volá `PopulateAssignedCourseData` metodu nikoli proto, že mohou být vybrány `foreach` kurzy, ale za účelem poskytnutí prázdné kolekce pro smyčku v zobrazení (jinak by kód zobrazení vyvolal výjimku s nulovým odkazem).

Metoda HttpPost `Create` přidá každý vybraný `CourseAssignments` kurz do navigační vlastnosti před tím, než zkontroluje chyby ověření a přidá nového instruktora do databáze. Kurzy jsou přidány i v případě, že jsou chyby modelu tak, že pokud jsou chyby modelu (například uživatel keyed neplatné datum) a stránka je znovu zobrazí s chybovou zprávou, všechny výběry kurzu, které byly provedeny jsou automaticky obnoveny.

Všimněte si, že aby bylo `CourseAssignments` možné přidat kurzy navigační vlastnosti, musíte inicializovat vlastnost jako prázdnou kolekci:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Jako alternativu k tomu v kódu kontrolora, můžete to udělat v modelu instruktora změnou vlastnosti getter automaticky vytvořit kolekci, pokud neexistuje, jak je znázorněno v následujícím příkladu:

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

Pokud změníte `CourseAssignments` vlastnost tímto způsobem, můžete odebrat explicitní kód inicializace vlastnosti v řadiči.

V *části Zobrazení/Instruktor/Create.cshtml*přidejte textové pole umístění kanceláře a zaškrtávací políčka pro kurzy před tlačítkem Odeslat. Stejně jako v případě upravit stránky, [opravit formátování, pokud Visual Studio přeformátuje kód při vložení](#notepad).

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Test spuštěním aplikace a vytvoření mno že instruktora.

## <a name="handling-transactions"></a>Zpracování transakcí

Jak je vysvětleno v [kurzu CRUD](crud.md), entity framework implicitně implementuje transakce. Pro scénáře, kde potřebujete větší kontrolu – například pokud chcete zahrnout operace provedené mimo entity frameworku v transakci – naleznete v [tématu Transakce](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Stránky kurzů na míru
> * Přidána stránka Úpravin instruktoři
> * Přidány kurzy na stránku Úpravy
> * Stránka Bylo aktualizováno Odstranit
> * Přidáno umístění kanceláře a kurzy pro vytvoření stránky

Přejdete k dalšímu kurzu, kde se dozvíte, jak zpracovat konflikty souběžnosti.

> [!div class="nextstepaction"]
> [Zpracování konfliktů souběžnosti](concurrency.md)

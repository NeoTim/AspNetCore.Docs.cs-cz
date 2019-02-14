---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Kurz: Aktualizace souvisejících dat v aplikaci ASP.NET MVC s EF'
description: V tomto kurzu budete aktualizovat související data. U většiny relací to můžete udělat prostřednictvím aktualizace pole cizích klíčů nebo navigační vlastnosti.
author: tdykstra
ms.author: riande
ms.date: 01/19/2019
ms.topic: tutorial
ms.assetid: 7ba88418-5d0a-437d-b6dc-7c3816d4ec07
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 1ef4242ff3bd1dd86f4d58bd04ba08e8b90fdaa4
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248274"
---
<a name="updating-related-data-with-the-entity-framework-in-an-aspnet-mvc-application"></a>Aktualizace souvisejících dat s Entity Framework v aplikaci ASP.NET MVC
====================


# <a name="tutorial-update-related-data-with-ef-in-an-aspnet-mvc-app"></a>Kurz: Aktualizace souvisejících dat v aplikaci ASP.NET MVC s EF

V předchozím kurzu zobrazí související data. V tomto kurzu budete aktualizovat související data. U většiny relací to můžete udělat prostřednictvím aktualizace pole cizích klíčů nebo navigační vlastnosti. U relací m: n Entity Framework nezveřejňuje tabulky spojení přímo, tak přidání a odebrání entity do a z odpovídající navigační vlastnosti.

Následující ilustrace znázorňují některé stránky, které budete pracovat.

![Course_create_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

![Upravit instruktorem s kurzy](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobení stránek kurzy
> * Přidat office na stránku Instruktoři
> * Přidání kurzů na stránku Instruktoři
> * Aktualizace DeleteConfirmed
> * Přidat na stránku vytvořit pobočky a kurzy

## <a name="prerequisites"></a>Požadavky

* [Čtení souvisejících dat](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="customize-courses-pages"></a>Přizpůsobení stránek kurzy

Při vytvoření nové entity kurzu musí mít relaci k existující oddělení. K provedení této obsahuje automaticky generovaný kód metody kontroleru a vytvořit a upravit zobrazení, které zahrnují rozevíracího seznamu pro výběr oddělení. Sady rozevíracího seznamu `Course.DepartmentID` vlastnost cizího klíče, a to je všechny Entity Framework, které potřebujete-li načíst `Department` navigační vlastnost s příslušnou `Department` entity. Budete používat automaticky generovaný kód, ale mírně se přidání zpracování chyb a rozevírací seznam seřadit změnit.

V *CourseController.cs*, odstraňte čtyři `Create` a `Edit` metody a nahraďte následujícím kódem:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,11-12,19-25,40,44,46,48-78)]

Přidejte následující `using` příkaz na začátku souboru:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

`PopulateDepartmentsDropDownList` Metoda načte seznam všech oddělení seřazené podle názvu, vytvoří `SelectList` kolekce rozevírací seznam a předá zobrazení v kolekci `ViewBag` vlastnost. Metoda přijímá volitelný `selectedDepartment` parametr, který umožňuje volajícímu kódu k určení, které se při vykreslení rozevíracího seznamu vybrat položku. Zobrazení předá název `DepartmentID` k [DropDownList](../../older-versions/working-with-the-dropdownlist-box-and-jquery/using-the-dropdownlist-helper-with-aspnet-mvc.md) pomocné rutiny a pomocné rutiny pak mohl podívat `ViewBag` objekt pro `SelectList` s názvem `DepartmentID`.

`HttpGet` `Create` Volání metod `PopulateDepartmentsDropDownList` metody bez nastavení vybrané položky, protože pro nový kurz oddělení nedojde k ještě:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

`HttpGet` `Edit` Metoda nastaví vybrané položky na základě ID oddělení, které je již přiřazen ke kurzu, který právě upravujete:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=12)]

`HttpPost` Metody pro oba `Create` a `Edit` také obsahovat kód, který nastaví vybrané položky při jejich opětovné zobrazení na stránce po chybě:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=6)]

Tento kód se zajistí, že při stránce se zobrazí znovu, chcete-li zobrazit chybová zpráva, ať oddělení byla vybrána pořád vybraný.

Kurz zobrazení jsou již generované uživatelské rozhraní s rozevírací seznamy pro pole oddělení, ale nechcete DepartmentID popisek pro toto pole, zkontrolujte následující zvýrazněný změnit na *Views\Course\Create.cshtml* do souboru Změna titulku.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml?highlight=43)]

Provést stejnou změnu v *Views\Course\Edit.cshtml*.

Obvykle scaffolder nepodporuje generování uživatelského rozhraní primární klíč protože hodnota klíče je generován databází nelze změnit a není smysluplnou hodnotu, který se má zobrazit uživatelům. Pro entity kurz zahrnuje scaffolder textové pole pro `CourseID` pole, protože chápe, že `DatabaseGeneratedOption.None` atribut znamená, že by měl mít možnost zadat hodnotu primárního klíče. Ale nerozumí, protože číslo má smysl chcete podívat, jak to v ostatních zobrazeních, takže je potřeba ji ručně přidat.

V *Views\Course\Edit.cshtml*, přidejte pole číslo kurzu před **název** pole. Protože je primární klíč, se zobrazí, ale nelze změnit.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cshtml)]

Již existuje skrytá pole (`Html.HiddenFor` pomocné rutiny) pro číslo kurzu v zobrazení pro úpravy. Přidání *Html.LabelFor* pomocné rutiny není eliminuje nutnost skryté pole, protože nezpůsobí kurzu číslo, které má být součástí odeslaných dat, když uživatel klikne **Uložit** na stránky pro úpravu.

V *Views\Course\Delete.cshtml* a *Views\Course\Details.cshtml*, změnit titulek název oddělení od "Name" do "Oddělení" a přidejte pole číslo kurzu před **nadpisu**  pole.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cshtml?highlight=2,9-15)]

Spustit **vytvořit** stránky (Zobrazit kurz indexovou stránku a klikněte na tlačítko **vytvořit nový**) a zadejte data pro nový kurzu:

| Hodnota | Nastavení |
| ----- | ------- |
| Číslo | Zadejte *1000*. |
| Název | Zadejte *algebraický*. |
| Závěrečné titulky | Zadejte *4*. |
|Oddělení | Vyberte **matematiky**. |

Klikněte na možnost **Vytvořit**. Kurz indexovou stránku se zobrazí nové kurzu přidat do seznamu. Název oddělení v seznamu Index stránky pochází z navigační vlastnosti zobrazující, že byla správně vytvoří vztah.

Spustit **upravit** stránky (Zobrazit kurz indexovou stránku a klikněte na tlačítko **upravit** v kurzu).

Data na stránce a klikněte na tlačítko **Uložit**. S daty aktualizovaný kurz se zobrazí stránka kurzu indexu.

## <a name="add-office-to-instructors-page"></a>Přidat office na stránku Instruktoři

Při úpravě záznamu instruktorem, budete chtít být schopen aktualizovat přiřazení kanceláře instruktorem. `Instructor` Entita má vztah k nule nebo jednom s `OfficeAssignment` entity, což znamená, že je nutné zpracovat v následujících případech:

- Pokud uživatel vymaže přiřazení kanceláře a původně hodnotu, je třeba odebrat a odstranit `OfficeAssignment` entity.
- Pokud uživatel zadá hodnotu přiřazení office a ho původně byla prázdná, je třeba vytvořit novou `OfficeAssignment` entity.
- Pokud uživatel změní hodnotu přiřazení kanceláře, musíte změnit hodnotu v existujícím `OfficeAssignment` entity.

Otevřít *InstructorController.cs* a podívejte se na `HttpGet` `Edit` metody:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

Automaticky generovaný kód není, co chcete. Nastavení dat pro rozevíracího seznamu, ale je nutné je textové pole. Tato metoda nahraďte následujícím kódem:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs?highlight=7-10)]

Tento kód klesne `ViewBag` příkazu a přidá předběžné načítání pro přidružený `OfficeAssignment` entity. Předběžné načítání s nelze provést `Find` metoda, proto `Where` a `Single` metody se používají místo toho vybrat instruktorem.

Nahradit `HttpPost` `Edit` metodu s následujícím kódem. která zpracovává aktualizace přiřazení sady office:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Odkaz na `RetryLimitExceededException` vyžaduje `using` příkaz Přidat – umístěte ukazatel myši nad `RetryLimitExceededException`. Zobrazí se následující zpráva: ![ Zkuste zpráva o výjimce](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image13.png)


Vyberte **ukazují potentital opravy**, pak **pomocí System.Data.Entity.Infrastructure**

![Vyřešit výjimku opakování](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image14.png)

Kód provede následující akce:

- Změní název metody k `EditPost` protože podpis je teď stejný jako `HttpGet` – metoda ( `ActionName` atribut určuje, že adresa URL /Edit/ se stále používá).
- Získá aktuální `Instructor` entit z databáze pomocí předběžné načítání pro `OfficeAssignment` navigační vlastnost. To je stejný jako co jste se naučili `HttpGet` `Edit` metody.
- Aktualizuje načtený `Instructor` entity s hodnotami z vazače modelu. [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.108).aspx) vám umožní použít přetížení *seznamu povolených IP adres* vlastnosti, které chcete zahrnout. To zabraňuje typu over-pass-the účtování, jak je vysvětleno v [druhé části kurzu](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md).

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]
- Pokud office umístění je prázdné, nastaví `Instructor.OfficeAssignment` vlastnost na hodnotu null tak, aby příslušného řádku v `OfficeAssignment` tabulce se odstraní.

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]
- Uloží změny do databáze.

V *Views\Instructor\Edit.cshtml*, poté, co `div` prvky pro **datum přijetí** pole, přidat nové pole pro úpravy pobočce:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml)]

Spuštění stránky (vyberte **Instruktoři** kartu a potom klikněte na tlačítko **upravit** na instruktorem). Změnit **pobočce** a klikněte na tlačítko **Uložit**.

## <a name="add-courses-to-instructors-page"></a>Přidání kurzů na stránku Instruktoři

Instruktoři může představuje libovolný počet kurzů. Teď budete vylepšit kurzů vedených upravit stránku tak, že přidáte změnit přiřazení kurz pomocí skupiny zaškrtávacích políček.

Vztah mezi `Course` a `Instructor` entity je many-to-many, což znamená, že nemáte přímý přístup k vlastnosti cizího klíče, které jsou v tabulce spojení. Místo toho můžete přidat nebo odebrat entity do a z `Instructor.Courses` navigační vlastnost.

Rozhraní, které vám umožní měnit které kurzy instruktorem je přiřazená je skupina zaškrtávacích políček. Zaškrtávací políčko pro každý kurz v databázi se zobrazí a jsou ty, které se kurzů vedených je aktuálně přiřazená k vybrané. Uživatel může vyberte nebo zrušte zaškrtnutí políček, chcete-li změnit přiřazení kurzu. Kdyby mnohem větší počet kurzů, by pravděpodobně chtít použít jinou metodu prezentace dat v zobrazení, ale můžete využít stejnou metodu manipulace s navigační vlastnosti, aby bylo možné vytvořit nebo odstranit relace.

Pro zadání dat pro zobrazení seznamu zaškrtávacích políček, použijete třídu modelu zobrazení. Vytvoření *AssignedCourseData.cs* v *modely ViewModels* složky a nahraďte existující kód následujícím kódem:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

V *InstructorController.cs*, nahraďte `HttpGet` `Edit` metodu s následujícím kódem. Změny jsou zvýrazněné.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs?highlight=9,12,20-35)]

Kód přidá předběžné načítání pro `Courses` navigační vlastnost a volá novou `PopulateAssignedCourseData` metodu k dispozici informaci pro použití pole zaškrtávací políčko `AssignedCourseData` zobrazení třídy modelu.

Kód v `PopulateAssignedCourseData` metoda přečte všechny `Course` třída entity-li načíst seznam kurzů pomocí zobrazení modelu. Jednotlivých kurzů, kód kontroluje, zda existuje kurz v kurzů vedených `Courses` navigační vlastnost. Chcete-li vytvořit efektivní vyhledávání při kontrole, zda je přiřazen kurzů vedených kurz, kurzy přiřazená kurzů vedených jsou vloženy do [HashSet](https://msdn.microsoft.com/library/bb359438.aspx) kolekce. `Assigned` Je nastavena na `true` kurzy kurzů vedených přiřazena. Zobrazení bude tuto vlastnost použít k určení, které Kontrola polí musí být zobrazen jako vybraný. Nakonec je předán zobrazení v seznamu `ViewBag` vlastnost.

Dál přidejte kód, který se spouští, když uživatel klikne **Uložit**. Nahradit `EditPost` metodu s následujícím kódem, který volá novou metodu, která aktualizuje `Courses` vlastnost navigace `Instructor` entity. Změny jsou zvýrazněné.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cs?highlight=3,11,25,37,40-68)]

Podpis metody se liší od teď `HttpGet` `Edit` metodu, tak název metody se změní z `EditPost` zpět `Edit`.

Protože zobrazení se nemusí kolekce `Course` entity, vazače modelu se nedají automaticky aktualizovat `Courses` navigační vlastnost. Namísto použití vazače modelu k aktualizaci `Courses` navigační vlastnost, můžete to udělat na novém `UpdateInstructorCourses` metody. Proto je třeba vyloučit `Courses` vlastnost z vazby modelu. To nevyžaduje změny kódu, který volá [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.98).aspx) vzhledem k tomu, že používáte *přidávání na seznam povolených* přetížení a `Courses` není v seznamu pro zahrnutí.

Pokud není žádná kontrola se zaškrtnuta, kód v `UpdateInstructorCourses` inicializuje `Courses` navigační vlastnost s prázdnou kolekci:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

Kód pak projde všechny kurzy v databázi a zkontroluje každý kurz oproti těm, které jsou přiřazeny k instruktorem a ty, které byly vybrány v zobrazení. K usnadnění efektivnější vyhledávání, druhé dvě kolekce jsou uloženy v `HashSet` objekty.

Pokud zaškrtli políčko pro kurz ale kurzu není v `Instructor.Courses` navigační vlastnost, kurz je přidán do kolekce ve vlastnosti navigace.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

Pokud není zaškrtnuté políčko kurzům, ale celé probíhá `Instructor.Courses` navigační vlastnost, kurz je odebrána z navigační vlastnost.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs)]

V *Views\Instructor\Edit.cshtml*, přidat **kurzy** pole s polem zaškrtávacích políček přidáním následujícího kódu ihned po `div` prvky pro `OfficeAssignment` pole a před `div` – element pro **Uložit** tlačítka:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cshtml)]

Po vložení kódu, pokud zalomení řádků a odsazení nevypadají jako tady, ručně všechno, co můžete opravit tak, aby to vypadá, co se zobrazí tady. Odsazení nemusí být dokonalý, ale `@</tr><tr>`, `@:<td>`, `@:</td>`, a `@</tr>` řádky musí být na jednom řádku znázorněno nebo zobrazí se chyba za běhu.

Tento kód vytvoří tabulku HTML, který obsahuje tři sloupce. V každém sloupci, představuje zaškrtávací políčko, za nímž následuje, který se skládá z kurzu počet a názvy popisků. Všechna zaškrtávací políčka mají stejný název ("selectedCourses"), která informuje o tom, které mají být považovány za skupinu vazače modelu. `value` Atribut každé zaškrtávací políčko je nastaven na hodnotu `CourseID.` při odeslání stránky vazače modelu předá kontroler, který se skládá z pole `CourseID` pouze zaškrtávací políčka, které jsou vybrané hodnoty.

Když zaškrtávací políčka jsou zpočátku vykresleno, mít ty, které jsou pro kurzy, které jsou přiřazeny kurzů vedených `checked` atributy, které vybere je (zobrazí se jim zaškrtnutí).

Po změně přiřazení kurzu, budete chtít mít možnost ověřit změny návratu na web `Index` stránky. Proto budete muset přidat sloupec do tabulky na této stránce. V tomto případě není nutné použít `ViewBag` objektu, protože je již v informace, které chcete zobrazit `Courses` vlastnost navigace `Instructor` entita, která se předá do stránky jako model.

V *Views\Instructor\Index.cshtml*, přidejte **kurzy** záhlaví hned za **Office** záhlaví, jak je znázorněno v následujícím příkladu:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cshtml?highlight=6)]

Pak přidejte novou buňku podrobností hned za buňku office umístění podrobností:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml?highlight=7-14)]

Spustit **kurzů vedených Index** stránku, abyste zobrazili kurzy přiřazená každé instruktorem.

Klikněte na tlačítko **upravit** na instruktorem zobrazíte stránky pro úpravu.

Některá přiřazení kurzu a klikněte na tlačítko **Uložit**. Provedené změny se projeví na indexovou stránku.

 Poznámka: Přístup provádět upravovat data kurzu kurzů vedených funguje dobře, když je omezený počet kurzů. Pro kolekce, které jsou mnohem větší různé uživatelské rozhraní a jinou metodu aktualizace by vyžaduje.

## <a name="update-deleteconfirmed"></a>Aktualizace DeleteConfirmed

V *InstructorController.cs*, odstranit `DeleteConfirmed` metoda a vložte následující kód na příslušné místo.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample24.cs?highlight=5-8,12-18)]

Tento kód provede následující změny:

- Pokud se kurzů vedených je přiřazen jako správce oddělení, odebere z oddělení přiřazení instruktorem. Bez tohoto kódu by dojde k chybě referenční integritu, pokud chcete odstranit instruktorem, který byl přiřazen jako správce pro oddělení.

Tento kód nezpracuje scénář jeden kurzů vedených přiřazen jako správce pro více oddělení. V posledním kurzu přidáte kód, který zabrání této situaci nedocházelo.

## <a name="add-office-location-and-courses-to-the-create-page"></a>Přidat na stránku vytvořit pobočky a kurzy

V *InstructorController.cs*, odstranit `HttpGet` a `HttpPost` `Create` metody a místo nich přidejte následující kód:


[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample25.cs)]

Tento kód je podobný co jste viděli pro úpravy metody s tím rozdílem, že původně jsou vybrány žádné kurzy. `HttpGet` `Create` Volání metod `PopulateAssignedCourseData` metoda není, protože může být ale vybrané kurzy účelem zadejte prázdnou kolekci pro `foreach` smyčky v zobrazení (jinak zobrazit kód vyvolá výjimka nulového odkazu ).

Metoda HttpPost vytvořit přidá jednotlivých vybraných kurzů pro navigační vlastnost kurzy před kód šablony, která kontroluje výskyt chyb ověření a přidá nový kurzů vedených do databáze. Kurzy jsou přidány, i když nejsou chyby modelu tak, aby po chyby modelu (pro příklad, uživatel s klíčem, což je neplatné datum) tak, aby když se zobrazí na stránce znovu s chybovou zprávou, se automaticky obnoví zvolené kurzů, které byly provedeny.

Všimněte si, aby bylo možné přidat kurzy, které `Courses` navigační vlastnost, je třeba inicializovat vlastnost jako prázdnou kolekci:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample26.cs)]

Jako alternativu k to v kódu kontroleru je může provést v modelu kurzů vedených změnou metoda getter vlastnosti pro vytvoření kolekce Pokud neexistuje, jak je znázorněno v následujícím příkladu:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample27.cs)]

Pokud změníte `Courses` vlastnost tímto způsobem můžete odebrat explicitní vlastnost inicializační kód v kontroleru.

V *Views\Instructor\Create.cshtml*, přidejte textové pole umístění office a po pronájem pole data a před kurzu zaškrtávací políčka **odeslat** tlačítko.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample28.cshtml)]

Po vložení kódu oprava konce řádků a odsazení jako jste to udělali dříve u stránky pro úpravu.

Spusťte stránka pro vytvoření a přidání instruktorem.

<a id="transactions"></a>

## <a name="handling-transactions"></a>Zpracování transakcí

Jak je vysvětleno v [základních funkcí CRUD kurzu](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md), ve výchozím nastavení rozhraní Entity Framework implicitně implementuje transakce. Pro scénáře, kde můžete potřebovat mít lepší kontrolu – například pokud budete chtít zahrnout operace provedené mimo rozhraní Entity Framework v rámci transakce – viz [práce s transakcí](https://msdn.microsoft.com/data/dn456843) na webové stránce MSDN.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework lze nalézt v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-step"></a>Další krok

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobené kurzy stránky
> * Přidání office na stránku Instruktoři
> * Přidání kurzů na stránku Instruktoři
> * Aktualizované DeleteConfirmed
> * Přidání pobočky a kurzů na stránku vytvořit

Přejděte k dalším článku se dozvíte, jak k implementaci asynchronního programovacího modelu.
> [!div class="nextstepaction"]
> [Asynchronní programovací model](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)

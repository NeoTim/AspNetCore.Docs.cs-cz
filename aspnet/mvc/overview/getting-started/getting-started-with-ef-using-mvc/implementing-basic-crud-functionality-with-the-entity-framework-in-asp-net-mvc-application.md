---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
title: 'Kurz: Implementace funkcí CRUD s rozhraním Entity Framework v architektuře ASP.NET MVC | Dokumentace Microsoftu'
description: Zkontrolovat a upravit vytvořit, číst, aktualizovat, odstranění (CRUD) kód, který automaticky vytvoří základní kostry aplikace MVC v kontrolerů a zobrazení.
author: tdykstra
ms.author: riande
ms.date: 01/11/2019
ms.topic: tutorial
ms.assetid: a2f70ba4-83d1-4002-9255-24732726c4f2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 9c6f8f3a2ffc0a9c5e15111ae47c331dab24ff43
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341717"
---
# <a name="tutorial-implement-crud-functionality-with-the-entity-framework-in-aspnet-mvc"></a>Kurz: Implementace funkcí CRUD s rozhraním Entity Framework v architektuře ASP.NET MVC

V [předchozí kurz o službě](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md), vytvoříte aplikaci MVC, která ukládá a zobrazuje data pomocí Entity Framework (EF) 6 a SQL Server LocalDB. V tomto kurzu můžete zkontrolovat a upravit vytvořit, číst, aktualizovat, odstranění (CRUD) kód, který generování uživatelského rozhraní MVC se automaticky vytvoří za vás do kontrolerů a zobrazení.

> [!NOTE]
> Je běžnou praxí pro implementaci vzoru úložiště chcete-li vytvořit vrstvu HAL mezi řadiči a vrstva přístupu k datům. Na tyto kurzy byly jednoduché a zaměřují se na vyučují použití EF 6 samotné, nepoužívejte úložišť. Informace o tom, jak implementovat úložiště najdete v tématu [mapa obsahu přístupu k Data technologie ASP.NET](../../../../whitepapers/aspnet-data-access-content-map.md).

Tady jsou příklady webových stránek, které vytvoříte:

![Snímek obrazovky stránky s podrobnostmi studentů.](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image1.png)

![Snímek obrazovky student vytvořit stránku.](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image2.png)

![Snímek obrazovky ot studenta odstranění stránky.](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image3.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření stránky podrobností
> * Aktualizovat stránku vytvořit
> * Aktualizovat metoda HttpPost úprav
> * Aktualizovat stránku Delete
> * Připojení k databázi zavřít
> * Zpracování transakcí

## <a name="prerequisites"></a>Požadavky

* [Vytvoření datového modelu Entity Framework](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)

## <a name="create-a-details-page"></a>Vytvoření stránky podrobností

Automaticky generovaný kód pro studenty `Index` vynechali stránky `Enrollments` vlastnost, protože tato vlastnost obsahuje kolekci. V `Details` stránce obsah kolekce bude zobrazen jako tabulku HTML.

V *Controllers\StudentController.cs*, metoda akce pro `Details` zobrazení používá [najít](https://msdn.microsoft.com/library/gg696418(v=VS.103).aspx) metodu pro načtení jedné `Student` entity.

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample1.cs)]

Hodnota klíče, která se předá metodě jako `id` parametr a pochází z *směrování dat* v **podrobnosti** hypertextový odkaz na indexovou stránku.

### <a name="tip-route-data"></a>Tip: **Data trasy**

Data trasy, která jsou data, která v segment adresy URL zadané ve směrovací tabulce nalezen vazač modelu. Například výchozí trasa určuje `controller`, `action`, a `id` segmenty:

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample2.cs?highlight=3)]

V následující adrese URL, výchozí trasu mapuje `Instructor` jako `controller`, `Index` jako `action` a 1 stejně jako `id`; jde o hodnot dat trasy.

`http://localhost:1230/Instructor/Index/1?courseID=2021`

`?courseID=2021` je hodnota řetězce dotazu. Vazač modelu budou fungovat i Pokud předáte `id` jako hodnotu řetězce dotazu:

`http://localhost:1230/Instructor/Index?id=1&CourseID=2021`

Adresy URL jsou vytvořeny pomocí `ActionLink` příkazy v zobrazení Razor. V následujícím kódu `id` parametr odpovídá výchozí trasu, takže `id` se přidá do data trasy.

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample3.cshtml)]

V následujícím kódu `courseID` neodpovídá parametru v této výchozí trase, tak se přidá jako řetězec dotazu.

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample4.cshtml)]

### <a name="to-create-the-details-page"></a>Chcete-li vytvořit na stránce podrobností

1. Otevřít *Views\Student\Details.cshtml*.

   Každé pole je zobrazeno pomocí `DisplayFor` pomocné rutiny, jak je znázorněno v následujícím příkladu:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample5.cshtml)]

2. Po `EnrollmentDate` pole a bezprostředně před uzavírací `</dl>` značky, přidejte zvýrazněný kód k zobrazení seznamu registrací, jak je znázorněno v následujícím příkladu:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample6.cshtml?highlight=8-29)]

    Pokud po vložení kódu je nesprávné odsazení kódu, stiskněte **Ctrl**+**K**, **Ctrl**+**D** k formátování ho.

    Tento kód projde entity v `Enrollments` navigační vlastnost. Pro každou `Enrollment` entity ve vlastnosti, zobrazí název kurzu a na podnikové úrovni. Název kurzu je načten z `Course` entita, která je uložena v `Course` vlastnost navigace `Enrollments` entity. Všechna tato data se načtou z databáze automaticky když ho nepotřebují. Jinými slovy že používáte opožděné načtení tady. Nezadali jste *předběžné načítání* pro `Courses` vlastnost navigace, takže registrací nebyly načteny ve stejném dotazu, které získali studenty. Místo toho při prvním pokusu o přístup k `Enrollments` navigační vlastnost, nový dotaz se odesílají do databáze načíst data. Další informace o opožděné načtení a nemůžou dočkat, až načítání v [čtení souvisejících dat](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) později v této sérii kurzů.

3. Otevřete stránku podrobností spuštěním programu (**Ctrl**+**F5**), vyberete **studenty** kartu a pak levým na **podrobnosti** odkaz Alexander Carsonovy. (Pokud stisknete **Ctrl**+**F5** zatímco *Details.cshtml* je soubor otevřen, dojde k chybě HTTP 400. Je to proto, že Visual Studio se pokusí spustit na stránce podrobností, ale nebyl dosažen z odkazu, který určuje student k zobrazení. Pokud k tomu dojde, odeberte "Student/Details" z adresy URL a zkuste to znovu, nebo, zavřete prohlížeč, klikněte pravým tlačítkem na projekt a klikněte na tlačítko **zobrazení** > **zobrazit v prohlížeči**.)

    Zobrazí seznam kurzů a známek pro vybrané studentů.

4. Zavřete prohlížeč.

## <a name="update-the-create-page"></a>Aktualizovat stránku vytvořit

1. V *Controllers\StudentController.cs*, nahraďte <xref:System.Web.Mvc.HttpPostAttribute> `Create` metody akce s následujícím kódem. Tento kód přidá `try-catch` bloku a odebere `ID` z <xref:System.Web.Mvc.BindAttribute> atributů pro metodu vygenerované:

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample7.cs?highlight=3,5-6,13-18)]

    Tento kód přidá `Student` entita vytvořená pomocí vazače modelu ASP.NET MVC do `Students` entity nastavení a pak uloží změny do databáze. *Vazač modelu* odkazuje na funkci ASP.NET MVC, která provádí, což usnadňuje práci s data odeslaná formuláře; vazač modelu převádí publikování formuláře hodnoty pro typy CLR a předává je na metodu akce v parametrech. V takovém případě vytvoří instanci vazače modelu `Student` entity pomocí vlastnosti hodnoty z `Form` kolekce.

    Můžete odebrat `ID` z vazba atributu, protože `ID` je hodnota primárního klíče, které SQL Server se nastaví automaticky při vložení řádku. Vstup od uživatele nenastaví `ID` hodnotu.

    <a id="overpost"></a>

    ### <a name="security-warning---the-validateantiforgerytoken-attribute-helps-prevent-cross-site-request-forgerysecurityxsrfcsrf-prevention-in-aspnet-mvc-and-web-pagesmd-attacks-it-requires-a-corresponding-htmlantiforgerytoken-statement-in-the-view-which-youll-see-later"></a>Upozornění zabezpečení – `ValidateAntiForgeryToken` atribut pomáhá zabránit [padělání žádosti více webů](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) útoky. Vyžaduje odpovídající `Html.AntiForgeryToken()` příkaz v zobrazení uvidíte později.

    `Bind` Atribut je jedním ze způsobů pro ochranu před *over-pass-the účtování* v vytvářet scénáře. Předpokládejme například, že `Student` zahrnuje entity `Secret` vlastnost, která nechcete tuto webovou stránku nastavení.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample8.cs?highlight=7)]

    I v případě, že nemáte `Secret` pole na webové stránce, se hacker použít nástroj, jako [fiddler](http://fiddler2.com/home), nebo napište určitého kódu JavaScript, k publikování `Secret` tvoří hodnotu. Bez <xref:System.Web.Mvc.BindAttribute> atribut pole, která používá vazač modelu při vytváření omezení `Student` instance<em>,</em> vazače modelu bude pokračovat, který `Secret` formu hodnotu a použijte ji k vytvoření `Student` entity instance. Pak cokoli, co hodnota kyberzločinci zadaný pro `Secret` pole formuláře se aktualizují v databázi. Následující obrázek ukazuje, fiddleru nástroj pro přidání `Secret` pole (hodnotu "OverPost") na hodnoty odeslaného formuláře.

    ![](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image5.png)

    Hodnota "OverPost" by poté úspěšně přidány do `Secret` vlastností vloženého řádku, i když jste zamýšleli nikdy, že je pro webové stránky moct nastavit tuto vlastnost.

    Je nejvhodnější použít `Include` parametr `Bind` atribut *seznamu povolených IP adres* pole. Je také možné použít `Exclude` parametr *blacklist* pole, které chcete vyloučit. Z důvodu `Include` je bezpečnější je, že při přidání nové vlastnosti do entity nové pole nejsou chráněny automaticky `Exclude` seznamu.

    Je možné zabránit overposting ve scénářích upravit, je nejprve čtení entit z databáze a následným voláním `TryUpdateModel`, předejte v seznamu povolených explicitní vlastností. To je metoda použitá v těchto kurzech.

    Alternativní způsob zabránění overposting, který mnoho vývojářů preferuje se má zobrazit modely, nikoli tříd entit pomocí vazby modelu. Zahrnout pouze vlastnosti, které chcete aktualizovat v modelu zobrazení. Po dokončení vazač modelu MVC zkopírovat vlastnosti zobrazení modelu do instance entity, případně můžete použít nástroj, jako [AutoMapper](http://automapper.org/). Použití databáze. Položka na zapisovanou instanci entity k nastavení stavu Unchanged a potom nastavte Property("PropertyName"). IsModified na hodnotu true na každé vlastnosti entity, která je součástí modelu zobrazení. Tato metoda funguje v obou upravovat a vytvářet scénáře.

    Jiné než `Bind` atribut, `try-catch` blok je pouze změny provedené na automaticky generovaný kód. Pokud výjimka, která je odvozena z <xref:System.Data.DataException> je zachycena, zatímco se ukládají se změny, zobrazí se obecné chybové zprávy. <xref:System.Data.DataException> výjimky jsou někdy způsobeny něco mimo aplikací, nikoli programovací chyba, takže uživatele se doporučuje to chcete zkusit znovu. I když není implementovaná v této ukázce, typicky zaznamenávají produkční kvality aplikace výjimku. Další informace najdete v tématu **protokolu insight** tématu [monitorování a Telemetrie (vytváření skutečných cloudových aplikací s Azure)](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log).

    Kód v *Views\Student\Create.cshtml* je podobný viděli v *Details.cshtml*, s tím rozdílem, že `EditorFor` a `ValidationMessageFor` pomocné rutiny, které se používají pro každé pole namísto `DisplayFor`. Tady je příslušný kód:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample9.cshtml)]

    *Create.chstml* také zahrnuje `@Html.AntiForgeryToken()`, který pracuje s `ValidateAntiForgeryToken` atribut v kontroleru, aby se zabránilo [padělání žádosti více webů](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) útoky.

    Nevyžaduje žádné změny v *Create.cshtml*.

2. Spuštěním programu spustit na stránce Výběr **studenty** kartu a pak levým na **vytvořit nový**.

3. Zadejte názvy a neplatné datum a klikněte na tlačítko **vytvořit** zobrazíte chybovou zprávu.

    Toto je ověřování na straně serveru, který získáte ve výchozím nastavení. V pozdějších kurzech uvidíte, jak přidat atributy, které generují kód pro ověřování na straně klienta. Následující zvýrazněný kód ukazuje model ověřování vrácení se změnami **vytvořit** metody.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample10.cs?highlight=1)]

4. Změňte na platnou hodnotu data a klikněte na tlačítko **vytvořit** zobrazíte nového objektu student joinkind **Index** stránky.

5. Zavřete prohlížeč.

## <a name="update-httppost-edit-method"></a>Metoda HttpPost upravit aktualizace

1. Nahradit <xref:System.Web.Mvc.HttpPostAttribute> `Edit` metody akce s následujícím kódem:

   [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample11.cs)]

   > [!NOTE]
   > V *Controllers\StudentController.cs*, `HttpGet Edit` – metoda (jeden bez `HttpPost` atribut) používá `Find` metodu pro načtení vybraných `Student` entity, jako jste viděli v `Details`metody. Nemusíte změnit tuto metodu.

   Tyto změny implementovat osvědčeným postupem zabezpečení, aby se zabránilo [overposting](#overpost), generátor vygeneruje `Bind` atribut a přidání entity vytvořené v entitě nastavení pomocí příznaku změněné vazače modelu. Že kód už nedoporučuje, protože `Bind` atribut vymaže všechny už existující data v polích, které nejsou uvedené v `Include` parametru. V budoucnu, generátor řadiče MVC aktualizují tak, aby negeneroval `Bind` atributy pro úpravy metody.

   Nový kód čte existující entity a volání <xref:System.Web.Mvc.Controller.TryUpdateModel%2A> aktualizace polí ze vstupu uživatele v datech odeslaného formuláře. Entity Framework automatické sledování sad změn [EntityState.Modified](<xref:System.Data.EntityState.Modified>) příznak v entitě. Když [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) metoda je volána, <xref:System.Data.EntityState.Modified> příznak způsobí, že rozhraní Entity Framework pro vytváření příkazů SQL aktualizujte řádek databáze. [Konflikty souběžnosti](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md) jsou ignorovány, a jsou aktualizovány všechny sloupce řádek databáze, včetně těch, které uživatel nezměnil. (Vyšší kurz ukazuje postupy při zpracování konfliktů souběžnosti, a pokud chcete jenom jednotlivá pole v databázi, můžete nastavit entity na [EntityState.Unchanged](<xref:System.Data.EntityState.Unchanged>) a nastavte jednotlivá pole na [ EntityState.Modified](<xref:System.Data.EntityState.Modified>).)

   Pokud chcete zabránit overposting, pole, která mají být možné aktualizovat pomocí stránky pro úpravu jsou povolené v `TryUpdateModel` parametry. Aktuálně nejsou žádné další pole, které chcete chránit, ale seznam polí, která má vazač modelu pro vazbu zajistí, že pokud přidáte pole do datového modelu v budoucnu, že jsou automaticky chráněné dokud explicitně přidáte je tady.

   V důsledku těchto změn podpis metody HttpPost úpravy metody je stejná jako metoda úprav HttpGet; Proto když jste přejmenovali metoda EditPost.

   > [!TIP]
   >
   > **Stavy entity a připojit a SaveChanges metody**
   >
   > Uchovává informace o kontextu databáze, jestli jsou synchronizované s jejich odpovídajících řádků v databázi entity v paměti a těchto informací Určuje, co se stane při volání `SaveChanges` metody. Například při předání do nové entity [přidat](https://msdn.microsoft.com/library/system.data.entity.dbset.add(v=vs.103).aspx) metoda, která stav entity je nastavený na `Added`. Potom při volání [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) metoda, vydá kontext databáze SQL `INSERT` příkazu.
   >
   > Entita může být v jednom z následujících [stavy](xref:System.Data.EntityState):
   >
   > - `Added`. Entita ještě neexistuje v databázi. `SaveChanges` Metody musíte vydat `INSERT` příkazu.
   > - `Unchanged`. Co je potřeba udělat s touto entitou podle `SaveChanges` metody. Při čtení entity z databáze entity je na začátku s tímto stavem.
   > - `Modified`. Některé nebo všechny hodnoty vlastností entity byly změněny. `SaveChanges` Metody musíte vydat `UPDATE` příkazu.
   > - `Deleted`. Entita byla označena k odstranění. `SaveChanges` Metody musíte vydat `DELETE` příkazu.
   > - `Detached`. Entita není sledován správou kontext databáze.
   >
   > V desktopové aplikaci změny stavu se obvykle nastaví automaticky. V typu desktopové aplikace čtení entity a provést změny na některé z jeho hodnot vlastností. To způsobí, že jeho stav entity automaticky změnil na `Modified`. Potom při volání `SaveChanges`, Entity Framework generuje SQL `UPDATE` příkaz, který aktualizuje pouze skutečné vlastnosti, které jste změnili.
   >
   > Toto pořadí průběžné neumožňuje odpojené povaha služby web apps. [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx) , která načte entity je uvolněn po vykreslení stránky. Když `HttpPost` `Edit` volání metody, vytvoří se nový požadavek a mít novou instanci třídy [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx), takže je nutné ručně nastavit stavu entity `Modified.` pak při volání `SaveChanges`, Entity Framework aktualizuje všechny sloupce řádek databáze, protože kontext nemá žádný způsob, jak zjistit vlastnosti, které jste změnili.
   >
   > Pokud chcete, aby SQL `Update` . doje k aktualizaci pouze pole, která uživatel ve skutečnosti změní, můžete uložit původní hodnoty nějakým způsobem (třeba skrytá pole) tak, aby byly k dispozici při `HttpPost` `Edit` metoda je volána. Můžete vytvořit `Student` entity pomocí původní hodnoty, volání `Attach` metodu s původní verzi entitou, aktualizujte hodnoty entity na nové hodnoty a následně zavolat `SaveChanges.` Další informace najdete v tématu [ Stavy entity a SaveChanges](/ef/ef6/saving/change-tracking/entity-state) a [místních dat](/ef/ef6/querying/local-data).

   Kód HTML a Razor v *Views\Student\Edit.cshtml* je podobný viděli v *Create.cshtml*, a nejsou potřeba žádné změny.

2. Spuštěním programu spustit na stránce Výběr **studenty** kartu a pak levým na **upravit** hypertextový odkaz.

3. Některá data a klikněte na tlačítko Změnit **Uložit**. Zobrazí změněných dat v indexovou stránku.

4. Zavřete prohlížeč.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

V *Controllers\StudentController.cs*, kód šablony pro <xref:System.Web.Mvc.HttpGetAttribute> `Delete` metoda používá `Find` metodu pro načtení vybraných `Student` entity, jako jste viděli v `Details` a `Edit` metody. Však implementovat vlastní chybová zpráva při volání `SaveChanges` selže, přidáte některé funkce pro tuto metodu a jeho odpovídající zobrazení.

Už znáte aktualizace a operace vytvoření, odstranění operace vyžadují dvě metody akce. Metoda, která je volána v reakci na požadavek GET zobrazí zobrazení, které umožňuje uživateli možnost schválit nebo zrušit operaci odstranění zopakovat. Pokud uživatel ho buď schválí, vytvoří se požadavek POST. Pokud k tomu dojde, `HttpPost` `Delete` volání metody a pak tato metoda ve skutečnosti provádí operaci odstranění zopakovat.

Přidáte `try-catch` bloku <xref:System.Web.Mvc.HttpPostAttribute> `Delete` metodu ke zpracování všechny chyby, které mohou nastat při aktualizaci databáze. Pokud dojde k chybě <xref:System.Web.Mvc.HttpPostAttribute> `Delete` volání metod <xref:System.Web.Mvc.HttpGetAttribute> `Delete` metodu předáním parametru, která označuje, že došlo k chybě. <xref:System.Web.Mvc.HttpGetAttribute> `Delete` Metoda pak znovu zobrazí na stránce potvrzení spolu s chybovou zprávu, poskytnou tak uživateli možnost zrušit, nebo to zkuste znovu.

1. Nahradit <xref:System.Web.Mvc.HttpGetAttribute> `Delete` metody akce, která spravuje zpráv o chybách následujícím kódem:

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample12.cs?highlight=1,7-10)]

    Tento kód přijímá [volitelný parametr](https://msdn.microsoft.com/library/dd264739.aspx) , která označuje, zda byla volána metoda po selhání, uložte změny. Tento parametr je `false` při `HttpGet` `Delete` metoda je volána bez předchozí chyby. Pokud je volán `HttpPost` `Delete` je parametr metody v reakci na chybě aktualizace databáze `true` a chybová zpráva je předána do zobrazení.

2. Nahradit <xref:System.Web.Mvc.HttpPostAttribute> `Delete` metody akce (s názvem `DeleteConfirmed`) následujícím kódem, který provádí operace odstranění skutečné a zachytí všechny chyby aktualizace databáze.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample13.cs)]

    Tento kód načte vybranou entitu, zavolá [odebrat](https://msdn.microsoft.com/library/system.data.entity.dbset.remove(v=vs.103).aspx) metodu pro nastavení stavu entity `Deleted`. Když `SaveChanges` nazývá SQL `DELETE` příkaz je generován. Také jste změnili název metody akce z `DeleteConfirmed` k `Delete`. Automaticky generovaný kód s názvem `HttpPost` `Delete` metoda `DeleteConfirmed` poskytnout `HttpPost` metoda jedinečnou signaturu. (CLR vyžaduje přetížené metody s parametry jinou metodu.) Teď, když podpisy jsou jedinečné, můžete zůstat u konvence MVC a použijte stejný název pro `HttpPost` a `HttpGet` metody pro odstranění.

    Pokud je vylepšení výkonu v aplikaci s velkým objemem prioritu, se můžete vyhnout zbytečným jazyka SQL k načtení na řádku tak, že nahradíte řádků kódu, které volají `Find` a `Remove` metody s následujícím kódem:

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample14.cs)]

    Tento kód vytvoří instanci `Student` entity pomocí pouze hodnotu primárního klíče a pak nastaví stav entity `Deleted`. To je vše, Entity Framework potřebuje, aby odstranit entitu.

    Jak je uvedeno, `HttpGet` `Delete` metoda neodstraní data. Operace delete v reakci na příkaz GET žádosti (nebo pro tento účel, provedení libovolné operace úprav vytvořte operace nebo jiné operace, která se mění data) vytvoří ohrožení zabezpečení. Další informace najdete v tématu [46 Tip # aplikace ASP.NET MVC – nepoužívejte odstranit odkazy, protože uživatel vytvořit bezpečnostní díry](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx) na blogu Stephen Walther.

3. V *Views\Student\Delete.cshtml*, přidejte mezi chybová zpráva `h2` záhlaví a `h3` záhlaví, jak je znázorněno v následujícím příkladu:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample15.cshtml?highlight=2)]

4. Spuštěním programu spustit na stránce Výběr **studenty** kartu a pak levým na **odstranit** hypertextový odkaz.

5. Zvolte **odstranit** na stránce, která uvádí, že **Opravdu chcete odstranit tento?**.

    Indexovou stránku zobrazuje informace bez odstraněné studentů. (Zobrazí se vám příklad kód v akci pro zpracování chyb [souběžnosti kurzu](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md).)

## <a name="close-database-connections"></a>Připojení k databázi zavřít

Zavřete připojení k databázi a uvolnit tak prostředky, které drží co nejdříve, uvolnění instance kontextu, až budete hotovi s ním. To znamená, proč obsahuje automaticky generovaný kód [Dispose](https://msdn.microsoft.com/library/system.idisposable.dispose(v=vs.110).aspx) metoda na konci `StudentController` třídy v *StudentController.cs*, jak je znázorněno v následujícím příkladu:

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample16.cs)]

Základní `Controller` implementuje třída již `IDisposable` rozhraní, takže tento kód jednoduše přidá přepsání `Dispose(bool)` metoda explicitně uvolnit instance kontextu.

## <a name="handle-transactions"></a>Zpracování transakcí

Ve výchozím nastavení rozhraní Entity Framework implementuje implicitně transakce. V situacích, kdy provést změny na více řádcích nebo tabulky a poté zavolejte `SaveChanges`, Entity Framework automaticky zajišťuje, že všechny změny úspěšné nebo se všechny nezdaří. Pokud některé změny nejprve dokončení a potom se stane chyba, tyto změny se automaticky vrátí zpět. Pro scénáře, kde je nutné mít lepší kontrolu&mdash;například, pokud chcete zahrnout operací provést v transakci mimo rozhraní Entity Framework&mdash;naleznete v tématu [práce s transakcí](/ef/ef6/saving/transactions).

## <a name="additional-resources"></a>Další zdroje

Teď máte úplnou sadu stránek, které provádějí jednoduché operace CRUD pro `Student` entity. Pomocné rutiny MVC jste použili k vygenerování prvky uživatelského rozhraní pro datová pole. Další informace o pomocné rutiny MVC najdete v tématu [vykreslení formuláři pomocí pomocných rutin HTML](/previous-versions/aspnet/dd410596(v=vs.98)) (Tento článek je pro MVC 3, ale je stále relevantní pro MVC 5).

Odkazy na další zdroje EF 6 lze nalézt v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření stránky podrobností
> * Aktualizovat na stránce vytvořit
> * Aktualizovat metoda HttpPost úprav
> * Aktualizovat stránku Delete
> * Připojení uzavřeno databáze
> * Zpracování transakcí

Přejděte k dalším článku se naučíte, jak přidat řazení, filtrování a stránkování do projektu.
> [!div class="nextstepaction"]
> [Řazení, filtrování a stránkování](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)
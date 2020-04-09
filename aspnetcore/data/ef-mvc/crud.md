---
title: 'Kurz: Implementace funkcí CRUD - ASP.NET MVC s EF Core'
description: V tomto kurzu zkontrolujete a přizpůsobíte kód CRUD (vytvoření, čtení, aktualizace, odstranění), který pro vás automaticky vytvoří uživatelské a uživatelské zařízení MVC v řadičích a zobrazeních.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/crud
ms.openlocfilehash: 2aa4ef48509b9a34f3b25eb657b1ecac51c1374b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416210"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Kurz: Implementace funkcí CRUD - ASP.NET MVC s EF Core

V předchozím kurzu jste vytvořili aplikaci MVC, která ukládá a zobrazuje data pomocí entity Framework a SQL Server LocalDB. V tomto kurzu zkontrolujete a přizpůsobíte kód CRUD (vytvoření, čtení, aktualizace, odstranění), který pro vás automaticky vytvoří uživatelské a uživatelské zařízení MVC v řadičích a zobrazeních.

> [!NOTE]
> Je běžnou praxí implementovat vzor úložiště za účelem vytvoření vrstvy abstrakce mezi řadičem a vrstvou přístupu k datům. Chcete-li zachovat tyto kurzy jednoduché a zaměřené na výuku, jak používat entity framework sám, nepoužívají úložiště. Informace o úložištích s EF naleznete [v posledním kurzu v této sérii](advanced.md).

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobení stránky Podrobnosti
> * Aktualizace stránky Vytvořit
> * Aktualizace stránky Úpravy
> * Aktualizace stránky Odstranit
> * Zavření připojení k databázi

## <a name="prerequisites"></a>Požadavky

* [Začínáme s EF Core a ASP.NET Core MVC](intro.md)

## <a name="customize-the-details-page"></a>Přizpůsobení stránky Podrobnosti

Kód scaffolded pro studenty index `Enrollments` stránky vynechal vlastnost, protože tato vlastnost obsahuje kolekci. Na stránce **Podrobnosti** zobrazíte obsah kolekce v tabulce HTML.

V *Controllers/StudentsController.cs*metoda akce pro zobrazení `SingleOrDefaultAsync` Podrobnosti používá `Student` metodu k načtení jedné entity. Přidejte kód, který volá `Include`. `ThenInclude`a `AsNoTracking` metody, jak je znázorněno v následujícím zvýrazněném kódu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

Metody `Include` `ThenInclude` a způsobit kontext načíst `Student.Enrollments` navigační vlastnost a `Enrollment.Course` v rámci každé registrace navigační vlastnost.  Další informace o těchto metodách najdete v kurzu [souvisejících dat pro čtení.](read-related-data.md)

Metoda `AsNoTracking` zlepšuje výkon ve scénářích, kde vrácené entity nebudou aktualizovány v aktuálním kontextu životnosti. Další informace se `AsNoTracking` dozvíte na konci tohoto kurzu.

### <a name="route-data"></a>Směrování dat

Hodnota klíče, která je `Details` předána metodě, pochází z *dat trasy*. Data trasy jsou data, která pořadač modelu našel v segmentu adresy URL. Například výchozí trasa určuje segmenty kontroléru, akce a id:

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

V následující adrese URL výchozí trasa mapuje instruktora jako řadič, index jako akci a 1 jako id; jedná se o hodnoty dat trasy.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

Poslední část adresy URL ("?courseID=2021") je hodnota řetězce dotazu. Pořadač modelu také předá hodnotu `Index` `id` ID parametru metody, pokud ji předáte jako hodnotu řetězce dotazu:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

Na stránce Rejstřík jsou adresy URL hypertextových odkazů vytvářeny příkazy pomocníka značek v zobrazení Holicí strojek. V následujícím kódu Razor `id` parametr odpovídá výchozí `id` postupu, takže je přidán do dat trasy.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Tím se vygeneruje `item.ID` následující html, pokud je 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

V následujícím kódu `studentID` Razor neodpovídá parametru ve výchozí trase, takže se přidá jako řetězec dotazu.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Tím se vygeneruje `item.ID` následující html, pokud je 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Další informace o pomocní <xref:mvc/views/tag-helpers/intro>spoje značek naleznete v tématu .

### <a name="add-enrollments-to-the-details-view"></a>Přidání registrací do zobrazení Podrobnosti

Otevřená *zobrazení/Studenti/Details.cshtml*. Každé pole je `DisplayNameFor` zobrazeno pomocí pomocných `DisplayFor` položek, jak je znázorněno v následujícím příkladu:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Za poslední pole a bezprostředně `</dl>` před uzavírací značku přidejte následující kód, který zobrazí seznam registrací:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Pokud je odsazení kódu po vložení kódu nesprávné, opravte jej stisknutím kláves CTRL-K-D.

Tento kód prochází entity v `Enrollments` navigační vlastnosti. Pro každou registraci se zobrazí název kurzu a hodnocení. Název kurzu se načte z entity Kurz, `Course` která je uložena ve vlastnosti navigace entity Registrace.

Spusťte aplikaci, vyberte kartu **Studenti** a klikněte na odkaz **Podrobnosti** pro studenta. Zobrazí se seznam kurzů a hodnocení pro vybraného studenta:

![Stránka Podrobnosti o studentovi](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Aktualizace stránky Vytvořit

V *StudentsController.cs*upravte `Create` metodu HttpPost přidáním bloku try-catch `Bind` a odebráním ID z atributu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Tento kód přidá entitu Student vytvořenou ASP.NET pořadače modelu Core MVC do sady entit Studenty a pak uloží změny do databáze. (Pořadač modelu odkazuje na ASP.NET základní funkce MVC, která usnadňuje práci s daty odeslanými formulářem; pořadač modelu převede zaúčtované hodnoty formuláře na typy CLR a předá je metodě akce v parametrech. V tomto případě vazavač modelu inkonzismuje entitu Student pomocí hodnot vlastností z kolekce Form.)

Z `ID` atributu `Bind` jste odebrali, protože ID je hodnota primárního klíče, kterou sql server automaticky nastaví při vložení řádku. Vstup od uživatele nenastaví hodnotu ID.

Kromě atributu `Bind` je blok try-catch jedinou změnou, kterou jste provedli v kódu sklopné kód. Pokud je při ukládání `DbUpdateException` změn zachycena výjimka, která je odvozena od, zobrazí se obecná chybová zpráva. `DbUpdateException`výjimky jsou někdy způsobeny něčím externím pro aplikaci spíše než chybou programování, takže uživateli doporučujeme to zkusit znovu. I když není implementována v této ukázce, aplikace kvality výroby by protokolovat výjimku. Další informace najdete v části **Protokol pro přehled** v monitorování a [telemetrie (vytváření real-world cloud apps s Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).

Atribut `ValidateAntiForgeryToken` pomáhá zabránit útokům padělání požadavků na příčce webu (CSRF). Token je automaticky vložen do zobrazení [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) a je zahrnuta při odeslání formuláře uživatelem. Token je ověřen atributem. `ValidateAntiForgeryToken` Další informace o CSRF naleznete [v tématu Anti-Request Pagery](../../security/anti-request-forgery.md).

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Bezpečnostní poznámka o overpostování

Atribut, `Bind` který kód scaffolded `Create` obsahuje na metodu je jedním ze způsobů, jak chránit před overpostování ve scénářích vytvoření. Předpokládejme například, že `Secret` entita Student obsahuje vlastnost, kterou nechcete, aby tato webová stránka nastavila.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

I když na webové `Secret` stránce nemáte pole, hacker může k zaúčtování hodnoty `Secret` formuláře použít nástroj, jako je Fiddler, nebo napsat nějaký JavaScript. Bez `Bind` atributu omezujícího pole, která pořadač modelu používá při vytváření instance `Secret` Student, by pořadač modelu tuto hodnotu formuláře zachytil a použil k vytvoření instance entity Student. Pak bez ohledu na `Secret` hodnotu hacker zadaný pro pole formuláře by být aktualizovány ve vaší databázi. Následující obrázek znázorňuje nástroj `Secret` Šumař, který přidává pole (s hodnotou "OverPost") k zaúčtovanéhodnotě formuláře.

![Fiddler přidání tajné pole](crud/_static/fiddler.png)

Hodnota "OverPost" by pak úspěšně přidána do `Secret` vlastnosti vloženého řádku, i když jste nikdy nezamýšleli, aby webová stránka mohla tuto vlastnost nastavit.

Overpostování v editačních scénářích můžete zabránit tak, `TryUpdateModel`že nejprve načtete entitu z databáze a poté zavoláte a předáte seznam explicitních povolených vlastností. To je metoda použitá v těchto kurzech.

Alternativní způsob, jak zabránit overpostování, které je preferováno mnoho vývojářů je použití modelů zobrazení spíše než entity třídy s vazbou modelu. Do modelu pohledu zahrňte pouze vlastnosti, které chcete aktualizovat. Po dokončení pořadače modelu MVC zkopírujte vlastnosti modelu zobrazení do instance entity, volitelně pomocí nástroje, jako je AutoMapper. Použijte `_context.Entry` instanci entity k `Unchanged`nastavení jejího `Property("PropertyName").IsModified` stavu na a potom nastavte hodnotu true u každé vlastnosti entity, která je zahrnuta v modelu pohledu. Tato metoda funguje ve scénářích úprav i vytváření.

### <a name="test-the-create-page"></a>Testování stránky Vytvořit

Kód v *zobrazení /Studenti /Create.cshtml* používá `label`, `input`a `span` (pro ověřovací zprávy) značky pomocné položky pro každé pole.

Spusťte aplikaci, vyberte kartu **Studenti** a klikněte na **Vytvořit nový**.

Zadejte jména a datum. Pokud vám to prohlížeč dovolí, zkuste zadat neplatné datum. (Některé prohlížeče vás nutí používat výběr data.) Potom kliknutím na **Vytvořit** zoašičit chybovou zprávu.

![Chyba ověření data](crud/_static/date-error.png)

Toto je ověření na straně serveru, které získáte ve výchozím nastavení; v pozdějším kurzu uvidíte, jak přidat atributy, které budou generovat kód pro ověření na straně klienta také. Následující zvýrazněný kód ukazuje kontrolu ověření `Create` modelu v metodě.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Změňte datum na platnou hodnotu a kliknutím na **Vytvořit** zobrazíte nového studenta na stránce **Rejstřík.**

## <a name="update-the-edit-page"></a>Aktualizace stránky Úpravy

V *StudentController.cs*metoda `Edit` HttpGet (metoda `HttpPost` bez atributu) používá metodu `SingleOrDefaultAsync` k načtení vybrané `Details` entity Student, jak jste viděli v metodě. Tuto metodu není nutné měnit.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Doporučený kód pro úpravu HttpPost: Čtení a aktualizace

Nahraďte metodu akce Úpravy HttpPost následujícím kódem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Tyto změny implementují osvědčený postup zabezpečení, aby se zabránilo nadměrnému zaúčtování. Složka generování vytvořeného pomocí `Bind` generování vytvořeného pomocí vazby modelu vygenerovala atribut a přidala entitu vytvořenou pořadačem modelu do sady entit s příznakem. `Modified` Tento kód se nedoporučuje pro mnoho `Bind` scénářů, protože atribut vymaže `Include` všechna již existující data v polích, která nejsou uvedena v parametru.

Nový kód přečte existující `TryUpdateModel` entitu a zavolá aktualizace polí v načtené entitě [na základě vstupu uživatele v zaúčtovaných datech formuláře](xref:mvc/models/model-binding). Automatické sledování změn rozhraní entity `Modified` nastaví příznak na pole, která jsou změněna vstupem formuláře. Při `SaveChanges` volání metody entity Framework vytvoří příkazy SQL aktualizovat řádek databáze. Konflikty souběžnosti jsou ignorovány a v databázi jsou aktualizovány pouze sloupce tabulky, které byly aktualizovány uživatelem. (Pozdější kurz ukazuje, jak zpracovat konflikty souběžnosti.)

Jako osvědčený postup, jak zabránit přeúčtování, jsou pole, která chcete aktualizovat pomocí `TryUpdateModel` stránky **Úpravy,** uvedena na seznamu povolených parametrů. (Prázdný řetězec předcházející seznamu polí v seznamu parametrů je pro předponu, která má být použita s názvy polí formuláře.) V současné době neexistují žádná další pole, která chráníte, ale výpis polí, která má pořadač modelu vázat, zajistí, že pokud pole v budoucnu přidáte do datového modelu, budou automaticky chráněna, dokud je sem explicitně nepřidáte.

V důsledku těchto změn je podpis metody `Edit` metody HttpPost stejný jako `Edit` metoda HttpGet; proto jste přejmenovali `EditPost`metodu .

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Alternativní httppost editační kód: vytvořit a připojit

Doporučený editační kód HttpPost zajišťuje, že se aktualizují pouze změněné sloupce a zachová data ve vlastnostech, které nechcete zahrnout do vazby modelu. Přístup pro čtení vyžaduje další čtení databáze a může mít za následek složitější kód pro zpracování konfliktů souběžnosti. Alternativou je připojit entitu vytvořenou pořadač modelu do kontextu EF a označit ji jako změněnou. (Neaktualizujte projekt pomocí tohoto kódu, zobrazí se pouze pro ilustraci volitelného přístupu.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Tento přístup můžete použít, pokud uživatelské tlačítko webové stránky obsahuje všechna pole v entitě a můžete je aktualizovat.

Kód skládaný šálecí zásadou používá přístup `DbUpdateConcurrencyException` create-and-attach, ale pouze zachytí výjimky a vrátí kódy chyb 404.  Zobrazený příklad zachytí výjimku aktualizace databáze a zobrazí chybovou zprávu.

### <a name="entity-states"></a>Stavy entity

Kontext databáze sleduje, zda entity v paměti jsou synchronizovány s jejich odpovídající řádky v databázi `SaveChanges` a tyto informace určuje, co se stane při volání metody. Například při předání nové entity `Add` metodě je stav této entity `Added`nastaven na . Potom při volání `SaveChanges` metody, kontext databáze vydá příkaz SQL INSERT.

Účetní jednotka může být v jednom z následujících stavů:

* `Added`. Entita ještě neexistuje v databázi. Metoda `SaveChanges` vydá příkaz INSERT.

* `Unchanged`. `SaveChanges` S touto entitou není třeba nic dělat metodou. Při čtení entity z databáze, entita začíná s tímto stavem.

* `Modified`. Některé nebo všechny hodnoty vlastností entity byly změněny. Metoda `SaveChanges` vydává příkaz UPDATE.

* `Deleted`. Entita byla označena k odstranění. Metoda `SaveChanges` vydává příkaz DELETE.

* `Detached`. Entita není sledována kontextem databáze.

V desktopové aplikaci jsou změny stavu obvykle nastaveny automaticky. Můžete číst entity a provádět změny některé její hodnoty vlastností. To způsobí, že stav entity `Modified`automaticky změní na . Potom při `SaveChanges`volání , entity Framework generuje příkaz SQL UPDATE, který aktualizuje pouze skutečné vlastnosti, které jste změnili.

Ve webové aplikaci, `DbContext` která zpočátku čte entitu a zobrazuje její data, která mají být upravena, je uvolněna po vykreslení stránky. Při volání `Edit` metody akce HttpPost je podán nový webový požadavek a `DbContext`máte novou instanci . Pokud znovu načtete entitu v tomto novém kontextu, simulujete zpracování plochy.

Ale pokud nechcete provést další operaci čtení, budete muset použít objekt entity vytvořené vazací vzor.  Nejjednodušší způsob, jak to udělat, je nastavit stav entity na Změněno, jak je tomu v alternativním httppost editační kód zobrazen dříve. Potom při `SaveChanges`volání , entity Framework aktualizuje všechny sloupce řádku databáze, protože kontext nemá žádný způsob, jak zjistit, které vlastnosti jste změnili.

Pokud se chcete vyhnout přístupu pro čtení první, ale také chcete, aby příkaz SQL UPDATE aktualizovat pouze pole, která uživatel skutečně změnil, kód je složitější. Původní hodnoty je třeba uložit nějakým způsobem (například pomocí skrytých polí), aby `Edit` byly k dispozici při volání metody HttpPost. Pak můžete vytvořit studentovu entitu pomocí `Attach` původních hodnot, volat metodu s původní verzí entity, aktualizovat `SaveChanges`hodnoty entity na nové hodnoty a pak volat .

### <a name="test-the-edit-page"></a>Testování stránky Úpravy

Spusťte aplikaci, vyberte kartu **Studenti** a klikněte na hypertextový odkaz **Upravit.**

![Studenti upravit stránku](crud/_static/student-edit.png)

Změňte některá data a klepněte na tlačítko **Uložit**. Otevře se stránka **Rejstřík** a zobrazí se změněná data.

## <a name="update-the-delete-page"></a>Aktualizace stránky Odstranit

V *StudentController.cs*použije kód šablony `Delete` metody HttpGet metodu `SingleOrDefaultAsync` k načtení vybrané entity Student, jak jste viděli v metodách Podrobnosti a Úpravy. Chcete-li však implementovat vlastní `SaveChanges` chybovou zprávu při volání nezdaří, přidáte některé funkce této metody a jeho odpovídající zobrazení.

Jak jste viděli pro aktualizaci a vytváření operací, operace odstranění vyžadují dvě metody akce. Metoda, která je volána v reakci na požadavek GET zobrazí zobrazení, které dává uživateli možnost schválit nebo zrušit operaci odstranění. Pokud jej uživatel schválí, vytvoří se požadavek POST. Když se to stane, je volána metoda HttpPost `Delete` a pak tato metoda skutečně provede operaci odstranění.

Přidáte try-catch blok httppost `Delete` metoda pro zpracování všech chyb, které mohou nastat při aktualizaci databáze. Pokud dojde k chybě, metoda HttpPost Delete zavolá metodu HttpGet Delete a předá jí parametr, který označuje, že došlo k chybě. Metoda HttpGet Delete pak znovu zobrazí stránku s potvrzením spolu s chybovou zprávou, která dává uživateli možnost zrušit nebo zkusit znovu.

Nahraďte `Delete` metodu akce HttpGet následujícím kódem, který spravuje zasílání zpráv o chybách.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Tento kód přijímá volitelný parametr, který označuje, zda byla metoda volána po selhání uložení změn. Tento parametr je false `Delete` při HttpGet metoda je volána bez předchozí selhání. Když je volána metodou `Delete` HttpPost v reakci na chybu aktualizace databáze, parametr je true a chybová zpráva je předána do zobrazení.

### <a name="the-read-first-approach-to-httppost-delete"></a>Přístup httppost první čtení

Nahraďte `Delete` metodu akce `DeleteConfirmed`HttpPost (s názvem) následujícím kódem, který provádí skutečnou operaci odstranění a zachycuje všechny chyby aktualizace databáze.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

Tento kód načte vybranou entitu a pak zavolá `Remove` `Deleted`metodu pro nastavení stavu entity na . Při `SaveChanges` volání je generován příkaz SQL DELETE.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>Přístup k vytvoření a připojení k odstranění HttpPost

Pokud je prioritou zlepšení výkonu v aplikaci s velkým objemem, můžete se vyhnout zbytečnému dotazu SQL tím, že `Deleted`získáte entitu Student pomocí pouze hodnoty primárního klíče a potom nastavíte stav entity na . To je vše, co entity Framework potřebuje k odstranění entity. (Nevkládáme tento kód do projektu; je zde jen pro ilustraci alternativy.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Pokud entita obsahuje související data, která by měla být také odstraněna, ujistěte se, že kaskádové odstranění je nakonfigurováno v databázi. S tímto přístupem k odstranění entity EF nemusí uvědomit, že jsou související entity, které mají být odstraněny.

### <a name="update-the-delete-view"></a>Aktualizace zobrazení Odstranit

V *části Zobrazení/Student/Delete.cshtml*přidejte mezi nadpis h2 a nadpis h3 chybovou zprávu, jak je znázorněno v následujícím příkladu:

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Spusťte aplikaci, vyberte kartu **Studenti** a klikněte na hypertextový odkaz **Odstranit:**

![Odstranit stránku potvrzení](crud/_static/student-delete.png)

Klepněte na **tlačítko Odstranit**. Stránka Rejstřík se zobrazí bez odstraněného studenta. (Uvidíte příklad kódu zpracování chyb v akci v kurzu souběžnosti.)

## <a name="close-database-connections"></a>Zavření připojení k databázi

Chcete-li uvolnit prostředky, které obsahuje připojení k databázi, musí být instance kontextu uvolněna co nejdříve, když jste s ní hotovi. Integrované [vkládání závislostí](../../fundamentals/dependency-injection.md) ASP.NET Core se o tento úkol postará za vás.

V *Startup.cs*zavoláte [metodu rozšíření AddDbContext](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) pro zřízení `DbContext` třídy v kontejneru ASP.NET Core DI. Tato metoda nastaví `Scoped` životnost služby ve výchozím nastavení. `Scoped`znamená, že životnost objektu kontextu se shoduje `Dispose` s životností webové žádosti a metoda bude volána automaticky na konci webového požadavku.

## <a name="handle-transactions"></a>Zpracování transakcí

Ve výchozím nastavení entity Framework implicitně implementuje transakce. Ve scénářích, kde provedete změny více `SaveChanges`řádků nebo tabulek a pak zavoláte , entity framework automaticky zajistí, že všechny změny úspěšné nebo všechny nezdaří. Pokud některé změny jsou provedeny nejprve a pak dojde k chybě, tyto změny jsou automaticky vráceny zpět. Pro scénáře, kde potřebujete větší kontrolu – například pokud chcete zahrnout operace provedené mimo entity frameworku v transakci – naleznete v [tématu Transakce](/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Dotazy bez sledování

Když kontext databáze načte řádky tabulky a vytvoří objekty entit, které je představují, ve výchozím nastavení sleduje, zda jsou entity v paměti synchronizovány s tím, co je v databázi. Data v paměti funguje jako mezipaměť a používá se při aktualizaci entity. Toto ukládání do mezipaměti je často zbytečné ve webové aplikaci, protože instance kontextu jsou obvykle krátkodobé (nový je vytvořen a vyřazen pro každý požadavek) a kontext, který čte entitu je obvykle vyřazen před tuto entitu je znovu použit.

Sledování objektů entity v paměti můžete `AsNoTracking` zakázat voláním metody. Typické scénáře, ve kterých můžete chtít provést, které zahrnují následující:

* Během životnosti kontextu není nutné aktualizovat žádné entity a nepotřebujete EF automaticky [načíst navigační vlastnosti s entitami načtenými samostatnými dotazy](read-related-data.md). Často tyto podmínky jsou splněny v řadiče HttpGet metody akce.

* Spouštějí se dotaz, který načítá velký objem dat a bude aktualizována pouze malá část vrácených dat. Může být efektivnější vypnout sledování pro velký dotaz a spustit dotaz později pro několik entit, které je třeba aktualizovat.

* Chcete připojit entitu, abyste ji mohli aktualizovat, ale dříve jste stejnou entitu načetli pro jiný účel. Vzhledem k tomu, že entita je již sledována kontextem databáze, nelze připojit entitu, kterou chcete změnit. Jedním ze způsobů, jak `AsNoTracking` tuto situaci zpracovat, je volání na předchozí dotaz.

Další informace naleznete [v tématu Sledování vs. No-Tracking](/ef/core/querying/tracking).

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobená stránka Podrobnosti
> * Byla aktualizována stránka Vytvořit
> * Stránka Úpravy byla aktualizována
> * Stránka Odstranit byla aktualizována
> * Uzavřená připojení k databázi

Přejdete k dalšímu kurzu, kde se dozvíte, jak rozšířit funkce stránky **Index** přidáním řazení, filtrování a stránkování.

> [!div class="nextstepaction"]
> [Další: Řazení, filtrování a stránkování](sort-filter-page.md)

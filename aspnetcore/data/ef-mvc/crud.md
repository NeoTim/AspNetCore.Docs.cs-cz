---
title: 'Kurz: implementace funkce CRUD – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu zkontrolujete a upravíte kód CRUD (vytvořit, číst, aktualizovat, odstranit), který pro vás automaticky vytvoří generování uživatelského rozhraní MVC v řadičích a zobrazeních.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/crud
ms.openlocfilehash: e7e4c4b15cca9612a552c58029ae8b34b79070d1
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060121"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Kurz: implementace funkce CRUD – ASP.NET MVC pomocí EF Core

V předchozím kurzu jste vytvořili aplikaci MVC, která ukládá a zobrazuje data pomocí Entity Framework a SQL Server LocalDB. V tomto kurzu zkontrolujete a upravíte kód CRUD (vytvořit, číst, aktualizovat, odstranit), který pro vás automaticky vytvoří generování uživatelského rozhraní MVC v řadičích a zobrazeních.

> [!NOTE]
> Je běžný postup implementace vzoru úložiště, aby bylo možné vytvořit vrstvu abstrakce mezi řadičem a vrstvou přístupu k datům. Aby byly tyto kurzy jednoduché a zaměřené na výuku, jak Entity Framework sebe sama, nepoužívají úložiště. Informace o úložištích s EF najdete v [posledním kurzu této série](advanced.md).

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobení stránky s podrobnostmi
> * Aktualizace stránky pro vytvoření
> * Aktualizace stránky pro úpravy
> * Aktualizace stránky pro odstranění
> * Zavřít databázová připojení

## <a name="prerequisites"></a>Požadavky

* [Začínáme s EF Core a ASP.NET Core MVC](intro.md)

## <a name="customize-the-details-page"></a>Přizpůsobení stránky s podrobnostmi

Vygenerovaný kód stránky indexu studentů opustil `Enrollments` vlastnost, protože tato vlastnost obsahuje kolekci. Na stránce **Podrobnosti** zobrazíte obsah kolekce v tabulce HTML.

V části *Controllers/StudentsController. cs*metoda Action pro zobrazení podrobností používá `SingleOrDefaultAsync` metodu k načtení jedné `Student` entity. Přidejte kód, který volá `Include` . `ThenInclude`a `AsNoTracking` metody, jak je znázorněno v následujícím zvýrazněném kódu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

`Include`Metody a `ThenInclude` způsobují, že kontext načte `Student.Enrollments` vlastnost navigace a v rámci každé registrace `Enrollment.Course` vlastnost navigace.  Další informace o těchto metodách najdete v kurzu [související data pro čtení](read-related-data.md) .

`AsNoTracking`Metoda zvyšuje výkon ve scénářích, kdy vrácené entity nebudou aktualizovány v životním cyklu aktuálního kontextu. `AsNoTracking`Na konci tohoto kurzu se dozvíte víc.

### <a name="route-data"></a>Směrování dat

Hodnota klíče, která je předána `Details` metodě, přichází z *dat trasy*. Data směrování jsou data, která modelový pořadač nalezl v segmentu adresy URL. Například výchozí trasa určuje segmenty kontroleru, akce a ID:

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

V následující adrese URL výchozí trasa mapuje jako kontroler, index jako akci a 1 jako ID; Jedná se o hodnoty dat tras.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

Poslední část adresy URL ("? courseID = 2021") je hodnota řetězce dotazu. Pořadač modelů také předává hodnotu ID `Index` parametru metody, `id` Pokud jej předáte jako hodnotu řetězce dotazu:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

Na stránce index jsou adresy URL hypertextového odkazu vytvořeny pomocí příkazů pomocníka značek v Razor zobrazení. V následujícím Razor kódu je `id` parametr shodný s výchozí trasou, takže `id` se přidá do dat trasy.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Tato akce generuje následující kód HTML, pokud `item.ID` je 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

V následujícím Razor kódu se `studentID` neshoduje s parametrem ve výchozí trase, takže se přidá jako řetězec dotazu.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Tato akce generuje následující kód HTML, pokud `item.ID` je 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Další informace o pomocníkech značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .

### <a name="add-enrollments-to-the-details-view"></a>Přidat registrace do zobrazení podrobností

Otevřete *zobrazení/studenty/podrobnosti. cshtml*. Každé pole se zobrazí pomocí `DisplayNameFor` a `DisplayFor` pomocníků, jak je znázorněno v následujícím příkladu:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Po posledním poli a bezprostředně před uzavírací `</dl>` značkou přidejte následující kód, který zobrazí seznam zápisů:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Pokud po vložení kódu není odsazení kódu správné, opravte jej stisknutím kombinace kláves CTRL + K-D.

Tento kód projde entitami v `Enrollments` navigační vlastnosti. U každého zápisu se zobrazí titul kurzu a stupeň. Název kurzu se načte z entity kurzu, která je uložená v `Course` navigační vlastnosti entity registrace.

Spusťte aplikaci, vyberte kartu **Students** a klikněte na odkaz **Podrobnosti** pro studenta. Zobrazí se seznam kurzů a stupňů pro vybraného studenta:

![Stránka s podrobnostmi studenta](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Aktualizace stránky pro vytvoření

V *StudentsController.cs*upravte `Create` metodu HTTPPOST přidáním bloku try-catch a odebráním ID z `Bind` atributu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Tento kód přidá entitu Student vytvořenou modelem ASP.NET Core MVC modelu do sady entit Students a následně uloží změny do databáze. (Modelový pořadač odkazuje na funkci ASP.NET Core MVC, která usnadňuje práci s daty odesílanými formulářem. modelový pořadač převádí zaúčtované hodnoty formuláře na typy CLR a předá je metodě akcí v parametrech. V tomto případě vytvoří pořadač modelů entitu studenta za použití hodnot vlastností z kolekce formulářů.)

Odebrali jste `ID` z `Bind` atributu, protože ID je hodnota primárního klíče, kterou SQL Server automaticky nastaví při vložení řádku. Vstup od uživatele nenastavuje hodnotu ID.

Kromě `Bind` atributu je blok try-catch jedinou změnou, kterou jste provedli v kódu generování. Pokud `DbUpdateException` je při ukládání změn zachycena výjimka, která je odvozena, zobrazí se obecná chybová zpráva. `DbUpdateException`výjimky jsou někdy způsobeny něčím externě pro aplikaci, nikoli při programování, takže se uživateli doporučuje akci opakovat. I když v této ukázce není implementované, aplikace produkční kvality by tuto výjimku zaznamenala. Další informace najdete v části **Log for Insight** v tématu [monitorování a telemetrie (vytváření skutečných cloudových aplikací s Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).

`ValidateAntiForgeryToken`Atribut pomáhá zabránit útokům na CSRF (mezi lokalitami). Token je automaticky vložen do zobrazení podle [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) a je obsažena v případě, že formulář odešle uživatel. Token je ověřen `ValidateAntiForgeryToken` atributem. Další informace o CSRF najdete v tématu [Ochrana proti padělání požadavků](../../security/anti-request-forgery.md).

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Bezpečnostní Poznámka týkající se přestavování

`Bind`Atribut, který obsahuje generovaný kód v `Create` metodě, je jedním ze způsobů, jak chránit před přeúčtováním ve scénářích vytváření. Předpokládejme například, že entita student obsahuje `Secret` vlastnost, kterou nechcete, aby tato webová stránka nastavila.

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

I v případě, že nemáte `Secret` pole na webové stránce, hacker by mohl k odeslání hodnoty formuláře použít nějaký nástroj, například Fiddler, nebo napsaný kód JavaScript `Secret` . Bez `Bind` atributu, který omezuje pole, která používá pořadač modelů při vytváření instance studenta, vytvoří pořadač modelů tuto `Secret` hodnotu formuláře a použije ji k vytvoření instance entity studenta. V databázi se pak aktualizuje bez ohledu na hodnotu, kterou zadal počítačový podvodník pro `Secret` pole formuláře. Následující obrázek znázorňuje nástroj Fiddler, který přidá `Secret` pole (s hodnotou "Repost") do publikovaných hodnot formuláře.

![Fiddler přidání tajného pole](crud/_static/fiddler.png)

Hodnota "" přeúčtování "by se pak úspěšně přidala do `Secret` Vlastnosti vloženého řádku, i když jste nikdy nezamýšleli, že webová stránka bude moct tuto vlastnost nastavit.

Je možné zabránit v úpravách v situacích, kdy nejprve přečtete entitu z databáze a potom zavoláte `TryUpdateModel` a předáte seznam explicitně povolených vlastností. To je metoda použitá v těchto kurzech.

Alternativní způsob, jak zabránit předávání, které upřednostňuje mnoho vývojářů, je použít modely zobrazení spíše než třídy entit s vazbou modelu. Do modelu zobrazení zahrňte pouze vlastnosti, které chcete aktualizovat. Po dokončení pořadače modelu MVC zkopírujte vlastnosti zobrazení modelu do instance entity, volitelně pomocí nástroje, jako je například automapper. Použijte `_context.Entry` v instanci entity k nastavení jeho stavu na `Unchanged` a pak nastavte `Property("PropertyName").IsModified` na hodnotu true u každé vlastnosti entity, která je obsažena v modelu zobrazení. Tato metoda funguje ve scénářích úpravy a vytváření.

### <a name="test-the-create-page"></a>Testování stránky pro vytvoření

Kód v *zobrazeních/Students/Create. cshtml* používá `label` `input` `span` pro každé pole nápovědu značek, a (pro zprávy ověření).

Spusťte aplikaci, vyberte kartu **Students** a klikněte na **vytvořit novou**.

Zadejte jména a datum. Pokud to váš prohlížeč umožňuje, zkuste zadat neplatné datum. (Některé prohlížeče vynutí použití ovládacího prvku pro výběr data.) Pak kliknutím na **vytvořit** zobrazíte chybovou zprávu.

![Chyba ověření data](crud/_static/date-error.png)

Toto je ověřování na straně serveru, které se ve výchozím nastavení zobrazí. v pozdějším kurzu se dozvíte, jak přidat atributy, které budou generovat kód pro ověřování na straně klienta také. Následující zvýrazněný kód ukazuje kontrolu ověřování modelu v `Create` metodě.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Změňte datum na platnou hodnotu a kliknutím na tlačítko **vytvořit** zobrazíte nového studenta na stránce **Rejstřík** .

## <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravy

V *StudentController.cs* `Edit` Metoda HttpGet (ta bez `HttpPost` atributu) používá `SingleOrDefaultAsync` metodu pro načtení vybrané entity studenta, jak jste viděli v `Details` metodě. Tuto metodu nemusíte měnit.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Doporučené upravit kód HttpPost: číst a aktualizovat

Nahraďte metodu HttpPost Edit Action následujícím kódem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Tyto změny implementují osvědčené postupy zabezpečení, které zabrání přestavení. Generování uživatelského rozhraní vygenerovalo `Bind` atribut a přidalo entitu vytvořenou pořadačem modelu do sady entit s `Modified` příznakem. Tento kód není doporučen pro mnoho scénářů, protože `Bind` atribut vymaže všechna dříve existující data v polích, která nejsou uvedena v `Include` parametru.

Nový kód přečte existující entitu a volání `TryUpdateModel` pro aktualizaci polí v načtené entitě [na základě vstupu uživatele v publikovaných datech formuláře](xref:mvc/models/model-binding). Automatické sledování změn Entity Framework nastaví `Modified` příznak pro pole, která jsou změněna vstupem formuláře. Při `SaveChanges` volání metody Entity Framework vytvoří příkazy SQL pro aktualizaci řádku databáze. Konflikty souběžnosti jsou ignorovány a v databázi se aktualizují pouze sloupce tabulky, které byly aktualizovány uživatelem. (V dalším kurzu se dozvíte, jak zvládnout konflikty souběžnosti.)

Jako osvědčený postup bráníte přeúčtování, pole, která chcete aktualizovat pomocí stránky pro **Úpravy** , jsou deklarována v `TryUpdateModel` parametrech. (Prázdný řetězec uvedený před seznamem polí v seznamu parametrů je určen pro předponu, která má být použita v názvech polí formuláře.) V současné době nejsou k dispozici žádná doplňková pole, která chráníte, ale seznam polí, ke kterým má pořadač modelů navazovat vazby, zajišťuje, že pokud přidáte pole do datového modelu v budoucnosti, budou automaticky chráněny, dokud je nepřidáte sem.

V důsledku těchto změn je signatura metody HttpPost `Edit` shodná s `Edit` metodou HttpGet; proto jste přejmenovali metodu `EditPost` .

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Alternativní HttpPost upravit kód: vytvořit a připojit

Doporučený HttpPost úpravový kód zajišťuje aktualizaci pouze změněných sloupců a zachovává data ve vlastnostech, které nechcete zahrnout do vazby modelu. Přístup pro čtení prvního vyžaduje navíc čtení databáze a může mít za následek složitější kód pro zpracování konfliktů souběžnosti. Alternativou je připojit entitu vytvořenou pořadačem modelu do kontextu EF a označit ji jako upravenou. (Projekt neaktualizujte tímto kódem, zobrazuje se jenom pro ilustraci volitelného přístupu.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Tento přístup můžete použít, pokud uživatelské rozhraní webové stránky zahrnuje všechna pole v entitě a může je aktualizovat.

Generovaný kód používá přístup k vytvoření a připojení, ale zachycuje pouze `DbUpdateConcurrencyException` výjimky a vrací kódy chyb 404.  Příklad zobrazuje zachycení jakékoli výjimky aktualizace databáze a zobrazí chybovou zprávu.

### <a name="entity-states"></a>Stavy entit

Kontext databáze uchovává informace o tom, zda jsou entity v paměti synchronizovány s odpovídajícími řádky v databázi, a tyto informace určují, co se stane při volání `SaveChanges` metody. Například když do metody předáte novou entitu `Add` , stav této entity je nastaven na `Added` . Poté při volání `SaveChanges` metody kontext databáze vydá příkaz SQL INSERT.

Entita může být v jednom z následujících stavů:

* `Added`. Entita zatím v databázi neexistuje. `SaveChanges`Metoda vydá příkaz INSERT.

* `Unchanged`. Tuto entitu není v této entitě potřeba dělat `SaveChanges` metodou. Při čtení entity z databáze se entita spouští s tímto stavem.

* `Modified`. Některé nebo všechny hodnoty vlastností entity byly změněny. `SaveChanges`Metoda vydá příkaz Update.

* `Deleted`. Entita byla označena pro odstranění. `SaveChanges`Metoda vydá příkaz DELETE.

* `Detached`. Entita není sledována kontextem databáze.

V aplikaci klasické pracovní plochy se obvykle automaticky nastaví změny stavu. Načetli jste entitu a provedete změny jejích hodnot vlastností. To způsobí, že se stav jeho entity automaticky změní na `Modified` . Poté při volání `SaveChanges` Entity Framework vygeneruje příkaz SQL Update, který aktualizuje pouze skutečné vlastnosti, které jste změnili.

Ve webové aplikaci, `DbContext` která zpočátku čte entitu a zobrazuje data, která se mají upravit, se po vykreslení stránky odstraní. Když `Edit` je volána metoda akce HTTPPOST, je vytvořena nová webová žádost a máte novou instanci `DbContext` . Pokud znovu načtete entitu v tomto novém kontextu, simulujete tím zpracování plochy.

Pokud ale nechcete provádět operaci čtení navíc, je nutné použít objekt entity vytvořený pořadačem modelu.  Nejjednodušší způsob, jak to provést, je nastavit stav entity na hodnotu změněno, jak je provedeno v alternativním HttpPost úpravě kódu zobrazeném výše. Když potom zavoláte `SaveChanges` , Entity Framework aktualizuje všechny sloupce řádku databáze, protože kontext nemá žádný způsob, jak zjistit, které vlastnosti jste změnili.

Pokud se chcete vyhnout prvnímu přístupu, ale také chcete, aby příkaz SQL UPDATE aktualizoval pouze ta pole, která uživatel skutečně změnil, kód je složitější. Původní hodnoty je nutné uložit nějakým způsobem (například pomocí skrytých polí), aby byly k dispozici při `Edit` volání metody HTTPPOST. Pak můžete vytvořit entitu Student pomocí původních hodnot, zavolat `Attach` metodu s touto původní verzí entity, aktualizovat hodnoty entity na nové hodnoty a potom zavolat `SaveChanges` .

### <a name="test-the-edit-page"></a>Test stránky pro úpravy

Spusťte aplikaci, vyberte kartu **Students** a pak klikněte na hypertextový odkaz **Upravit** .

![Stránka pro úpravy studentů](crud/_static/student-edit.png)

Změňte některá data a klikněte na **Uložit**. Otevře se stránka **index** a uvidíte změněná data.

## <a name="update-the-delete-page"></a>Aktualizace stránky pro odstranění

V *StudentController.cs*kód šablony pro `Delete` metodu HttpGet používá `SingleOrDefaultAsync` metodu k načtení vybrané entity studenta, jak jste viděli v metodách Details a Edit. Chcete-li však implementovat vlastní chybovou zprávu v případě, že volání bude `SaveChanges` neúspěšné, přidáte k této metodě a příslušnému zobrazení některé funkce.

Jak jste viděli pro operace aktualizace a vytváření, vyžadují operace odstranění dvě metody akcí. Metoda, která je volána jako odpověď na požadavek GET, zobrazuje zobrazení, které uživateli umožňuje schválit nebo zrušit operaci odstranění. Pokud ho uživatel schválí, vytvoří se požadavek POST. V takovém případě se `Delete` zavolá metoda HTTPPOST a pak tato metoda skutečně provede operaci odstranění.

Do metody HttpPost přidáte blok try-catch, který bude `Delete` zpracovávat chyby, ke kterým může dojít při aktualizaci databáze. Pokud dojde k chybě, metoda HttpPost Delete zavolá metodu HttpGet DELETE a předá jí parametr, který indikuje, že došlo k chybě. Metoda HttpGet DELETE potom znovu zobrazí potvrzovací stránku spolu s chybovou zprávou, takže uživatel může příležitost zrušit nebo zkusit znovu.

Nahraďte `Delete` metodu akce HttpGet následujícím kódem, který spravuje zasílání zpráv o chybách.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Tento kód akceptuje volitelný parametr, který označuje, zda byla metoda volána po neúspěšném uložení změn. Tento parametr má hodnotu false, pokud `Delete` je metoda HttpGet volána bez předchozí chyby. Pokud je volána `Delete` metodou HTTPPOST v reakci na chybu aktualizace databáze, je parametr true a do zobrazení se předává chybová zpráva.

### <a name="the-read-first-approach-to-httppost-delete"></a>Přístup pro čtení, který se má HttpPost odstranit

Nahraďte `Delete` metodu akce HTTPPOST (s názvem `DeleteConfirmed` ) následujícím kódem, který provede skutečnou operaci odstranění a zachytí všechny chyby aktualizace databáze.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

Tento kód načte vybranou entitu a pak zavolá `Remove` metodu pro nastavení stavu entity na `Deleted` . Při `SaveChanges` volání metody je vygenerován příkaz SQL DELETE.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>Postup vytvoření a připojení k HttpPost odstranění

Pokud je lepší výkon v aplikaci s vysokým objemem, je možné vyhnout se zbytečnému dotazu SQL vytvořením instance entity studenta s použitím pouze hodnoty primárního klíče a nastavením stavu entity na `Deleted` . To je vše, co Entity Framework potřebuje, aby se entita odstranila. (Tento kód neumísťujte do projektu; je tu jenom k ilustraci alternativy.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Pokud má entita související data, která by se měla taky odstranit, ujistěte se, že je v databázi nakonfigurované kaskádové odstranění. V případě tohoto přístupu k odstranění entit nemusí EF vědět, že existují související entity, které by se měly odstranit.

### <a name="update-the-delete-view"></a>Aktualizace zobrazení pro odstranění

V *zobrazení/student/odstranit. cshtml*přidejte chybovou zprávu mezi nadpisem H2 a záhlavím H3, jak je znázorněno v následujícím příkladu:

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Spusťte aplikaci, vyberte kartu **Students** a klikněte na **Odstranit** hypertextový odkaz:

![Stránka pro potvrzení odstranění](crud/_static/student-delete.png)

Klikněte na **Odstranit**. Stránka index se zobrazí bez odstraněného studenta. (Příklad kódu pro zpracování chyb v akci v kurzu souběžnosti najdete v tématu.)

## <a name="close-database-connections"></a>Zavřít databázová připojení

Chcete-li uvolnit prostředky, které připojení k databázi obsahuje, je nutné instanci kontextu uvolnit co nejdříve, jakmile s ní budete hotovi. ASP.NET Core vestavěné [závislosti](../../fundamentals/dependency-injection.md) se postará o tento úkol za vás.

V *Startup.cs*zavoláte [metodu rozšíření AddDbContext](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) pro zřízení `DbContext` třídy v kontejneru ASP.NET Core di. Tato metoda nastavuje dobu platnosti služby na `Scoped` výchozí hodnotu. `Scoped`znamená, že doba života objektu kontextu se shoduje s časem životnosti webové žádosti a `Dispose` Metoda bude na konci webového požadavku volána automaticky.

## <a name="handle-transactions"></a>Zpracování transakcí

Ve výchozím nastavení Entity Framework implicitně implementuje transakce. Ve scénářích, kdy provedete změny více řádků nebo tabulek a potom zavoláte `SaveChanges` , Entity Framework automaticky zajistěte, aby všechny změny byly úspěšné nebo všechny selžou. Pokud se nějaké změny provedly a pak dojde k chybě, změny se automaticky vrátí zpět. V případě scénářů, kde potřebujete větší kontrolu – například pokud chcete zahrnout operace provedené mimo Entity Framework v transakci – viz [transakce](/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Žádné dotazy pro sledování

Když kontext databáze načte řádky tabulky a vytvoří objekty entit, které je reprezentují, ve výchozím nastavení udržuje přehled o tom, zda jsou entity v paměti synchronizovány s těmi, které jsou v databázi. Data v paměti fungují jako mezipaměť a používají se při aktualizaci entity. Toto ukládání do mezipaměti je často zbytečné v rámci webové aplikace, protože instance kontextu jsou obvykle krátkodobé – neuvolňuje (nové je vytvořeno a odstraněno pro každý požadavek) a kontext, který čte entitu, je obvykle uvolněn předtím, než se entita znovu použije.

Sledování objektů entit v paměti lze zakázat voláním `AsNoTracking` metody. Mezi obvyklé scénáře, které byste mohli chtít udělat, patří následující:

* Během životnosti kontextu nemusíte aktualizovat žádné entity a nepotřebujeme EF pro [Automatické načítání navigačních vlastností s entitami načtenými pomocí samostatných dotazů](read-related-data.md). Často se tyto podmínky splní v metodách HttpGetch akcí kontroleru.

* Spouštíte dotaz, který načte velký objem dat a aktualizuje se jenom malá část vrácených dat. Může být efektivnější vypnout sledování pro velký dotaz a spustit dotaz později pro několik entit, které je potřeba aktualizovat.

* Chcete připojit entitu, abyste ji mohli aktualizovat, ale dříve jste získali stejnou entitu pro jiný účel. Vzhledem k tomu, že entita je již sledována kontextem databáze, nelze připojit entitu, kterou chcete změnit. Jedním ze způsobů, jak tuto situaci zpracovat, je zavolat `AsNoTracking` na předchozí dotaz.

Další informace najdete v tématu [sledování vs. bez sledování](/ef/core/querying/tracking).

## <a name="get-the-code"></a>Získání kódu

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobení stránky podrobností
> * Aktualizace stránky pro vytvoření
> * Aktualizace stránky pro úpravy
> * Aktualizace stránky odstranění
> * Uzavřená databázová připojení

Přejděte k dalšímu kurzu, kde se dozvíte, jak rozšířit funkčnost stránky **indexu** přidáním řazení, filtrování a stránkování.

> [!div class="nextstepaction"]
> [Další: řazení, filtrování a stránkování](sort-filter-page.md)

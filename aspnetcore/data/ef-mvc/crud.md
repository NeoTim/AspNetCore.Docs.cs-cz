---
title: 'Kurz: Implementace funkcí CRUD – ASP.NET MVC s EF Core'
description: V tomto kurzu zkontrolujete a přizpůsobit CRUD (vytváření, čtení, aktualizace nebo odstranění) kód, který generování uživatelského rozhraní MVC se automaticky vytvoří za vás do kontrolerů a zobrazení.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/crud
ms.openlocfilehash: 83f5d4bdc3d5872109649818b61a6dbb656fd8be
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264835"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Kurz: Implementace funkcí CRUD – ASP.NET MVC s EF Core

V předchozím kurzu jste vytvořili aplikaci MVC, která ukládá a zobrazuje data pomocí rozhraní Entity Framework a SQL Server LocalDB. V tomto kurzu zkontrolujete a přizpůsobit CRUD (vytváření, čtení, aktualizace nebo odstranění) kód, který generování uživatelského rozhraní MVC se automaticky vytvoří za vás do kontrolerů a zobrazení.

> [!NOTE]
> Je běžnou praxí pro implementaci vzoru úložiště chcete-li vytvořit vrstvu HAL mezi řadiči a vrstva přístupu k datům. Na tyto kurzy byly jednoduché a zaměřují se na vyučují způsob použití rozhraní Entity Framework samotné, nepoužívejte úložišť. Informace o úložištích s EF najdete v tématu [poslední kurz v této sérii](advanced.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobení stránky podrobností
> * Aktualizovat stránku vytvořit
> * Aktualizace stránky pro úpravu
> * Aktualizovat stránku Delete
> * Připojení k databázi zavřít

## <a name="prerequisites"></a>Požadavky

* [Začínáme s EF Core ve webové aplikaci ASP.NET Core MVC](intro.md)

## <a name="customize-the-details-page"></a>Přizpůsobení stránky podrobností

Automaticky generovaný kód pro studenty indexovou stránku vynechali `Enrollments` vlastnost, protože tato vlastnost obsahuje kolekci. V **podrobnosti** stránce obsah kolekce bude zobrazen jako tabulku HTML.

V *Controllers/StudentsController.cs*, metoda akce pro podrobnosti zobrazení používá `SingleOrDefaultAsync` metodu pro načtení jedné `Student` entity. Přidejte kód, který volá `Include`. `ThenInclude`, a `AsNoTracking` metod, jak je znázorněno v následující zvýrazněný kód.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

`Include` a `ThenInclude` metody způsobit kontextu načtení `Student.Enrollments` navigační vlastnost a v rámci každé registraci `Enrollment.Course` navigační vlastnost.  Získáte další informace o těchto metodách v [čtení souvisejících dat](read-related-data.md) kurzu.

`AsNoTracking` Metoda zvyšuje výkon ve scénářích, kde se nebude aktualizovat vrácených entit v aktuálním kontextu životnost. Získáte další informace o `AsNoTracking` na konci tohoto kurzu.

### <a name="route-data"></a>Data trasy

Hodnota klíče, který je předán `Details` metody pochází z *směrování dat*. Data trasy, která jsou data, která v segment adresy URL nalezen vazač modelu. Například výchozí trasa určuje kontroler, akci a id segmenty:

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

V následující adrese URL výchozí trasu mapy kurzů vedených jako kontroler, Index jako akci a 1 jako identifikátor; Toto jsou hodnot dat trasy.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

Poslední část adresy URL ("? courseID = 2021") je hodnota řetězce dotazu. Vazače modelu bude také předat hodnotu ID do `Details` metoda `id` parametru předat jako hodnotu řetězce dotazu:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

Na stránce Index adresy URL hypertextového odkazu jsou vytvořené příkazy pomocné rutiny značky v zobrazení Razor. V následujícím kódu Razor `id` parametr odpovídá výchozí trasu, takže `id` se přidá do data trasy.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Tím se vytvoří následující kód HTML při `item.ID` 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

V následujícím kódu Razor `studentID` neodpovídá parametru v této výchozí trase, tak se přidá jako řetězec dotazu.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Tím se vytvoří následující kód HTML při `item.ID` 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Další informace o pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

### <a name="add-enrollments-to-the-details-view"></a>Přidat registraci k zobrazení podrobností

Otevřít *Views/Students/Details.cshtml*. Každé pole je zobrazeno pomocí `DisplayNameFor` a `DisplayFor` pomocné rutiny, jak je znázorněno v následujícím příkladu:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Za poslední pole a bezprostředně před uzavírací `</dl>` značky, přidejte následující kód k zobrazení seznamu registrací:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Pokud po vložení kódu je nesprávné odsazení kódu, stiskněte kombinaci kláves CTRL-K-D na opravu.

Tento kód projde entity v `Enrollments` navigační vlastnost. Pro každé registraci zobrazí název kurzu a třída. Název kurzu je načten z kurzu entitu, která je uložena v `Course` navigační vlastnost entity registrace.

Spusťte aplikaci, vyberte **studenty** kartu a klikněte na tlačítko **podrobnosti** odkaz student. Zobrazí seznam kurzů a známek pro vybrané studenta:

![Stránka s podrobnostmi o studenta](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Aktualizovat stránku vytvořit

V *StudentsController.cs*, upravte HttpPost `Create` metoda bloku try-catch přidáváním a odebíráním ID z `Bind` atribut.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Tento kód přidá Student entita vytvořená pomocí ASP.NET Core MVC vazač modelu pro studenty entity nastavení a pak uloží změny do databáze. (Vazač modelu odkazuje na funkci ASP.NET Core MVC, která usnadňuje práci s data odeslaná formuláře; vazač modelu převádí hodnoty odeslaného formuláře na typy CLR a předává je na metodu akce v parametrech. V tomto případě vazače modelu vytvoří instanci entity studenta pomocí hodnoty vlastností z kolekce formuláře.)

Můžete odebrat `ID` z `Bind` atribut, protože ID je hodnota primárního klíče, které SQL Server se nastaví automaticky při vložení řádku. Vstup od uživatele nelze nastavit hodnotu ID.

Jiné než `Bind` atribut, bloku try-catch je pouze změny provedené na automaticky generovaný kód. Pokud výjimka, která je odvozena z `DbUpdateException` je zachycena, zatímco se ukládají se změny, zobrazí se obecné chybové zprávy. `DbUpdateException` výjimky jsou někdy způsobeny něco mimo aplikací, nikoli programovací chyba, takže uživatele se doporučuje to chcete zkusit znovu. I když není implementovaná v této ukázce, typicky zaznamenávají produkční kvality aplikace výjimku. Další informace najdete v tématu **protokolu insight** tématu [monitorování a Telemetrie (vytváření skutečných cloudových aplikací s Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).

`ValidateAntiForgeryToken` Atribut lze zabránit útokům padělání (CSRF) podvržení žádosti. Token, který se automaticky vloží do zobrazení podle [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) a je dostupná při odeslání formuláře uživatelem. Token, který je ověřen `ValidateAntiForgeryToken` atribut. Další informace o CSRF najdete v tématu [ochrana proti padělání požadavků](../../security/anti-request-forgery.md).

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Poznámka k zabezpečení o overposting

`Bind` Atribut, který obsahuje automaticky generovaný kód na `Create` metoda je jedním ze způsobů pro ochranu před overposting v vytvářet scénáře. Předpokládejme například, že obsahuje entity studentů `Secret` vlastnost, která nechcete tuto webovou stránku nastavení.

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

I v případě, že nemáte `Secret` pole na webové stránce, se hacker může použít například nástroj Fiddler nebo napsat určitého kódu JavaScript, k publikování `Secret` tvoří hodnotu. Bez `Bind` , který by vyzvednutí atribut pole, která používá vazač modelu při vytváření instance studentů, vazače modelu omezení `Secret` formu hodnotu a použijte ji k vytvoření instance entity studentů. Pak cokoli, co hodnota kyberzločinci zadaný pro `Secret` pole formuláře se aktualizují v databázi. Následující obrázek ukazuje přidání nástroj Fiddler `Secret` pole (hodnotu "OverPost") na hodnoty odeslaného formuláře.

![Přidání tajného kódu pole fiddleru](crud/_static/fiddler.png)

Hodnota "OverPost" by poté úspěšně přidány do `Secret` vlastností vloženého řádku, i když jste zamýšleli nikdy, že je pro webové stránky moct nastavit tuto vlastnost.

Abyste zabránili overposting ve scénářích úpravy nejprve čtení entit z databáze a potom voláním `TryUpdateModel`, předejte v seznamu povolených explicitní vlastností. To je metoda použitá v těchto kurzech.

Alternativní způsob zabránění overposting, který mnoho vývojářů preferuje se má zobrazit modely, nikoli tříd entit pomocí vazby modelu. Zahrnout pouze vlastnosti, které chcete aktualizovat v modelu zobrazení. Po dokončení vazač modelu MVC, zkopírujte do instance entity, případně můžete použít nástroje, jako je AutoMapper zobrazit vlastnosti modelu. Použití `_context.Entry` na zapisovanou instanci entity k nastavení jeho stavu `Unchanged`a potom nastavte `Property("PropertyName").IsModified` na hodnotu true na každé vlastnosti entity, která je součástí modelu zobrazení. Tato metoda funguje v obou upravovat a vytvářet scénáře.

### <a name="test-the-create-page"></a>Stránka pro vytvoření testu

Kód v *Views/Students/Create.cshtml* používá `label`, `input`, a `span` (pro ověřovacích zpráv) pomocníků pro každé pole.

Spusťte aplikaci, vyberte **studenty** kartu a klikněte na tlačítko **vytvořit nový**.

Zadejte názvy a data. Zkuste zadat neplatné datum, pokud váš prohlížeč umožňuje udělat. (Některé prohlížeče vynutit použití ovládacího prvku pro výběr data.) Pak klikněte na tlačítko **vytvořit** zobrazíte chybovou zprávu.

![Chyba ověřování datum](crud/_static/date-error.png)

Toto je ověření na straně serveru, který získáte ve výchozím nastavení; v pozdějších kurzech uvidíte, jak přidat atributy, které budou také generovat kód pro ověřování na straně klienta. Následující zvýrazněný kód ukazuje model ověřování vrácení se změnami `Create` metody.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Změňte na platnou hodnotu data a klikněte na tlačítko **vytvořit** zobrazíte nového objektu student joinkind **Index** stránky.

## <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravu

V *StudentController.cs*, třídy MetadataExchangeClientMode `Edit` – metoda (, aniž byste `HttpPost` atribut) používá `SingleOrDefaultAsync` metodu pro načtení vybranou entitu Student, jak jste viděli v `Details` metoda. Nemusíte změnit tuto metodu.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Doporučené HttpPost úpravy kódu: Čtení a aktualizace

Nahraďte následující kód metody HttpPost úpravy akce.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Tyto změny implementace bránící overposting osvědčeným postupem zabezpečení. Generátor vygeneruje `Bind` atribut a přidání entity vytvořené vazače modelu v entitě sadu s `Modified` příznak. V mnoha scénářích se nedoporučuje kódu, protože `Bind` atribut vymaže všechny už existující data v polích, které nejsou uvedené v `Include` parametru.

Nový kód čte existující entity a volání `TryUpdateModel` aktualizovat pole v načtenou entitu [na základě uživatelského zadání v datech odeslaného formuláře](xref:mvc/models/model-binding#how-model-binding-works). Entity Framework automatické sledování sad změn `Modified` příznak na pole, která změní vstup formuláře. Když `SaveChanges` metoda je volána, Entity Framework vytvoří SQL příkazy pro aktualizaci řádku databáze. Konflikty souběžnosti jsou ignorovány a pouze sloupce tabulky, které byly aktualizovány uživatelem se aktualizují v databázi. (Vyšší kurz ukazuje, jak zpracování konfliktů souběžnosti.)

Jako osvědčený postup, aby se zabránilo overposting, pole, která mají být možné aktualizovat pomocí **upravit** stránky jsou povolené v `TryUpdateModel` parametry. (Prázdný řetězec předcházející seznamu polí v seznamu parametrů je u předpony pro použití s názvy polí formuláře.) Aktuálně nejsou žádné další pole, které chcete chránit, ale seznam polí, která má vazač modelu pro vazbu zajistí, že pokud přidáte pole do datového modelu v budoucnu, že jsou automaticky chráněné dokud explicitně přidáte je tady.

V důsledku těchto změn, podpis metody HttpPost `Edit` metodu je stejná jako HttpGet `Edit` metoda; proto jste přejmenovali metodu `EditPost`.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Alternativní HttpPost úpravy kódu: Vytvoření a připojení

Doporučené úpravy kódu HttpPost zajistí, že pouze změněné sloupce aktualizován a zachová data ve vlastnostech, které nechcete, aby zahrnuté pro vazbu modelu. Přístup pro čtení na prvním ale vyžaduje další databáze čtení a může mít za následek složitější kód pro zpracování konfliktů souběžnosti. Alternativou je připojit entitu vytvořené vazač modelu ke kontextu EF a označte ji jako upravená. (Nechcete aktualizovat svůj projekt s tímto kódem má pouze uvedené pro ilustraci metodiky volitelný.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Tento postup můžete použít, pokud webové stránky uživatelského rozhraní obsahuje všechna pole v entitě a také možnost aktualizovat jakoukoli z nich.

Automaticky generovaný kód používá metodu vytvořit a připojit, ale pouze zachytí `DbUpdateConcurrencyException` výjimky a vrátí 404 kódy chyb.  Jak ukazuje příklad zachytí jakoukoli výjimku aktualizace databáze a zobrazí chybovou zprávu.

### <a name="entity-states"></a>Stavy entity

Uchovává informace o kontextu databáze, jestli jsou synchronizované s jejich odpovídajících řádků v databázi entity v paměti a těchto informací Určuje, co se stane při volání `SaveChanges` metody. Například při předání do nové entity `Add` metoda, která stav entity je nastavený na `Added`. Potom při volání `SaveChanges` metody kontext databáze vydá příkaz INSERT jazyka SQL.

Entity mohou být v jednom z následujících stavů:

* `Added`. Entita ještě neexistuje v databázi. `SaveChanges` Metoda vydá příkaz INSERT.

* `Unchanged`. Co je potřeba udělat s touto entitou podle `SaveChanges` metody. Při čtení entity z databáze entity je na začátku s tímto stavem.

* `Modified`. Některé nebo všechny hodnoty vlastností entity byly změněny. `SaveChanges` Metoda vydá příkazu UPDATE.

* `Deleted`. Entita byla označena k odstranění. `SaveChanges` Metoda vydá příkaz DELETE.

* `Detached`. Entita není sledován správou kontext databáze.

V desktopové aplikaci změny stavu se obvykle nastaví automaticky. Přečtěte si entity a provést změny na některé z jeho hodnot vlastností. To způsobí, že jeho stav entity automaticky změnil na `Modified`. Potom při volání `SaveChanges`, Entity Framework generuje SQL příkaz UPDATE, která aktualizuje pouze skutečné vlastnosti, které jste změnili.

Ve webové aplikaci `DbContext` , která původně načte entity a zobrazí jeho data upravit je uvolněn po vykreslení stránky. Když HttpPost `Edit` volání metody, provedení požadavku na nový web a máte novou instanci třídy `DbContext`. Pokud znovu načtete entity v kontextu této nové, simulovat klasické pracovní plochy zpracování.

Ale pokud nechcete provést nadbytečné operace čtení, je nutné použít entitu objekt vytvořený pomocí vazače modelu.  K nastavení stavu entity změněno, jak je tomu v alternativní HttpPost úpravy kódu uvedeného výše je nejjednodušší způsob, jak to provést. Potom při volání `SaveChanges`, Entity Framework aktualizuje všechny sloupce řádek databáze, protože kontext nemá žádný způsob, jak zjistit vlastnosti, které jste změnili.

Pokud chcete se vyhnout přístup pro čtení první, ale také chcete aktualizovat SQL doje k aktualizaci pouze pole, která uživatel ve skutečnosti změnil, kód je složitější. Budete muset uložit původní hodnoty nějakým způsobem (například pomocí skrytá pole) tak, že jsou k dispozici při HttpPost `Edit` metoda je volána. Poté vytvoříte Student entity pomocí původní hodnoty, volání `Attach` metodu s původní verzi entitou, aktualizujte hodnoty entity na nové hodnoty a následně zavolat `SaveChanges`.

### <a name="test-the-edit-page"></a>Testování stránky pro úpravu

Spusťte aplikaci, vyberte **studenty** kartu a potom klikněte na **upravit** hypertextový odkaz.

![Stránka upravit studentů](crud/_static/student-edit.png)

Některá data a klikněte na tlačítko Změnit **Uložit**. **Index** stránka se otevře a zobrazí změněná data.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

V *StudentController.cs*, kód šablony pro třídy MetadataExchangeClientMode `Delete` metoda používá `SingleOrDefaultAsync` metodu pro načtení vybranou entitu Student, jak jste viděli v podrobností a úprav metody. Však implementovat vlastní chybová zpráva při volání `SaveChanges` selže, přidáte některé funkce pro tuto metodu a jeho odpovídající zobrazení.

Už znáte aktualizace a operace vytvoření, odstranění operace vyžadují dvě metody akce. Metoda, která je volána v reakci na požadavek GET zobrazí zobrazení, které umožňuje uživateli možnost schválit nebo zrušit operaci odstranění zopakovat. Pokud uživatel ho buď schválí, vytvoří se požadavek POST. Když se to stane, HttpPost `Delete` volání metody a pak tato metoda ve skutečnosti provádí operaci odstranění zopakovat.

Přidáte blok try-catch HttpPost `Delete` metodu ke zpracování všechny chyby, které mohou nastat při aktualizaci databáze. Pokud dojde k chybě, volá metoda HttpPost Delete metodu HttpGet Delete, předají se jí parametr, který označuje, že došlo k chybě. Metodu HttpGet Delete znovu pak zobrazí na stránce potvrzení spolu s chybovou zprávu, poskytnou tak uživateli možnost zrušit, nebo to zkuste znovu.

Nahraďte třídy MetadataExchangeClientMode `Delete` metody akce s následujícím kódem, který spravuje zpráv o chybách.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Tento kód je možné zadat volitelný parametr, který označuje, zda byla volána metoda po selhání, uložte změny. Tento parametr má hodnotu false, kdy HttpGet `Delete` metoda je volána bez předchozí chyby. Když je volána metodou HttpPost `Delete` metody v reakci na chybě aktualizace databáze, tento parametr má hodnotu true a je předána do zobrazení, chybová zpráva.

### <a name="the-read-first-approach-to-httppost-delete"></a>Čtení první přístup k odstranění HttpPost

Nahraďte HttpPost `Delete` metody akce (s názvem `DeleteConfirmed`) následujícím kódem, který provádí operace odstranění skutečné a zachytí všechny chyby aktualizace databáze.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6,8-11,13-14,18-23)]

Tento kód načte vybranou entitu, zavolá `Remove` metodu pro nastavení stavu entity `Deleted`. Když `SaveChanges` nazývá SQL odstranit vygenerované příkaz.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>Vytvořit a připojit přístup k odstranění HttpPost

Pokud je vylepšení výkonu v aplikaci s velkým objemem prioritu, se můžete vyhnout nepotřebných dotazů SQL vytvoření instance Student entity pomocí jenom primární klíčové hodnoty a pak nastavení stavu entity `Deleted`. To je vše, Entity Framework potřebuje, aby odstranit entitu. (Není ve vašem projektu vložte tento kód; je zde pouze k objasnění alternativu.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Pokud entita má související data, která je rovněž třeba odstranit, ujistěte se, že kaskádové odstranění je nakonfigurovaný v databázi. S tímto přístupem k odstranění entity nemusí EF Uvědomte si, že existují související entity, která se má odstranit.

### <a name="update-the-delete-view"></a>Zobrazení aktualizovat, odstranit

V *Views/Student/Delete.cshtml*, přidejte mezi h2 záhlaví a záhlaví h3 chybovou zprávu, jak je znázorněno v následujícím příkladu:

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Spusťte aplikaci, vyberte **studenty** kartu a klikněte na tlačítko **odstranit** hypertextový odkaz:

![Stránka pro potvrzení odstranění](crud/_static/student-delete.png)

Klikněte na tlačítko **odstranit**. Zobrazí se indexovou stránku bez odstraněné studentů. (Zobrazí se vám příklad kód v akci v tomto kurzu souběžnosti pro zpracování chyb.)

## <a name="close-database-connections"></a>Připojení k databázi zavřít

Tím se uvolní prostředky, které obsahuje připojení k databázi, instance kontextu musí být uvolněn co nejdříve až budete hotovi s ním. ASP.NET Core předdefinované [injektáž závislostí](../../fundamentals/dependency-injection.md) dané úlohy postará za vás.

V *Startup.cs*, volání [AddDbContext rozšiřující metoda](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) ke zřízení `DbContext` třídy v ASP.NET Core DI kontejneru. Že metoda nastaví doba platnosti služby `Scoped` ve výchozím nastavení. `Scoped` znamená, že doba života objektu kontextu se shoduje s webovou žádost životnosti a `Dispose` metoda se bude automaticky volána na konci webový požadavek.

## <a name="handle-transactions"></a>Zpracování transakcí

Ve výchozím nastavení rozhraní Entity Framework implementuje implicitně transakce. V situacích, kdy provést změny na více řádcích nebo tabulky a poté zavolejte `SaveChanges`, Entity Framework automaticky zajišťuje, že všechny změny úspěch nebo selžou všechny. Pokud některé změny nejprve dokončení a potom se stane chyba, tyto změny se automaticky vrátí zpět. Pro scénáře, kde můžete potřebovat mít lepší kontrolu – například pokud budete chtít zahrnout operace provedené mimo rozhraní Entity Framework v rámci transakce – viz [transakce](/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Sledování bez dotazy

Když kontext databáze načte řádky tabulky, vytvoří objekty entity, které představují je ve výchozím nastavení se uchovává informace o zda entity v paměti jsou synchronizované s tím, co je v databázi. Data v paměti funguje jako mezipaměť a používá se při aktualizaci entity. Tento ukládání do mezipaměti je často zbytečné ve webové aplikaci, protože kontextu instance jsou krátkodobé a jednorázové obvykle (nový je vytvořen a uvolnění pro každý požadavek) a kontextu, která načte entity je obvykle uvolněn předtím, než je znovu použít danou entitu.

Můžete zakázat sledování objektů entit v paměti, že volání `AsNoTracking` metody. Typické scénáře, ve kterých můžete chtít provést, patří:

* Po celou dobu životnosti kontextu není potřeba aktualizovat žádné entity, a není nutné EF k [automaticky načíst vlastnosti navigace s entitami pomocí samostatné dotazy](read-related-data.md). Do metody akce kontroler HttpGet často jste splnili tyto podmínky.

* Spustíte dotaz, který načte velké objemy dat a aktualizují pouze malou část vrácená data. Může být efektivnější vypnout sledování pro velké dotaz, a spusťte dotaz později pro několik entit, které je potřeba aktualizovat.

* Chcete připojit entitu, aby bylo možné ji aktualizovat, ale dříve, který jste získali stejné entity k jinému účelu. Vzhledem k tomu, že entita již sledován správou kontext databáze, nelze připojit entitu, kterou chcete změnit. Jeden způsob, jak tuto situaci je volání `AsNoTracking` na předchozí dotaz.

Další informace najdete v tématu [sledování vs. Sledování bez](/ef/core/querying/tracking).

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobit na stránku podrobností
> * Aktualizovat na stránce vytvořit
> * Aktualizace stránky pro úpravu
> * Aktualizovat stránku Delete
> * Připojení uzavřeno databáze

Pokračujte k dalším článku se naučíte, jak rozšířit funkce **Index** stránky tak, že přidáte řazení, filtrování a stránkování.
> [!div class="nextstepaction"]
> [Řazení, filtrování a stránkování](sort-filter-page.md)

---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
title: 'Kurz: Další informace o pokročilých scénářích EF pro aplikaci MVC 5'
description: V tomto kurzu zahrnuje zavádí několik témat, které jsou užitečné mít na paměti při nad rámec základní informace o vývoji webových aplikací ASP.NET, které používají Entity Framework Code First.
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: f35a9b0c-49ef-4cde-b06d-19d1543feb0b
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
msc.type: authoredcontent
ms.openlocfilehash: ff480f7e8c2801fcb6a64c37d95e7e15467acde6
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837491"
---
# <a name="tutorial-learn-about-advanced-ef-scenarios-for-an-mvc-5-web-app"></a>Kurz: Další informace o pokročilých scénářích EF pro aplikaci MVC 5

V předchozím kurzu jste implementovali tabulky na hierarchii dědičnosti. V tomto kurzu zahrnuje zavádí několik témat, které jsou užitečné mít na paměti při nad rámec základní informace o vývoji webových aplikací ASP.NET, které používají Entity Framework Code First. Části první několik mít podrobné pokyny, které vás provedou kód a pomocí sady Visual Studio k provedení úloh následující části představují několik témat s stručné úvodní informace a odkazy na prostředky pro další informace.

Pro většinu z těchto témat budete pracovat stránky, které jste už vytvořili. Použití nezpracovaných SQL hromadné aktualizace vytvoříte novou stránku, která aktualizuje počet kredity všechny kurzy v databázi:

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Spouštějte nezpracované dotazy SQL
> * Provádění dotazů bez sledování
> * Prozkoumejte SQL dotazy odeslané do databáze

Dozvíte se taky o:

> [!div class="checklist"]
> * Vytváření abstraktní vrstvu
> * Třídy proxy
> * Automaticky změnit zjišťování
> * Automatické ověřování
> * Entity Framework Power Tools
> * Entity Framework zdrojového kódu

## <a name="prerequisite"></a>Předpoklad

* [Implementace dědičnosti](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="perform-raw-sql-queries"></a>Spouštějte nezpracované dotazy SQL

Prvního rozhraní API sady Entity Framework kód obsahuje metody, které vám umožní předat příkazů SQL přímo do databáze. Máte následující možnosti:

- Použití [DbSet.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.dbset.sqlquery.aspx) metodu pro dotazy vracející typy entit. Vrácených objektů musí být typu očekávání tím, `DbSet` objektu a automaticky sleduje provedené kontext databáze není-li vypnout sledování. (Viz následující část o [AsNoTracking](https://msdn.microsoft.com/library/system.data.entity.dbextensions.asnotracking.aspx) metoda.)
- Použití [Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery.aspx) metodu pro dotazy vracející typy, které nejsou entity. Vrácená data není sledována kontext databáze, i když používáte tuto metodu pro načtení typů entit.
- Použití [Database.ExecuteSqlCommand](https://msdn.microsoft.com/library/gg679456.aspx) pro příkazy bez dotazů.

Jednou z výhod používající nástroj Entity Framework je, že se eliminuje propojí váš kód příliš úzce na konkrétní metodu ukládat data. Dělá to tak, že generování dotazy SQL a příkazy, které také díky které by bylo nutné napsat sami. Ale existují výjimečné situace, kdy je potřeba spustit konkrétní dotazy SQL, které ručně vytvoříte, a tyto metody umožňuje zpracovávat takové výjimky.

Jak platí vždy při spuštění příkazů SQL ve webové aplikaci, je nutné provést opatření k ochraně před útoky prostřednictvím injektáže SQL serveru. Jednou z možností, které se pomocí parametrizovaných dotazů se ujistěte, že řetězce odeslané na webové stránce se nedá interpretovat jako příkazy SQL. V tomto kurzu použijete parametrizovaných dotazů při integraci uživatelský vstup do dotazu.

### <a name="calling-a-query-that-returns-entities"></a>Volání dotazu, který vrací entity

[DbSet&lt;TEntity&gt; ](https://msdn.microsoft.com/library/gg696460.aspx) třída poskytuje metodu, která můžete použít k provedení dotazu, který vrací entity typu `TEntity`. Jak vám to funguje, budete změnit kód v `Details` metodu `Department` kontroleru.

V *DepartmentController.cs*v `Details` metoda, nahraďte `db.Departments.FindAsync` volání metody s `db.Departments.SqlQuery` volání metody, jak je znázorněno v následující zvýrazněný kód:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample1.cs?highlight=8-14)]

Chcete-li ověřit, že nový kód funguje správně, vyberte **oddělení** kartu a potom **podrobnosti** pro jeden z jako vodítko použijte oddělení. Ujistěte se, že všechna data zobrazuje podle očekávání.

### <a name="calling-a-query-that-returns-other-types-of-objects"></a>Volání dotazu, který vrátí další typy objektů

Dříve jste vytvořili mřížky student statistiky o stránky, které jsme si ukázali, počet studentů pro každé datum registrace. Kód, který to dělá v *HomeController.cs* používá technologii LINQ:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample2.cs)]

Předpokládejme, že chcete napsat kód, který načte tato data přímo v SQL a ne pomocí jazyka LINQ. Provedete tak, že budete muset spustit dotaz, který vrací jinou hodnotu než objekty entity, což znamená, že musíte použít [Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery(v=VS.103).aspx) metody.

V *HomeController.cs*, nahraďte příkazem LINQ v `About` metoda s příkazem SQL, jak je znázorněno v následující zvýrazněný kód:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample3.cs?highlight=3-18)]

Na stránce o spuštění. Ověřte, zda se zobrazí stejná data, která předtím.

### <a name="calling-an-update-query"></a>Volání dotaz Update

Předpokládejme, že správce společnosti Contoso University chtějí mít možnost k provádění hromadných změn v databázi, jako je například změna číslo kredity pro každý kurz. Pokud univerzity má velký počet kurzů, bylo by neefektivní načíst vše jako entity a měnit je jednotlivě. V této části budete implementovat webovou stránku, která umožňuje uživateli zadat faktor, podle kterého chcete změnit počet kredity pro všechny kurzy a provede změny pomocí provádí SQL `UPDATE` příkazu. 

V *CourseContoller.cs*, přidejte `UpdateCourseCredits` metody pro `HttpGet` a `HttpPost`:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample4.cs)]

Když kontroler zpracovává `HttpGet` požadavek, nevrátí `ViewBag.RowsAffected` proměnné a zobrazení se zobrazí prázdné textové pole a tlačítka Odeslat.

Když **aktualizace** kliknutí na tlačítko `HttpPost` metoda je volána, a `multiplier` má hodnota zadaná v textovém poli. Kód spustí SQL, který aktualizuje kurzy a vrátí počet ovlivněných řádků na zobrazení `ViewBag.RowsAffected` proměnné. Při zobrazení získá hodnotu této proměnné, zobrazí počty řádků aktualizovat místo do textového pole a tlačítko Odeslat.

V *CourseController.cs*, klikněte pravým tlačítkem `UpdateCourseCredits` metody a pak klikněte na tlačítko **přidat zobrazení**. **Přidat zobrazení** se zobrazí dialogové okno. Ponechte výchozí hodnoty a vyberte **přidat**.

V *Views\Course\UpdateCourseCredits.cshtml*, nahraďte kód šablony následujícím kódem:

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample5.cshtml)]

Spustit `UpdateCourseCredits` metodu tak, že vyberete **kurzy** kartu a následným přidáním "/ UpdateCourseCredits" na konci adresy URL v adresním řádku prohlížeče (například: `http://localhost:50205/Course/UpdateCourseCredits`). Zadejte číslo v textovém poli:

![Update_Course_Credits_initial_page_with_2_entered](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

Klikněte na tlačítko **aktualizace**. Zobrazí počet ovlivněných řádků.

Klikněte na tlačítko **zpět do seznamu** zobrazíte seznam kurzů s revidované množství kreditu, které.

Další informace o nezpracované dotazy SQL najdete v tématu [nezpracované dotazy SQL](https://msdn.microsoft.com/data/jj592907) na webové stránce MSDN.

## <a name="no-tracking-queries"></a>Sledování bez dotazy

Když kontext databáze načte řádky tabulky, vytvoří objekty entity, které představují je ve výchozím nastavení se uchovává informace o zda entity v paměti jsou synchronizované s tím, co je v databázi. Data v paměti funguje jako mezipaměť a používá se při aktualizaci entity. Tento ukládání do mezipaměti je často zbytečné ve webové aplikaci, protože kontextu instance jsou krátkodobé a jednorázové obvykle (nový je vytvořen a uvolnění pro každý požadavek) a kontextu, která načte entity je obvykle uvolněn předtím, než je znovu použít danou entitu.

Sledování objektů entit v paměti můžete zakázat s použitím [AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx) metody. Typické scénáře, ve kterých můžete chtít provést, patří:

- Dotaz načte velké objemy dat, která vypnutí sledování může výrazně zvýšit výkon.
- Chcete připojit entitu, aby bylo možné ji aktualizovat, ale jste dříve získali stejné entity k jinému účelu. Vzhledem k tomu, že entita již sledován správou kontext databáze, nelze připojit entitu, kterou chcete změnit. Jeden způsob, jak tuto situaci je použít `AsNoTracking` možnost s předchozí dotaz.

Příklad, který ukazuje, jak používat [AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx) metodu, najdete v článku [starší verzi tohoto kurzu](../../older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application.md). Tato verze tohoto kurzu není nastavíte příznak změněné vytvořené vazače modelu entity v metodě upravit tak, aby nepotřebuje `AsNoTracking`.

## <a name="examine-sql-sent-to-database"></a>Prozkoumejte odeslán do databáze SQL

Někdy je vhodné se může zobrazit skutečné dotazy SQL, které se odesílají do databáze. V předchozí lekci jste viděli, jak to udělat v kódu zachycování; Nyní uvidíte některé způsoby, jak provést bez psaní kódu zachycování. Pokud to pokud chcete vyzkoušet si, budete podívejte se na jednoduchý dotaz a podívejte se na co se stane do ní, jak přidat možnosti takové eager načítání, filtrování a řazení.

V *řadiče/CourseController*, nahraďte `Index` metodu s následujícím kódem, pokud chcete dočasně zastavit předběžné načítání:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample6.cs)]

Nyní nastavte zarážku na `return` – příkaz (F9 umístěte kurzor na tento řádek). Stisknutím klávesy **F5** a spusťte projekt v režimu ladění, vyberte kurzu indexovou stránku. Pokud kód dosáhne zarážky, podívejte se `sql` proměnné. Zobrazí se dotaz, který je odeslána do systému SQL Server. Je jednoduchý `Select` příkazu.

[!code-json[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample7.json)]

Klikněte na lupu zobrazit dotaz **Vizualizátor textu**.

![](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image10.png)

Nyní přidáte rozevíracího seznamu na kurzy indexovou stránku tak, aby uživatelé mohou filtrovat pro konkrétní oddělení. Kurzy vám seřadit podle názvu, a specifikujete, nemůžou dočkat, až načítání pro `Department` navigační vlastnost.

V *CourseController.cs*, nahraďte `Index` metodu s následujícím kódem:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample8.cs)]

Obnovit na zarážku `return` příkazu.

Metoda přijímá vybranou hodnotu v rozevíracím seznamu v `SelectedDepartment` parametru. Pokud není nic vybráno, bude mít tento parametr hodnotu null.

A `SelectList` kolekce obsahující všechna oddělení je předána do zobrazení pro rozevíracího seznamu. Parametry předávané `SelectList` konstruktor zadejte název hodnoty pole, název textového pole a vybrané položky.

Pro `Get` metodu `Course` úložiště, kód určuje výraz filtru, pořadí řazení a eager načítání pro `Department` navigační vlastnost. Výraz filtru vždy vrátí `true` Pokud není proveden žádný výběr v rozevíracím seznamu (to znamená, `SelectedDepartment` má hodnotu null).

V *Views\Course\Index.cshtml*, bezprostředně před zahájením `table` značky, přidejte následující kód k vytvoření rozevíracího seznamu a tlačítka Odeslat:

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample9.cshtml)]

S zarážka nastavena, spusťte kurz indexovou stránku. Pokračujte až do doby první, že kód narazí na zarážku, tak, aby stránka se zobrazí v prohlížeči. Vyberte oddělení z rozevíracího seznamu a klikněte na tlačítko **filtr**.

Tentokrát pro oddělení dotaz pro rozevírací seznam bude k první zarážce. Přeskočit a zobrazit `query` proměnné při příštím kód dosáhne zarážky Chcete-li zobrazit, co `Course` dotazu teď vypadá podobně jako. Zobrazí se vám něco jako toto:

[!code-sql[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample10.sql)]

Uvidíte, že dotaz je teď `JOIN` dotaz, který načte `Department` dat spolu s `Course` dat a že obsahuje `WHERE` klauzuli.

Odeberte `var sql = courses.ToString()` řádku.

## <a name="create-an-abstraction-layer"></a>Vytvořit vrstvu HAL

Mnoho vývojářů napsat kód pro implementaci úložiště a jednotky pracovních vzorů jako obálka kolem kód, který funguje s Entity Framework. Tyto vzory jsou určená k vytvoření abstraktní vrstvu mezi vrstva přístupu k datům a vrstvu obchodní logiky aplikace. Implementaci těchto vzorců může pomoci izolovat aplikace před změnami v úložišti dat a mohou usnadnit automatizované testování částí nebo testy řízený vývoj (TDD). Ale psaním dalšího kódu k implementaci těchto vzorců není vždy nejlepší volbou pro aplikace, které používají EF, z několika důvodů:

- Vlastní třídy kontextu EF insulates kódu z konkrétní data úložiště kódu.
- Třídy kontextu EF může fungovat jako třída jednotek práce pro databázi aktualizace, který používáte pomocí EF.
- Vlastnosti představené v Entity Framework 6 snadněji implementovat TDD bez psaní kódu v úložišti.

Další informace o tom, jak implementovat úložiště a jednotky pracovních vzorů najdete v tématu [verze Entity Framework 5 v této sérii kurzů](../../older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md). Informace o způsoby, jak implementovat TDD v Entity Framework 6 najdete v článku na následujících odkazech:

- [Jak EF6 umožňuje napodobování DbSets snadněji](http://thedatafarm.com/data-access/how-ef6-enables-mocking-dbsets-more-easily/)
- [Testování s napodobování framework](https://msdn.microsoft.com/data/dn314429)
- [Testování s čísly typu Double vlastní test](https://msdn.microsoft.com/data/dn314431)

<a id="proxies"></a>

## <a name="proxy-classes"></a>Třídy proxy

Když rozhraní Entity Framework vytváří instance entity (například při spuštění dotazu), často vytvoří jako instance dynamicky generované odvozeného typu, který funguje jako proxy pro entitu. Příklad najdete v následujících dvou obrázcích ladicího programu. Na prvním obrázku můžete vidět, že `student` proměnná je očekávané `Student` zadejte ihned poté, co vytvoříte instanci entity. Druhý obrázek po EF se použil ke čtení entit studentů z databáze, se zobrazí třídu proxy.

![Před třídu proxy](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image12.png)

![Po třídu proxy](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image13.png)

Tato třída proxy serveru přepíše některé virtuální vlastnosti entity, chcete-li vložit háky pro provádění akcí automaticky při přístupu k vlastnosti. Jedna funkce použita pro tento mechanismus je opožděné načtení.

Ve většině případů není nutné mít na paměti toto použití proxy, ale existují výjimky:

- V některých případech můžete chtít zabránit ve vytváření instancí proxy Entity Framework. Například při při serializaci entit obvykle chcete třídy POCO, nikoliv třídy proxy serveru. Jedním ze způsobů, aby nedocházelo k problémům serializace je určená k serializaci objektů pro přenos dat (DTO) namísto objekty entity, jak je znázorněno [pomocí webového rozhraní API s Entity Framework](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-1.md) kurzu. Dalším způsobem je [zakázat vytváření proxy](https://msdn.microsoft.com/data/jj592886.aspx).
- Při vytváření instance třídy entity pomocí `new` operátoru, nebudete mít instanci proxy serveru. To znamená, že se že vám funkce, jako je opožděné načtení a automatické sledování změn. Obvykle je to v pořádku; obvykle není nutné opožděné načtení, vzhledem k tomu, že vytváříte novou entitu, která není v databázi, a obvykle není nutné řešení change tracking, pokud jste explicitní označení entity jako `Added`. Ale pokud potřebujete opožděné načtení a potřebujete řešení change tracking, můžete vytvořit nové instance entity s použitím proxy servery [vytvořit](https://msdn.microsoft.com/library/gg679504.aspx) metodu `DbSet` třídy.
- Můžete chtít získat typ skutečné entity z typ proxy serveru. Můžete použít [Metoda GetObjectType](https://msdn.microsoft.com/library/system.data.objects.objectcontext.getobjecttype.aspx) metodu `ObjectContext` třídy pro získání skutečné entity typu instance typu proxy serveru.

Další informace najdete v tématu [práce s proxy](https://msdn.microsoft.com/data/JJ592886.aspx) na webové stránce MSDN.

## <a name="automatic-change-detection"></a>Automaticky změnit zjišťování

Entity Framework Určuje, jak se entity změnil (a proto aktualizací musí být odeslán do databáze) srovnáním aktuální entity s původními hodnotami. Původní hodnoty jsou uloženy při entity je dotazovat nebo připojen. Některé metody, které způsobí automatickou změnu zjišťování jsou následující:

- `DbSet.Find`
- `DbSet.Local`
- `DbSet.Remove`
- `DbSet.Add`
- `DbSet.Attach`
- `DbContext.SaveChanges`
- `DbContext.GetValidationErrors`
- `DbContext.Entry`
- `DbChangeTracker.Entries`

Pokud sledujete velké množství entit a volání jedné z těchto metod v mnoha případech ve smyčce, můžete dosáhnout výrazné zlepšení výkonu dočasně vypnout automatickou změnu pomocí zjišťování [AutoDetectChangesEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.autodetectchangesenabled.aspx) vlastnost. Další informace najdete v tématu [automaticky Detekovaly změny](https://msdn.microsoft.com/data/jj556205) na webové stránce MSDN.

## <a name="automatic-validation"></a>Automatické ověřování

Při volání `SaveChanges` metody, ve výchozím nastavení rozhraní Entity Framework ověří data ve všech vlastností veškeré entitám před aktualizací databáze. Pokud jste aktualizovali velké množství entit a jste už ověřili dat, není nutné tuto práci a můžete provést proces ukládání pak změny budou méně času tak, že dočasně vypnete ověřování. Můžete provést, že při použití [ValidateOnSaveEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.validateonsaveenabled.aspx) vlastnost. Další informace najdete v tématu [ověření](https://msdn.microsoft.com/data/gg193959) na webové stránce MSDN.

## <a name="entity-framework-power-tools"></a>Entity Framework Power Tools

[Entity Framework Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) je doplněk sady Visual Studio, který byl použit k vytváření diagramů datový model je znázorněno v těchto kurzech. Nástroje můžete provést i jiné funkce, jako jsou vytvoření tříd entit založené na tabulkách v existující databázi tak, aby databáze můžete používat s Code First. Po instalaci nástrojů se zobrazí některé další možnosti v kontextové nabídky. Například když kliknete pravým tlačítkem na váš context – třída v **Průzkumníka řešení**, se zobrazí a **Entity Framework** možnost. To vám umožňuje generovat diagram. Pokud používáte Code First nelze změnit datový model v diagramu, ale můžete přesunout objekty aby bylo snazší porozumět.

![EF diagram](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image15.png)

## <a name="entity-framework-source-code"></a>Entity Framework zdrojového kódu

Zdrojový kód pro Entity Framework 6 je k dispozici na [Githubu](https://github.com/aspnet/EntityFramework6). Můžete hlášení chyb a může přispět ke zdrojovému kódu EF vlastní rozšíření.

I když se zdrojový kód je otevřený, Entity Framework plně podporovat jako produkt společnosti Microsoft. Tým Microsoft Entity Framework udržuje ovládacího prvku nad tím, které jsou přijaty příspěvky a testy k zajištění kvality každé vydané verze všech změn kódu.

## <a name="acknowledgments"></a>Potvrzení

- Petr Dykstra napsal původní verzi tohoto kurzu, spoluautorem aktualizace EF 5 a napsal EF 6 aktualizace. Petr je vedoucí pro programování zápis na webovou platformu společnosti Microsoft a týmu obsahu nástroje.
- [Rick Anderson](https://blogs.msdn.com/b/rickandy/) (twitter [ @RickAndMSFT ](http://twitter.com/RickAndMSFT)) nebyla většinu práce aktualizuje kurz EF 5 a MVC 4 a spoluautorem EF 6 aktualizace. Rick je vedoucí pro programování zápis pro společnost Microsoft, zaměřuje se na Azure a MVC.
- [Rowan Miller](http://www.romiller.com) a ostatní členové týmu, Entity Framework s asistencí s revizemi kódu a pomohly ladění mnoho problémů s migrace, které vznikly během kurzu jsme byly aktualizace pro EF 5 a EF 6.

## <a name="troubleshoot-common-errors"></a>Řešení běžných chyb

### <a name="cannot-createshadow-copy"></a>Nelze vytvořit/stínové kopie

Chybová zpráva:

> Nejde vytvořit nebo stínovou kopii "&lt;filename&gt;' Pokud tento soubor již existuje.

Řešení

Počkejte několik sekund a aktualizujte stránku.

### <a name="update-database-not-recognized"></a>Aktualizace databáze nebyl rozpoznán

Chybová zpráva (z `Update-Database` příkazu v konzole PMC):

> Termín 'Aktualizace databáze' nebyl rozpoznán jako název rutiny, funkce, soubor skriptu nebo spustitelného programu. Zkontrolujte, zda název, nebo pokud cesty byl zahrnut, ověřte správnost cesty a zkuste to znovu.

Řešení

Ukončení sady Visual Studio. Znovu otevřít projekt a zkuste to znovu.

### <a name="validation-failed"></a>Ověření se nezdařilo

Chybová zpráva (z `Update-Database` příkazu v konzole PMC):

> Ověření se nezdařilo pro jednu nebo více entit. Viz vlastnost 'EntityValidationErrors' Další podrobnosti.

Řešení

Jeden příčinou tohoto problému je chyby ověřování při `Seed` metoda spuštění. Naleznete v tématu [financování a ladění Entity Framework (EF) databází](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) tipy pro ladění `Seed` metody.

### <a name="http-50019-error"></a>HTTP Error 500.19 chyba

Chybová zpráva:

> Chyba protokolu HTTP 500.19 – vnitřní chyba serveru požadovanou stránku nelze získat přístup, protože související konfigurační data pro stránky je neplatná.

Řešení

Jedním ze způsobů, které se zobrazí tato chyba je nemusíte několik kopií řešení, každý z nich používat stejné číslo portu. Obvykle můžete tento problém vyřešit ukončením všechny instance sady Visual Studio a poté restartuje projektu, na kterém právě pracujete. Pokud to nefunguje, zkuste změnit číslo portu. Klikněte pravým tlačítkem na soubor projektu a pak klikněte na vlastnosti. Vyberte **webové** kartu a potom změňte číslo portu **adresa Url projektu** textového pole.

### <a name="error-locating-sql-server-instance"></a>Chyba vyhledáním instanci systému SQL Server

Chybová zpráva:

> Při navazování připojení k serveru SQL Server došlo k chybě související se sítí nebo s instancí. Server nebyl nalezen nebo nebyl přístupný. Ověřte, zda je název instance správný a, SQL Server je nakonfigurován pro povolit vzdálená připojení. (poskytovatel: Rozhraní sítě systému SQL, chyba: 26 - Chyba při vyhledávání zadaného serveru či Instance)

Řešení

Zkontrolujte připojovací řetězec. Pokud jste ručně odstranit databázi, změňte název databáze v řetězci konstrukce.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Další zdroje

 Další informace o tom, jak pracovat s daty pomocí Entity Frameworku, najdete v článku [EF stránky dokumentace na webu MSDN](https://msdn.microsoft.com/data/ee712907) a [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

Další informace o tom, jak nasadit webové aplikace po začlenění ho najdete v tématu [nasazení webu ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-web-deployment-content-map.md) v knihovně MSDN.

Informace o dalších témat souvisejících s MVC, jako je například ověřování a autorizaci, najdete v článku [ASP.NET MVC – doporučené zdroje informací](../recommended-resources-for-mvc.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Prováděné nezpracované dotazy SQL
> * Prováděné dotazy bez sledování
> * Přezkoumán dotazy SQL odeslané do databáze

Také jste se naučili o:

> [!div class="checklist"]
> * Vytváření abstraktní vrstvu
> * Třídy proxy
> * Automaticky změnit zjišťování
> * Automatické ověřování
> * Entity Framework Power Tools
> * Entity Framework zdrojového kódu

Dokončení tohoto postupu Tato série kurzů týkající se použití rozhraní Entity Framework v aplikaci ASP.NET MVC. Pokud chcete další informace o EF Database First, přečtěte si téma DB první série kurzů.
> [!div class="nextstepaction"]
> [První databáze Entity Framework](../database-first-development/setting-up-database.md)
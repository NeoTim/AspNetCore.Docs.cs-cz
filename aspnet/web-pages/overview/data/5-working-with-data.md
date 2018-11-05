---
uid: web-pages/overview/data/5-working-with-data
title: Úvod k práci s databází v rozhraní ASP.NET Web Pages servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola popisuje, jak přistupovat k datům z databáze a zobrazení pomocí rozhraní ASP.NET Web Pages.
ms.author: riande
ms.date: 02/18/2014
ms.assetid: 673d502f-2c16-4a6f-bb63-dbfd9a77ef47
msc.legacyurl: /web-pages/overview/data/5-working-with-data
msc.type: authoredcontent
ms.openlocfilehash: a688761c87376aa93463c13eaa07858d3acb9dc2
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021687"
---
<a name="introduction-to-working-with-a-database-in-aspnet-web-pages-razor-sites"></a>Úvod k práci s databází v rozhraní ASP.NET Web Pages servery (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje, jak vytvořit databázi na webu rozhraní ASP.NET Web Pages (Razor) pomocí nástroje Microsoft WebMatrix a vytvoření stránek, které umožňují zobrazit, přidat, upravit nebo odstranit data.
> 
> **Co se dozvíte:** 
> 
> - Jak vytvořit databázi.
> - Jak se připojit k databázi.
> - Jak zobrazit data na webové stránce.
> - Jak vkládat, aktualizovat a odstraňovat záznamy v databázi.
> 
> Toto jsou vlastnosti představené v následujícím článku:
> 
> - Práce s databází Microsoft SQL Server Compact Edition.
> - Práce s dotazy SQL.
> - `Database` Třídy.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Služba WebMatrix 2
>   
> 
> V tomto kurzu funguje taky pomocí služby WebMatrix 3. Můžete použít 3 webových stránek ASP.NET a Visual Studio 2013 (nebo Visual Studio Express 2013 for Web); uživatelské rozhraní, ale bude lišit.


## <a name="introduction-to-databases"></a>Úvod do databáze

Představte typické adresáře. Pro každou položku v adresáři (to znamená, že pro každou osobu) mají různé druhy informace, jako je jméno, příjmení, adresu, e-mailovou adresu a telefonní číslo.

Typické způsob, jak data obrázku tímto způsobem je jako tabulku s řádky a sloupce. V podmínkách databáze každý řádek se často označuje jako záznam. Každý sloupec (někdy označované jako pole) obsahuje hodnotu pro každý typ hledaných dat: křestní jméno, poslední název a tak dále.

| **ID** | **Jméno** | **LastName** | **Adresa** | **E-mail** | **Telefon** |
| --- | --- | --- | --- | --- | --- |
| 1 | Jan | Abrus | 210 100th St SE Orcas WA 98031 | jim@contoso.com | 555 0100 |
| 2 | Terry | Adams | 1234 hlavní St. Seattle WA 99011 | terry@cohowinery.com | 555 0101 |

Pro většinu tabulky databáze v tabulce musí mít sloupec obsahující jedinečný identifikátor, jako je číslo, číslo účtu atd. To se označuje jako v tabulce *primární klíč*, a můžete ji použít k identifikaci každý řádek v tabulce. V tomto příkladu je ID sloupce primární klíč pro adresář.

Pomocí této základní znalosti o databáze, jste připraveni zjistěte, jak vytvořit jednoduchou databázi a provádět operace, jako jsou přidání, úprava nebo odstranění data.

> [!TIP] 
> 
> **Relační databáze**
> 
> Ukládejte data v mnoha způsoby, včetně textových souborů a tabulek. Pro většinu obchodních použití ale data se ukládají v relační databázi.
> 
> Tento článek není velmi hluboce přejděte do databáze. Ale můžete se setkat se vás seznámí s tím něco o nich. V relační databázi je informace logicky rozdělena do samostatných tabulek. Například databázi pro školy může obsahovat samostatných tabulek pro studenty a pro nabídky třídy. Databáze softwaru (jako je SQL Server) podporuje výkonné příkazy, které vám umožní dynamicky vytvořit relace mezi tabulkami. Můžete například relační databáze k navázání vztahu logické mezi studentů a zařízení, chcete-li vytvořit plán. Ukládání dat v samostatných tabulkách zjednodušuje struktura tabulky a snižuje nutnost zachovat redundantních dat v tabulkách.


## <a name="creating-a-database"></a>Vytvoření databáze

Tento postup ukazuje, jak vytvořit databázi s názvem SmallBakery s použitím nástroje návrh databáze SQL Server Compact, která je součástí služby WebMatrix. Přestože je možné vytvořit databázi pomocí kódu, je obvyklejší k vytvoření databáze a tabulky databáze pomocí nástroje návrhu, jako jsou služby WebMatrix.

1. Spusťte službu WebMatrix a klikněte na stránce Rychlý Start **šablony z webu**.
2. Vyberte **prázdný web**a **název lokality** pole zadejte "SmallBakery" a potom klikněte na tlačítko **OK**. Web se vytvoří a zobrazí ve službě WebMatrix.
3. V levém podokně klikněte **databází** pracovního prostoru.
4. Na pásu karet klikněte na tlačítko **novou databázi**. Vytvoření prázdné databáze se stejným názvem jako Web.
5. V levém podokně rozbalte **SmallBakery.sdf** uzlu a pak klikněte na tlačítko **tabulky**.
6. Na pásu karet klikněte na tlačítko **nová tabulka**. Služba WebMatrix otevření Návrháře tabulky.

    ![[image]](5-working-with-data/_static/image1.jpg)
7. Klikněte na tlačítko v **název** sloupci a zadejte &quot;Id&quot;.
8. V **datový typ** sloupci vyberte **int**.
9. Nastavte **je primární klíč?** a **je identifikovat?** možnosti k **Ano**.

    Jak název napovídá, **je primární klíč** říká databáze, že toto bude primární klíč v tabulce. **Je identita** říká databáze mohla automaticky vytvářet identifikační číslo pro každý nový záznam a k ní přiřadit další pořadové číslo (počínaje 1).
10. Klikněte na další řádek. Editor spustí novou definici sloupce.
11. Zadejte hodnotu názvu &quot;název&quot;.
12. Pro **datový typ**, zvolte &quot;nvarchar&quot; a nastavit délku až 50. *Var* součástí `nvarchar` říká databáze, že data pro tento sloupec bude řetězec, jehož velikost může lišit od záznamu. ( *n* předpony představuje *národní*, označující, že pole může obsahovat textových dat, která představuje všechny abecedy nebo systému zápisu &#8212; to znamená, že pole obsahuje data ve formátu Unicode.)
13. Nastavte **Allow Nulls** umožňuje **ne**. Tím se vynutí *název* sloupec není ponecháno prázdné.
14. Pomocí tohoto stejného procesu vytvořit sloupec s názvem *popis*. Nastavte **datový typ** "nvarchar" a 50 pro délku a nastavte **Allow Nulls** na hodnotu false.
15. Vytvořit sloupec s názvem *cena*. Nastavte **datový typ "money"** a nastavte **Allow Nulls** na hodnotu false.
16. Do pole v horní části název tabulky &quot;produktu&quot;.

    Jakmile budete hotovi, definice bude vypadat například takto:

    ![[image]](5-working-with-data/_static/image2.jpg)
17. Stisknutím kláves Ctrl + S uložte tabulku.

## <a name="adding-data-to-the-database"></a>Přidání dat do databáze

Nyní můžete přidat nějaká ukázková data do databáze, kterou budete pracovat později v tomto článku.

1. V levém podokně rozbalte **SmallBakery.sdf** uzlu a pak klikněte na tlačítko **tabulky**.
2. Klikněte pravým tlačítkem na tabulku produktů a potom klikněte na tlačítko **Data**.
3. V podokně úpravy zadejte následující záznamy:

    | **Jméno** | **Popis** | **Cena** |
    | --- | --- | --- |
    | Chléb | Dokončené čerstvé každý den. | 2.99 |
    | Strawberry Shortcake | S organickým jahody provedené v našich zahrada. | 9.99 |
    | Apple výsečový | Druhý pouze na výseč vaše máma. | 12.99 |
    | Pecan výsečový | Pokud chcete pecans, je to za vás. | 10.99 |
    | Citronový výsečový | Provedené s nejlepší citrony na světě. | 11.99 |
    | Cupcakes | Dětem a dětský v vám budou líbit tyto. | 7.99 |

    Mějte na paměti, že nemusíte zadávat nic pro *Id* sloupce. Při vytváření *Id* sloupce, nastavíte její **je identita** vlastnost na hodnotu true, což způsobí, že se automaticky vyplní.

    Jakmile budete hotovi, zadávání dat, Návrhář tabulky bude vypadat například takto:

    ![[image]](5-working-with-data/_static/image3.jpg)
4. Zavřete kartu, která obsahuje data databáze.

## <a name="displaying-data-from-a-database"></a>Zobrazení dat z databáze

Jakmile máte databázi s daty v něm můžete zobrazit data na webové stránce ASP.NET. Pro výběr řádků tabulky, chcete-li zobrazit, použijte příkaz SQL, který je příkaz, který můžete předat do databáze.

1. V levém podokně klikněte **soubory** pracovního prostoru.
2. V kořenovém adresáři webu, vytvoří novou stránku CSHTML s názvem *ListProducts.cshtml*.
3. Nahraďte existující kód následujícím kódem:

    [!code-cshtml[Main](5-working-with-data/samples/sample1.cshtml)]

    V prvním bloku kódu, otevřete *SmallBakery.sdf* souboru (databáze), který jste vytvořili dříve. `Database.Open` Metoda předpokládá, že *SDF* soubor je na vašem webu *aplikace\_Data* složky. (Všimněte si, že není nutné určit *SDF* rozšíření &#8212; ve skutečnosti, pokud to uděláte, `Open` metoda nebude fungovat.)

    > [!NOTE]
    > *Aplikace\_Data* složka je zvláštní v technologii ASP.NET, který se používá k ukládání dat souborů. Další informace najdete v tématu [připojení k databázi](#SB_ConnectingToADatabase) dále v tomto článku.

    Pak vytvoříte žádost o k dotazování databáze pomocí následující příkaz SQL `Select` – příkaz:

    [!code-sql[Main](5-working-with-data/samples/sample2.sql)]

    V příkazu `Product` identifikuje tabulku pro dotaz. `*` Znaků určuje, že dotaz by měl vrátit všechny sloupce z tabulky. (Může být také seznam sloupců jednotlivě, oddělených čárkami, pokud chcete zobrazit jenom některé sloupce.) `Order By` Klauzuli Určuje, jak mají být řazeny data &#8212; v tomto případě podle *název* sloupce. To znamená, že data seřadí podle abecedy podle hodnoty *název* sloupec pro každý řádek.

    V těle stránky značky vytvoří tabulku HTML, který se použije pro zobrazení údajů. Uvnitř `<tbody>` elementu, je použít `foreach` smyčky zobrazíte jednotlivě každý řádek dat, která je vrácena dotazem. Pro každý řádek dat vytvořit řádek tabulky HTML (`<tr>` element). Poté vytvoříte buněk tabulky HTML (`<td>` elementy) pro každý sloupec. Pokaždé, když procházejí smyčky, probíhá na další dostupný řádek z databáze `row` proměnné (můžete nastavit v `foreach` příkaz). Chcete-li získat jednotlivé sloupce z řádku, můžete použít `row.Name` nebo `row.Description` nebo bez ohledu název je ve sloupci.
4. Spuštění stránky v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.) Na stránce se zobrazí seznam vypadat asi takto:

    ![[image]](5-working-with-data/_static/image4.jpg)

> [!TIP] 
> 
> **Structured Query Language (SQL)**
> 
> SQL je jazyk, který se používá v Většina relačních databází pro správu dat v databázi. Obsahuje příkazy, které umožňují načtení dat a aktualizovat je a, které umožňují vytvářet, upravovat a spravovat databázové tabulky. SQL se liší od programovací jazyk (jako ten, že používáte v nástroji WebMatrix) vzhledem k tomu, že pomocí jazyka SQL, cílem je, že dáte databáze, co chcete a je databáze úlohu zjistit, jak získat data nebo provést úlohu. Tady jsou příklady některých příkazů SQL a jejich význam:
> 
> `SELECT Id, Name, Price FROM Product WHERE Price > 10.00 ORDER BY Name`
> 
> To načítá *Id*, *název*, a *cena* sloupce ze záznamů *produktu* tabulky, pokud hodnota *cena* je více než 10 a vrátí výsledky v abecedním pořadí podle hodnoty *název* sloupce. Tento příkaz vrátí sadu výsledků dotazu, který obsahuje záznamy, které splňují kritéria nebo prázdnou sadou, pokud neodpovídají žádné záznamy.
> 
> `INSERT INTO Product (Name, Description, Price) VALUES ("Croissant", "A flaky delight", 1.99)`
> 
> Vloží nový záznam do *produktu* tabulky, nastavení *název* sloupec &quot;Croissant&quot;, *popis* sloupec &quot; Nejednoznačné naprostou spokojenost&quot;a cena 1,99.
> 
> `DELETE FROM Product WHERE ExpirationDate < "01/01/2008"`
> 
> Tento příkaz odstraní záznamy *produktu* tabulky, jehož sloupec Datum vypršení platnosti je starší než 1. ledna 2008. (Předpokladem je, že *produktu* tabulka obsahuje sloupce, samozřejmě.) Datum je ve formátu MM/DD/RRRR tady zadáte, ale by měly být zadány ve formátu, který se používá pro vaše národní prostředí.
> 
> `Insert Into` a `Delete` příkazy nevracejte sad výsledků dotazu. Místo toho vrátí číslo, které se říká, kolik záznamů byly ovlivněny příkazu.
> 
> Pro některé z těchto operací (např. vkládání a odstranění záznamů) proces, který žádá o operaci musí mít příslušná oprávnění v databázi. To je důvod, proč pro produkční databáze je často nutné zadat uživatelské jméno a heslo při připojování k databázi.
> 
> Existují desítek příkazů SQL, ale všechny se řídí vzorem následujícím způsobem. Příkazy SQL můžete použít k vytvoření databázových tabulek, počet záznamů v tabulce, výpočtu ceny a provádět mnoho dalších operacích.


## <a name="inserting-data-in-a-database"></a>Vložení dat do databáze

Tato část ukazuje, jak vytvořit stránku, která umožňuje uživatelům přidání nového produktu do *produktu* databázové tabulky. Po vložení nového záznamu produktu na stránce se zobrazí aktualizovaná tabulka pomocí *ListProducts.cshtml* stránku, kterou jste vytvořili v předchozí části.

Na stránce zahrnuje ověření, abyste měli jistotu, že data, která uživatel zadá je platný pro databázi. Například kód na stránce zajišťuje, že byla zadána hodnota pro všechny požadované sloupce.

1. Na webu vytvořte nový soubor CSHTML *InsertProducts.cshtml*.
2. Nahraďte existující kód následujícím kódem:

    [!code-cshtml[Main](5-working-with-data/samples/sample3.cshtml)]

    Tělo stránky obsahuje formulář HTML s tři textová pole, které umožňují uživateli zadat název, popis a cena. Když uživatelé kliknou **vložit** tlačítko, kód v horní části stránky otevře připojení k *SmallBakery.sdf* databáze. Potom získat hodnoty, které uživatel odeslal pomocí `Request` objektu a přiřadit tyto hodnoty místní proměnné.

    K ověření, zda uživatel zadal hodnotu pro každý požadovaný sloupec, zaregistrujete, každý `<input>` element, který chcete ověřit:

    [!code-csharp[Main](5-working-with-data/samples/sample4.cs)]

    `Validation` Kontroluje pomocná rutina, že je hodnota v každé pole, které jste registrovali. Můžete zkontrolovat, jestli všechna pole zdařilo ověření tak, že zkontrolujete `Validation.IsValid()`, který to obvykle děláte za před zpracováním informace získáte od uživatele:

    [!code-csharp[Main](5-working-with-data/samples/sample5.cs)]

    ( `&&` Znamená, že operátor AND – tento test je *Pokud se odeslání formuláře a všechna pole prošly ověření*.)

    Je-li ověřit všechny sloupce (žádné byly prázdné), pokračujte a vytvořit dotaz SQL vložte data a pak spustit jak je ukázáno dále:

    [!code-csharp[Main](5-working-with-data/samples/sample6.cs)]

    Zadání hodnot pro vložení, zahrnete parametr zástupné symboly (`@0`, `@1`, `@2`).

    > [!NOTE]
    > Jako bezpečnostní opatření vždy předejte hodnoty příkazu SQL pomocí parametrů, jako v předchozím příkladu. To vám dává možnost ověřit data uživatele, a navíc pomáhá chránit proti pokusům o odesílání škodlivých příkazů do databáze (někdy označované jako útoky prostřednictvím injektáže SQL).

    K provedení dotazu, použijete tento příkaz předání proměnných, které obsahují hodnoty, které mají nahraďte zástupné symboly:

    [!code-csharp[Main](5-working-with-data/samples/sample7.cs)]

    Po `Insert Into` po provedení příkazu, odešlete mu na stránce, která zobrazuje seznam produktů, pomocí tohoto řádku:

    [!code-javascript[Main](5-working-with-data/samples/sample8.js)]

    Pokud ověření nebylo úspěšné, můžete přeskočit, insert. Místo toho máte pomocné rutiny na stránce, která může zobrazovat souhrnné chybové zprávy (pokud existuje):

    [!code-cshtml[Main](5-working-with-data/samples/sample9.cshtml)]

    Všimněte si, že blok stylu v kódu obsahuje definice třídy šablony stylů CSS s názvem `.validation-summary-errors`. Toto je název třídy CSS, který se používá ve výchozím nastavení `<div>` element, který obsahuje všechny chyby ověření. V tomto případě určuje třídu šablony stylů CSS souhrnu chyb při ověřování se zobrazí červeně a tučně, ale můžete definovat `.validation-summary-errors` třídu veškeré formátování vám vyhovuje.

### <a name="testing-the-insert-page"></a>Testování stránky vložení

1. Zobrazte stránku v prohlížeči. Na stránce zobrazí formulář, který je podobný ten, který je znázorněno na následujícím obrázku.

    ![[image]](5-working-with-data/_static/image5.jpg)
2. Zadejte hodnoty pro všechny sloupce, ale ujistěte se, že necháte *cena* sloupce prázdné.
3. Klikněte na tlačítko **vložit**. Na stránce zobrazí chybovou zprávu, jak je znázorněno na následujícím obrázku. (Žádný nový záznam je vytvořený.)

    ![[image]](5-working-with-data/_static/image6.jpg)
4. Zcela vyplňte formulář a potom klikněte na tlačítko **vložit**. Tentokrát *ListProducts.cshtml* stránky se zobrazí a zobrazí nový záznam.

## <a name="updating-data-in-a-database"></a>Aktualizace dat v databázi

Poté, co byl zadán data do tabulky, můžete potřebovat ji aktualizovat. Tento postup ukazuje, jak vytvořit dvě stránky, které jsou podobné jako ty, které jste vytvořili pro vkládání dat výše. První stránka zobrazí produkty a umožňuje uživateli vybrat z nich se má změnit. Na druhé stránce umožňuje uživatelům ve skutečnosti proveďte změny a uložte je.

> [!NOTE] 
> 
> **Důležité** do produkční webové stránky, obvykle omezíte kdo má povoleno provádět změny na data. Informace o tom, jak nastavit členství a způsoby, jak autorizovat uživatele k provádění úloh na webu, naleznete v tématu [přidání zabezpečení a s členstvím na server webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202904).


1. Na webu vytvořte nový soubor CSHTML *EditProducts.cshtml*.
2. Nahraďte stávající kód v souboru následujícím kódem:

    [!code-cshtml[Main](5-working-with-data/samples/sample10.cshtml)]

    Jediným rozdílem mezi tuto stránku a *ListProducts.cshtml* stránky z dříve je, že tabulka HTML na této stránce obsahuje další sloupec, který zobrazuje **upravit** odkaz. Po kliknutí na tento odkaz vám trvá *UpdateProducts.cshtml* stránce (která vytvoříte vedle) ve kterém můžete upravit vybraný záznam.

    Podívejte se na kód, který vytvoří **upravit** odkaz:

    [!code-cshtml[Main](5-working-with-data/samples/sample11.cshtml)]

    Tím se vytvoří HTML `<a>` elementu jehož `href` atribut je nastaven dynamicky. `href` Atribut určuje stránku má zobrazit, když uživatel klikne na odkaz. Také předá `Id` hodnotu aktuálního řádku propojení. Při spuštění stránky zdroj stránky může obsahovat odkazy, jako jsou tyto:

    [!code-html[Main](5-working-with-data/samples/sample12.html)]

    Všimněte si, `href` atribut je nastaven na `UpdateProducts/n`, kde *n* je číslo produktu. Když uživatel klikne na jednu z těchto odkazů, bude Výsledná adresa URL bude vypadat přibližně takto:

    `http://localhost:18816/UpdateProducts/6`

    Číslo produktu, který má být upraven jinými slovy, budou předány v adrese URL.
3. Zobrazte stránku v prohlížeči. Na stránce zobrazí data ve formátu tímto způsobem:

    ![[image]](5-working-with-data/_static/image7.jpg)

    V dalším kroku vytvoříte stránky, která umožňuje uživatelům aktualizovat data ve skutečnosti. Stránka pro aktualizaci zahrnuje ověření ověření dat, který uživatel zadá. Například kód na stránce zajišťuje, že byla zadána hodnota pro všechny požadované sloupce.
4. Na webu vytvořte nový soubor CSHTML *UpdateProducts.cshtml*.
5. Nahraďte stávající kód v souboru následujícím kódem.

    [!code-cshtml[Main](5-working-with-data/samples/sample13.cshtml)]

    Těla stránky obsahuje formuláře HTML, kde se zobrazí produktu a kde mohou uživatelé upravovat ho. Získat produktu, který má zobrazit, použijte tento příkaz SQL:

    [!code-sql[Main](5-working-with-data/samples/sample14.sql)]

    To bude vyberte produkt, jejíž ID odpovídá hodnotě předané `@0` parametru. (Protože *Id* je primární klíč a proto musí být jedinečný, pouze jeden produkt záznamu je někdy možné vybrat tímto způsobem.) Získat hodnotu ID pro předání do to `Select` příkazu si můžete přečíst hodnotu, která je předána na stránku jako část adresy URL, pomocí následující syntaxe:

    [!code-csharp[Main](5-working-with-data/samples/sample15.cs)]

    Ve skutečnosti načíst záznam produktu, můžete použít `QuerySingle` metodu, která budou vracet jenom jeden záznam:

    [!code-csharp[Main](5-working-with-data/samples/sample16.cs)]

    Vrátí jeden řádek do `row` proměnné. Můžete získat data z každého sloupce a přiřadit ji k lokální proměnné následujícím způsobem:

    [!code-csharp[Main](5-working-with-data/samples/sample17.cs)]

    V kódu pro formulář tyto hodnoty se zobrazí automaticky v jednotlivých polí pomocí vloženého kódu takto:

    [!code-html[Main](5-working-with-data/samples/sample18.html)]

    Tuto část kódu zobrazí záznam produktu, který chcete aktualizovat. Jakmile se zobrazí záznam, uživatel může upravovat jednotlivé sloupce.

    Když uživatel formulář odešle kliknutím **aktualizace** kód v tlačítka `if(IsPost)` blok. Načte uživatele hodnoty z `Request` objektu, uloží hodnoty v proměnné a ověří, že každý sloupec byla vyplněna. V případě úspěšného ověření kódu vytvoří následující příkaz SQL pro sadu Vs11:

    [!code-sql[Main](5-working-with-data/samples/sample19.sql)]

    V SQL `Update` příkazu, zadejte každý sloupec k aktualizaci a nastavte ho na hodnotu. V tomto kódu jsou hodnoty zadané pomocí parametru zástupné symboly `@0`, `@1`, `@2`, a tak dále. (Jak je uvedeno výše, zabezpečení, by měl vždy předáváte hodnoty pro příkaz jazyka SQL s použitím parametrů.)

    Při volání `db.Execute` metodě předáte proměnné, které obsahují hodnoty v pořadí, ve kterém odpovídající parametrům v příkazu SQL:

    [!code-csharp[Main](5-working-with-data/samples/sample20.cs)]

    Po `Update` byl proveden příkaz, zavolat následující metodu, aby bylo možné přesměruje uživatele zpět na stránku pro úpravy:

    [!code-cshtml[Main](5-working-with-data/samples/sample21.cshtml)]

    Efekt je, že uživatel uvidí aktualizovaný seznam všech dat v databázi a můžete upravit jiného produktu.
6. Uložte na stránku.
7. Spustit *EditProducts.cshtml* stránky (ne aktualizace stránky) a pak klikněte na tlačítko **upravit** vyberte produkt, který má upravit. *UpdateProducts.cshtml* se zobrazí stránka zobrazující záznam, který jste vybrali.

    ![[image]](5-working-with-data/_static/image8.jpg)
8. Proveďte změnu a klikněte na tlačítko **aktualizace**. Znovu se zobrazí seznam produktů s aktualizovanými daty.

## <a name="deleting-data-in-a-database"></a>Odstranění dat v databázi

Tato část ukazuje, jak umožnit uživatelům odstranit produkt z *produktu* databázové tabulky. Příklad se skládá ze dvou stránkách. Na první stránce uživatelé vybrat záznam odstranit. Na druhé stránce, ve kterém je potvrďte, že chce odstranit záznam se následně zobrazí záznam, který chcete odstranit.

> [!NOTE] 
> 
> **Důležité** do produkční webové stránky, obvykle omezíte kdo má povoleno provádět změny na data. Informace o tom, jak nastavit členství a způsoby, jak autorizovat uživatele k provádění úloh na webu, naleznete v tématu [přidání zabezpečení a s členstvím na server webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202904).


1. Na webu vytvořte nový soubor CSHTML *ListProductsForDelete.cshtml*.
2. Nahraďte existující kód následujícím kódem:

    [!code-cshtml[Main](5-working-with-data/samples/sample22.cshtml)]

    Tato stránka je podobný *EditProducts.cshtml* z předchozí stránky. Však místo **upravit** odkaz pro jednotlivé produkty, se zobrazí **odstranit** odkaz. **Odstranit** je vytvořeno propojení pomocí následujícího kódu vložený v kódu:

    [!code-cshtml[Main](5-working-with-data/samples/sample23.cshtml)]

    Tím se vytvoří adresa URL, která vypadá takto, když uživatelé kliknou na odkaz:

    `http://<server>/DeleteProduct/4`

    Adresa URL volá stránku s názvem *DeleteProduct.cshtml* (která vytvoříte další) a předává je ID produktu, který se má odstranit (tady, 4).
3. Uložte soubor, ale nechte otevřené.
4. Vytvořte jiný soubor CHTML s názvem *DeleteProduct.cshtml*. Nahraďte existující obsah následujícím kódem:

    [!code-cshtml[Main](5-working-with-data/samples/sample24.cshtml)]

    Tato stránka je volán *ListProductsForDelete.cshtml* a umožňuje uživatelům potvrďte, že chce odstranit produkt. Pro zobrazení seznamu produktu, která se má odstranit, můžete získat ID produktu, který se má odstranit z adresy URL pomocí následujícího kódu:

    [!code-csharp[Main](5-working-with-data/samples/sample25.cs)]

    Na stránce pak žádá uživatele, klikněte na tlačítko ve skutečnosti záznam odstranit. To je důležité bezpečnostní opatření: při provádění operace citlivé na vašem webu, jako je aktualizace nebo odstranění dat, tyto operace by měla vždy provádět pomocí operace POST, není operace GET. Pokud váš web je nastaven tak, aby operace odstranění je možné provádět pomocí operace GET, všem uživatelům můžete předat adresu URL podobnou `http://<server>/DeleteProduct/4` a cokoli, co chtějí z databáze odstranit. Přidáním potvrzení a psaní kódu stránky tak, aby odstranění lze provést pouze pomocí příspěvek, přidejte míru zabezpečení vašeho webu.

    Operace odstranění skutečné se provádí pomocí následující kód, který nejdřív potvrdí, že se jedná o operaci post a zda není Identifikátor prázdný:

    [!code-csharp[Main](5-working-with-data/samples/sample26.cs)]

    Kód se spustí příkaz SQL, který odstraní zadaný záznam a pak přesměruje uživatele zpět na stránku pro výpis.
5. Spustit *ListProductsForDelete.cshtml* v prohlížeči.

    ![[image]](5-working-with-data/_static/image9.jpg)
6. Klikněte na tlačítko **odstranit** odkaz pro některý z produktů. *DeleteProduct.cshtml* potvrďte, že chcete odstranit tento záznam se zobrazí stránka.
7. Klikněte na tlačítko **odstranit** tlačítko. Odstranění záznamu produktu a na stránce aktualizují se aktualizované výpis.

> [!TIP]
> 
> <a id="SB_ConnectingToADatabase"></a>
> ### <a name="connecting-to-a-database"></a>Připojení k databázi
> 
> Můžete připojit k databázi dvěma způsoby. První je použití `Database.Open` metoda a zadat název souboru databáze (méně *SDF* rozšíření):
> 
> `var db = Database.Open("SmallBakery");`
> 
> `Open` Metoda předpokládá,. *SDF* soubor je na webu *aplikace\_Data* složky. Tato složka je určený speciálně pro obsahující data. Například má příslušná oprávnění k povolení webu ke čtení a zápisu dat a bezpečnostních opatření, služba WebMatrix neumožňuje přístup k souborům z této složky.
> 
> Druhý způsob je použít připojovací řetězec. Připojovací řetězec obsahuje informace o tom, jak se připojit k databázi. To může zahrnovat cestu k souboru nebo může obsahovat název databáze systému SQL Server na místním nebo vzdáleném serveru, spolu s uživatelské jméno a heslo pro připojení k tomuto serveru. (Pokud data uchováváte v centrálně řízené verzi systému SQL Server, jako v lokalitě poskytovatele hostingu, vždy používáte připojovací řetězec k zadání informací o připojení databáze.)
> 
> V nástroji WebMatrix, připojovací řetězce jsou obvykle uloženy v souboru XML s názvem *Web.config*. Jak již název napovídá, můžete použít *Web.config* souboru v kořenovém adresáři vašeho webu uložit informace o konfiguraci tohoto webu, včetně libovolné púřipojovací řetězce, které mohou vyžadovat váš web. Příklad připojovacího řetězce v *Web.config* souboru může vypadat třeba takto:
> 
> [!code-xml[Main](5-working-with-data/samples/sample27.xml)]
> 
> V tomto příkladu připojovací řetězec odkazuje na databázi v instanci systému SQL Server, na kterém běží na jiném serveru (na rozdíl od místní *SDF* souboru). Budete muset nahraďte příslušnými názvy pro `myServer` a `myDatabase`a zadejte hodnoty přihlášení serveru SQL Server `username` a `password`. (Uživatelské jméno a heslo hodnoty nejsou nutně stejné jako vaše přihlašovací údaje Windows nebo jako hodnoty, které váš poskytovatel hostingu udělil pro přihlášení na jejich serverech. Obraťte se na správce pro přesné hodnoty, které potřebujete.)
> 
> `Database.Open` Metoda je flexibilní, protože to umožňuje předat název databáze *SDF* název připojovacího řetězce, která je uložena v souboru nebo *Web.config* souboru. Následující příklad ukazuje, jak se připojit k databázi pomocí připojovacího řetězce, který je znázorněný v předchozím příkladu:
> 
> [!code-cshtml[Main](5-working-with-data/samples/sample28.cshtml)]
> 
> Jak je uvedeno, `Database.Open` metoda vám umožňuje předat název databáze nebo připojovací řetězec a budete přijít na to, které chcete použít. To je velmi užitečné při nasazování (publikovat) vašeho webu. Můžete použít *SDF* soubor *aplikace\_Data* složky při vývoji a testování vašeho webu. Při přesunu vašeho webu do provozního serveru, můžete použít připojovací řetězec v *Web.config* soubor, který má stejný název jako vaše *SDF* souboru, ale, že odkazuje na poskytovatele hostingu database &#8212;vše bez nutnosti měnit kód.
> 
> Nakonec, pokud chcete pracovat přímo s připojovacím řetězcem, můžete volat `Database.OpenConnectionString` a předáte jeho skutečné připojovacího řetězce namísto pouze název jednoho v *Web.config* souboru. To může být užitečné v situacích, kde z nějakého důvodu nemáte přístup na připojovací řetězec (nebo hodnoty, například *SDF* název_souboru) dokud je stránka je spuštěná. Pro většinu scénářů, ale můžete použít `Database.Open` jak je popsáno v tomto článku.


## <a name="additional-resources"></a>Další prostředky

- [SQL Server Compact](https://www.microsoft.com/sqlserver/2008/en/us/compact.aspx)
- [Připojení k serveru SQL Server nebo databázi MySQL ve službě WebMatrix](https://go.microsoft.com/fwlink/?LinkId=208661)
- [Ověřování uživatelských vstupů na webech s webovými stránkami ASP.NET](https://go.microsoft.com/fwlink/?LinkId=253002)

---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/displaying-data
title: Představení rozhraní ASP.NET Web Pages – zobrazení dat | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte, jak vytvořit databázi v nástroji WebMatrix a způsob zobrazení dat z databáze na stránce při použití webových stránek ASP.NET (Razor). Předpokládá y...
ms.author: riande
ms.date: 05/28/2015
ms.assetid: b3a006a0-3ea2-4d45-b833-e20e3a3c0a1a
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/displaying-data
msc.type: authoredcontent
ms.openlocfilehash: 9158a1f53268daec6e6fbdf003dd73e1d62cc667
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021583"
---
<a name="introducing-aspnet-web-pages---displaying-data"></a>Úvod do webových stránek ASP.NET – zobrazení dat
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V tomto kurzu se dozvíte, jak vytvořit databázi v nástroji WebMatrix a způsob zobrazení dat z databáze na stránce při použití webových stránek ASP.NET (Razor). Předpokládá, že jste dokončili řady prostřednictvím [Úvod do programování webových stránek ASP.NET](../introducing-razor-syntax-c.md).
> 
> Co se dozvíte:
> 
> - Jak vytvořit databázi a databázových tabulek pomocí nástroje WebMatrix.
> - Jak přidat data do databáze pomocí nástroje WebMatrix.
> - Jak zobrazit data z databáze na stránce.
> - Jak spouštět SQL příkazy v rozhraní ASP.NET Web Pages.
> - Jak přizpůsobit `WebGrid` pomocná Změna zobrazených dat a přidání stránkování a řazení.
>   
> 
> Popsané funkce a technologie:
> 
> - Databázové nástroje WebMatrix.
> - `WebGrid` pomocné rutiny.


## <a name="what-youll-build"></a>Co budete vytvářet

V předchozím kurzu jste se seznámili s pro webové stránky ASP.NET (*.cshtml* soubory), základní informace o syntaxi Razor a pomocné rutiny. V tomto kurzu budete začít vytvářet skutečné webové aplikace, které budete používat pro zbývající části této série. Aplikace je jednoduché filmové aplikace, která umožňuje zobrazit, přidat, změnit nebo odstranit informace o filmech.

Až budete hotovi s tímto kurzem, budete moci zobrazit výpis film, který vypadá jako na této stránce:

![WebGrid zobrazení, která obsahuje parametry nastavena na názvy tříd šablon stylů CSS](displaying-data/_static/image1.png)

Ale pokud chcete začít, je nutné vytvořit databázi.

## <a name="a-very-brief-introduction-to-databases"></a>Velmi stručný úvod do databáze

V tomto kurzu budou poskytovat jenom briefest Úvod do databáze. Pokud máte zkušenosti s databáze, můžete přeskočit Tento krátký oddíl.

Databáze obsahuje jednu nebo více tabulek, které obsahují informace &mdash; například tabulky pro zákazníky, objednávky a dodavatele, nebo pro studenty, učitelé, třídy a třídy. Databázové tabulky strukturálně, je jako tabulku. Představte typické adresáře. Pro každou položku v adresáři (to znamená, že pro každou osobu) mají různé druhy informace, jako je jméno, příjmení, adresu, e-mailovou adresu a telefonní číslo.

![Ukázkové tabulky databáze jako jednoduchý mřížky](displaying-data/_static/image2.png)

(Řádky jsou někdy označovány jako *záznamy*, a sloupce jsou někdy označovány jako *pole*.)

Pro většinu tabulky databáze v tabulce musí mít sloupec, který obsahuje jedinečná hodnota, například zákaznické číslo, číslo účtu a tak dále. Tato hodnota se označuje jako v tabulce *primární klíč*, a můžete ji použít k identifikaci každý řádek v tabulce. V tomto příkladu je ID sloupce primární klíč pro adresář je znázorněno v předchozím příkladu.

Velká část práce, kterou provedete v webových aplikací se skládá z čtení informací z databáze a jeho zobrazení na stránce. Kromě toho často budete shromažďovat informace od uživatelů a přidejte ho do databáze, nebo upravíte záznamy, které jsou již v databázi. (Probereme všechny tyto operace v této sérii kurzů.)

Pracovní databáze může být enormously složité a můžou vyžadovat, aby specializované znalosti. Pro tuto sérii kurzů, musíte pochopit pouze základní koncepty, které budou všechny vysvětlena průběžně.

## <a name="creating-a-database"></a>Vytvoření databáze

Služba WebMatrix obsahuje nástroje, které umožňují snadno vytvořit databázi a vytvoření tabulky v databázi. (Struktura databáze se označuje jako databáze *schématu*.) Pro tuto sérii kurzů, vytvoříte databázi, která obsahuje pouze jednu tabulku v něm &mdash; videa.

Otevřete nástroj WebMatrix, pokud jste tak již neučinili a otevřete web WebPagesMovies, kterou jste vytvořili v předchozím kurzu.

V levém podokně klikněte **databáze** pracovního prostoru.

![Karta WebMatrix databáze pracovního prostoru](displaying-data/_static/image3.png)

Pásu karet se změní a objeví úloh souvisejících s databází. Na pásu karet klikněte na tlačítko **novou databázi**.

!['Nové databáze' tlačítko na pásu karet nástroje WebMatrix](displaying-data/_static/image4.png)

Služba WebMatrix vytvoří databázi SQL Server CE ( *SDF* soubor), který má stejný název jako lokalitu pro &mdash; *WebPagesMovies.sdf*. (Není to zde, ale můžete přejmenovat soubor na cokoli, co vám vyhovuje, za předpokladu, má *SDF* rozšíření.)

## <a name="creating-a-table"></a>Vytvoření tabulky

Na pásu karet klikněte na tlačítko **nová tabulka**. Služba WebMatrix otevře na nové kartě návrháře tabulky. (Pokud není k dispozici možnost nové tabulky, ujistěte se, že se nová databáze je vybraný ve stromovém zobrazení na levé straně.)

!['Nové tabulky' tlačítko na pásu karet nástroje WebMatrix](displaying-data/_static/image5.png)

Do textového pole v horní části stránky (kde vodoznak říká "Zadejte název tabulky") zadejte "Filmy".

![Zadání názvu tabulky v Návrháři databáze pro službu WebMatrix](displaying-data/_static/image6.png)

V podokně pod název tabulky je tady můžete definovat jednotlivých sloupců. Pro *filmy* tabulky v tomto kurzu vytvoříte pouze několik sloupců: *ID*, *Title*, *žánr*, a *rok*.

V **název** zadejte "ID". Zadáním hodnoty tady aktivuje všechny ovládací prvky pro nový sloupec.

Záložku **datový typ** seznam a zvolte **int**. Tato hodnota určuje, že sloupec ID bude obsahovat data o celé číslo (číslo).

> [!NOTE]
> Nebude voláme ho žádné najdete tady (mnohem), ale můžete použít standardní klávesnice gesta Windows pro navigaci v této mřížce. Například můžete vytvořit kartu mezi poli, stačí začít psát, abyste mohli vybrat položku v seznamu a tak dále.


Karta minulé **výchozí hodnota** pole (to znamená, že necháte prázdné). Záložku **je primární klíč** zaškrtněte políčko a vyberte ji. Tato možnost informuje databázi, která *ID* sloupec bude obsahovat data, která identifikuje jednotlivé řádky. (To znamená, že každý řádek bude mít jedinečnou hodnotu ve sloupci ID, který vám pomůže najít tento řádek.)

Zvolte **je identita** možnost. Tato možnost informuje databázi, má automaticky generovat další pořadové číslo pro každý nový řádek. ( **Je identita** možnost funguje pouze v případě, že jste vybrali **je primární klíč** možnost.)

Klikněte na další řádek mřížky nebo stisknutím klávesy Tab dvakrát na dokončení aktuální řádek. Buď gesta uloží aktuální řádek a spustí další příkaz. Všimněte si, že **výchozí hodnota** sloupce teď uvádí, že **Null**. (Null je výchozí hodnota pro výchozí hodnotu, takže na speak.)

Až dokončíte definování nového **ID** sloupec, Návrhář bude vypadat jako na tomto obrázku:

![Služba WebMatrix návrháře databáze po definování ID sloupce pro tabulku filmy](displaying-data/_static/image7.png)

Chcete-li vytvořit další sloupce, klikněte do pole ve **název** sloupce. Zadejte "Title" pro sloupec a pak vyberte **nvarchar** pro **datový typ** hodnotu. Část "var" **nvarchar** říká databáze, že data pro tento sloupec bude řetězec, jehož velikost může lišit od záznamu. (Předpona "n" představuje "national", což znamená, že pole může obsahovat znak data pro jakékoli abecedy nebo systému zápisu – to znamená, že pole obsahuje data ve formátu Unicode.)

Pokud zvolíte **nvarchar**, zobrazí se jiné pole, ve kterém můžete zadat maximální počet znaků pro pole. Zadejte hodnotu 50, za předpokladu, že žádný název filmu, budete pracovat v tomto kurzu bude delší než 50 znaků.

Přeskočit **výchozí hodnota** a zrušte **Allow Nulls** možnost. Nechcete databáze, kterou chcete povolit všechny filmy se zapisují do databáze, které nemají název.

Když jste hotovi a přechod na další řádek, Návrhář bude vypadat jako na tomto obrázku:

![Služba WebMatrix návrháře databáze po definování název sloupce pro tabulku filmy](displaying-data/_static/image8.png)

Opakováním těchto kroků můžete vytvořit sloupec s názvem "Žánr", s výjimkou délky, nastavte ho na právě 30.

Vytvořit jiný sloupec s názvem "Rok." Datový typ, zvolte **nchar** (ne **nvarchar**) a nastavit délku 4. Za rok budete používat 4 číslice jako "1995" nebo "2010", takže nevyžadují proměnlivé velikosti sloupce.

Zde je, jak vypadá dokončení návrhu:

![Služba WebMatrix návrháře databáze po všechna pole jsou definována pro tabulku filmy](displaying-data/_static/image9.png)

Stisknutím klávesy Ctrl + S nebo kliknutím **Uložit** tlačítko na panelu nástrojů Rychlý přístup. Když zavřete kartu zavřete návrháře databáze.

## <a name="adding-some-example-data"></a>Přidání některých ukázková Data

Dále v této sérii kurzů vytvoříte stránky, kde můžete zadat nové filmy ve formuláři. Prozatím se však můžete přidat některé ukázková data, která pak můžete zobrazit na stránce.

V **databáze** pracovního prostoru v nástroji WebMatrix, Všimněte si, že je strom, který ukazuje *SDF* souborů, které jste vytvořili dříve. Otevřete uzel nové *SDF* souboru a pak otevřete **tabulky** uzlu.

![Služba WebMatrix databáze pracovního prostoru s stromu otevřít na filmy tabulku](displaying-data/_static/image10.png)

Klikněte pravým tlačítkem myši **filmy** uzlu a pak zvolte možnost **Data**. Služba WebMatrix se otevře do mřížky, kde můžete zadat data pro *filmy* tabulky:

![Databáze položky mřížky ve službě WebMatrix (prázdné)](displaying-data/_static/image11.png)

Klikněte na tlačítko **název** sloupci a zadejte "Při Harry splněny Sally". Přesunout **žánr** sloupce (můžete použít klávesu Tab) a zadejte "Milostné komedie". Přesunout **rok** sloupci a zadejte "1989":

![Položky mřížky databáze v nástroji WebMatrix s jedním záznamem](displaying-data/_static/image12.png)

Stisknutím klávesy Enter a službě WebMatrix uloží nový film. Všimněte si, **ID** bylo vyplněno sloupce.

![Položky mřížky databáze v nástroji WebMatrix s jedním záznamem a automaticky generovaný kód](displaying-data/_static/image13.png)

Zadejte jiný film (například "pryč s the větru", "Drama", "1939"). Sloupec ID vyplněno znovu:

![Položky mřížky databáze v nástroji WebMatrix se dva záznamy a automaticky generované ID](displaying-data/_static/image14.png)

Zadejte třetí movie (například "Ghostbusters", "Komedie"). Jako experiment, nechat **rok** sloupce prázdné a potom stiskněte klávesu Enter. Protože jste zrušení výběru **Allow Nulls** možnost, ukazuje chybu, databáze:

![Pokud požadovaný sloupec hodnota je prázdné, zobrazí se chyba 'neplatná data.](displaying-data/_static/image15.png)

Klikněte na tlačítko **OK** přejděte zpět a opravte položka (pole year pro "Ghostbusters" je 1984) a stiskněte klávesu Enter.

Vyplňte několik filmy, dokud nebudete mít 8 nebo tak. (Zadání 8 usnadňuje práci s stránkování později. Ale pokud se jedná o příliš mnoho, zadejte jenom některé zatím.) Skutečná data nezáleží.

![Položky mřížky databáze v nástroji WebMatrix se buď záznamy](displaying-data/_static/image16.png)

Pokud jste zadali všechny filmy bez chyb, jsou hodnoty ID sekvenční. Pokud jste se pokusili uložit záznam neúplné film, nemusí být čísla ID sekvenční. Pokud ano, který je v pořádku. Tato čísla nemají žádné nepsaný význam a jediné, co je důležité je, že jsou jedinečné *filmy* tabulky.

Zavřete kartu, která obsahuje návrháře databáze.

Teď můžete zapnout pro zobrazení těchto dat na webové stránce.

## <a name="displaying-data-in-a-page-by-using-the-webgrid-helper"></a>Zobrazení dat na stránce s využitím pomocné rutiny WebGrid

K zobrazení dat na stránce, se chystáte použít `WebGrid` pomocné rutiny. Tato pomocná vytvoří zobrazení v mřížce nebo tabulce (řádků a sloupců). Jak uvidíte, bude možné upravit mřížku formátování a další funkce.

Ke spuštění mřížky, budete muset napsat pár řádků kódu. Těchto několik řádků bude sloužit jako druh vzor pro téměř všechny přístup k datům, které provedete v tomto kurzu.

> [!NOTE]
> Ve skutečnosti máte celou řadu možností pro zobrazení dat na stránce. `WebGrid` pomocné rutiny je jen jednou. Rozhodli jsme se pro účely tohoto kurzu vzhledem k tomu, že je nejjednodušší způsob, jak zobrazit data, a proto je poměrně flexibilní. V následující kurz uvidíte, jak používat další "Ruční" způsob, jak pracovat s daty na stránce, která uděluje více přímou kontrolu nad způsob zobrazení dat.


V levém podokně v nástroji WebMatrix, klikněte na tlačítko **soubory** pracovního prostoru.

Je nový vytvořená databáze zobrazí v *aplikace\_Data* složky. Pokud složka ještě neexistuje, služba WebMatrix vytvoření nové databáze. (Složka může mít existoval Pokud jste dříve nainstalovali pomocné rutiny.)

Ve stromovém zobrazení vyberte kořenový Web. Musíte vybrat kořenový web; v opačném případě může do aplikace přidá nový soubor\_složku Data.

Na pásu karet klikněte na tlačítko **nový**. V **vyberte typ souboru** zvolte **CSHTML**.

V **název** pole, zadejte název nové stránky "Movies.cshtml":

!["Vyberte typ souboru" dialogové okno stránkou "videa.](displaying-data/_static/image17.png)

Klikněte na tlačítko **OK** tlačítko. Služba WebMatrix otevře nový soubor s některé kostru prvky v ní. Nejprve napíšete kód získá data z databáze. Potom přidáte kód na stránku ve skutečnosti zobrazit data.

### <a name="writing-the-data-query-code"></a>Psaní kódu dotazování dat

V horní části stránky mezi `@{` a `}` znaky, zadejte následující kód. (Ujistěte se, že zadáte tento kód mezi otevírací a uzavírací závorky.)

[!code-csharp[Main](displaying-data/samples/sample1.cs)]

První řádek se otevře databáze, kterou jste vytvořili dříve, což je vždy první krok před provedením něco s databází. Dáte `Database.Open` metoda název databáze, kterou chcete otevřít. Všimněte si, že nechcete zahrnout *SDF* v názvu. `Open` Metoda předpokládá, že hledá *SDF* souboru (to znamená, *WebPagesMovies.sdf*) a že *SDF* soubor *aplikace\_ Data* složky. (Dříve jsme už uvedli, který *aplikace\_Data* vyhrazené složky; Tento scénář je jeden z míst, kde technologie ASP.NET je předpoklady o tímto názvem.)

Při otevření databáze na ni odkaz přejde do proměnné s názvem `db`. (Která by mohla mít název nic.) `db` Proměnná je, jak budete mít nakonec interakce s databází.

Druhý řádek ve skutečnosti načte data databáze s použitím `Query` metody. Všimněte si, jak funguje takto: `db` proměnná obsahuje odkaz na databázi otevřený a je zapotřebí vyvolat `Query` metoda pomocí `db` proměnné (`db.Query`).

Je samotný dotaz SQL `Select` příkazu. (Trochu související informace o SQL, viz vysvětlení později.) V příkazu `Movies` identifikuje tabulku pro dotaz. `*` Znaků určuje, že dotaz by měl vrátit všechny sloupce z tabulky. (Může být také seznam sloupců jednotlivě, oddělené čárkami.)

Výsledky dotazu, pokud existuje, jsou vráceny a k dispozici v `selectedData` proměnné. Může mít znovu, název proměnné, cokoli.

Nakonec třetí řádek říká technologie ASP.NET, že chcete použít instanci `WebGrid` pomocné rutiny. Vytvoříte (*vytvořit instanci*) pomocný objekt s použitím `new` – klíčové slovo a předat ji přes výsledky dotazu `selectedData` proměnné. Nové `WebGrid` jsou k dispozici v objektu, spolu s výsledky dotazu databáze `grid` proměnné. Bude nutné tento výsledek v tuto chvíli ve skutečnosti zobrazit data na stránce.

V této fázi databázi otevřít, zobrazila data mají být, a jste připravili `WebGrid` pomocné rutiny s daty. Dále je vytvoření značky na stránce.

> [!TIP] 
> 
> **Structured Query Language (SQL)**
> 
> SQL je jazyk, který se používá v Většina relačních databází pro správu dat v databázi. Obsahuje příkazy, které umožňují načtení dat a aktualizovat je a, které umožňují vytvářet, upravovat a spravovat data v tabulkách databáze. SQL se liší od programovací jazyk (jako je C#). Pomocí jazyka SQL dáte databáze, co má a je databáze úlohu zjistit, jak získat data nebo provést úlohu. Tady jsou příklady některých příkazů SQL a jejich význam:
> 
> `Select * From Movies`
> 
> `SELECT ID, Name, Price FROM Product WHERE Price > 10.00 ORDER BY Name`
> 
> První `Select` příkaz načte všechny sloupce (určená `*`) z *filmy* tabulky.
> 
> Druhá `Select` příkaz načte ID, název a cena sloupce ze záznamů ve *produktu* tabulku, jejíž hodnota sloupce cena je více než 10. Příkaz vrátí výsledky v abecedním pořadí podle hodnot ve sloupci Název. Pokud cenu kritériím neodpovídají žádné záznamy, příkaz vrátí prázdnou sadou.
> 
> `INSERT INTO Product (Name, Description, Price) VALUES ('Croissant', 'A flaky delight', 1.99)`
> 
> Tento příkaz vloží nový záznam do *produktu* tabulku nastavení sloupec název "Croissant", ve sloupci Popis na "Nespolehlivé naprostou spokojenost A" a cena na 1,99.
> 
> Všimněte si, že když zadáváte nečíselná hodnota, hodnota se udává v jednoduchých uvozovkách (ne dvojitých uvozovek, stejně jako v jazyce C#). Můžete použít tyto uvozovky kolem hodnot textu nebo datum, ale nikoli celého čísla.
> 
> `DELETE FROM Product WHERE ExpirationDate < '01/01/2008'`
> 
> Tento příkaz odstraní záznamy *produktu* tabulky, jehož sloupec Datum vypršení platnosti je starší než 1. ledna 2008. (Příkaz předpokládá, že *produktu* tabulka obsahuje sloupce, samozřejmě.) Datum je ve formátu MM/DD/RRRR tady zadáte, ale by měly být zadány ve formátu, který se používá pro vaše národní prostředí.
> 
> `Insert` a `Delete` příkazy nevracejte sad výsledků dotazu. Místo toho vrátí číslo, které se říká, kolik záznamů byly ovlivněny příkazu.
> 
> Pro některé z těchto operací (např. vkládání a odstranění záznamů) proces, který žádá o operaci musí mít příslušná oprávnění v databázi. To je důvod, proč pro produkční databáze je často nutné zadat uživatelské jméno a heslo při připojování k databázi.
> 
> Existují desítek příkazů SQL, ale všechny se řídí vzorem stejně jako příkazy, kterou tady vidíte. Příkazy SQL můžete použít k vytvoření databázových tabulek, počet záznamů v tabulce, výpočtu ceny a provádět mnoho dalších operacích.


### <a name="adding-markup-to-display-the-data"></a>Přidání značek k zobrazení dat

Uvnitř `<head>` elementu, obsah sady `<title>` element na "Filmy":

[!code-html[Main](displaying-data/samples/sample2.html?highlight=3)]

Uvnitř `<body>` element na stránce, přidejte následující:

[!code-html[Main](displaying-data/samples/sample3.html)]

Je to. `grid` Proměnné je hodnota jste vytvořili při vytváření `WebGrid` objekt v kódu výše.

Služba WebMatrix stromové zobrazení, klikněte pravým tlačítkem na stránku a vyberte **spustit v prohlížeči**. Zobrazí se stránka podobná této:

![Výchozí WebGrid pomocné rutiny výstup z tabulky filmy](displaying-data/_static/image18.png)

Kliknutím na odkaz záhlaví sloupců řadit podle sloupce. Schopnost seřadit klepnutím na záhlaví je funkce, která je integrovaná **WebGrid** pomocné rutiny.

`GetHtml` Způsob, jak její název napovídá, generuje kód, který se zobrazí data. Ve výchozím nastavení `GetHtml` metoda generuje HTML `<table>` element. (Pokud chcete, můžete ověřit vykreslování zobrazením zdroje stránky v prohlížeči.)

## <a name="modifying-the-look-of-the-grid"></a>Změna vzhledu mřížky

Použití `WebGrid` pomocné rutiny, jako je právě předvedl je jednoduché, ale výsledné zobrazení je jednoduché. `WebGrid` Pomocné rutiny má celou řadu možností, které umožňují řídit, jak se data zobrazí. Existuje příliš mnoho chcete data prozkoumat v tomto kurzu, ale tato část vám poskytne vám představu o některé z těchto možností. V dalších kurzech v tomto seriálu se budeme několik dalších možností.

### <a name="specifying-individual-columns-to-display"></a>Určení jednotlivé sloupce zobrazení

Pokud chcete začít, můžete určit, že chcete zobrazit jenom určité sloupce. Ve výchozím nastavení, jak už víte, mřížky se zobrazí všechny čtyři sloupce *filmy* tabulky.

V *Movies.cshtml* souboru, nahradí `@grid.GetHtml()` kód, který jste právě přidali následujícím kódem:

[!code-css[Main](displaying-data/samples/sample4.css)]

Zjistit pomocné rutiny, které sloupce se zobrazí, můžete zahrnout `columns` parametr pro `GetHtml` a předáte v kolekci sloupců. V kolekci zadejte jednotlivé sloupce, které chcete zahrnout. Zadejte jednotlivé sloupce pro zobrazení zahrnutím `grid.Column` objektu a předejte název sloupce dat, který chcete. (Tyto sloupce musí být součástí výsledků dotazu SQL – `WebGrid` pomocné rutiny nemůže zobrazit sloupce, které nebyly vrácených dotazem.)

Spusťte *Movies.cshtml* stránku v prohlížeči znovu a tentokrát získat zobrazení jako na následující (Všimněte si, že je zobrazen žádný sloupec ID):

![Zobrazení WebGrid zobrazující pouze vybrané sloupce](displaying-data/_static/image19.png)

### <a name="changing-the-look-of-the-grid"></a>Změna vzhledu mřížky

Existuje mnoho další možnosti pro zobrazení sloupce, z nichž některé budeme se zabývat v dalších kurzech v této sadě. Prozatím se v této části vás seznámí s možnosti, ve kterém můžete styl mřížky jako celek.

Uvnitř `<head>` části stránky těsně před uzavírací `</head>` značky, přidejte následující `<style>` element:

[!code-css[Main](displaying-data/samples/sample5.css)]

Tento kód šablony stylů CSS definuje třídy s názvem `grid`, `head`, a tak dále. Můžete také umístit tyto definice stylů v samostatném *.css* souboru a odkaz na stránku. (Ve skutečnosti, můžete to udělat později v této sérii kurzů.) Ale aby to bylo snadné pro účely tohoto kurzu, jsou na stejné stránce, která zobrazuje data.

Teď si můžete nechat `WebGrid` pomocné rutiny k použití těchto tříd style. Pomocná rutina má několik vlastností (například `tableStyle`) pro právě tento účel – k nim přiřadíte název třídy stylu CSS, a tento název třídy je vykreslen jako součást kód, který je vykreslen metodou pomocné rutiny.

Změnit `grid.GetHtml` kód tak, že teď vypadá nějak takto:

[!code-css[Main](displaying-data/samples/sample6.css)]

Co je nového v tomto poli je, že jste přidali `tableStyle`, `headerStyle`, a `alternatingRowStyle` parametrů `GetHtml` metody. Tyto parametry jsou nastavené na názvy, které jste přidali před chvílí styly CSS.

Spuštění stránky a tentokrát se zobrazí v mřížce, která vypadá mnohem méně prostý než před:

![WebGrid zobrazení, která obsahuje parametry nastavena na názvy tříd šablon stylů CSS](displaying-data/_static/image20.png)

Abyste zjistili, co `GetHtml` metodou, vygenerované, můžete se podívat na zdroji stránku v prohlížeči. Nebude přejdeme do podrobností tady ale důležité je, že tak, že zadáte parametry jako `tableStyle`, způsobil mřížky mají vygenerovat značky HTML vypadat asi takto:

`<table class="grid">`

`<table>` Měl značky `class` do ní přidat atribut, který odkazuje na jeden z pravidel šablon stylů CSS, které jste přidali dříve. Tento kód vám zobrazuje základní vzor &mdash; různé parametry pro `GetHtml` metoda umožňují vám odkaz šablon stylů CSS třídy, poté vygeneruje metodu spolu s značky. Co dělat s třídami šablony stylů CSS je na vás.

## <a name="adding-paging"></a>Přidání stránkování

Jako poslední úlohy pro účely tohoto kurzu přidáte stránkování mřížky. V tuto chvíli bez obav zobrazíte všechny filmů najednou. Ale pokud jste přidali stovky filmy, zobrazení stránky byste získali dlouho.

V kódu stránky, změňte řádek, který vytvoří `WebGrid` objektu v následujícím kódu:

[!code-csharp[Main](displaying-data/samples/sample7.cs)]

Jediný rozdíl oproti před je, že jste přidali `rowsPerPage` parametr, který je nastaven na 3.

Spuštění stránky. V mřížce zobrazené na čas prodloužený navigační odkazy, které umožní procházet videa ve vaší databázi 3 řádky:

![Zobrazení WebGrid pomocí stránkování](displaying-data/_static/image21.png)

## <a name="coming-up-next"></a>Chystá se další

V dalším kurzu se dozvíte, jak používat kód Razor a C# k získání vstupu uživatele ve formuláři. Přidáte vyhledávacího pole na stránce videa, abyste našli filmy podle názvu nebo rozšířením podle tematických.

## <a name="complete-listing-for-movies-page"></a>Úplný seznam pro stránku filmy

[!code-cshtml[Main](displaying-data/samples/sample8.cshtml)]

## <a name="additional-resources"></a>Další prostředky

- [Úvod k programování v prostředí ASP.NET pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)

> [!div class="step-by-step"]
> [Předchozí](intro-to-web-pages-programming.md)
> [další](form-basics.md)

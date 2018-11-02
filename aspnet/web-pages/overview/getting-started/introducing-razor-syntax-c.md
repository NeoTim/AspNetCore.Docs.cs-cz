---
uid: web-pages/overview/getting-started/introducing-razor-syntax-c
title: Úvod k programování v rozhraní ASP.NET Web používající syntaxi Razor (C#) | Dokumentace Microsoftu
author: tfitzmac
description: Tato kapitola obsahuje přehled programování s webovými stránkami ASP.NET pomocí syntaxe Razor. ASP.NET je technologie od Microsoftu pro spouštění dynamického webového pa...
ms.author: riande
ms.date: 02/07/2014
ms.assetid: aa67d304-583b-4bf8-a231-195656cfb587
msc.legacyurl: /web-pages/overview/getting-started/introducing-razor-syntax-c
msc.type: authoredcontent
ms.openlocfilehash: b242bf52bbd63d726e6ce6ab7be01a1b81c5bf1b
ms.sourcegitcommit: fc2486ddbeb15ab4969168d99b3fe0fbe91e8661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50758255"
---
<a name="introduction-to-aspnet-web-programming-using-the-razor-syntax-c"></a>Úvod k programování v rozhraní ASP.NET Web používající syntaxi Razor (C#)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek obsahuje přehled programování s webovými stránkami ASP.NET pomocí syntaxe Razor. ASP.NET je technologie od Microsoftu pro spouštění dynamické webové stránky na webové servery. Toto se články zaměřuje na pomocí programovacího jazyka C#.
> 
> **Co se dozvíte**:
> 
> - Začátek 8 Programovací tipy pro zahájení práce s programování pomocí syntaxe Razor rozhraní ASP.NET Web Pages.
> - Základní koncepty programování, které budete potřebovat.
> - Jaký kód serveru technologie ASP.NET a syntaxe Razor je všechno o.
>   
> 
> ## <a name="software-versions"></a>Verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


## <a name="the-top-8-programming-tips"></a>Začátek 8 Tipy pro programování

Tato část uvádí několik tipů, které je nezbytně potřeba ví, jak můžete začít psát kód serveru ASP.NET pomocí syntaxe Razor.

> [!NOTE]
> Syntaxe Razor je založena na programovací jazyk C# a, který je jazyk, který se často používá s webovými stránkami ASP.NET. Syntaxe Razor, ale také podporuje jazyka Visual Basic a všechno, co uvidíte, že můžete provést také v jazyce Visual Basic. Podrobnosti najdete v tématu dodatku [syntaxe a jazyk Visual Basic](https://go.microsoft.com/fwlink/?LinkId=202908).


Později v tomto článku najdete další podrobnosti o většinu těchto programovacích technik.

### <a name="1-you-add-code-to-a-page-using-the--character"></a>1. Přidejte kód do stránky pomocí znak @

`@` Začne znak vložené výrazy, jeden příkaz bloky a bloky více příkazy:

[!code-html[Main](introducing-razor-syntax-c/samples/sample1.html)]

Je to, jak tyto příkazy vypadat při spuštění stránky v prohlížeči:

![Razor Img1](introducing-razor-syntax-c/_static/image1.jpg)

> [!TIP] 
> 
> **Kódování HTML**
> 
> Při zobrazení obsahu do stránky pomocí `@` znaků, stejně jako v předchozích příkladech ASP.NET umístí kódování HTML výstup. To nahrazuje vyhrazené znaky HTML (například `<` a `>` a `&`) s kódy, které umožňují znaků, který se má zobrazit jako znaků na webové stránce nebude interpretován jako značku jazyka HTML nebo entity. Bez kódování HTML výstup z kódu serveru se nemusí zobrazit správně a může zpřístupnit stránky na bezpečnostní rizika.
> 
> Pokud je vaším cílem je výstup kód HTML, který vykreslí značky jako značka (třeba `<p></p>` odstavce nebo `<em></em>` pro zvýraznění textu), najdete v části [kombinaci textu, značek a kódu v blocích kódu](#BM_CombiningTextMarkupAndCode) dále v tomto článku.
> 
> Další informace o kódování HTML v [práce s formuláři](https://go.microsoft.com/fwlink/?LinkId=202892).


### <a name="2-you-enclose-code-blocks-in-braces"></a>2. Bloky kódu je uzavřít do složených závorek

A *blok kódu* obsahuje jeden nebo více příkazů kódu a je uzavřené ve složených závorkách.

[!code-html[Main](introducing-razor-syntax-c/samples/sample2.html)]

Výsledek zobrazí v prohlížeči:

![Razor Img2](introducing-razor-syntax-c/_static/image2.jpg)

### <a name="3-inside-a-block-you-end-each-code-statement-with-a-semicolon"></a>3. Uvnitř bloku ukončit každý příkaz kódu oddělte středníkem.

Každý příkaz kompletní kód uvnitř bloku kódu, musí končit středníkem. Vložené výrazy nejsou končí středníkem.

[!code-html[Main](introducing-razor-syntax-c/samples/sample3.html)]

### <a name="4-you-use-variables-to-store-values"></a>4. Použití proměnných k ukládání hodnot

Můžete ukládat hodnoty *proměnnou*, včetně řetězců, čísel a data, atd. Vytvoření nové proměnné pomocí `var` – klíčové slovo. Hodnoty proměnných můžete vložit přímo do stránky pomocí `@`.

[!code-html[Main](introducing-razor-syntax-c/samples/sample4.html)]

Výsledek zobrazí v prohlížeči:

![Razor Img3](introducing-razor-syntax-c/_static/image3.jpg)

<a id="ID_StringLiterals"></a>
### <a name="5-you-enclose-literal-string-values-in-double-quotation-marks"></a>5. Použijte hodnoty řetězcový literál v uvozovkách

A *řetězec* je posloupnost znaků, které jsou považovány za text. Pokud chcete zadat řetězec, uzavřete do dvojitých uvozovek:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample5.cshtml)]

Pokud řetězec, který chcete zobrazit obsahuje znak zpětného lomítka ( `\` ) nebo dvojité uvozovky ( `"` ), použijte *doslovný řetězec literálu* , který je s předponou `@` operátor. (V jazyce C# \ znak má zvláštní význam, pokud nechcete použít doslovný řetězec literálu.)

[!code-html[Main](introducing-razor-syntax-c/samples/sample6.html)]

K vložení uvozovek, použijte doslovný řetězec literálu a opakujte uvozovek:

[!code-html[Main](introducing-razor-syntax-c/samples/sample7.html)]

Tady je výsledkem použití obou těchto příkladech na stránce:

![Razor Img4](introducing-razor-syntax-c/_static/image4.jpg)

> [!NOTE]
> Všimněte si, že `@` znak slouží k označení literálů doslovném řetězci v jazyce C# a označit kód stránek v ASP.NET.


### <a name="6-code-is-case-sensitive"></a>6. Kód je velká a malá písmena

V jazyce C#, klíčová slova (stejně jako `var`, `true`, a `if`) a názvy proměnných jsou malá a velká písmena. Následující řádky kódu vytvářejí dva různé proměnné, `lastName` a `LastName.`

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample8.cshtml)]

Pokud deklarujete proměnnou jako `var lastName = "Smith";` a pokud se pokusíte odkazují na tuto proměnnou na stránce jako `@LastName`, dochází k chybě, protože `LastName` nebude rozpoznán.

> [!NOTE]
> V jazyce Visual Basic, klíčová slova a proměnné jsou *není* velká a malá písmena.


### <a name="7-much-of-your-coding-involves-objects"></a>7. Velká část psaní kódu zahrnuje objekty

*Objekt* představuje věc, kterou můžete programovat s &#8212; stránku, textové pole, soubor, obrázek, webový požadavek, e-mailovou zprávu, záznam zákazníka (řádek databáze), atd. Objekty mají vlastnosti, které popisují jejich vlastností a že může číst nebo změnit &#8212; textový objekt pole má `Text` vlastnost (mimo jiné), má objekt žádosti `Url` vlastnost, nemá e-mailovou zprávu `From` vlastnost, a objekt zákazníka má `FirstName` vlastnost. Objekty mají také metody, které jsou &quot;příkazy&quot; mohou provádět. Mezi příklady patří objektu soubor `Save` metoda, objektu image `Rotate` metoda a objekt e-mailu `Send` – metoda.

Často budete pracovat `Request` objekt, který poskytuje informace, například hodnoty textová pole (polí formuláře) na stránce, jaký typ prohlížeče přišel požadavek, adresa URL stránky, identita uživatele, atd. Následující příklad ukazuje, jak přistupovat k vlastnosti `Request` objekt a jak volat `MapPath` metodu `Request` objektu, který obsahuje absolutní cestu na stránce na serveru:

[!code-html[Main](introducing-razor-syntax-c/samples/sample9.html)]

Výsledek zobrazí v prohlížeči:

![Razor Img5](introducing-razor-syntax-c/_static/image5.jpg)

### <a name="8-you-can-write-code-that-makes-decisions"></a>8. Můžete napsat kód, který provádí rozhodnutí

Klíčovou funkcí dynamické webové stránky je, že určíte, co se provedou v závislosti na podmínkách. Nejběžnější způsob je pomocí `if` – příkaz (a volitelné `else` příkaz).

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample10.cshtml)]

Příkaz `if(IsPost)` je zjednodušený způsob psaní `if(IsPost == true)`. Spolu s `if` příkazy, existuje široká škála způsobů, jak otestovat podmínky, při opakovaném bloky kódu, a tak dále, které jsou popsány dále v tomto článku.

Výsledek zobrazí v prohlížeči (po kliknutí na tlačítko **odeslat**):

![Razor Img6](introducing-razor-syntax-c/_static/image6.jpg)

> [!TIP] 
> 
> <a id="SB_HttpGetPost"></a>
> ### <a name="http-get-and-post-methods-and-the-ispost-property"></a>HTTP GET a POST metody a vlastnosti IsPost
> 
> Protokol použitý pro webové stránky (HTTP) podporuje velmi omezený počet metody (akce), které se používají k provádění požadavků na server. Nichž dva nejčastější jsou GET, který slouží k načtení stránky a příspěvku, který se používá k odeslání stránky. Obecně platí uživatel požádá o stránku při prvním požadavku na stránku pomocí GET. Pokud uživatel vyplní ve formuláři a poté klikne na tlačítko Odeslat, prohlížeč odešle požadavek POST na server.
> 
> Ve webovém programování často je užitečné vědět, jestli na stránce jsou požadovány jako GET nebo POST, abyste věděli, jak zpracování stránky. ASP.NET Web Pages, můžete použít `IsPost` vlastnosti chcete zobrazit, zda je požadavek GET nebo POST. Pokud je příspěvek, požadavek `IsPost` vlastnost vrátí hodnotu PRAVDA, a můžete provádět věci, jako je čtení hodnoty polí ve formuláři. Mnoho příkladů, zobrazí se vám ukazují, jak zpracovat stránce odlišně v závislosti na hodnotě `IsPost`.


## <a name="a-simple-code-example"></a>Jednoduchým příkladem kódu

Tento postup ukazuje, jak vytvořit stránku, která ukazuje základní programovací techniky. V tomto příkladu vytvoříte stránky, která umožňuje uživatelům zadat dvě čísla, potom se přidají a zobrazí výsledek.

1. V editoru, vytvořte nový soubor s názvem *AddNumbers.cshtml*.
2. Zkopírujte následující kód a kód na stránku, nahrazení nic již na stránce.  

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample11.cshtml)]

    Tady jsou některé věci za vás mějte na paměti:

    - `@` Znak spustí první blok kódu ve stránce, a předchází `totalMessage` proměnné, který je vložený v dolní části stránky.
    - Blok v horní části stránky je uzavřen do složených závorek.
    - V bloku v horní části všech řádků končí středníkem.
    - Proměnné `total`, `num1`, `num2`, a `totalMessage` uložení několika čísla a řetězce.
    - Řetězcový literál hodnota přiřazená `totalMessage` proměnná je do dvojitých uvozovek.
    - Protože kód je velká a malá písmena, když `totalMessage` proměnná se používá v dolní části stránky, jeho název musí přesně odpovídat proměnné v horní části.
    - Výraz `num1.AsInt() + num2.AsInt()` ukazuje, jak pracovat s objekty a metody. `AsInt` Metodu na každou proměnnou převede řetězec zadaný uživatelem na číslo (integer) tak, aby aritmetické operace můžete provádět v něm.
    - `<form>` Značka zahrnuje `method="post"` atribut. Toto určuje, že když uživatel klikne **přidat**, stránky se odešlou na server pomocí metody POST protokolu HTTP. Při odeslání stránky `if(IsPost)` test vyhodnocen jako true a podmíněný kód spuštění zobrazení výsledek přidání čísla.
3. Uložit na stránku a spustíte ji v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.) Zadejte dvěma celými čísly a poté klikněte **přidat** tlačítko. 

    ![Razor Img7](introducing-razor-syntax-c/_static/image7.jpg)

## <a name="basic-programming-concepts"></a>Základní koncepty programování

Tento článek poskytuje přehled technologie ASP.NET webové programování. Není vyčerpávající zkoumání, pouze chcete zobrazit rychlou prohlídku prostřednictvím koncepty programování, které budete používat nejčastěji. I tak zahrnuje téměř vše, co budete potřebovat, abyste mohli začít s webovými stránkami ASP.NET.

Ale nejprve, málo technické znalosti potřebné.

### <a name="the-razor-syntax-server-code-and-aspnet"></a>Syntaxe Razor, kódu serveru a technologie ASP.NET

Syntaxe Razor je jednoduché programovací syntaxi pro vkládání serverový kód na webové stránce. Na webové stránce, která používá syntaxi Razor, existují dva typy obsahu: obsah a server kód klienta. Obsah klienta je věci, které jste zvyklí na webových stránkách: Značka jazyka HTML (prvky), stylu informace, jako jsou šablony stylů CSS, možná některé klientského skriptu, jako je JavaScript nebo jako prostý text.

Syntaxe Razor můžete přidat kód serveru obsah tohoto klienta. -Li existuje serverový kód na stránce, serveru spustí tento kód nejprve předtím, než ji odešle stránky v prohlížeči. Běží na serveru, kód můžete provádět úlohy, které může být mnohem složitější udělat pomocí klienta obsahu samostatně, jako je přístup k databázím na serveru. Co je nejdůležitější, můžete vytvořit kód serveru dynamicky klientům obsahu &#8212; může generovat kód HTML nebo další obsah v reálném čase a potom je odešle do prohlížeče spolu s statický kód HTML, který stránka může obsahovat. Z pohledu prohlížeče se nijak neliší od jakýkoli klient obsah klientům obsahu, který je generován kódu serveru. Jak už víte, do kódu serveru, který je potřeba je úplně jednoduché.

Webové stránky ASP.NET, které obsahují syntaxi Razor mít speciální příponu (*.cshtml* nebo *.vbhtml*). Server rozpozná tato rozšíření, spustí kód, který je označen se syntaxí Razor a potom odešle stránku v prohlížeči.

### <a name="where-does-aspnet-fit-in"></a>Kde se uplatní technologie ASP.NET?

Syntaxe Razor je založena na technologii od Microsoftu, volá se, ASP.NET, která zase je založena na rozhraní Microsoft .NET Framework. Rozhraní.NET Framework je velký objem, komplexní programovací rozhraní od Microsoftu pro vývoj prakticky libovolného typu aplikace. ASP.NET je součástí rozhraní .NET Framework, která je navržená speciálně pro vytváření webových aplikací. Vývojáři ASP.NET použili k vytvoření mnoha největší a nejvyšší provoz webů na světě. (Když se zobrazí příponu názvu souboru *.aspx* jako část adresy URL v lokalitě, budete vědět, zda byla vytvořena webu pomocí technologie ASP.NET.)

Syntaxe Razor poskytuje veškerou sílu technologie ASP.NET, ale pomocí zjednodušenou syntaxi, která je jednodušší zjistit, zda jste začátečník, a díky tomu budete produktivnější Pokud nejste odborníkem. I když tato syntaxe se snadno používá, jeho řady relaci ASP.NET a .NET Framework znamená, že vaše weby jsou složitější, nutné sílu větší architektury, které jsou k dispozici.

![Razor Img8](introducing-razor-syntax-c/_static/image8.jpg)

> [!TIP] 
> 
> **Třídy a instance**
> 
> Kód serveru ASP.NET používá objekty, které jsou zase založené na nápad třídy. Třída je definice nebo šablony objektu. Například může aplikace obsahovat `Customer` třídu, která definuje vlastnosti a metody, které potřebuje každý objekt zákazníka.
> 
> Pokud aplikace potřebuje pracovat s informacemi o skutečné zákazníka, vytvoří instanci (nebo *vytvoří instanci*) objekt zákazníka. Je každého zákazníka samostatnou instanci `Customer` třídy. Každá instance podporuje stejné vlastnosti a metody, ale hodnoty vlastností pro každou instanci se obvykle liší, protože každý objekt zákazníka je jedinečný. V objektu jednoho zákazníka `LastName` vlastnost může být "Novák"; v jiném objektu zákazníků, `LastName` vlastnost může být "Jones."
> 
> Podobně je všechny jednotlivé webové stránky ve vaší lokalitě `Page` objekt, který je instancí `Page` třídy. Je tlačítko na stránce `Button` objekt, který je instancí `Button` třídy a tak dále. Každá instance má vlastní vlastnosti, ale všechny jsou založeny na zadaných v definici třídy objektu.


## <a name="basic-syntax"></a>Základní syntaxe

Dříve jste viděli základní příklad, jak vytvořit stránku ASP.NET Web Pages a jak můžete přidat kód serveru do kódu HTML. Zde se dozvíte základní informace o psaní kódu serveru ASP.NET pomocí syntaxe Razor &#8212; tedy programovací jazyk pravidla.

Pokud máte zkušenosti s programování (zejména pokud jste použili C, C++, C#, Visual Basic nebo JavaScript), velkou část můžete přečíst tady bude známé. Budete pravděpodobně potřebovat pouze s jak server kód přidá značku v *.cshtml* soubory.

<a id="BM_CombiningTextMarkupAndCode"></a>
### <a name="combining-text-markup-and-code-in-code-blocks"></a>Kombinování textu, značek a kódu v blocích kódu

V serveru bloky kódu často chcete výstup text značky (nebo obojí) na stránce. Pokud blok kódu serveru obsahuje text, který není kódu a který místo toho má být vykreslen jako je, musí být nedokáží rozlišit tento text z kódu ASP.NET. Chcete-li to provést několika způsoby.

- Uzavřete text v elementu HTML jako `<p></p>` nebo `<em></em>`:   

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample12.cshtml)]

    HTML element mohou zahrnovat text, další prvky jazyka HTML a výrazy kódu serveru. Když ASP.NET uvidí počáteční značky HTML (například `<p>`), se vykresluje všechno včetně elementu a jeho obsah, jako je v prohlížeči, jak funguje překlad výrazů kódu na serveru.
- Použití `@:` operátor nebo `<text>` elementu. `@:` Výstupy jednořádkový obsah, který obsahuje prostý text nebo nespárované značky jazyka HTML; `<text>` element vloží několik řádků výstupu. Tyto možnosti jsou užitečné, pokud nechcete, aby k vykreslení elementu HTML jako část ve výstupu.  

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample13.cshtml)]

    Pokud chcete výstup více řádků textu nebo nespárované značky HTML, může předcházet každý řádek `@:`, nebo je možné uzavřít do uvozovek na řádku `<text>` elementu. Podobně jako `@:` operátor`<text>` značky jsou použitý technologií ASP.NET k identifikaci obsahu textu a nikdy se zobrazují v výstup stránky.

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample14.cshtml)]

    První příklad v předchozím příkladu se opakuje, ale používá jednu dvojici `<text>` značky uzavřít text k vykreslení. V druhém příkladu `<text>` a `</text>` značky uzavřít tři řádky, které mají některé uncontained text a bezkonkurenční značky HTML (`<br />`), spolu s kódu serveru a odpovídající značky jazyka HTML. Znovu, může také předcházet každý řádek zvlášť `@:` operátor; buď způsob, jak funguje.

    > [!NOTE]
    > Když text výstupu jak je znázorněno v této části &#8212; pomocí HTML elementu, `@:` operátor nebo `<text>` element &#8212; ASP.NET není s kódováním HTML výstup. (Jak je uvedeno výše, ASP.NET kódování výstup výrazů kódu na server a server bloky kódu, které předchází `@`, s výjimkou zvláštní případy, které jste si poznamenali v této části.)

### <a name="whitespace"></a>Prázdné znaky

Nadbytečné mezery v příkazu (i mimo něj řetězcový literál) nemají vliv – příkaz:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample15.cshtml)]

Konec řádku v příkazu nemá žádný vliv na příkazu a příkazy pro lepší čitelnost může obtékat. Následující příkazy jsou stejné:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample16.cshtml)]

Nelze však zalomení řádku uprostřed řetězcový literál. Nebude fungovat v následujícím příkladu:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample17.cshtml)]

Kombinování dlouhý řetězec, který obtéká stejně jako ve výše uvedeném kódu více řádků, existují dvě možnosti. Můžete použít operátor zřetězení (`+`), což uvidíte dále v tomto článku. Můžete také použít `@` znak pro vytvoření doslovný řetězec literálu, protože jste viděli dříve v tomto článku. Můžete přerušit doslovný řetězec literálů na řádcích:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample18.cshtml)]

### <a name="code-and-markup-comments"></a>Kód (a značky) komentáře

Komentáře umožňují ponechte poznámky k sobě nebo jiné. Také umožňují zakázat (*okomentujte*) část kódu nebo značky, které nechcete, aby ke spuštění, ale chcete zachovat na stránce prozatím.

Existuje jiný komentáře syntaxe Razor kód a kód HTML. Stejně jako u všech kódu Razor, komentáře syntaxe Razor jsou zpracování (a potom se odeberou) na serveru před stránky je odesláno prohlížeči. Proto přinesly syntaxe Razor umožňuje umístit komentáře do kódu (nebo dokonce do kódu), která se zobrazí, když upravíte soubor, ale uživatelé nezobrazí, i v zdroje stránky.

Pro komentáře syntaxe Razor rozhraní ASP.NET, spusťte komentář `@*` a ukončit s `*@`. Komentář může být na jeden nebo více řádků:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample19.cshtml)]

Tady je komentář v rámci bloku kódu:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample20.cshtml)]

Tady je stejného bloku kódu, s řádkem kódu označené jako komentář tak, aby na něm nebudou spouštět:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample21.cshtml)]

Uvnitř bloku kódu jako alternativu k použití komentáře syntaxe Razor, můžete použít přinesly syntaxe programovací jazyk, který používáte, jako je C#:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample22.cshtml)]

V jazyce C#, Jednořádkové komentáře předchází `//` znaků a víceřádkových komentářů začínat `/*` a na konci `*/`. (Stejně jako u komentáře syntaxe Razor, C# komentáře se zobrazí v prohlížeči.)

Pro značky pravděpodobně znáte, můžete vytvořit komentář HTML:

[!code-xml[Main](introducing-razor-syntax-c/samples/sample23.xml)]

Komentáře HTML začínat `<!--` znaků a končit `-->`. Komentáře ve formátu HTML můžete použít ohraničit pouze text, ale také všechny značky HTML, které si chtít zachovat na stránce, ale nechcete k vykreslení. Tento komentář HTML se skrýt celý obsah značky a text, který obsahují:

[!code-html[Main](introducing-razor-syntax-c/samples/sample24.html)]

Na rozdíl od komentáře syntaxe Razor, komentáře HTML *jsou* vykreslen na stránce a uživatel můžete zobrazit podle zdrojové stránky.

Razor má omezení na vnořené bloky jazyka C#. Další informace najdete v části [s názvem proměnné jazyka C# a vnořené bloky generovat nefunkční kód](http://aspnetwebstack.codeplex.com/workitem/1914)

## <a name="variables"></a>Proměnné

Proměnná je pojmenovaný objekt, který použijete k ukládání dat. Můžete pojmenovat proměnné cokoli, ale název musí začínat znakem abecedy a nesmí obsahovat prázdné znaky nebo vyhrazené znaky.

### <a name="variables-and-data-types"></a>Datové typy a proměnné

Proměnná může mít určitý datový typ, který označuje, jaký druh dat je uložen v proměnné. Můžete použít proměnné řetězce, které ukládají hodnoty řetězce (například &quot;Hello world&quot;), celočíselné proměnné, které ukládají hodnoty celé číslo (např. 3 nebo 79) a proměnné pro datum, které ukládají data v různých formátech (např. 4/12/2012 nebo března 2009 ). A existuje mnoho dalších typů dat, která vám pomůže.

Však obvykle není nutné zadat typ pro proměnnou. Ve většině případů, technologie ASP.NET můžete zjistit typ závislosti na tom, jak se používá data v proměnné. (Někdy je nutné zadat typ; uvidíte příkladech, kde je hodnota true.)

Deklarujte proměnné pomocí `var` – klíčové slovo (Pokud nechcete zadat typ) nebo pomocí názvu typu:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample25.cshtml)]

Následující příklad ukazuje některé typické použití proměnných na webové stránce:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample26.cshtml)]

Pokud kombinujete v předchozích příkladech na stránce, uvidíte toto zobrazí v prohlížeči:

![Razor Img9](introducing-razor-syntax-c/_static/image9.jpg)

### <a name="converting-and-testing-data-types"></a>Převod a testování datových typů

Přestože ASP.NET obvykle můžete automaticky určit typ dat, někdy se to neplatí. Proto můžete potřebovat pomoc ASP.NET pomocí provádí explicitní převod. I když nemáte k dispozici pro převod typů, někdy je vhodné otestovat, jaký typ dat je možné, že pracujete s.

Nejběžnější případ je, že budete muset převést řetězec na jiný typ, například na celé číslo nebo datum. Následující příklad ukazuje typické případy, kdy je nutné převést řetězec na číslo.

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample27.cshtml)]

Jako pravidlo uživatelský vstup je pro vás řetězce. I v případě, že výzva uživateli k zadání čísla a i v případě, že jste zadali číslici, při odeslání vstup uživatele a čtení v kódu, data jsou ve formátu řetězce. Proto je nutné převést řetězec na číslo. V příkladu při pokusu o provedení aritmetické operace na hodnotách bez převodu, chybová zpráva výsledky, protože ASP.NET nejde přidat dva řetězce:

*Typ "řetězec" na 'int' nelze implicitně převést.*

K převodu hodnoty na celá čísla, volání `AsInt` metody. Pokud převod není úspěšné, pak přidáte čísla.

Následující tabulka uvádí některé běžné metody převodu a testování pro proměnné.

:::row:::
    :::column:::
    <strong>– Metoda</strong>
    :::column-end:::
    :::column:::
    <strong>Popis</strong>
    :::column-end:::
    :::column:::
    <strong>Příklad</strong>
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsInt(), IsInt()`
    :::column-end:::
    :::column:::
    Převede řetězec představující celé číslo (např. "593") na celé číslo.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample28.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsBool(), IsBool()`
    :::column-end:::
    :::column:::
    Převede řetězec jako &quot;true&quot; nebo &quot;false&quot; s typem Boolean.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample29.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsFloat(), IsFloat()`
    :::column-end:::
    :::column:::
    Převede řetězec obsahující desetinná hodnota jako &quot;1.3&quot; nebo &quot;7.439&quot; na číslo s plovoucí desetinnou čárkou.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample30.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsDecimal(), IsDecimal()`
    :::column-end:::
    :::column:::
    Převede řetězec obsahující desetinná hodnota jako &quot;1.3&quot; nebo &quot;7.439&quot; na desetinné číslo. (V technologii ASP.NET je přesnější než číslo s plovoucí desetinnou čárkou desetinné číslo.)
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample31.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsDateTime(), IsDateTime()`
    :::column-end:::
    :::column:::
    Převede řetězec představující hodnotu data a času na ASP.NET `DateTime` typu.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample32.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `ToString()`
    :::column-end:::
    :::column:::
    Převede jakýkoli jiný typ dat na řetězec.
    :::column-end:::
    :::column:::
        [!code-javascript[Main](introducing-razor-syntax-c/samples/sample33.js)]
    :::column-end:::
:::row-end:::

## <a name="operators"></a>Operátory

Operátor je klíčové slovo nebo znak, který říká technologie ASP.NET, jaký druh příkaz k provedení ve výrazu. Jazyk C# (a syntaxi Razor, která je založena na něm) podporuje mnoho operátorů, ale je potřeba jenom rozpoznat pár, abyste mohli začít. Následující tabulka shrnuje nejčastější operátory.


:::row:::
    :::column:::
    <strong>– Operátor</strong>
    :::column-end:::
    :::column:::
    <strong>Popis</strong>
    :::column-end:::
    :::column:::
    <strong>Příklady</strong>
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `+` `-` `*` `/`
    :::column-end:::
    :::column:::
    Matematické operátory používat ve výrazech pro číselná.
    :::column-end:::
    :::column:::
        [!code-css[Main](introducing-razor-syntax-c/samples/sample34.css)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `=`
    :::column-end:::
    :::column:::
    Přiřazení. Přiřadí hodnotu na pravé straně příkazu na objekt na levé straně.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample35.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `==`
    :::column-end:::
    :::column:::
    Rovnost. Vrátí `true` Pokud jsou hodnoty stejné. (Všimněte si rozdílu mezi `=` operátor a `==` operátor.)
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample36.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `!=`
    :::column-end:::
    :::column:::
    Nerovnost. Vrátí `true` Pokud hodnoty nejsou shodné.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample37.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `< > <= >=`
    :::column-end:::
    :::column:::
    Less – než, větší-než, menší než – nebo se znaménkem rovná a větší než nebo rovno.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample38.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `+`
    :::column-end:::
    :::column:::
    Zřetězení, který se používá pro připojení řetězce. ASP.NET ví rozdíl mezi tento operátor a operátor sčítání na základě datového typu výrazu.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample39.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `+=` `-=`
    :::column-end:::
    :::column:::
    Přírůstek a snížení operátory, které operátorů sčítání a odečítání 1 (v uvedeném pořadí) z proměnné.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample40.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `.`
    :::column-end:::
    :::column:::
    Tečka. Použít k rozlišení objekty a jejich vlastnosti a metody.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample41.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `()`
    :::column-end:::
    :::column:::
    Závorky. Použít skupinové výrazy a předat parametry metody.
    :::column-end:::
    :::column:::
        [!code-javascript[Main](introducing-razor-syntax-c/samples/sample42.js)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `[]`
    :::column-end:::
    :::column:::
    Složené závorky. Používá pro přístup k hodnoty pole nebo kolekce.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample43.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `!`
    :::column-end:::
    :::column:::
    Není. Obrátí `true` hodnota, která se `false` a naopak. Obvykle se používá jako zjednodušený způsob, jak otestovat pro `false` (to znamená pro není `true`).
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample44.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `&&` <code>&#124;&#124;</code>
    :::column-end:::
    :::column:::
    Logický operátor AND a které se používají k propojení podmínky společně.
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample45.cs)]
    :::column-end:::
:::row-end:::

<a id="ID_WorkingWithFileAndFolderPaths"></a>
## <a name="working-with-file-and-folder-paths-in-code"></a>Práce s souboru a cesty ke složkám v kódu

Často budete pracovat s cestami k souborům a složkám v kódu. Tady je příklad struktury fyzické složce pro web, jak se může zobrazovat ve svém vývojovém počítači:

`C:\WebSites\MyWebSite default.cshtml datafile.txt \images Logo.jpg \styles Styles.css`

Tady jsou některé důležité podrobnosti o adresy URL a cesty:

- Adresa URL začíná na buď název domény (`http://www.example.com`) nebo název serveru (`http://localhost`, `http://mycomputer`).
- Adresa URL odpovídá fyzickou cestu na hostitelském počítači. Například `http://myserver` může odpovídat složce *C:\websites\mywebsite* na serveru.
- Virtuální cesta je zkratka pro reprezentaci cest v kódu bez nutnosti zadávat úplnou cestu. Obsahuje části adresy URL, která následuje název domény nebo serveru. Při použití virtuální cesty, můžete přesunout kód do jiné domény nebo serveru bez nutnosti aktualizovat cesty.

Tady je příklad, který vám pomůže pochopit rozdíly:

| Úplnou adresu URL | `http://mycompanyserver/humanresources/CompanyPolicy.htm` |
| --- | --- |
| Název serveru | *mycompanyserver* |
| Virtuální cesta | */humanresources/CompanyPolicy.htm* |
| Fyzická cesta | *C:\mywebsites\humanresources\CompanyPolicy.htm* |

Virtuální kořenový adresář je /, stejně jako kořenový C: jednotka je \. (Virtuální složka cesty vždy používat lomítka.) Virtuální cesta ke složce nemusí mít stejný název jako fyzické složce. může se jednat jako alias. (Na provozních serverech virtuální cesta zřídka odpovídá přesně fyzickou cestu.)

Při práci se soubory a složkami v kódu, někdy potřebujete odkazovat na fyzickou cestu a někdy virtuální cesty, v závislosti na tom, jaké objekty pracujete s. Technologie ASP.NET poskytuje tyto nástroje pro práci s cestami k souborům a složkám v kódu: `Server.MapPath` metody a `~` operátor a `Href` metoda.

### <a name="converting-virtual-to-physical-paths-the-servermappath-method"></a>Převod virtuálních a fyzických cest: Server.MapPath – metoda

`Server.MapPath` Metoda převede virtuální cestu (například */default.cshtml*) na absolutní cestu, fyzické (jako *C:\WebSites\MyWebSiteFolder\default.cshtml*). Tuto metodu použijte kdykoli potřebujete úplná fyzická cesta. Typickým příkladem je při čtení nebo zápis textový soubor nebo soubor obrázku na webovém serveru.

Obvykle neznáte absolutní fyzická cesta k webu na serveru pro hostování lokality, abyste této metody můžete převést cestu si jisti, virtuální cestu – do příslušné cesty na serveru za vás. Předat virtuální cestu k souboru nebo složky na metodu a vrátí fyzickou cestu:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample46.cshtml)]

### <a name="referencing-the-virtual-root-the--operator-and-href-method"></a>Odkazování na virtuální kořenový adresář: ~ – operátor a metoda Href

V *.cshtml* nebo *.vbhtml* soubor, můžete odkazovat pomocí cesty kořenové složky `~` operátor. To je velmi užitečný, protože se můžete pohybovat stránky na webu a jakékoli odkazy, které obsahují další stránky nesmí být nefunkční. Je taky užitečný v případě, kdy přesunout svůj web do jiného umístění. Následuje několik příkladů:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample47.cshtml)]

Pokud webová stránka se `http://myserver/myapp`, zde je, jak ASP.NET bude zpracovávat tyto cesty při spuštění stránky:

- `myImagesFolder`: `http://myserver/myapp/images`
- `myStyleSheet` : `http://myserver/myapp/styles/Stylesheet.css`

(Ve skutečnosti neuvidíte těchto cest jako hodnotu proměnné, ale ASP.NET bude zacházet s cesty, jak, pokud je to, co byly.)

Můžete použít `~` operátor v serverovém kódu (jak je uvedeno výše) a v kódu, například takto:

[!code-html[Main](introducing-razor-syntax-c/samples/sample48.html)]

V kódu, můžete použít `~` operátor k vytvoření cesty k prostředkům, jako jsou soubory obrázků, jiné webové stránky a souborů CSS. Při spuštění stránky ASP.NET vypadat prostřednictvím stránky (kódu a kódu) a řeší všechny `~` odkazy na příslušné cesty.

## <a name="conditional-logic-and-loops"></a>Podmíněnou logiku a smyčky

Kód serveru ASP.NET vám umožní provádět úkoly na základě podmínek a napsat kód, který se opakuje příkazy s konkrétním počtem opakování (to znamená, že kód, který běží smyčky).

### <a name="testing-conditions"></a>Testování podmínek

K otestování jednoduché podmínky použití `if` příkaz, který vrátí hodnotu true nebo false podle testu zadáte:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample49.cshtml)]

`if` – Klíčové slovo začíná bloku. Skutečný test (podmínky) v závorkách a vrátí hodnotu true nebo false. Příkazy, na kterých běží, je-li test true jsou uzavřeny ve složených závorkách. `if` Výraz může obsahovat `else` blok, který určuje příkazy ke spuštění, pokud podmínka není splněna:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample50.cshtml)]

Můžete přidat více podmínek použití `else if` blok:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample51.cshtml)]

V tomto příkladu, pokud první podmínka vyhodnocena jako v oblasti, pokud blok není true, `else if` podmínka je zaškrtnuté políčko. Pokud tato podmínka je splněna, příkazy v `else if` jsou spuštěny. Pokud jsou splněna žádná z podmínek, příkazy v `else` jsou spuštěny. Můžete přidat libovolný počet else if blokuje a pak zavřete s `else` blokovat, jako &quot;všechno ostatní&quot; podmínku.

Chcete-li otestovat velký počet podmínek, použijte `switch` blok:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample52.cshtml)]

K otestování hodnotu v závorkách (v tomto příkladu `weekday` proměnné). Používá každý samostatný test `case` příkaz, který končí dvojtečkou (:). Pokud hodnota `case` příkazu se shoduje s hodnotou testu, spuštění kódu v tomto případě bloku. Zavřete každou case – příkaz s `break` příkazu. (Pokud jste zapomněli začlenit přerušení v každém `case` bloku kódu z dalších `case` bude také spustit příkaz.) A `switch` blok má často `default` příkaz jako poslední případ &quot;všechno ostatní&quot; možnost, která se spustí v případě, že žádný z ostatních případech není true.

Výsledek poslední dva podmíněné bloky zobrazí v prohlížeči:

![Razor Img10](introducing-razor-syntax-c/_static/image10.jpg)

### <a name="looping-code"></a>Opakování ve smyčce kódu

Často je potřeba spustit stejné příkazy opakovaně. To provedete tak, že opakování ve smyčce. Například můžete často spustit stejné příkazy pro každou položku v kolekci dat. Pokud víte přesně kolikrát chcete opakovat, můžete použít `for` smyčky. Tento druh smyčky je užitečné zejména pro počítání nebo počítání:

[!code-html[Main](introducing-razor-syntax-c/samples/sample53.html)]

Smyčky začíná `for` – klíčové slovo, za nímž následuje tři příkazy v závorkách, každý ukončeným středníkem.

- Uvnitř závorek, prvním příkazem (`var i=10;`) vytvoří čítač a inicializuje ji na hodnotu 10. Není nutné pojmenovat čítač `i` &#8212; můžete použít jakoukoli proměnnou. Když `for` cyklu, hodnota čítače se zvýší automaticky.
- Druhý příkaz (`i < 21;`) nastaví podmínku, jak daleko se mají spočítat. V takovém případě bude nutné přejít na maximálně 20 (to znamená, pokračujte dál čítač je menší než 21).
- Třetí příkaz (`i++` ) používá operátor přírůstku, který jednoduše Určuje, že čítač musí mít 1 přidá do něj při každém spuštění smyčky.

Uvnitř závorek je kód, který se spustí pro každou iteraci smyčky. Kód vytvoří nový odstavec (`<p>` element) každý čas a přidá nový řádek do výstupu, zobrazování hodnotě z `i` (Čítač). Když spustíte tuto stránku, tento příklad vytvoří 11 řádky zobrazení výstupu, s textem na každém řádku označující počet položek.

![Razor Img11](introducing-razor-syntax-c/_static/image11.jpg)

Pokud pracujete s kolekce nebo pole, často používají `foreach` smyčky. Kolekce je skupina podobných objektů a `foreach` smyčky umožňuje provést úlohu pro každou položku v kolekci. Tento druh smyčky je vhodné pro kolekce, protože na rozdíl od `for` smyčky, není nutné zvýší čítač nebo nastavit omezení. Místo toho `foreach` kód smyčky jednoduše pokračuje přes kolekci, dokud se nedokončí.

Například následující kód vrátí položky v `Request.ServerVariables` kolekce, které je objekt, který obsahuje informace o webovém serveru. Použije `foreac` h cyklu zobrazovaný název každé položky tak, že vytvoříte nový `<li>` prvek v seznamu s odrážkami HTML.

[!code-html[Main](introducing-razor-syntax-c/samples/sample54.html)]

`foreach` – Klíčové slovo postupuje podle závorky, pokud deklarujete proměnnou, která představuje jednu položku v kolekci (v tomto příkladu `var item`) a po něm `in` – klíčové slovo, za nímž následuje kolekci chcete projít. V těle `foreach` smyčky, dostanete aktuální položky pomocí proměnné, která je deklarována jako dříve.

![Razor Img12](introducing-razor-syntax-c/_static/image12.jpg)

Chcete-li vytvořit smyčku více pro obecné účely, použijte `while` – příkaz:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample55.cshtml)]

A `while` smyčky začíná `while` – klíčové slovo, za nímž následuje závorky, kde můžete určit, jak dlouho bude pokračovat smyčky (zde pro tak dlouho, dokud `countNum` je menší než 50), pak bloku opakovat. Obvykle zvýšit smyčky (přidání do) nebo dekrementace (odečíst od) proměnné nebo objektu se používá pro počítání. V tomto příkladu `+=` operátor přičte 1 k `countNum` při každém spuštění smyčky. (Chcete-li snížení proměnná ve smyčce počítá směrem dolů, použijte operátor dekrementace `-=`).

## <a name="objects-and-collections"></a>Objekty a kolekce

Téměř vše na webu technologie ASP.NET je objekt, včetně webové stránky. Tato část popisuje některé důležité objekty, které budete pracovat se často ve vašem kódu.

### <a name="page-objects"></a>Objekty stránky

Na stránce je nejzákladnější objekt v technologii ASP.NET. Můžete přistupovat k vlastnosti objektu page přímo bez žádné opravňující objektu. Následující kód načte cestu k souboru na stránce, pomocí `Request` objektu stránky:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample56.cshtml)]

Aby bylo jasné, že už odkazuje vlastnosti a metody na aktuální objekt stránky, můžete volitelně použít klíčové slovo `this` k reprezentaci objektu page ve vašem kódu. Tady je předchozí příklad kódu s `this` přidán k reprezentaci stránky:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample57.cshtml)]

Můžete použít vlastnosti `Page` můžete získat velké množství informací, jako například:

- `Request`. Jak už víte, to je kolekce informací o aktuálním požadavku, včetně typu prohlížeče přišel požadavek, adresa URL stránky, identita uživatele, atd.
- `Response`. Toto je kolekce informací o odpovědi (stránky), které se odešlou do prohlížeče při dokončení kódu serveru. Tuto vlastnost můžete například použít při zápisu informací do odpovědi. 

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample58.cshtml)]

<a id="ID_CollectionsAndObjects"></a>
### <a name="collection-objects-arrays-and-dictionaries"></a>Kolekce objektů (polí a slovníky)

A *kolekce* je skupina objektů stejného typu, jako je například kolekce `Customer` objekty z databáze. Technologie ASP.NET obsahuje mnoho předdefinovaných kolekcí, jako `Request.Files` kolekce.

Často budete pracovat s daty v kolekcích. Jsou dvě běžné typy kolekcí *pole* a *slovníku*. Pole je užitečné, když chcete uložit kolekci podobných položek, ale nebudete chtít vytvořit samostatné proměnnou pro uchování položky:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample59.cshtml)]

S poli, je třeba deklarovat určitý datový typ. `string`, `int`, nebo `DateTime`. K označení, že proměnné můžou obsahovat pole, přidat závorky deklarace (například `string[]` nebo `int[]`). Můžete přístup k položkám v poli pomocí jejich pozice (index) nebo pomocí `foreach` příkazu. Indexy pole jsou počítány od nuly &#8212; tedy první položka je na pozici 0, druhá položka je na pozici 1 a tak dále.

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample60.cshtml)]

Můžete určit počet položek v poli získáním jeho `Length` vlastnost. Získá pozici konkrétní položku v poli (k vyhledání pole), použijte `Array.IndexOf` metody. Můžete také provést třeba zpětného obsah pole ( `Array.Reverse` metoda) nebo řazení obsahu ( `Array.Sort` metoda).

Výstupní řetězec pole Kód zobrazený v prohlížeči:

![Razor Img13](introducing-razor-syntax-c/_static/image13.jpg)

Slovník je kolekce párů klíč/hodnota, ve kterém zadat klíč (nebo název) k nastavení nebo načtení odpovídající hodnotě:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample61.cshtml)]

Chcete-li vytvořit slovník, použijte `new` – klíčové slovo k označení, že vytváříte nový objekt slovníku. Slovník můžete přiřadit k proměnné pomocí `var` – klíčové slovo. Označení datové typy položek ve slovníku pomocí ostrých závorek ( `< >` ). Na konci deklarace je nutné přidat dvojici závorek, protože je to ve skutečnosti metodou, která vytvoří nový slovník.

Přidání položek do slovníku, můžete volat `Add` metoda proměnnou slovníku (`myScores` v tomto případě) a pak zadejte klíč a hodnotu. Alternativně můžete použít hranaté závorky k označení klíče a provádět jednoduché přiřazení, jako v následujícím příkladu:

[!code-csharp[Main](introducing-razor-syntax-c/samples/sample62.cs)]

Pokud chcete získat hodnotu ze slovníku, můžete zadat klíč v závorkách:

[!code-csharp[Main](introducing-razor-syntax-c/samples/sample63.cs)]

## <a name="calling-methods-with-parameters"></a>Volání metody s parametry

Jak čtete výše v tomto článku, může mít objekty, které můžete naprogramovat pomocí metody. Například `Database` objekt může mít `Database.Connect` metody. Mnoho metody mají také jeden nebo více parametrů. A *parametr* je hodnota, která můžete předat metodě pro povolení metody a dokončení úkolu. Podívejte se například na deklaraci `Request.MapPath` metodu, která přijímá tři parametry:

[!code-csharp[Main](introducing-razor-syntax-c/samples/sample64.cs)]

(Řádek byl zabalen do byl čitelnější. Mějte na paměti, že můžete vložit konce řádků téměř všech místech, s výjimkou uvnitř řetězců, které jsou uzavřeny v uvozovkách.)

Tato metoda vrací fyzickou cestu na serveru, který odpovídá pro zadanou virtuální cestu. Jsou tři parametry pro metodu `virtualPath`, `baseVirtualDir`, a `allowCrossAppMapping`. (Všimněte si, že v deklaraci, parametry jsou uvedeny s datovými typy dat, který bude přijímat.) Při volání této metody, je třeba zadat hodnoty pro všemi třemi parametry.

Syntaxe Razor poskytuje dvě možnosti pro předání parametrů metodě: *poziční parametry* a *pojmenované parametry*. Volání metody pomocí poziční parametry, předání parametrů v případě přísného pořadí zadaném v deklaraci metody. (By obvykle znáte toto pořadí najdete dokumentaci k metodě.) Je nutné postupovat podle pořadí, a přeskočíte nelze některé z parametrů &#8212; Pokud potřeby předáte prázdný řetězec (`""`) nebo `null` pro poziční parametr, který není nutné hodnotu.

Následující příklad předpokládá, že máte složku s názvem *skripty* na vašem webu. Kód volá `Request.MapPath` metoda a předá hodnoty pro tři parametry ve správném pořadí. Pak zobrazí výslednou cestu namapované.

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample65.cshtml)]

Když metoda má velký počet parametrů, můžete zachovat váš kód lépe čitelný pomocí pojmenovaných parametrů. Volání metody pomocí pojmenované parametry, zadejte název parametru následovaný dvojtečkou (:) a hodnota. Výhodou pojmenované parametry je, že budete předávat v libovolném pořadí, které chcete. (Nevýhodou je, že volání metody není co nejkompaktnější.)

Následující příklad volá stejnou metodu, jak je uvedeno výše, ale používá pojmenované parametry k zadání hodnot:

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample66.cshtml)]

Jak je vidět, parametry jsou předány v jiném pořadí. Pokud spustíte z předchozího příkladu a v tomto příkladu, ale budete vrátí stejnou hodnotu.

<a id="ID_HandlingErrors"></a>
## <a name="handling-errors"></a>Zpracování chyb

### <a name="try-catch-statements"></a>Try-Catch – příkazy

Často musíte příkazů v kódu, který může selhat z důvodu mimo vaši kontrolu. Příklad:

- Pokud váš kód se pokusí vytvořit nebo získat přístup k souboru, může dojít k všechny možné druhy chyb. Požadovaný soubor nemusí existovat, může být zablokován, kód nemusí mít oprávnění a tak dále.
- Podobně pokud váš kód se pokouší aktualizovat záznamy v databázi, může být problémy s oprávněními, může dojít ke ztrátě připojení k databázi, data a šetřit tak může být neplatný a tak dále.

V programovacím prostředí, se nazývají těmito situacemi *výjimky*. Pokud váš kód narazí na výjimku, generuje (vyvolá výjimku) chybovou zprávu, která je v nejlepším případě obtěžující uživatelům:

![Razor Img14](introducing-razor-syntax-c/_static/image14.jpg)

V situacích, kdy váš kód může nastat výjimky a pokud se chcete vyhnout chybové zprávy tohoto typu, můžete použít `try/catch` příkazy. V `try` příkazu spustit kód, který při kontrole. V jedné nebo více `catch` příkazy, můžete vyhledat konkrétní chyby (konkrétní typy výjimek), které mohly nastat. Můžete vytvořit tolik `catch` příkazy jako vy, musíme se podívat chyb, které jsou očekávání.

> [!NOTE]
> Doporučujeme, abyste je velmi riskantní používat `Response.Redirect` metoda `try/catch` příkazy, protože to může způsobit výjimku na stránce.


Následující příklad ukazuje stránka, která vytváří textový soubor na první žádost o a poté zobrazí tlačítko, které umožňuje uživateli otevřít soubor. V příkladu záměrně používá chybný název souboru tak, aby způsobí výjimku. Tento kód obsahuje `catch` příkazy pro dvě výjimky: `FileNotFoundException`, která nastane, pokud název souboru je chybný, a `DirectoryNotFoundException`, která nastane, pokud ASP.NET i nelze najít složku. (Příkaz v tomto příkladu můžete odkomentovat Chcete-li zobrazit, jak se spustí při všechno funguje správně.)

Pokud váš kód nebyl zpracovat výjimky, zobrazí se chybová stránka jako na předchozím snímku obrazovky. Ale `try/catch` část pomůže zabránit uživateli v zobrazení tyto typy chyb.

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample67.cshtml)]

## <a name="additional-resources"></a>Další prostředky

**Programování v jazyce Visual Basic**


[Příloha: syntaxe a jazyk Visual Basic](https://go.microsoft.com/fwlink/?LinkId=202908)


**Referenční dokumentace**


[ASP.NET](https://msdn.microsoft.com/library/ee532866.aspx)

[Jazyk C#](https://msdn.microsoft.com/library/kx37x362.aspx)

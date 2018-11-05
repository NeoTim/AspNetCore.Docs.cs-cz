---
uid: web-pages/overview/getting-started/introducing-razor-syntax-vb
title: Úvod k programování v rozhraní ASP.NET Web používající syntaxi Razor (Visual Basic) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento dodatek poskytuje přehled o programování s webovými stránkami ASP.NET v jazyce Visual Basic pomocí syntaxe Razor.
ms.author: riande
ms.date: 02/07/2014
ms.assetid: 5da59646-e973-41cd-88a9-c6b2c0594027
msc.legacyurl: /web-pages/overview/getting-started/introducing-razor-syntax-vb
msc.type: authoredcontent
ms.openlocfilehash: 17a3a4925766b74446955a8e3a6fddbf9d29a721
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021700"
---
<a name="introduction-to-aspnet-web-programming-using-the-razor-syntax-visual-basic"></a>Úvod k programování v rozhraní ASP.NET Web používající syntaxi Razor (Visual Basic)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek obsahuje přehled programování s webovými stránkami ASP.NET pomocí syntaxe Razor a Visual Basic. ASP.NET je technologie od Microsoftu pro spouštění dynamické webové stránky na webové servery.
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


Většina příkladů použití rozhraní ASP.NET Web Pages se syntaxí Razor pomocí C#. Ale syntaxe Razor také podporuje jazyka Visual Basic. Programování v jazyce Visual Basic webové stránky ASP.NET, vytvoříte webovou stránku pomocí *.vbhtml* příponu názvu souboru a poté přidejte kód jazyka Visual Basic. Tento článek obsahuje přehled práce s jazykem Visual Basic a syntaxe pro vytvoření webových stránek ASP.NET.

> [!NOTE]
> Výchozí šablony webu pro Microsoft WebMatrix (**pekařství**, **Fotogalerie**, a **Starter Site**atd) jsou k dispozici v jazyce C# a Visual Basic verze. Šablony jazyka Visual Basic, tak můžete nainstalovat jako balíčky NuGet. Šablony webu jsou nainstalovány v kořenové složce webu do složky s názvem *Templates Microsoft*.


## <a name="the-top-8-programming-tips"></a>Začátek 8 Tipy pro programování

Tato část uvádí několik tipů, které je nezbytně potřeba ví, jak můžete začít psát kód serveru ASP.NET pomocí syntaxe Razor.

### <a name="1-you-add-code-to-a-page-using-the--character"></a>1. Přidejte kód do stránky pomocí znak @

`@` Začne znak vložené výrazy, jedním příkazem bloky a bloky více příkazů:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample1.vbhtml)]

Výsledek zobrazí v prohlížeči:

![Razor Img1](introducing-razor-syntax-vb/_static/image1.jpg)

> [!TIP] 
> 
> **Kódování HTML**
> 
> Při zobrazení obsahu do stránky pomocí `@` znaků, stejně jako v předchozích příkladech ASP.NET umístí kódování HTML výstup. To nahrazuje vyhrazené znaky HTML (například `<` a `>` a `&`) s kódy, které umožňují znaků, který se má zobrazit jako znaků na webové stránce nebude interpretován jako značku jazyka HTML nebo entity. Bez kódování HTML výstup z kódu serveru se nemusí zobrazit správně a může zpřístupnit stránky na bezpečnostní rizika.
> 
> Pokud je vaším cílem je výstup kód HTML, který vykreslí značky jako značka (třeba `<p></p>` odstavce nebo `<em></em>` pro zvýraznění textu), najdete v části [kombinaci textu, značek a kódu v blocích kódu](#BM_CombiningTextMarkupAndCode) dále v tomto článku.
> 
> Další informace o kódování HTML v [práce s formuláři HTML v ASP.NET Web Pages Sites](https://go.microsoft.com/fwlink/?LinkId=202892).


### <a name="2-you-enclose-code-blocks-with-codeend-code"></a>2. Použijte bloky kódu s kódem... Kód konce

Blok kódu obsahuje jeden nebo více příkazů kódu a je uzavřené s klíčovými slovy `Code` a `End Code`. Umístit otevírání `Code` – klíčové slovo ihned po `@` znak &#8212; nemůže být prázdné znaky mezi nimi.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample2.vbhtml)]

Výsledek zobrazí v prohlížeči:

![Razor Img2](introducing-razor-syntax-vb/_static/image2.jpg)

### <a name="3-inside-a-block-you-end-each-code-statement-with-a-line-break"></a>3. Uvnitř bloku ukončit každý příkaz kódu se konec řádku

V bloku kódu jazyka Visual Basic každý příkaz končí konec řádku. (Dále v tomto článku uvidíte způsob, jak zabalit příkaz dlouhé kódu do více řádků v případě potřeby.)

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample3.vbhtml)]

### <a name="4-you-use-variables-to-store-values"></a>4. Použití proměnných k ukládání hodnot

Můžete ukládat hodnoty *proměnnou*, včetně řetězců, čísel a data, atd. Vytvoření nové proměnné pomocí `Dim` – klíčové slovo. Hodnoty proměnných můžete vložit přímo do stránky pomocí `@`.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample4.vbhtml)]

Výsledek zobrazí v prohlížeči:

![Razor Img3](introducing-razor-syntax-vb/_static/image3.jpg)

### <a name="5-you-enclose-literal-string-values-in-double-quotation-marks"></a>5. Použijte hodnoty řetězcový literál v uvozovkách

A *řetězec* je posloupnost znaků, které jsou považovány za text. Pokud chcete zadat řetězec, uzavřete do dvojitých uvozovek:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample5.vbhtml)]

K vložení uvozovek v rámci hodnotu řetězce, vložte dva znaky uvozovek. Pokud chcete znak dvojité uvozovky, které chcete zobrazit jednou v výstup stránky, zadejte ho jako `""` v rámci citovaný řetězec a pokud chcete, aby se dvakrát zobrazí, zadejte ho jako `""""` v rámci řetězec v uvozovkách.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample6.vbhtml)]

Výsledek zobrazí v prohlížeči:

![Razor Img4](introducing-razor-syntax-vb/_static/image4.jpg)

### <a name="6-visual-basic-code-is-not-case-sensitive"></a>6. Kód jazyka Visual Basic není malá a velká písmena

Jazyk Visual Basic není malá a velká písmena. Programování klíčová slova (stejně jako `Dim`, `If`, a `True`) a názvy proměnných (například `myString`, nebo `subTotal`) může být napsán v každém případě.

Následující řádky kódu přiřadit hodnotu k proměnné `lastname` pomocí malé název a potom výstupní hodnoty proměnné stránky pomocí názvu velká písmena.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample7.vbhtml)]

Výsledek zobrazí v prohlížeči:

![vb-syntax-5](introducing-razor-syntax-vb/_static/image5.jpg)

### <a name="7-much-of-your-coding-involves-working-with-objects"></a>7. Velká část psaní kódu zahrnuje práci s objekty

Objekt představuje věc, kterou můžete programovat s &#8212; stránku, textové pole, soubor, obrázek, webový požadavek, e-mailovou zprávu, záznam zákazníka (řádek databáze), atd. Objekty mají vlastnosti, které popisují jejich vlastnosti &#8212; textový objekt pole má `Text` vlastnost, má objekt žádosti `Url` vlastnost, nemá e-mailovou zprávu `From` vlastnost a objekt zákazníka má `FirstName` Vlastnost. Objekty mají také metody, které jsou &quot;příkazy&quot; mohou provádět. Mezi příklady patří objektu soubor `Save` metoda, objektu image `Rotate` metoda a objekt e-mailu `Send` – metoda.

Často budete pracovat `Request` pole objektu, který poskytuje informace, například hodnoty formuláře na stránce (textových polí atd.), jaký typ prohlížeče přišel požadavek, adresa URL stránky, identita uživatele, atd. Tento příklad ukazuje způsob pro přístup k vlastnostem `Request` objekt a jak volat `MapPath` metodu `Request` objektu, který obsahuje absolutní cestu na stránce na serveru:

[!code-html[Main](introducing-razor-syntax-vb/samples/sample8.html)]

Výsledek zobrazí v prohlížeči:

![Razor Img5](introducing-razor-syntax-vb/_static/image6.jpg)

### <a name="8-you-can-write-code-that-makes-decisions"></a>8. Můžete napsat kód, který provádí rozhodnutí

Klíčovou funkcí dynamické webové stránky je, že určíte, co se provedou v závislosti na podmínkách. Nejběžnější způsob je pomocí `If` – příkaz (a volitelné `Else` příkaz).

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample9.vbhtml)]

Příkaz `If IsPost` je zjednodušený způsob psaní `If IsPost = True`. Spolu s `If` příkazy, existuje široká škála způsobů, jak otestovat podmínky, při opakovaném bloky kódu, a tak dále, které jsou popsány dále v tomto článku.

Výsledek zobrazí v prohlížeči (po kliknutí na tlačítko **odeslat**):

![Razor Img6](introducing-razor-syntax-vb/_static/image7.jpg)

> [!TIP] 
> 
> **HTTP GET a POST metody a vlastnosti IsPost**
> 
> Protokol použitý pro webové stránky (HTTP) podporuje velmi omezený počet metod (&quot;příkazy&quot;), která se používají k provádění požadavků na server. Nichž dva nejčastější jsou GET, který slouží k načtení stránky a příspěvku, který se používá k odeslání stránky. Obecně platí uživatel požádá o stránku při prvním požadavku na stránku pomocí GET. Pokud uživatel vyplní ve formuláři a poté klikněte na tlačítko **odeslat**, prohlížeč odešle požadavek POST na server.
> 
> Ve webovém programování často je užitečné vědět, jestli na stránce jsou požadovány jako GET nebo POST, abyste věděli, jak zpracování stránky. ASP.NET Web Pages, můžete použít `IsPost` vlastnosti chcete zobrazit, zda je požadavek GET nebo POST. Pokud je příspěvek, požadavek `IsPost` vlastnost vrátí hodnotu PRAVDA, a můžete provádět věci, jako je čtení hodnoty polí ve formuláři. Mnoho příkladů, zobrazí se vám ukazují, jak zpracovat stránce odlišně v závislosti na hodnotě `IsPost`.


## <a name="a-simple-code-example"></a>Jednoduchým příkladem kódu

Tento postup ukazuje, jak vytvořit stránku, která ukazuje základní programovací techniky. V tomto příkladu vytvoříte stránky, která umožňuje uživatelům zadat dvě čísla, potom se přidají a zobrazí výsledek.

1. V editoru, vytvořte nový soubor s názvem *AddNumbers.vbhtml*.
2. Zkopírujte následující kód a kód na stránku, nahrazení nic již na stránce.

    [!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample10.vbhtml)]

    Tady jsou některé věci za vás mějte na paměti:

    - `@` Znak spustí první blok kódu ve stránce, a předchází `totalMessage` vložené proměnné dole.
    - Bloku v horní části stránky je ohraničen `Code...End Code`.
    - Proměnné `total`, `num1`, `num2`, a `totalMessage` uložení několika čísla a řetězce.
    - Řetězcový literál hodnota přiřazená `totalMessage` proměnná je do dvojitých uvozovek.
    - Protože kód jazyka Visual Basic není velká a malá písmena, když `totalMessage` proměnná se používá v dolní části stránky, jeho název potřebuje pouze tak, aby odpovídaly pravopisu deklarace proměnné v horní části stránky. Malých a velkých písmen nezáleží.
    - Výraz `num1.AsInt()`  +  `num2.AsInt()` ukazuje, jak pracovat s objekty a metody. `AsInt` Metodu na každou proměnnou převede řetězec zadaný uživatelem na celé číslo (integer), které mohou být přidány.
    - `<form>` Značka zahrnuje `method="post"` atribut. Toto určuje, že když uživatel klikne **přidat**, stránky se odešlou na server pomocí metody POST protokolu HTTP. Při odeslání stránky, kód `If IsPost` vyhodnotí na hodnotu true a podmíněný kód spuštění zobrazení výsledek přidání čísla.
3. Uložit na stránku a spustíte ji v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.) Zadejte dvěma celými čísly a poté klikněte **přidat** tlačítko.

    ![Razor Img7](introducing-razor-syntax-vb/_static/image8.jpg)

## <a name="visual-basic-language-and-syntax"></a>Syntaxe a jazyk Visual Basic

Dříve jste viděli základní příklad, jak vytvořit webovou stránku ASP.NET a jak můžete přidat kód serveru do kódu HTML. Zde se dozvíte základní informace o používání jazyka Visual Basic k zápisu kódu serveru ASP.NET pomocí syntaxe Razor &#8212; tedy programovací jazyk pravidla.

Pokud máte zkušenosti s programování (zejména pokud jste použili C, C++, C#, Visual Basic nebo JavaScript), velkou část můžete přečíst tady bude známé. Budete pravděpodobně potřebovat pouze s jak služba WebMatrix kód přidá značku v *.vbhtml* soubory.

### <a id="BM_CombiningTextMarkupAndCode"></a>  Kombinování textu, značek a kódu v blocích kódu

V blocích kódu serveru budete často chcete výstup text a značku na stránku. Pokud blok kódu serveru obsahuje text, který není kódu a který místo toho má být vykreslen jako je, musí být nedokáží rozlišit tento text z kódu ASP.NET. Chcete-li to provést několika způsoby.

- Uzavřete text do bloku element HTML jako `<p></p>` nebo `<em></em>`:

    [!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample11.vbhtml)]

    HTML element mohou zahrnovat text, další prvky jazyka HTML a výrazy kódu serveru. Když ASP.NET uvidí počáteční značky HTML (například `<p>`), všechno, co se vykreslí element a její obsah, jako je prohlížeče (a odstraňuje výrazů kódu na serveru).

- Použití `@:` operátor nebo `<text>` elementu. `@:` Výstupy jednořádkový obsah, který obsahuje prostý text nebo nespárované značky jazyka HTML; `<text>` element vloží několik řádků výstupu. Tyto možnosti jsou užitečné, pokud nechcete, aby k vykreslení elementu HTML jako část ve výstupu.

    [!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample12.vbhtml)]

    V následujícím příkladu se opakuje v předchozím příkladu, ale používá jednu dvojici `<text>` značky uzavřít text k vykreslení.

    [!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample13.vbhtml)]

    V následujícím příkladu `<text>` a `</text>` značky uzavřít tři řádky, které mají některé uncontained text a bezkonkurenční značky HTML (`<br />`), spolu s kódu serveru a odpovídající značky jazyka HTML. Znovu, může také předcházet každý řádek zvlášť `@:` operátor; buď způsob, jak funguje.

    [!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample14.vbhtml)]

    > [!NOTE]
    > Když text výstupu jak je znázorněno v této části &#8212; pomocí HTML elementu, `@:` operátor nebo `<text>` element &#8212; ASP.NET není s kódováním HTML výstup. (Jak je uvedeno výše, ASP.NET kódování výstup výrazů kódu na server a server bloky kódu, které předchází `@`, s výjimkou zvláštní případy, které jste si poznamenali v této části.)

### <a name="whitespace"></a>Prázdné znaky

Nadbytečné mezery v příkazu (i mimo něj řetězcový literál) nemají vliv – příkaz:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample15.vbhtml)]

### <a name="breaking-long-statements-into-multiple-lines"></a>Rozdělení dlouhé příkazy do více řádků

Příkaz dlouhé kódu lze rozdělit na více řádků pomocí znaku podtržítka `_` (v jazyce Visual Basic tzv. *znak pro pokračování*) po každém řádku kódu. Chcete-li break – příkaz na další řádek, na konci řádku přidejte mezeru a potom znak pro pokračování. Příkaz pokračujte na dalším řádku. Můžete zabalit příkazy na libovolný počet řádků, kolik potřebujete, aby se zlepšila čitelnost. Následující příkazy jsou stejné:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample16.vbhtml)]

Nelze však zalomení řádku uprostřed řetězcový literál. Nebude fungovat v následujícím příkladu:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample17.vbhtml)]

Kombinování dlouhý řetězec, který obtéká stejně jako ve výše uvedeném kódu více řádků, je třeba použít *operátoru pro zřetězení* (`&`), což uvidíte dále v tomto článku.

### <a name="code-comments"></a>Komentáře kódu

Komentáře umožňují ponechte poznámky k sobě nebo jiné. Komentáře syntaxe Razor mají předponu `@*` a na konci `*@`.

[!code-cshtml[Main](introducing-razor-syntax-vb/samples/sample18.cshtml)]

V rámci bloků kódu můžete použít komentáře syntaxe Razor, nebo můžete použít běžný znak komentář jazyka Visual Basic, což je jednoduchá uvozovka (`'`) předponu pro každý řádek.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample19.vbhtml)]

## <a name="variables"></a>Proměnné

Proměnná je pojmenovaný objekt, který použijete k ukládání dat. Můžete pojmenovat proměnné cokoli, ale název musí začínat znakem abecedy a nesmí obsahovat prázdné znaky nebo vyhrazené znaky. V jazyce Visual Basic protože jste viděli dříve, malá a velká písmena v názvu proměnné nezáleží.

### <a name="variables-and-data-types"></a>Proměnné a datové typy

Proměnná může mít určitý datový typ, který označuje, jaký druh dat je uložen v proměnné. Můžete použít proměnné řetězce, které ukládají hodnoty řetězce (například &quot;Hello world&quot;), celočíselné proměnné, které ukládají hodnoty celé číslo (např. 3 nebo 79) a proměnné pro datum, které ukládají data v různých formátech (např. 4/12/2012 nebo března 2009 ). A existuje mnoho dalších typů dat, která vám pomůže.

Však není nutné zadat typ pro proměnnou. Ve většině případů technologie ASP.NET můžete zjistit typ závislosti na tom, jak se používá data v proměnné. (Někdy je nutné zadat typ; uvidíte příkladech, kde je hodnota true.)

Chcete-li deklarovat proměnnou bez určení typu, použijte `Dim` plus název proměnné (například `Dim myVar`). Chcete-li deklarovat proměnnou s typem, použijte `Dim` plus název proměnné, za nímž následuje `As` a pak zadejte název (například `Dim myVar As String`).

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample20.vbhtml)]

Následující příklad ukazuje některé vložené výrazy, které používají proměnné na webové stránce.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample21.vbhtml)]

Výsledek zobrazí v prohlížeči:

![Razor Img9](introducing-razor-syntax-vb/_static/image9.jpg)

### <a name="converting-and-testing-data-types"></a>Převod a testování datových typů

Přestože ASP.NET obvykle můžete automaticky určit typ dat, někdy se to neplatí. Proto můžete potřebovat pomoc ASP.NET pomocí provádí explicitní převod. I když nemáte k dispozici pro převod typů, někdy je vhodné otestovat, jaký typ dat je možné, že pracujete s.

Nejběžnější případ je, že budete muset převést řetězec na jiný typ, například na celé číslo nebo datum. Následující příklad ukazuje typické případy, kdy je nutné převést řetězec na číslo.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample22.vbhtml)]

Jako pravidlo uživatelský vstup je pro vás řetězce. I v případě, že jste výzva, aby uživatel zadal číslo, a i v případě, že jste zadali číslici, při odeslání vstup uživatele a čtení v kódu, data jsou ve formátu řetězce. Proto je nutné převést řetězec na číslo. V příkladu při pokusu o provedení aritmetické operace na hodnotách bez převodu, chybová zpráva výsledky, protože ASP.NET nejde přidat dva řetězce:

`Cannot implicitly convert type 'string' to 'int'.`

K převodu hodnoty na celá čísla, volání `AsInt` metody. Pokud převod není úspěšné, pak přidáte čísla.

Následující tabulka uvádí některé běžné metody převodu a testování pro proměnné.


:::row:::
    :::column:::
        <strong>Method</strong>
    :::column-end:::
    :::column:::
        <strong>Description</strong>
    :::column-end:::
    :::column:::
        <strong>Example</strong>
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsInt(), IsInt()`
    :::column-end:::
    :::column:::
        Converts a string that represents a whole number (like &quot;593&quot;) to an integer.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample23.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsBool(), IsBool()`
    :::column-end:::
    :::column:::
        Converts a string like &quot;true&quot; or &quot;false&quot; to a Boolean type.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample24.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsFloat(), IsFloat()`
    :::column-end:::
    :::column:::
        Converts a string that has a decimal value like &quot;1.3&quot; or &quot;7.439&quot; to a floating-point number.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample25.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsDecimal(), IsDecimal()`
    :::column-end:::
    :::column:::
        Converts a string that has a decimal value like &quot;1.3&quot; or &quot;7.439&quot; to a decimal number. (In ASP.NET, a decimal number is more precise than a floating-point number.)
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample26.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsDateTime(), IsDateTime()`
    :::column-end:::
    :::column:::
        Converts a string that represents a date and time value to the ASP.NET `DateTime` type.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample27.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `ToString()`
    :::column-end:::
    :::column:::
        Converts any other data type to a string.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample28.vb)]
    :::column-end:::
:::row-end:::


## <a name="operators"></a>Operátory

Operátor je klíčové slovo nebo znak, který říká technologie ASP.NET, jaký druh příkaz k provedení ve výrazu. Visual Basic podporuje mnoho operátorů, ale je potřeba jenom rozpoznat pár, abyste mohli začít vyvíjet webové stránky ASP.NET. Následující tabulka shrnuje nejčastější operátory.


:::row:::
    :::column:::
        <strong>Operator</strong>
    :::column-end:::
    :::column:::
        <strong>Description</strong>
    :::column-end:::
    :::column:::
        <strong>Examples</strong>
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `+ - * /`
    :::column-end:::
    :::column:::
        Math operators used in numerical expressions.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample29.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `=`
    :::column-end:::
    :::column:::
        Assignment and equality. Depending on context, either assigns the value on the right side of a statement to the object on the left side, or checks the values for equality.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample30.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `<>`
    :::column-end:::
    :::column:::
        Inequality. Returns `True` if the values are not equal.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample31.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `< > <= >=`
    :::column-end:::
    :::column:::
        Less than, greater than, less than or equal, and greater than or equal.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample32.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `&`
    :::column-end:::
    :::column:::
        Concatenation, which is used to join strings.
    :::column-end:::
    :::column:::
        [!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample33.vbhtml)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `+= -=`
    :::column-end:::
    :::column:::
        The increment and decrement operators, which add and subtract 1 (respectively) from a variable.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample34.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `.`
    :::column-end:::
    :::column:::
        Dot. Used to distinguish objects and their properties and methods.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample35.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `()`
    :::column-end:::
    :::column:::
        Parentheses. Used to group expressions, to pass parameters to methods, and to access members of arrays and collections.
    :::column-end:::
    :::column:::
        [!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample36.vbhtml)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `Not`
    :::column-end:::
    :::column:::
        Not. Reverses a true value to false and vice versa. Typically used as a shorthand way to test for `False` (that is, for not `True`).
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample37.vb)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AndAlso OrElse`
    :::column-end:::
    :::column:::
        Logical AND and OR, which are used to link conditions together.
    :::column-end:::
    :::column:::
        [!code-vb[Main](introducing-razor-syntax-vb/samples/sample38.vb)]
    :::column-end:::
:::row-end:::

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

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample39.vbhtml)]

### <a name="referencing-the-virtual-root-the--operator-and-href-method"></a>Odkazování na virtuální kořenový adresář: ~ – operátor a metoda Href

V *.cshtml* nebo *.vbhtml* soubor, můžete odkazovat pomocí cesty kořenové složky `~` operátor. To je velmi užitečný, protože se můžete pohybovat stránky na webu a jakékoli odkazy, které obsahují další stránky nesmí být nefunkční. Je taky užitečný v případě, kdy přesunout svůj web do jiného umístění. Následuje několik příkladů:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample40.vbhtml)]

Pokud webová stránka se `http://myserver/myapp`, zde je, jak ASP.NET bude zpracovávat tyto cesty při spuštění stránky:

- `myImagesFolder`: `http://myserver/myapp/images`
- `myStyleSheet` : `http://myserver/myapp/styles/Stylesheet.css`

(Ve skutečnosti neuvidíte těchto cest jako hodnotu proměnné, ale ASP.NET bude zacházet s cesty, jak, pokud je to, co byly.)

Můžete použít `~` operátor v serverovém kódu (jak je uvedeno výše) a v kódu, například takto:

[!code-html[Main](introducing-razor-syntax-vb/samples/sample41.html)]

V kódu, můžete použít `~` operátor k vytvoření cesty k prostředkům, jako jsou soubory obrázků, jiné webové stránky a souborů CSS. Při spuštění stránky ASP.NET vypadat prostřednictvím stránky (kódu a kódu) a řeší všechny `~` odkazy na příslušné cesty.

## <a name="conditional-logic-and-loops"></a>Podmíněnou logiku a smyčky

Kód serveru ASP.NET umožňuje provádět úkoly na základě podmínky a zápisu kód, který bude opakovat příkazy s konkrétním počtem opakování tedy kód, který běží smyčku).

### <a name="testing-conditions"></a>Testování podmínek

K otestování jednoduché podmínky použití `If...Then` příkaz, který vrací `True` nebo `False` na základě testu zadáte:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample42.vbhtml)]

`If` – Klíčové slovo začíná bloku. Skutečný test (podmínky) následuje `If` – klíčové slovo a vrátí hodnotu true nebo false. `If` Příkaz končí `Then`. Příkazy, které se spustí při splnění testu jsou uzavřeny ve `If` a `End If`. `If` Výraz může obsahovat `Else` blok, který určuje příkazy ke spuštění, pokud podmínka není splněna:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample43.vbhtml)]

Pokud `If` příkaz spustí bloku kódu, není nutné použít normální `Code...End Code` příkazy k zahrnutí bloků. Lze přidat `@` na blok, a bude fungovat. Tento přístup funguje s `If` stejně jako ostatní jazyka Visual Basic programování klíčová slova, která následuje bloky kódu, včetně `For`, `For Each`, `Do While`atd.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample44.vbhtml)]

Můžete přidat více podmínek použití jednoho nebo více `ElseIf` bloků:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample45.vbhtml)]

V tomto příkladu, pokud první podmínka vyhodnocena jako v `If` blok není true, `ElseIf` podmínka je zaškrtnuté políčko. Pokud tato podmínka je splněna, příkazy v `ElseIf` jsou spuštěny. Pokud jsou splněna žádná z podmínek, příkazy v `Else` jsou spuštěny. Můžete přidat libovolný počet `ElseIf` blokuje a pak zavřete s `Else` blokovat, jako &quot;všechno ostatní&quot; podmínku.

Chcete-li otestovat velký počet podmínek, použijte `Select Case` blok:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample46.vbhtml)]

K otestování hodnotu v závorkách (v příkladu proměnná den v týdnu). Používá každý samostatný test `Case` příkaz, který obsahuje hodnotu. Pokud hodnota `Case` příkazu se shoduje s hodnotou testu, kód, který `Case` je blok proveden.

Výsledek poslední dva podmíněné bloky zobrazí v prohlížeči:

![Razor Img10](introducing-razor-syntax-vb/_static/image10.jpg)

### <a name="looping-code"></a>Opakování ve smyčce kódu

Často je potřeba spustit stejné příkazy opakovaně. To provedete tak, že opakování ve smyčce. Například můžete často spustit stejné příkazy pro každou položku v kolekci dat. Pokud víte přesně kolikrát chcete opakovat, můžete použít `For` smyčky. Tento druh smyčky je užitečné zejména pro počítání nebo počítání:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample47.vbhtml)]

Smyčky začíná `For` – klíčové slovo, za nímž následuje tři prvky:

- Ihned po `For` prohlášení, deklarujete proměnnou čítače (není nutné používat `Dim`) a pak poznáte, rozsah, jako v `i = 10 to 20`. To znamená, že proměnná `i` spustí, počítací na 10 a pokračovat, dokud nedosáhne 20 (včetně).
- Mezi `For` a `Next` příkazy je obsah bloku. Tento název může obsahovat jeden nebo více příkazy kódu, které fungují s každou smyčku.
- `Next i` Příkazu ukončení smyčky. Zvýší čítač a spustí další iteraci smyčky.

Řádek kódu mezi `For` a `Next` řádky obsahuje kód, který běží u jednotlivých iterací smyčky. Kód vytvoří nový odstavec (`<p>` element) každý čas a přidá nový řádek do výstupu, zobrazení hodnoty i (Čítač). Když spustíte tuto stránku, tento příklad vytvoří 11 řádky zobrazení výstupu, s textem na každém řádku označující počet položek.

![Razor Img11](introducing-razor-syntax-vb/_static/image11.jpg)

Pokud pracujete s kolekce nebo pole, často používají `For Each` smyčky. Kolekce je skupina podobných objektů a `For Each` smyčky umožňuje provést úlohu pro každou položku v kolekci. Tento druh smyčky je vhodné pro kolekce, protože na rozdíl od `For` smyčky, není nutné zvýší čítač nebo nastavit omezení. Místo toho `For Each` kód smyčky jednoduše pokračuje přes kolekci, dokud se nedokončí.

Vrátí položky v tomto příkladu `Request.ServerVariables` kolekce (který obsahuje informace o webovém serveru). Použije `For Each` smyčky zobrazovaný název každé položky tak, že vytvoříte nový `<li>` prvek v seznamu s odrážkami HTML.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample48.vbhtml)]

`For Each` Klíčovým slovem následovalo proměnná, která představuje jednu položku v kolekci (v tomto příkladu `myItem`) a po něm `In` – klíčové slovo, za nímž následuje kolekci chcete projít. V těle `For Each` smyčky, dostanete aktuální položky pomocí proměnné, která je deklarována jako dříve.

![Razor Img12](introducing-razor-syntax-vb/_static/image12.jpg)

Chcete-li vytvořit smyčku více pro obecné účely, použijte `Do While` – příkaz:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample49.vbhtml)]

Tato smyčka začíná `Do While` – klíčové slovo, za nímž následuje podmínky, za nímž následuje blok opakovat. Obvykle zvýšit smyčky (přidání do) nebo dekrementace (odečíst od) proměnné nebo objektu se používá pro počítání. V tomto příkladu `+=` operátor přičte 1 k hodnotě proměnné při každém spuštění smyčky. (Chcete-li snížení proměnná ve smyčce počítá směrem dolů, použijte operátor dekrementace `-=`.)

## <a name="objects-and-collections"></a>Objekty a kolekce

Téměř vše na webu technologie ASP.NET je objekt, včetně webové stránky. Tato část popisuje některé důležité objekty, které budete pracovat se často ve vašem kódu.

### <a name="page-objects"></a>Objekty stránky

Na stránce je nejzákladnější objekt v technologii ASP.NET. Můžete přistupovat k vlastnosti objektu page přímo bez žádné opravňující objektu. Následující kód načte cestu k souboru na stránce, pomocí `Request` objektu stránky:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample50.vbhtml)]

Můžete použít vlastnosti `Page` můžete získat velké množství informací, jako například:

- `Request`. Jak už víte, to je kolekce informací o aktuálním požadavku, včetně typu prohlížeče přišel požadavek, adresa URL stránky, identita uživatele, atd.
- `Response`. Toto je kolekce informací o odpovědi (stránky), které se odešlou do prohlížeče při dokončení kódu serveru. Tuto vlastnost můžete například použít při zápisu informací do odpovědi.

    [!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample51.vbhtml)]

### <a name="collection-objects-arrays-and-dictionaries"></a>Kolekce objektů (polí a slovníky)

Kolekce je skupina objektů stejného typu, jako je například kolekce `Customer` objekty z databáze. Technologie ASP.NET obsahuje mnoho předdefinovaných kolekcí, jako `Request.Files` kolekce.

Často budete pracovat s daty v kolekcích. Jsou dvě běžné typy kolekcí *pole* a *slovníku*. Pole je užitečné, když chcete uložit kolekci podobných položek, ale nebudete chtít vytvořit samostatné proměnnou pro uchování položky:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample52.vbhtml)]

S poli, je třeba deklarovat určitý datový typ. `String`, `Integer`, nebo `DateTime`. K označení, že proměnné můžou obsahovat pole, přidat závorky k názvu v deklaraci proměnné (jako například `Dim myVar() As String`). Můžete přístup k položkám v poli pomocí jejich pozice (index) nebo pomocí `For Each` příkazu. Indexy pole jsou počítány od nuly &#8212; tedy první položka je na pozici 0, druhá položka je na pozici 1 a tak dále.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample53.vbhtml)]

Můžete určit počet položek v poli získáním jeho `Length` vlastnost. Chcete-li získat pozice konkrétní položku v poli (to znamená, k vyhledání pole), použijte `Array.IndexOf` – metoda. Můžete také provést třeba zpětného obsah pole ( `Array.Reverse` metoda) nebo řazení obsahu ( `Array.Sort` metoda).

Výstupní řetězec pole Kód zobrazený v prohlížeči:

![Razor Img13](introducing-razor-syntax-vb/_static/image13.jpg)

Slovník je kolekce párů klíč/hodnota, ve kterém zadat klíč (nebo název) k nastavení nebo načtení odpovídající hodnotě:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample54.vbhtml)]

Chcete-li vytvořit slovník, použijte `New` – klíčové slovo k označení, že vytváříte novou `Dictionary` objektu. Slovník můžete přiřadit k proměnné pomocí `Dim` – klíčové slovo. Označení datové typy položek ve slovníku pomocí závorek ( `( )` ). Na konci deklarace je nutné přidat jinou dvojici závorek, protože je to ve skutečnosti metodou, která vytvoří nový slovník.

Přidání položek do slovníku, můžete volat `Add` metoda proměnnou slovníku (`myScores` v tomto případě) a pak zadejte klíč a hodnotu. Alternativně můžete použít závorky k označení klíče a provádět jednoduché přiřazení, jako v následujícím příkladu:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample55.vbhtml)]

Pokud chcete získat hodnotu ze slovníku, můžete zadat klíč v závorkách:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample56.vbhtml)]

## <a name="calling-methods-with-parameters"></a>Volání metody s parametry

Jak už jste viděli dříve v tomto článku, mají objekty, které můžete naprogramovat pomocí metody. Například `Database` objekt může mít `Database.Connect` metody. Mnoho metody mají také jeden nebo více parametrů. A *parametr* je hodnota, která můžete předat metodě pro povolení metody a dokončení úkolu. Podívejte se například na deklaraci `Request.MapPath` metodu, která přijímá tři parametry:

[!code-vb[Main](introducing-razor-syntax-vb/samples/sample57.vb)]

Tato metoda vrací fyzickou cestu na serveru, který odpovídá pro zadanou virtuální cestu. Jsou tři parametry pro metodu `virtualPath`, `baseVirtualDir`, a `allowCrossAppMapping`. (Všimněte si, že v deklaraci, parametry jsou uvedeny s datovými typy dat, který bude přijímat.) Při volání této metody, je třeba zadat hodnoty pro všemi třemi parametry.

Pokud používáte Visual Basic se syntaxí Razor, máte dvě možnosti pro předání parametrů metodě: *poziční parametry* nebo *pojmenované parametry*. Volání metody pomocí poziční parametry, předání parametrů v případě přísného pořadí zadaném v deklaraci metody. (By obvykle znáte toto pořadí najdete dokumentaci k metodě.) Je nutné postupovat podle pořadí, a přeskočíte nelze některé z parametrů &#8212; Pokud potřeby předáte prázdný řetězec (`""`) nebo pro pozičních parametrů, které nemají hodnotu null.

Následující příklad předpokládá, že máte složku s názvem *skripty* na vašem webu. Kód volá `Request.MapPath` metoda a předá hodnoty pro tři parametry ve správném pořadí. Pak zobrazí výslednou cestu namapované.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample58.vbhtml)]

Pokud existuje velký počet parametrů pro metodu, můžete svůj kód uchováváte přehlednější a čitelnější pomocí pojmenovaných parametrů. Volání metody pomocí pojmenované parametry, zadejte název parametru, za nímž následuje `:=` a pak zadejte hodnotu. Výhoda pojmenovaných parametrů je, že můžete je přidat do požadovaného pořadí. (Nevýhodou je, že volání metody není co nejkompaktnější.)

Následující příklad volá stejnou metodu, jak je uvedeno výše, ale používá pojmenované parametry k zadání hodnot:

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample59.vbhtml)]

Jak je vidět, parametry jsou předány v jiném pořadí. Pokud spustíte z předchozího příkladu a v tomto příkladu, ale budete vrátí stejnou hodnotu.

## <a name="handling-errors"></a>Zpracování chyb

### <a name="try-catch-statements"></a>Try-Catch – příkazy

Často musíte příkazů v kódu, který může selhat z důvodu mimo vaši kontrolu. Příklad:

- Pokud váš kód se pokusí otevřít, vytvoření, čtení nebo zápis do souboru, může dojít ke všem typům chyb. Požadovaný soubor nemusí existovat, může být zablokován, kód nemusí mít oprávnění a tak dále.
- Podobně pokud váš kód se pokouší aktualizovat záznamy v databázi, může být problémy s oprávněními, může dojít ke ztrátě připojení k databázi, data a šetřit tak může být neplatný a tak dále.

V programovacím prostředí, se nazývají těmito situacemi *výjimky*. Pokud váš kód narazí na výjimku, generuje (vyvolá výjimku) chybové zprávy, který je, v nejlepším případě obtěžující uživatelům.

![Razor Img14](introducing-razor-syntax-vb/_static/image14.jpg)

V situacích, kdy váš kód může nastat výjimky a pokud se chcete vyhnout chybové zprávy tohoto typu, můžete použít `Try/Catch` příkazy. V `Try` příkazu spustit kód, který při kontrole. V jedné nebo více `Catch` příkazy, můžete vyhledat konkrétní chyby (konkrétní typy výjimek), které mohly nastat. Můžete vytvořit tolik `Catch` příkazy jako vy, musíme se podívat chyb, které jste očekávání.

> [!NOTE]
> Doporučujeme, abyste je velmi riskantní používat `Response.Redirect` metoda `Try/Catch` příkazy, protože to může způsobit výjimku na stránce.


Následující příklad ukazuje stránka, která vytváří textový soubor na první žádost o a poté zobrazí tlačítko, které umožňuje uživateli otevřít soubor. V příkladu záměrně používá chybný název souboru tak, aby způsobí výjimku. Tento kód obsahuje `Catch` příkazy pro dvě výjimky: `FileNotFoundException`, která nastane, pokud název souboru je chybný, a `DirectoryNotFoundException`, která nastane, pokud ASP.NET i nelze najít složku. (Příkaz v tomto příkladu můžete odkomentovat Chcete-li zobrazit, jak se spustí při všechno funguje správně.)

Pokud váš kód nebyl zpracovat výjimky, zobrazí se chybová stránka jako na předchozím snímku obrazovky. Ale `Try/Catch` část pomůže zabránit uživateli v zobrazení tyto typy chyb.

[!code-vbhtml[Main](introducing-razor-syntax-vb/samples/sample60.vbhtml)]

## <a name="additional-resources"></a>Další prostředky

### <a name="reference-documentation"></a>Referenční dokumentace

- [ASP.NET](https://msdn.microsoft.com/library/ee532866.aspx)
- [Jazyk Visual Basic](https://msdn.microsoft.com/library/2x7h1hfk.aspx)

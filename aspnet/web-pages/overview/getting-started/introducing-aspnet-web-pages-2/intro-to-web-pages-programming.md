---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/intro-to-web-pages-programming
title: Představení rozhraní ASP.NET Web Pages – základy programování | Dokumentace Microsoftu
author: Rick-Anderson
description: 'Tento kurz poskytuje přehled toho, jak do aplikace v ASP.NET Web Pages se syntaxí Razor. Co se dozvíte: základní syntaxe "Razor", který používáte pro žádosti o přijetí změn...'
ms.author: riande
ms.date: 06/17/2015
ms.assetid: 7526ed45-a97d-4e8a-8301-01324ef0eff9
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/intro-to-web-pages-programming
msc.type: authoredcontent
ms.openlocfilehash: ec1c055d1b3f6ca5c6374a18840c2595bb368e0e
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021531"
---
<a name="introducing-aspnet-web-pages---programming-basics"></a>Úvod do ASP.NET Web Pages – základy programování
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento kurz poskytuje přehled toho, jak do aplikace v ASP.NET Web Pages se syntaxí Razor.
> 
> Co se dozvíte:
> 
> - Základní syntaxe "Razor", který používáte pro programování v rozhraní ASP.NET Web Pages.
> - Některé základní C#, která je programovací jazyk, který budete používat.
> - Některé základní koncepty programování pro webové stránky.
> - Postup instalace balíčků (komponenty, které obsahují předem připravených kódu) pro použití s vaší lokality.
> - Jak používat *pomocné rutiny* k provádění běžných programovacích úkolů.
>   
> 
> Popsané funkce a technologie:
> 
> - A Správce balíčků NuGet.
> - `Gravatar` Pomocné rutiny.


Tento kurz je primárně cvičení v Úvod k programování syntaxe, které budete používat pro rozhraní ASP.NET Web Pages. Získáte informace o *syntaxe Razor* a kód, který je napsán v jazyce C# je programovací jazyk. Jste získali v předchozím kurzu; balíčku glimpse této syntaxe v tomto kurzu vám objasníme další syntaxe.

Slibujeme, že tento kurz zahrnuje většinu programovacích, zobrazí se vám v jedné kurzu a je jediným kurz, který je *pouze* o programování. Ve zbývající kurzy v této sadě skutečně vytvoříte stránky, které se zajímavých věcí.

Dozvíte se víc i o *pomocné rutiny*. Pomocné rutiny je komponenta – zabalené nahoru část kódu –, který můžete přidat na stránku. Pomocná rutina provede práci, který jinak může být zdlouhavý nebo komplexní udělat ručně.

## <a name="creating-a-page-to-play-with-razor"></a>Vytvoření stránky přehrávání s jádrem Razor

V této části budete přehrávání trochu s jádrem Razor získali představu o základní syntaxe.

Pokud ještě není spuštěná, spusťte službu WebMatrix. Na webu, kterou jste vytvořili v předchozím kurzu budete používat ([získávání spuštěn pomocí webových stránek](https://go.microsoft.com/fwlink/?LinkId=251578)). Chcete-li ho znovu otevřít, klikněte na tlačítko **části osobní weby** a zvolte **WebPageMovies**:

![Služba WebMatrix úvodní obrazovku zobrazující možnosti Otevřít web a osobní weby zvýrazněnou](intro-to-web-pages-programming/_static/image1.png)

Vyberte **soubory** pracovního prostoru.

Na pásu karet klikněte na tlačítko **nový** vytvořit stránku. Vyberte **CSHTML** a novou stránku pojmenujte *TestRazor.cshtml*.

Klikněte na tlačítko **OK**.

Zkopírujte do něj následující do souboru, úplná výměna, co již existuje.

> [!NOTE]
> Při kopírování kód nebo kód z příkladů do stránky odsazení a zarovnání nemusí být stejný jako v tomto kurzu. Jak kód poběží, ale nemají vliv odsazení a zarovnání.


[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample1.cshtml)]

## <a name="examining-the-example-page"></a>Zkoumání příklad stránky

Většina co uvidí je běžný HTML. V horní části existuje ale tento blok kódu:

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample2.cshtml)]

Všimněte si, že následující věci, o tento blok kódu:

- Znak @ řekne technologie ASP.NET, že následuje kódu Razor, není ve formátu HTML. ASP.NET bude považovat za všechno @ znak jako kód, dokud se nespustí se znovu na kód HTML. (V tomto případě to &lt;! Typ dokumentu&gt; elementu.
- Složené závorky ({a}) uzavřete blok kódu Razor, pokud kód má více než jeden řádek. Složené závorky řekněte technologie ASP.NET, pokud kód pro daný blok začíná a končí.
- / / Komentář označte znaků –, který je součástí kódu, který nebude spuštěno.
- Každý příkaz musí končit středníkem (;). (Není komentáře, i když.)
- Můžete ukládat hodnoty v *proměnné*, kterou vytvoříte (*deklarovat*) s – klíčové slovo var. Když vytvoříte proměnnou, můžete jí název, který může obsahovat písmena, číslice a podtržítka (\_). Názvy proměnných nesmí začínat číslicí a název programování – klíčové slovo (například var) nelze použít.
- Řetězce znaků (např. "ASP.NET" nebo "Webové stránky") je uzavřít do uvozovek. (Dvojité uvozovky musí být.) Čísla nejsou v uvozovkách.
- Prázdné znaky mimo uvozovek nezáleží. Konce řádků převážně na mezerách nezáleží; výjimkou je, že nelze rozdělit řetězec v uvozovkách na řádcích. Odsazení a zarovnání na mezerách nezáleží.

To není zřejmé z tohoto příkladu je, že veškerý kód je velká a malá písmena. To znamená, že proměnné TheSum je proměnnou jiný než proměnné, které může mít název theSum nebo thesum. Podobně je klíčové slovo var, ale není proměnná.

### <a name="objects-and-properties-and-methods"></a>Objekty a vlastnosti a metody

Pak je výraz DateTime.Now. Jednoduše řečeno, data a času je *objekt*. Objekt je věc, kterou můžete programovat s – na stránce, textové pole, soubor, obrázek, webový požadavek, e-mailovou zprávu, záznam zákazníka, atd. Objekty mají jednu nebo více *vlastnosti* , které popisují jejich vlastnosti. Textový objekt pole má vlastnost Text (mimo jiné), žádost o objekt má vlastnost adresa Url (a další), e-mailovou zprávu má vlastnost od a do vlastnosti, a tak dále. Objekty mají také *metody* , které jsou "akce" mohou provádět. Můžete pracovat se objekty hodně.

Jak je vidět z příkladu, data a času je objekt, který vám umožní program data a časy. Má vlastnost s názvem nyní, který vrátí aktuální datum a čas.

### <a name="using-code-to-render-markup-in-the-page"></a>Pomocí kódu k vykreslení kódu na stránce

V těle stránky Všimněte si následujícího:

[!code-html[Main](intro-to-web-pages-programming/samples/sample3.html)]

Znovu znak @ řekne ASP.NET této dále je kód, není ve formátu HTML. V kódu můžete přidat za nímž následuje výrazu kódu a technologie ASP.NET vykreslit hodnota tohoto výrazu vpravo od tohoto okamžiku. V tomto příkladu @a vykreslí cokoli, co je hodnota proměnné s názvem, @product vykreslí cokoli, co je v proměnné s názvem produktu a tak dále.

Nejste omezeni pouze na proměnné, i když. V několika instancí, znak @ předchází výraz:

- @(\*b) vykreslí součin cokoli, co je v proměnné a b. ( \* Provozovatelem násobení.)
- @(technologie + "" + produktu) vykreslí hodnoty v proměnné technologie a produktů po zřetězení je a přidání mezeru mezi. Operátor (+) pro zřetězení řetězců je stejný jako operátor pro přidání čísel. Technologie ASP.NET můžete obvykle říct, jestli pracujete s čísla nebo řetězce a dělá správné věci se + – operátor.
- @Request.Url vykreslí vlastnost adresa Url objekt žádosti. Objekt žádosti obsahuje informace o aktuální požadavek z prohlížeče a samozřejmě vlastnost adresa Url obsahuje adresu URL aktuální žádosti.

V příkladu je navržený tak, aby zobrazit, který je možné pracovat v různých způsobů. Můžete provádět výpočty v bloku kódu v horní části, vložte výsledky do proměnné a následnému vykreslení proměnné v kódu. Nebo můžete provést výpočty výrazu vpravo v kódu. Přístup, který použijete, závisí na co děláte a do určité míry na vašich vlastních předvoleb.

### <a name="seeing-the-code-in-action"></a>Zobrazuje kód v akci

Klikněte pravým tlačítkem na název souboru a klikněte na tlačítko **spustit v prohlížeči**. Zobrazí se stránka v prohlížeči s všechny hodnoty a výrazy ve stránce přeložit.

![Stránka "TestRazor" spuštěná v prohlížeči](intro-to-web-pages-programming/_static/image2.png)

Podívejte se na zdroje v prohlížeči.

!["Testování Razor" zdroje stránky v prohlížeči](intro-to-web-pages-programming/_static/image3.png)

Jak budete chtít z prostředí v předchozím kurzu, žádná z kódu Razor není na stránce. Všechno, co se zobrazí se zobrazení skutečné hodnoty. Při spuštění stránky ve skutečnosti děláte požadavku na webový server, která je integrována do služby WebMatrix. Při přijetí požadavku ASP.NET řeší všechny hodnoty a výrazy a vykreslí jejich hodnoty do stránky. Potom odešle stránku v prohlížeči.

> [!TIP] 
> 
> **Razor a C#**
> 
> Až nyní jsme uvedli jste, že pracujete se syntaxí Razor. Který má hodnotu true, ale není kompletní. Skutečné programovací jazyk, který používáte, se nazývá *jazyka C#*. C# byl vytvořen microsoftem přes objevil před deseti lety a se stal jednou z primární programovací jazyk pro vytváření aplikací pro Windows. Ve skutečnosti všechna pravidla jazyka C# jsou všechna pravidla, které se zobrazily informace o tom, jak pojmenovat proměnnou a jak vytvořit příkazy a tak dále.
> 
> Razor odkazuje přesněji řečeno na malou sadu konvence pro jak vložit tento kód do stránky. Například konvence používání označit kód na stránce a @ {} Vložit blok kódu je aspekt stránky Razor. Pomocné rutiny jsou také považovány za součást Razor. Na více místech než jenom v ASP.NET Web Pages se používá syntaxi Razor. (Například používá se v zobrazeních ASP.NET MVC stejně.)
> 
> Jsme zmínili, to vzhledem k tomu, že pokud hledáte informace o programovacím rozhraní ASP.NET Web Pages, najdete velké množství odkazů na Razor. Velké množství tyto odkazy se však nevztahují na jste postup a může být matoucí. A ve skutečnosti řadu programovacích dotazy jsou ve skutečnosti se informace o práci s jazykem C# nebo práce s technologií ASP.NET. Proto když se podíváte speciálně pro informace o syntaxi Razor, nemusí najít odpovědi, které potřebujete.


## <a name="adding-some-conditional-logic"></a>Přidání některých podmíněnou logiku

Jednou z nejlepších funkcí o pomocí kódu na stránce je, které lze změnit, co se stane, na základě různých podmínek. V této části kurzu jste budete pohrajte si s některé způsoby, jak změnit obsah zobrazený na stránce.

V příkladu bude jednoduchá a trochu contrived tak, aby nám umožní soustředit se na podmíněnou logiku. Stránka, kterou vytvoříte, bude postupujte takto:

- Zobrazte různé text na stránce v závislosti na tom, zda je první stránka se zobrazí nebo určuje, zda jste ke kliknutí na tlačítko pro odeslání stránky. Který bude prvním podmíněným testem.
- Zobrazte zprávu pouze v případě, že určitou hodnotu předaný řetězec dotazu adresy URL (http://...?show=true). Které se stanou druhý podmíněným testem.

V nástroji WebMatrix, vytvořte stránku s názvem *TestRazorPart2.cshtml*. (Na pásu karet klikněte na tlačítko **nový**, zvolte **CSHTML**, zadejte název souboru a pak klikněte na tlačítko **OK**.)

Nahraďte obsah této stránky s následujícími možnostmi:

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample4.cshtml)]

Blok kódu v horní části inicializuje proměnnou s názvem zprávu s nějaký text. V těle stránky, obsah proměnné, zpráva se zobrazí uvnitř &lt;p&gt; elementu. Také obsahuje značku &lt;vstupní&gt; prvku k vytvoření **odeslat** tlačítko.

Spustíte stránku, abyste viděli, jak to funguje teď. Teď je v podstatě jako statická stránka i v případě, že kliknete **odeslat** tlačítko.

Vraťte se do prostředí WebMatrix. Uvnitř bloku kódu přidejte následující zvýrazněný kód *po* řádek, který inicializuje zpráva:

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample5.cshtml?highlight=4-6)]

### <a name="the-if---block"></a>Když bloku {}

Co jste právě přidali byl if podmínku. V kódu pokud podmínka má strukturu takto:

[!code-csharp[Main](intro-to-web-pages-programming/samples/sample6.cs)]

Je testovanou podmínku do závorek. Musí být hodnota nebo výraz, který vrátí hodnotu true nebo false. Pokud je podmínka pravdivá, spustí ASP.NET příkaz nebo příkazy, které jsou uvnitř složených závorek. (To jsou *pak* součástí *if-then* logiky.) Pokud podmínka není splněna, blok kódu přeskočen.

Tady je pár příkladů podmínky můžete otestovat v příkazu if – příkaz:

[!code-csharp[Main](intro-to-web-pages-programming/samples/sample7.cs)]

Proměnné s hodnotami nebo proti výrazy můžete otestovat pomocí <em>logického operátoru</em> nebo <em>operátor porovnání</em>: rovno (==) větší než (&gt;), menší než (&lt;), větší než nebo rovno (&gt;=) a menší než nebo rovno (&lt;=). ! = Znamená operátor není rovno – například pokud (! = 0) znamená <em>Pokud</em> <em>a</em><em>není rovno 0</em>.

> [!NOTE]
> Ujistěte se, že si všimnete, že operátor porovnání pro (==) se rovná není stejný jako =. = – Operátor se používá jenom pro přiřazení hodnoty (var = 2). Jsou-li zkombinovány tyto operátory budete buď dojde k chybě nebo získáte některé strangeová výsledky.


K otestování, jestli je hodnota true, je úplnou syntaxi if(IsDone == true). Ale můžete použít také if(IsDone) zástupce. Pokud neexistuje žádný operátor porovnání, ASP.NET předpokládá, že testujete pro hodnotu true.

Do! operátor samostatně znamená, že logický operátor NOT. Například, podmínky if (! IsPost) znamená, že *pokud neplatí IsPost*.

Podmínky můžete kombinovat pomocí logické AND (&amp; &amp; operátor) nebo logický operátor OR (|| – operátor). Například poslední if podmínky v předchozích příkladech znamená *Pokud FileProcessingIsDone není nastavená na hodnotu true a displayMessage nastavená na false*.

### <a name="the-else-block"></a>Jiného bloku

Jednou z poslední věcí o tom, zda bloky: Pokud blok může být následován z jiného bloku. Je užitečné z jiného bloku je budete muset provést různý kód, pokud podmínka není splněna. Tady je jednoduchý příklad:

[!code-csharp[Main](intro-to-web-pages-programming/samples/sample8.cs)]

Zobrazí se vám několik příkladů v dalších kurzech v této sérii, ve kterém pomocí jiného bloku je užitečné.

### <a name="testing-whether-the-request-is-a-submit-post"></a>Testování, zda je požadavek na odeslání (post)

Víc, ale umožňuje vrátit k tomu v příkladu, který má podmínku if(IsPost) {...}. IsPost je ve skutečnosti vlastností aktuální stránky. Při prvním vyžádání stránky IsPost vrátí hodnotu false. Ale pokud klikněte na tlačítko nebo jinak stránku odeslat – tedy účtovat – IsPost vrací hodnotu true. Takže IsPost vám umožní určit, jestli pracujete s odesláním formuláře. (Z hlediska příkazů HTTP, pokud se jedná o operaci GET, IsPost vrátí hodnotu false. Pokud se jedná o operaci POST, IsPost vrací hodnotu true.) V pozdějších kurzech budete pracovat s vstupní formuláře, ve kterém bude užitečné tento test.

Spuštění stránky. Protože to je poprvé, kdy se požadované na stránce se zobrazí "To je poprvé, kterou jste požadovali na stránce". Tento řetězec je hodnota, která inicializovala proměnnou zprávy do. If(IsPost) test se však, která vrátí hodnotu false v tomto okamžiku tak kód uvnitř if blokovat se nespustí.

Klikněte na tlačítko **odeslat** tlačítko. Na stránce je požádat znovu. Jako dřív, a zpráva proměnná je nastavena na "Toto je první přihlášení...". Ale tentokrát test if(IsPost) vrátí hodnotu true, tak kód uvnitř if blok. Hodnota proměnné zpráva změn kódu na jinou hodnotu, která bude vykreslen v kódu.

Nyní přidejte if podmínky v kódu. Níže &lt;p&gt; element, který obsahuje **odeslat** tlačítko, přidejte následující kód:

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample9.cshtml)]

Přidáte kód uvnitř značky, takže je nutné začít s @. Pokud se test podobný jste přidali dříve v bloku kódu. Uvnitř složených závorek, ale přidáváte běžný HTML – minimální, je běžné dokud nenarazí @DateTime.Now. Je to něco jiného kódu Razor, proto znovu budete muset přidat před tímto prvkem.

Tady je bod je, že můžete přidat, pokud podmínky v obou kód bloku v horní části a v kódu. Pokud použijete, pokud podmínka v těle stránky řádky uvnitř bloku může být značek nebo kódu. V takovém případě a jako hodnotu true, kdykoli je kombinovat značek a kódu, je nutné použít k němu zrušte na technologii ASP.NET ve kterém je kód.

Spuštění stránky a klikněte na tlačítko **odeslat**. Tentokrát nejen zobrazí různé zprávy při odeslání ("nyní jste odeslali..."), ale zobrazit novou zprávu, která obsahuje data a času.

![Odeslání stránky "Zkušební Razor 2" s časovým razítkem zobrazení po spuštění v prohlížeči](intro-to-web-pages-programming/_static/image4.png)

### <a name="testing-the-value-of-a-query-string"></a>Testování hodnoty řetězce dotazu

Jeden další test. Tentokrát přidáte if blok, který testuje hodnotu s názvem show, která mohla být předána v řetězci dotazu. (Tímto způsobem: `http://localhost:43097/TestRazorPart2.cshtml?show=true`) na stránce změníte tak, aby zpráva vám jste se zobrazení ("Toto je první přihlášení...", atd.) se zobrazí jenom v případě zobrazit hodnotu true.

V dolní části (ale uvnitř) přidejte následující blok kódu v horní části stránky:

[!code-csharp[Main](intro-to-web-pages-programming/samples/sample10.cs)]

Kompletní kód bloku nyní vzhled jako v následujícím příkladu. (Mějte na paměti, že pokud nezkopírujete kód na vaši stránku odsazení může vypadat jinak. Ale, který nemá vliv na způsob spuštění kódu.)

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample11.cshtml)]

Nový kód v bloku inicializuje proměnnou s názvem Zobrazitzpravu na hodnotu false. Pak provede if test a vyhledejte hodnotu v řetězci dotazu. Při prvním požadavku na stránku, má adresu URL podobný následujícímu:

`http://localhost:43097/TestRazorPart2.cshtml`

Kód určuje, zda adresa URL obsahuje proměnnou s názvem show v řetězci dotazu, podobně jako tato verze adresy URL:

`http://localhost:43097/TestRazorPart2.cshtml`? Zobrazit = true

Samotný test prohledá řetězec dotazu vlastnosti objektu žádosti. Pokud řetězec dotazu obsahuje položku s názvem show a této položky je nastavena na hodnotu true, pokud blok spustí a nastaví Zobrazitzpravu proměnnou na hodnotu true.

Existuje zdvih tady, jak je vidět. Jako název říká, řetězec dotazu je řetězec. Ale můžete jenom otestovat pro hodnotu true a false hodnota, kterou testujete-li logická hodnota (true/false). Před otestováním hodnotu proměnné zobrazit v řetězci dotazu, budete muset převést na logickou hodnotu. To je, co dělá metodu AsBool – přijímá řetězec jako vstup a převede ho na hodnotu typu Boolean. Je zřejmé zda je řetězec "true", metoda AsBool převede tuto hodnotu na hodnotu true. Pokud je hodnota řetězce cokoli jiného, AsBool vrátí hodnotu false.

> [!TIP] 
> 
> **Datové typy a metody As()**
> 
> Jsme uvedli jste, pouze pokud při vytváření proměnné, používat klíčové slovo var. To není celý scénář, ale. Aby bylo možné manipulaci s hodnotami – přidání čísel, nebo řetězení řetězců, nebo porovnávání dat nebo test pro Pravda/Nepravda – C# má pro práci s odpovídající vnitřní reprezentaci hodnoty. C# můžete *obvykle* zjistit, co by měl být tohoto vyjádření (to znamená, co *typ* data) podle co děláte s hodnotami. Teď nebo později ale to nemůžete udělat. Pokud ne, je nutné zvýšit tak, že explicitně jak C# by měla představovat data. Metoda AsBool činí – říká C#, že řetězcovou hodnotu "true" nebo "false" by se měla zpracovávat jako logická hodnota. Podobné metody existovat k reprezentaci řetězce jako jiné typy, jako jsou AsInt (nakládání jako s celým číslem), AsDateTime (nakládání jako datum/čas), AsFloat (nakládání jako číslo s plovoucí desetinnou čárkou) a tak dále. Když použijete tyto metody (), pokud C# nemůže představovat hodnotu řetězce podle požadavku, zobrazí se vám chyba.


V kódu stránky, odstranit nebo okomentovat tento prvek (tady se zobrazují komentářem navýšení kapacity):

[!code-html[Main](intro-to-web-pages-programming/samples/sample12.html)]

Right kde odebrán nebo zakomentované text, přidejte následující:

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample13.cshtml)]

Pokud je test zobrazeno, pokud je hodnota true, proměnná Zobrazitzpravu vykreslení &lt;p&gt; element s hodnotou proměnné zpráva.

### <a name="summary-of-your-conditional-logic"></a>Souhrn podmíněnou logiku

V případě, že nejste úplně jistí co jste právě provedli, tady je souhrn.

- Zpráva proměnná je inicializována na výchozí řetězec ("to je poprvé...").
- Pokud požadavek na stránku je výsledek odeslání (post), hodnota zprávy se změní na "nyní jste odeslali..."
- Zobrazitzpravu proměnná je inicializována na hodnotu false.
- Pokud řetězec dotazu obsahuje? zobrazit = true, Zobrazitzpravu proměnná je nastavená na hodnotu true.
- V kódu, pokud je hodnota true, Zobrazitzpravu &lt;p&gt; prvek je vykreslovaný, který zobrazuje hodnotu zprávy. (Pokud Zobrazitzpravu má hodnotu false, nic se vykreslí v tomto bodě v kódu.)
- V kódu, pokud je příspěvek, žádost &lt;p&gt; prvek je vykreslovaný, který zobrazí datum a čas.

Spuštění stránky. Neexistuje žádná zpráva, protože Zobrazitzpravu má hodnotu false, takže v kódu testu if(showMessage) vrátí hodnotu false.

Klikněte na tlačítko **odeslat**. Zobrazí datum a čas, ale stále žádná zpráva.

V prohlížeči přejdete na pole adresy URL a přidejte následující na konec adresy URL:? Zobrazit = true a potom stiskněte klávesu Enter.

!["Testování Razor 2" stránku v prohlížeči a zobrazí řetězec dotazu](intro-to-web-pages-programming/_static/image5.png)

Na stránce se zobrazí znovu. (Protože jste změnili adresu URL, to je novou žádost, ne odeslat.) Klikněte na tlačítko **odeslat** znovu. Zpráva se zobrazí znovu, jako je datum a čas.

!["Testování Razor 2" stránku po odesílat, pokud je řetězec dotazu](intro-to-web-pages-programming/_static/image6.png)

V adrese URL, změňte? zobrazit = true na? Zobrazit = false a stiskněte klávesu Enter. Na stránce odešlete znovu. Na stránce je zpátky na tom, jak začít – žádná zpráva.

Jak bylo uvedeno dříve, logika v tomto příkladu je trochu contrived. Nicméně, pokud se objevují v mnoha stránkách, a bude trvat nejméně jeden z formuláře Seznámili jste se sem.

## <a name="installing-a-helper-displaying-a-gravatar-image"></a>Instalace pomocné rutiny (zobrazení obrázku Gravatar)

Některé úlohy, které uživatelé často chtějí dělat na webových stránkách vyžadují velké množství kódu nebo vyžadovat další znalostní báze. Příklady: zobrazuje graf pro data. uvedení služby Facebook "Jako" na stránce. odeslání e-mailů z vašeho webu; oříznutí nebo změny velikosti obrázků; pomocí služby PayPal pro váš web. Abyste usnadnili snadnou udělat Tyhle druhy věcí, rozhraní ASP.NET Web Pages vám umožní používat *pomocné rutiny*. Pomocné rutiny jsou komponenty, které instalujete pro lokalitu a, které umožňují provádět typické úlohy pomocí pár řádků kódu Razor.

ASP.NET Web Pages má několik pomocných rutin součástí. Mnoho pomocné rutiny jsou však k dispozici v balíčcích (doplňky), které jsou k dispozici pomocí Správce balíčků NuGet. NuGet umožňuje vybrat balíček pro instalaci a pak se postará o všechny podrobnosti o instalaci.

V této části kurzu nainstalujete pomocné rutiny, která umožňuje zobrazit obrázek Gravatar ("globálně uznávané avatar"). Dozvíte dvě věci. Jedna je pro vyhledání a instalace pomocné rutiny. Se také dozvíte, jak pomocné rutiny umožňuje snadno udělat něco, co by jinak musíte udělat pomocí velké množství kódu, které byste museli napsat sami.

Můžete zaregistrovat na webu Gravatar na vlastní Gravatar [ http://www.gravatar.com/ ](http://www.gravatar.com/), ale to není nezbytné pro vytvoření účtu Gravatar k provedení této části kurzu.

V nástroji WebMatrix, klikněte na tlačítko **NuGet** tlačítko.

![Dialogové okno Galerie NuGet v nástroji WebMatrix](intro-to-web-pages-programming/_static/image7.png)

To spustí Správce balíčků NuGet a zobrazí dostupné balíčky. (Všechny balíčky jsou pomocné rutiny, některé přidávají funkce do prostředí WebMatrix, sama, některé jsou další šablony atd.) Může zobrazit chybová zpráva týkající se nekompatibilita verzí. Kliknutím můžete ignorovat tuto chybovou zprávu **OK** a pokračovat v tomto kurzu.

![Dialogové okno Galerie NuGet v nástroji WebMatrix](intro-to-web-pages-programming/_static/image8.png)

Do vyhledávacího pole zadejte "pomocné rutiny technologie asp.net". NuGet zobrazuje balíčky, které odpovídají hledaným výrazům.

![Galerie NuGet ve službě WebMatrix zobrazující balíčky](intro-to-web-pages-programming/_static/image9.png)

Knihovnu ASP.NET Web Helpers obsahuje kód, který zjednodušuje mnoho běžných úkolů, včetně využití čipu TPM Gravatar imagí. Vyberte **knihovnu ASP.NET Web Helpers** balíček a pak klikněte na tlačítko **nainstalovat** ke spuštění Instalační služby. Vyberte **Ano** když se zobrazí výzva, pokud chcete balíček nainstalovat a přijměte podmínky dokončete instalaci.

Je to. NuGet stáhne a nainstaluje vše, včetně všechny další součásti, které mohou vyžadovat (*závislosti*).

Pokud z nějakého důvodu je nutné odinstalovat pomocné rutiny, je velmi podobné. Klikněte na tlačítko **NuGet** tlačítko, klikněte na tlačítko **nainstalováno** kartu a vyberte balíček, který chcete odinstalovat.

## <a name="using-a-helper-in-a-page"></a>Použití pomocné rutiny na stránce

Teď použijete pomocné rutiny, které jste právě nainstalovali. Proces pro přidání na stránku pomocné rutiny je podobný pro většinu pomocné rutiny.

V nástroji WebMatrix, vytvořte stránku s názvem *GravatarTest.cshml*. (Vytváření speciální stránky k otestování pomocné rutiny, ale pomocné rutiny můžete použít na libovolné stránce ve vaší lokalitě.)

Uvnitř &lt;tělo&gt; element, přidejte &lt;div&gt; element. Uvnitř &lt;div&gt; elementu, zadejte toto:

@Gravatar.

Znak @ je ve stejném zatím jste používali pro označení kódu Razor. **Gravatar** je objekt pomocné rutiny, které pracujete.

Jakmile zadáte tečku (.), služba WebMatrix zobrazí seznam *metody* (funkce) zpřístupní Gravatar pomocné rutiny:

![Gravatar pomocné rutiny technologie IntelliSense rozevíracího seznamu](intro-to-web-pages-programming/_static/image10.png)

Tato funkce se označuje jako *IntelliSense*. Pomáhá při kódování tím, že poskytuje kontext odpovídající možnosti. Technologie IntelliSense funguje s HTML, CSS, kód technologie ASP.NET, JavaScriptu a dalších jazycích, které jsou podporovány v nástroji WebMatrix. Je další funkce, která usnadňuje vývoj webových stránek v nástroji WebMatrix.

G klávesu na klávesnici a podívejte se, aby IntelliSense najde GetHtml metoda. Stisknutím klávesy Tab. Vybrané metody (GetHtml) vloží technologie IntelliSense. Zadejte levou (otevírací) a Všimněte si, že pravou závorku se přidá automaticky. Zadejte e-mailovou adresu v uvozovkách mezi dvěma závorky. Pokud máte účet Gravatar, vrátí se váš profilový obrázek. Pokud nemáte účet Gravatar, vrátí se výchozí image. Jakmile budete hotovi, řádku vypadá takto:

[!code-css[Main](intro-to-web-pages-programming/samples/sample14.css)]

Nyní zobrazení stránky v prohlížeči. Váš obrázek nebo výchozí image se zobrazí, v závislosti na tom, jestli máte účet Gravatar.

![Gravatar](intro-to-web-pages-programming/_static/image11.png) ![Výchozí image](intro-to-web-pages-programming/_static/image12.png)

Chcete-li získat představu, co dělá pomocné rutiny pro vás, Zobrazit zdroj stránky v prohlížeči. Spolu s HTML, který měl na stránce se zobrazí element image, která obsahuje identifikátor. Toto je kód, který do stránky v místě, kde jste měli vykresluje pomocné rutiny @Gravatar.GetHtml. Pomocná rutina trvalo informace k dispozici a generovaného kódu, která komunikuje přímo Gravatar aby zpět získal správné bitové kopie pro zadaný účet.

Metoda GetHtml také umožňuje přizpůsobit obrázek tím, že poskytuje další parametry. Následující kód ukazuje, jak požádat o image má šířku a výšku 40 pixelů a používá zadané výchozí image s názvem **wavatar** Pokud uvedený účet neexistuje.

[!code-javascript[Main](intro-to-web-pages-programming/samples/sample15.js)]

Tento kód vytvoří něco jako následující výsledek (výchozí image bude náhodně lišit).

![](intro-to-web-pages-programming/_static/image13.png)

## <a name="coming-up-next"></a>Chystá se další

Zachovat tento krátký kurz, jsme museli zaměřit jenom pár základy. Samozřejmě je *hodně* víc informací, které Razor a C#. Dozvíte se víc při projděte si tyto kurzy. Pokud chcete dozvědět více o programování aspektů Razor a C# teď si můžete přečíst tady důkladnější Úvod: [Úvod do ASP.NET webové programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890).

V dalším kurzu se seznámíte práci s databází. V tomto kurzu budete začít vytvářet ukázkovou aplikaci, která umožňuje seznamu Oblíbené videa.

## <a name="complete-listing-for-testrazor-page"></a>Úplný seznam TestRazor stránky

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample16.cshtml)]

## <a name="complete-listing-for-testrazorpart2-page"></a>Úplný seznam TestRazorPart2 stránky

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample17.cshtml)]

## <a name="complete-listing-for-gravatartest-page"></a>Úplný seznam GravatarTest stránky

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample18.cshtml)]

## <a name="additional-resources"></a>Další prostředky

- [Úvod k programování v prostředí ASP.NET pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)
- [Pomocník Twitter](../../ui-layouts-and-themes/twitter-helper.md)

> [!div class="step-by-step"]
> [Předchozí](getting-started.md)
> [další](displaying-data.md)

---
uid: mvc/overview/views/using-page-inspector-in-aspnet-mvc
title: Použití Page Inspectoru v ASP.NET MVC | Dokumentace Microsoftu
author: rick-anderson
description: Nástroj Page Inspector v sadě Visual Studio 2012 je nástroj pro vývoj webů pomocí integrovaného prohlížeče. Vybrat jakýkoli element ve integrovaného prohlížeče a nástroj Page Inspector i...
ms.author: riande
ms.date: 08/15/2012
ms.assetid: c7e4e1ab-4932-4614-9f53-aaf7c706d498
msc.legacyurl: /mvc/overview/views/using-page-inspector-in-aspnet-mvc
msc.type: authoredcontent
ms.openlocfilehash: 0dea8b077878139a3f513cb51447b86a93fe55b8
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236533"
---
<a name="using-page-inspector-in-aspnet-mvc"></a>Použití Page Inspectoru v ASP.NET MVC
====================
podle Tim Ammann

> Nástroj Page Inspector v sadě Visual Studio 2012 je nástroj pro vývoj webů pomocí integrovaného prohlížeče. Vybrat jakýkoli element ve integrovaného prohlížeče a nástroj Page Inspector okamžitě zvýrazní zdroje a šablon stylů CSS prvku. Můžete procházet všechna zobrazení MVC, rychle najít zdroje vykreslované značky a použít nástroje prohlížeče přímo v prostředí sady Visual Studio.
> 
> [Podívejte se na Video](../../videos/mvc-4/using-page-inspector-in-aspnet-mvc.md)
> 
> Tento kurz ukazuje, jak povolit režim kontroly a rychle najít a upravit značky a šablony stylů CSS v rámci webového projektu. V tomto kurzu použijete projektu aplikace MVC, ale můžete použít také nástroje Page Inspector pro [webových formulářů](https://go.microsoft.com/?linkid=9802001) a dalších aplikací ASP.NET.
> 
> Tento kurz obsahuje následující oddíly:
> 
> - [Požadavky](#_1_prerequisites)
> - [Vytvoření webové aplikace](#_2_creating_a)
> - [Použití Page Inspectoru pro přejděte do zobrazení](#_3_using_page)
> - [Povolit režim kontroly](#_4_inspection_mode)
> - [Použití Page Inspectoru provádět změny kódu](#_5_using_page)
> - [Režim kontroly a v okně HTML](#_6_inspection_mode)
> - [Náhled změn šablon stylů CSS v okně Styly](#_7_previewing_css)
> - [Automatická synchronizace šablon stylů CSS](#css_auto_sync)
> - [Výběr barvy šablon stylů CSS](#css_color_picker)
> - [Mapování elementů dynamických stránek pro jazyk JavaScript](#map_dynamic_elements)


<a id="_prerequisites"></a><a id="_1_prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2012](https://www.microsoft.com/visualstudio/11) nebo [sady Visual Studio Express 2012 pro Web](https://www.microsoft.com/visualstudio/11/downloads#express-web).

> [!NOTE]
> Chcete-li získat nejnovější verzi nástroje Page Inspector, použijte [instalačního programu webové platformy](https://go.microsoft.com/fwlink/?LinkId=255386) k instalaci sady Windows Azure SDK pro .NET 2.0.


Nástroj Page Inspector je součástí nástroje Microsoft Web Developer Tools. Nejnovější verze je verze 1.3. Zjištění verze máte, spusťte Visual Studio a vyberte **o Microsoft Visual Studio** z **pomáhají** nabídky.

<a id="_creating_a_web"></a><a id="_2_creating_a"></a>

## <a name="create-a-web-application"></a>Vytvoření webové aplikace

Nejprve vytvořte webovou aplikaci, kterou budete používat nástroj Page Inspector se. V sadě Visual Studio, zvolte **souboru** &gt; **nový projekt**. Na levé straně rozbalte **Visual C#** vyberte **webové**a pak vyberte **webová aplikace ASP.NET MVC4**.

![Nové aplikace ASP.NET MVC](using-page-inspector-in-aspnet-mvc/_static/image2.png)

Klikněte na **OK**.

V **nového projektu ASP.NET MVC 4** dialogu **internetovou aplikaci**. Ponechte **Razor** jako výchozí modul zobrazení.

![Nový projekt ASP.NET MVC – Internetové aplikace.](using-page-inspector-in-aspnet-mvc/_static/image4.png)

Aplikace se otevře v **zdroj** zobrazení.

![Nové aplikace ASP.NET MVC v zobrazení zdroje](using-page-inspector-in-aspnet-mvc/_static/image6.png)

Teď, když máte aplikaci pro práci s, můžete použít nástroj Page Inspector sloužící ke zkoumání a upravte ho.

<a id="_starting_page_inspector"></a><a id="_3_using_page"></a>

## <a name="use-page-inspector-to-browse-to-a-view"></a>Použití Page Inspectoru pro přejděte do zobrazení

V sadě Visual Studio 2012, je můžete klikněte pravým tlačítkem na libovolné zobrazení ve vašem projektu, vyberte **zobrazení v nástroje Page Inspector**, a nástroj Page Inspector se zjistit trasy a zobrazení stránky.

V **Průzkumníka řešení**, rozbalte **zobrazení** složku a potom **Domů** složky. Souboru Index.cshtml klikněte pravým tlačítkem myši a zvolte **zobrazení v nástroje Page Inspector**.

![Zobrazení index.cshtm ve nástroj Page Inspector](using-page-inspector-in-aspnet-mvc/_static/image8.png)

Ve výchozím nastavení nástroj Page Inspector je ukotven jako okno na levé straně prostředí sady Visual Studio. Pokud dáváte přednost, můžete ukotvit jinde, nebo zrušit ukotvení v okně. Zobrazit [jak: Rozvržení a dokování Windows](https://msdn.microsoft.com/library/z4y0hsax.aspx).

Horní podokno okna nástroje Page Inspector aktuální stránky zobrazí v okně prohlížeče. V dolním podokně zobrazí na stránce v kódu HTML, spolu s některé karty, která umožňují kontrolovat různé aspekty stránky. Dolní podokno je podobný [vývojářské nástroje F12 pomáhají](https://msdn.microsoft.com/ie/aa740478) v aplikaci Internet Explorer.

![Aplikace ASP.NET MVC do nástroje Page Inspector](using-page-inspector-in-aspnet-mvc/_static/image10.png)

V tomto kurzu budete používat **HTML** a **styly** karet umožňuje rychle přejít a provádět změny aplikace.

<a id="_examining_(&quot;decomposing&quot;)_the"></a><a id="_inspection_mode_and"></a><a id="_4_inspection_mode"></a>

## <a name="enableinspection-mode"></a>Režim EnableInspection

Nástroj Page Inspector převést do režimu kontroly, klikněte na tlačítko **zkontrolujte, jestli se** tlačítko. V režimu kontroly když podržíte ukazatel myši nad libovolné části na vykreslené stránce odpovídající zdrojový kód nebo kód je zvýrazněn.

![Přepnout režim kontroly](using-page-inspector-in-aspnet-mvc/_static/image12.png)

Nyní najeďte myší různé části stránky v rámci nástroje Page Inspector. Stejně jako, ukazatel myši se změní na velké znaménko plus a zvýrazní prvek pod:

![Ukazatel myši div.content obálky](using-page-inspector-in-aspnet-mvc/_static/image14.png)

Při přesunu ukazatele myši, Visual Studio zvýrazní odpovídající syntaxe Razor ve zdrojovém souboru. Pokud prvek HTML pochází z jiného zdrojového souboru, Visual Studio automaticky otevře soubor.

V nástroje Page Inspector **HTML** kartě se zobrazí kód HTML, který byl vygenerován v syntaxi Razor. Při přesunu ukazatele myši, jsou zvýrazněny elementů HTML. **Styly** karta zobrazuje pravidla šablon stylů CSS prvku.

<a id="_5_using_page"></a>

## <a name="use-page-inspector-to-make-changes-to-markup"></a>Použití Page Inspectoru provádět změny kódu

Nástroj Page Inspector vám umožní najít značek, jehož umístění nemusí být zřejmé. Potom můžete upravit značky a zobrazit výsledné změny.

Tento údaj zobrazíte, klikněte na tlačítko **zkontrolujte, jestli se** a sjeďte k dolnímu okraji stránky v okně nástroje Page Inspector.

Při přesunutí ukazatele myši do oblasti zápatí, otevře se nástroj Page Inspector \_Layout.cshtml soubor a zvýrazní část rozložení stránky, kterou jste vybrali. Jak je vidět v zápatí je uvedené jsou definované v souboru rozložení a ne zobrazení.

![Zápatí](using-page-inspector-in-aspnet-mvc/_static/image16.png)

Nyní přesunout ukazatel myši nad řádek s copyrightu <a id="a"> </a>Všimněte si, že. V \_stránku Layout.cshtml, odpovídajícím řádku se zvýrazní.

![Zápatí o autorských právech řádek zvýrazněný](using-page-inspector-in-aspnet-mvc/_static/image18.png)

Přidejte nějaký text do konce řádku v \_souboru Layout.cshtml.

&lt;p&gt;&amp;kopírovat; @DateTime.Now.Year – Moje aplikace ASP.NET MVC Rocks! &lt;/p&gt;

Nyní stiskněte kombinaci kláves Ctrl + Alt + Enter nebo klikněte na panel aktualizace pro zobrazení výsledků v okně prohlížeče nástroj Page Inspector.

![My ASP.NET Application Rocks!](using-page-inspector-in-aspnet-mvc/_static/image20.png)

Může mít představit, že definované v zápatí je uvedené v Index.cshtml, ale ukázalo v \_Layout.cshtml a nástroj Page Inspector zjistila, že pro vás.

<a id="_inspection_mode_and_1"></a><a id="_6_inspection_mode"></a>

## <a name="inspection-mode-and-the-html-window"></a>Režim kontroly a v okně HTML

V dalším kroku bude mít rychlý pohled na okno HTML a jak mapuje prvky za vás.

Klikněte na tlačítko **zkontrolujte, jestli se** uvést do režimu kontroly nástroje Page Inspector.

Klepněte na horní část stránky, která říká "logohere". Prohlížené konkrétní element podrobněji, tak zobrazení v okně prohlížeče už mění při přesunutí ukazatele myši.

Nyní přesunutí ukazatele myši **HTML** okna. Při přesunu ukazatele myši, nástroj Page Inspector popisuje element v rámci **HTML** okno a zvýrazní odpovídající element v okně prohlížeče.

![Okno HTML](using-page-inspector-in-aspnet-mvc/_static/image22.png)

Jako předtím, otevře se nástroj Page Inspector \_Layout.cshtml souboru, který v dočasné kartu. Klikněte na tlačítko \_Layout.cshtml dočasné kartu a odpovídající značky budou zvýrazněny ve &lt;záhlaví&gt; část za vás:

![Zvýrazněná značka](using-page-inspector-in-aspnet-mvc/_static/image24.png)

<a id="_using_page_inspector"></a><a id="_7_previewing_css"></a>

## <a name="preview-css-changes-in-the-styles-window"></a>Náhled změn šablon stylů CSS v okně Styly

V dalším kroku použijete nástroj Page Inspector **styly** okna Náhled změn do šablony stylů CSS.

Klikněte na tlačítko **zkontrolujte, jestli se** uvést do režimu kontroly nástroje Page Inspector.

V okně prohlížeče nástroj Page Inspector, přesuňte ukazatel myši nad oddíl "Home Page" až do **div.content obálky** popisek se zobrazí.

![Ukazatel myši div.content obálky](using-page-inspector-in-aspnet-mvc/_static/image26.png)

Klikněte v části div.content obálky jednou a poté přesuňte ukazatel myši na **styly** okna. **Styly** okno zobrazuje všechna pravidla šablon stylů CSS u tohoto elementu. Posuňte se dolů najít .featured .content – obálky třídy selektor. Nyní zrušte zaškrtnutí políčka pro vlastnost barvu pozadí.

![Barva pozadí vymazat](using-page-inspector-in-aspnet-mvc/_static/image28.png)

Všimněte si, jak tato změna přináší náhled okamžitě v okně prohlížeče nástroj Page Inspector.

Zaškrtněte toto políčko znovu, klikněte dvakrát na hodnotu vlastnosti a změní na červený. Změna ukazuje hned:

![Barva pozadí Red](using-page-inspector-in-aspnet-mvc/_static/image30.png)

**Styly** okno umožňuje snadno test a náhled šablon stylů CSS změní před potvrzením změn na styl listu samotný.

<a id="css_auto_sync"></a>
## <a name="css-auto-sync"></a>Automatická synchronizace šablon stylů CSS

> [!NOTE]
> Tato funkce vyžaduje verzi 1.3 nástroj Page Inspector.


Funkce Automatická synchronizace šablon stylů CSS lze upravit přímo soubor šablony stylů CSS a podívejte se změny okamžitě v prohlížeči nástroj Page Inspector.

Klikněte na tlačítko **zkontrolujte, jestli se** uvést do režimu kontroly nástroje Page Inspector.

V prohlížeči nástroj Page Inspector, přesuňte ukazatel myši nad oddíl "Home Page" až do **div.content obálky** popisek se zobrazí. Kliknutím vyberte tento element.

**Styly** okno zobrazuje všechna pravidla šablon stylů CSS u tohoto elementu. Posuňte se dolů najít .featured .content – obálky třídy selektor. Klikněte na ".featured .content obálku". Nástroj Page Inspector otevře soubor šablony stylů CSS, která definuje tento styl (Site.css) a zvýrazní odpovídající stylu CSS.

![](using-page-inspector-in-aspnet-mvc/_static/image32.png)

Teď změňte hodnotu `background-color` na hodnotu "red". Změny se okamžitě zobrazí v prohlížeči nástroj Page Inspector.

![](using-page-inspector-in-aspnet-mvc/_static/image34.png)

<a id="css_color_picker"></a>
## <a name="using-the-css-color-picker"></a>Výběr barvy šablon stylů CSS

Editor šablon stylů CSS v sadě Visual Studio 2012 obsahuje barvu ovládacího prvku pro výběr, která umožňuje jednoduše vybrat a vložit barvy. Výběr barvy obsahuje standardní paletu barev, podporuje názvy standardních barev, hash kódy, barvy RGB, RGBA, HSL a HSLA a udržuje seznam barvy, které jste naposledy použili v dokumentu.

V předchozí části, změnil hodnotu ovládacího prvku `background-color` vlastnost. K vyvolání barev, umístěte kurzor po názvu vlastnosti a typ **#** nebo **rgb (**.

![Výběr pruhu barev šablon stylů CSS](using-page-inspector-in-aspnet-mvc/_static/image36.png)

Klikněte na barvu, kterou chcete vyberte ho, nebo stisknutím klávesy se šipkou dolů a pak pomocí kláves šipka doleva a doprava procházení barvy. Při návštěvě barvu odpovídající šestnáctková hodnota je zobrazen náhled:

![Hodnota vlastnosti barvu pozadí náhledu](using-page-inspector-in-aspnet-mvc/_static/image38.png)

Pokud pruhu barev nemá Přesná barva, kterou chcete, můžete použít v pop – seznamu pro výběr barvy. Pokud chcete soubor otevřít, klikněte na dvojitou šipku na pravém konci pruhu barev nebo jednou nebo dvakrát klávesu šipka dolů na klávesnici.

![Výběr barvy šablon stylů CSS Pop dolů](using-page-inspector-in-aspnet-mvc/_static/image40.png)

Klikněte na barvu z svislá čára na pravé straně. V hlavním okně zobrazí přechod pro tuto barvu. Zvolte barvu přímo z svislá čára stisknutím klávesy Enter nebo klikněte na tlačítko kdekoli v hlavním okně Vybrat s větší přesností.

Pokud je na obrazovce počítače, který chcete použít barvu (nemusí být uvnitř uživatelské rozhraní Visual Studia), můžete zachytit její hodnotu s použitím nástroje kapátko vpravo dole.

Neprůhlednost barvu také můžete změnit přesunutím posuvníku v dolní části nástroje pro výběr barvy. Provádění, změní se barva hodnot na hodnoty RGBA, protože formát RGBA může představovat neprůhlednosti.

Po výběru barvy, stiskněte klávesu Enter a pak zadejte středníkem k dokončení barvu pozadí položky v *Site.css* souboru.

<a id="_the_update_bar"></a>

### <a name="the-page-inspector-update-bar"></a>Kontrola aktualizace posuvník

Nástroj Page Inspector okamžitě zjistí změnu *Site.css* soubor a zobrazí výstrahu na panelu aktualizace.

![Panel aktualizace](using-page-inspector-in-aspnet-mvc/_static/image42.png)

Uložte všechny soubory a aktualizujte prohlížeč, nástroj Page Inspector stisknutím klávesy Ctrl + Alt + Enter nebo klikněte na panel aktualizace. Změna barvy zvýraznění se zobrazí v prohlížeči.

<a id="map_dynamic_elements"></a>
## <a name="mapping-dynamic-page-elements-to-javascript"></a>Mapování elementů dynamických stránek pro jazyk JavaScript

V moderních webových aplikací elementy na stránce často dynamicky generované s použitím jazyka JavaScript. To znamená, že není žádné statické značky (HTML nebo Razor), které odpovídá tyto prvky stránky.

Verze 1.3 nástroj Page Inspector můžete nyní mapa položky, které byly dynamicky přidány na stránku zpět na odpovídající kód jazyka JavaScript. Abychom si předvedli tuto funkci, použijeme [jedné stránky aplikace (SPA) šablony](../../../single-page-application/overview/introduction/knockoutjs-template.md).

> [!NOTE]
> Šablona jednostránková aplikace vyžaduje [ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650) aktualizovat.


V sadě Visual Studio, zvolte **souboru** &gt; **nový projekt**. Na levé straně rozbalte **Visual C#** vyberte **webové**a pak vyberte **webová aplikace ASP.NET MVC4**. Klikněte na **OK**.

V **nového projektu ASP.NET MVC 4** dialogového okna, vyberte **jednostránkové aplikace**.

V Průzkumníku řešení rozbalte **zobrazení** složku a potom **Domů** složky. Souboru Index.cshtml klikněte pravým tlačítkem myši a zvolte **zobrazení v nástroje Page Inspector**.

Nejprve thing, který je zobrazený v prohlížeči nástroj Page Inspector je přihlašovací stránku. Klikněte na "Zaregistrovat" a vytvořte uživatelské jméno a heslo. Po registraci, aplikace vás přihlásí a vytvoří se některé ukázkové položky seznamu úkolů.

Klikněte na tlačítko **zkontrolujte, jestli se** uvést do režimu kontroly nástroje Page Inspector. V prohlížeči nástroj Page Inspector klikněte na jednu z položek úkolů. Všimněte si, že místo zvýrazněným modrou barvu elementu je zvýrazněn oranžově "JS" vedle názvu elementu. To znamená, že element vytvořil dynamicky prostřednictvím skriptu.

![](using-page-inspector-in-aspnet-mvc/_static/image44.png)

Kromě toho se zobrazí oranžové podtržení na **zásobník volání** kartu. Znamená to, že **zásobník volání** podokno má další informace o elementu.

Klikněte na **zásobník volání** kartu. **Zásobník volání** podokně se zobrazí zásobník volání pro volání jazyka JavaScript vytvořeného elementu. Volání externí knihovny, jako je jQuery, jsou sbaleny, takže můžete snadno zobrazit volání skriptu vaší aplikace.

![](using-page-inspector-in-aspnet-mvc/_static/image46.png)

Pokud chcete zobrazit úplný zásobník volání externích knihovnách, včetně lze rozbalit uzly s popiskem "Externí knihovny":

![](using-page-inspector-in-aspnet-mvc/_static/image48.png)

Pokud kliknete na položku v zásobníku volání, Visual Studio otevře soubor kódu a zvýrazní odpovídající skript.

![](using-page-inspector-in-aspnet-mvc/_static/image50.png)

## <a name="see-also"></a>Viz také

[Úvod do ASP.NET MVC 4 pomocí sady Visual Studio](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md) (webové stránky ASP.net)

[Představení nástroje Page Inspector](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) (video Channel 9)

[Chybové zprávy nástroje Page Inspector](https://go.microsoft.com/?linkid=9813062) (MSDN)

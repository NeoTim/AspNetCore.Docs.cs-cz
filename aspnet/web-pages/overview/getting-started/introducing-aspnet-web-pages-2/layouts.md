---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/layouts
title: Představení rozhraní ASP.NET Web Pages – vytvoření konzistentního rozložení | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte, jak k vytvoření konzistentního vzhledu stránky na webu, který používá rozhraní ASP.NET Web Pages použít rozložení. Předpokládá, že jste dokončili...
ms.author: riande
ms.date: 05/28/2015
ms.assetid: c85ec591-f8d7-4882-b763-de6ab9f3df7a
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/layouts
msc.type: authoredcontent
ms.openlocfilehash: a6a007678d58547e9987ebda46bd08ae8aea66f7
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021176"
---
<a name="introducing-aspnet-web-pages---creating-a-consistent-layout"></a>Úvod do webových stránek ASP.NET – vytvoření konzistentního rozložení
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V tomto kurzu se dozvíte, jak používat *rozložení* k vytvoření konzistentního vzhledu stránky na webu, který používá rozhraní ASP.NET Web Pages. Předpokládá, že jste dokončili řady prostřednictvím [odstranění databázových dat ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251584).
> 
> Co se dozvíte:
> 
> - Rozložení stránky je.
> - Jak kombinovat rozložení stránek s dynamickým obsahem.
> - Jak předávat hodnoty na stránku rozložení.


## <a name="about-layouts"></a>Informace o rozložení

Na stránkách jsme dosud vytvořili všechny byly dokončení samostatné stránky. Všechny patřit do stejné lokality, ale nemají žádné společné prvky nebo standardní vzhled.

Většina serverů mají konzistentní vzhled a rozložení. Například, pokud přejdete [http://www.microsoft.com/web/gallery/install.aspx?appid=IISExpress](https://www.microsoft.com/web/) lokality a vyhledejte, uvidíte, že na všech stránkách dodržovat celkové rozložení a vizuální motiv:

![Stránka webu http://www.microsoft.com/web/gallery/install.aspx?appid=IISExpress znázorňující rozložení záhlaví, navigační oblast, oblast obsahu a zápatí](layouts/_static/image1.png)

*Neefektivní* způsob, jak vytvořit toto rozložení by k definování záhlaví, v navigačním panelu a v zápatí samostatně na jednotlivých stránkách. Jste byste být duplikování stejné značky pokaždé, když. Pokud chcete něco změnit (například aktualizovat zápatí), je třeba změnit každou stránku samostatně.

Tady *rozložení stránky* se dělí na. ASP.NET Web Pages, můžete definovat rozložení stránky, která poskytuje celkový kontejneru pro stránky na webu. Rozložení stránky může obsahovat například záhlaví, navigační oblasti a zápatí. Stránka rozložení obsahuje zástupný symbol, kde se vloží hlavní obsah.

Potom můžete definovat jednotlivé stránky obsahu, které obsahují kód a kód pouze pro danou stránku. Stránky obsahu nemusí být kompletní HTML stránky. ještě nemají mít `<body>` elementu. Mají také řádku kódu, který určuje jaké rozložení stránky, které chcete zobrazit obsah v ASP.NET. Tady je obrázek, který zhruba ukazuje, jak tento vztah funguje:

![Koncepční diagram zobrazující dvě obsahu stránky a rozložení stránky, do kterého se vešly](layouts/_static/image2.png)

Tato interakce je snadno pochopitelný při pozorování v akci. V tomto kurzu změníte filmy stránky rozložení.

## <a name="adding-a-layout-page"></a>Přidání rozložení stránky

Začnete tím, že vytvoříte stránku rozložení, který definuje rozložení stránky s záhlaví, zápatí nebo oblast pro hlavní obsah. V lokalitě WebPagesMovies přidat CSHTML stránku s názvem  *\_Layout.cshtml*.

Počáteční podtržítko ( `_` ) znak je důležité. Pokud na stránce název začíná podtržítkem, nebude ASP.NET přímo odeslat tuto stránku v prohlížeči. Tato konvence umožňuje definovat stránky, které jsou požadovány pro váš web, ale, že uživatelé by neměla mít možnost požádat o přímo.

Nahraďte obsah na stránce s následujícími možnostmi:

[!code-html[Main](layouts/samples/sample1.html)]

Jak je vidět, tento kód je právě ve formátu HTML, který používá `<div>` můžete definovat tři oddíly v stránce plus jeden více `<div>` – element pro uložení tři oddíly. Zápatí obsahuje hodně kódu Razor: `@DateTime.Now.Year`, který bude vykreslení na tomto místě na stránce do aktuálního roku.

Všimněte si, že je odkaz s názvem šablony stylů *Movies.css*. Šablona stylů je, kde budou podrobnosti fyzické rozložení elementů určené. Který vytvoříte za chvíli.

Pouze neobvyklé funkce v tomto  *\_Layout.cshtml* stránka je `@Render.Body()` řádku. To je zástupný symbol, kde je obsah přejde při toto rozložení je sloučen s jinou stránku.

## <a name="adding-a-css-file"></a>Přidává se soubor CSS

Preferovaný způsob, jak definovat skutečné uspořádání prvků (to znamená, vzhled) na stránce je použití pravidla šablony kaskádových stylů (CSS). Proto vytvoříte *.css* soubor, který obsahuje pravidla pro nové rozložení.

V nástroji WebMatrix vyberte kořenového webu. Pak v **soubory** kartu na pásu karet klikněte na šipku v části **nový** tlačítko a pak klikněte na tlačítko **novou složku**.

![Možnost "Nová složka" v rámci New na pásu karet.](layouts/_static/image3.png)

Název nové složky *styly*.

![Pojmenování nové složky "styly.](layouts/_static/image4.png)

Uvnitř nové *styly* složce vytvořte soubor s názvem *Movies.css*.

![Vytvoření nového souboru Movies.css](layouts/_static/image5.png)

Nahraďte obsah nového *.css* souboru následujícím kódem:

[!code-css[Main](layouts/samples/sample2.css)]

Říkáme nebude téměř těchto pravidel šablon stylů CSS, s výjimkou Poznámka: dvě věci. Jeden je, že kromě nastavení písma a velikosti, pravidla použít absolutní pozici k navázání umístění záhlaví, zápatí a oblast hlavního obsahu. Pokud začínáte umístění v jazyce CSS, si můžete přečíst [umístění šablon stylů CSS](http://www.w3schools.com/css/css_positioning.asp) kurz v lokalitě W3Schools.

Druhou věcí si je, že v dolní části, jsme jste zkopírovali pravidel stylu, které byly původně definována jednotlivě v *Movies.cshtml* souboru. Tato pravidla byly použity v [Úvod do zobrazení dat podle pomocí rozhraní ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251580) kurzu provádět `WebGrid` pomocné rutiny vykreslení kódu, který rozděluje přidán do tabulky. (Pokud se chystáte používat *.css* souboru pro definice stylů, můžete také ukládat pravidel stylu, které pro celý web v něm.)

## <a name="updating-the-movies-file-to-use-the-layout"></a>Aktualizuje se soubor videa rozložení

Nyní můžete aktualizovat existující soubory v tak, aby používal nové rozložení. Otevřít *Movies.cshtml* souboru. V horní části stránky přidejte jako první řádek kódu, následující:

[!code-csharp[Main](layouts/samples/sample3.cs)]

Na stránce nyní začíná tímto způsobem:

[!code-cshtml[Main](layouts/samples/sample4.cshtml?highlight=2)]

Tento jeden řádek kódu říká technologie ASP.NET, že *filmy* spuštění stránky, by měly být sloučeny s  *\_Layout.cshtml* souboru.

Protože *Movies.cshtml* souboru teď používá rozložení stránky, můžete odebrat značku ze *Movies.cshtml* stránka, která už se postaral o podle  *\_Layout.cshtml*souboru. Vysypat `<!DOCTYPE>`, `<html>`, a `<body>` počátečních a koncových značek. Vysypat celý `<head>` elementu a jeho obsah, včetně pravidel stylu mřížky, protože Teď máte tato pravidla *.css* souboru. Ať jste na něj, změňte existující `<h1>` elementu `<h2>` element; můžete mít `<h1>` elementu na stránce rozložení již. Změnit `<h2>` text na "Seznamu filmy".

Za normálních okolností nemusí provádět tyto druhy změny na stránce obsahu. Při spuštění webu navýšení kapacity s stránku rozložení, vytváření obsahu stránky bez těchto prvků na začátku. V takovém případě však při převádění na samostatné stránce, který se používá rozložení, takže je hodně vyčištění.

Jakmile budete hotovi, *Movies.cshtml* stránka bude vypadat nějak takto:

[!code-cshtml[Main](layouts/samples/sample5.cshtml)]

### <a name="testing-the-layout"></a>Testování rozložení

Nyní uvidíte, jak vypadá rozložení. V nástroji WebMatrix, klikněte pravým tlačítkem myši *Movies.cshtml* stránku a vybrat **spustit v prohlížeči**. Prohlížeč zobrazí stránky, vypadá jako na této stránce:

![Stránka filmy vykreslen pomocí rozložení](layouts/_static/image6.png)

ASP.NET se sloučil obsah stránky Movies.cshtml do  *\_Layout.cshtml* stránky, kde vpravo `RenderBody` je metoda. A samozřejmě  *\_Layout.cshtml* stránce odkazy *.css* souboru, která definuje vzhled stránky.

## <a name="updating-the-addmovie-page-to-use-the-layout"></a>Aktualizuje se AddMovie stránku rozložení

Skutečná výhoda rozložení je, že můžete použít je pro všechny stránky ve vaší lokalitě. Otevřít *AddMovie.cshtml* stránky.

Může být nezapomeňte, že *AddMovie.cshtml* stránky původně některá pravidla šablon stylů CSS v něm k definování vzhledu chybových zpráv ověření. Vzhledem k tomu, že máte *.css* soubor pro váš webový server, můžete přesunout tyto pravidla, která *.css* souboru. Odeberte je ze *AddMovie.cshtml* soubor a přidat je do dolní části *Movies.css* souboru. Při přesunutí následující pravidla:

[!code-css[Main](layouts/samples/sample6.css)]

Provést stejnou řadu změn v *AddMovie.cshtml* , jaké jste použili *Movies.cshtml* – přidání `Layout="~/_Layout.cshtml;` a odebrat značka jazyka HTML, který je teď nadbytečné. Změnit `<h1>` elementu `<h2>`. Až budete hotovi, stránka bude vypadat jako v tomto příkladu:

[!code-cshtml[Main](layouts/samples/sample7.cshtml)]

Spuštění stránky. Teď vypadá jako na tomto obrázku:

!["Přidat video" stránku vykreslen pomocí rozložení](layouts/_static/image7.png)

Chcete provést změny podobné změny stránky na webu – *EditMovie.cshtml* a *DeleteMovie.cshtml*. Ale předtím, než se pustíte, můžete provádět jiná změna rozložení, které je o něco víc.

## <a name="passing-title-information-to-the-layout-page"></a>Informace o předávání titulech ke stránce rozložení

*\_Layout.cshtml* má stránka, kterou jste vytvořili `<title>` element, který je nastavena na "Můj web video". Většina prohlížečů zobrazit obsah tohoto prvku jako text na kartě:

![Na stránce &lt;název&gt; element zobrazí na kartě prohlížeče](layouts/_static/image8.png)

Tyto informace název je obecná. Předpokládejme, že chcete text nadpisu na konkrétnější na aktuální stránku. (Text nadpisu se používá také vyhledávači jak zjistit, co vaše stránka o.) Můžete předat informace z obsahu stránky jako *Movies.cshtml* nebo *AddMovie.cshtml* rozložení stránky a pak pomocí těchto informací k přizpůsobení stránky rozložení vykreslí.

Otevřít *Movies.cshtml* stránku znovu nezobrazovat. V kódu v horní části přidejte následující řádek:

[!code-csharp[Main](layouts/samples/sample8.cs)]

`Page` Objekt je k dispozici na všech *.cshtml* stránky a je pro tento účel, a to ke sdílení informací mezi stránky a rozložení.

Otevřít<em>\_Layout.cshtml</em> stránky. Změnit `<title>` element tak, že bude vypadat jako tento kód:

[!code-html[Main](layouts/samples/sample9.html)]

Tento kód vykreslí, bez ohledu `Page.Title` vlastnost přímo na tomto místě na stránce.

Spustit *Movies.cshtml* stránky. Tentokrát kartu prohlížeče ukazuje předané jako hodnotu `Page.Title`:

![Na kartě prohlížeče, který je zobrazen titulek vytvářet dynamicky](layouts/_static/image9.png)

Pokud chcete, zobrazte zdroj stránky v prohlížeči. Vidíte, že `<title>` prvek je vykreslovaný jako `<title>List Movies</title>`.

> [!TIP] 
> 
> **Objekt stránky**
> 
> Užitečné funkce `Page` je, že se jedná o dynamický objekt, `Title` vlastnost není pevný nebo vyhrazený název. Můžete použít *jakékoli* název hodnotu `Page` objektu. Například, kterou může stejně snadno, jste předali název pomocí vlastnosti s názvem `Page.CurrentName` nebo `Page.MyPage`. Jediným omezením je, že název obsahuje postupy běžných pravidel pro vlastnosti, které může mít název. (Například název nesmí obsahovat mezery.)
> 
> Můžete předat libovolný počet hodnot s použitím `Page` objektu. Pokud chcete předat informace o filmu ke stránce rozložení, mohli předat hodnoty pomocí něco jako `Page.MovieTitle` a `Page.Genre` a `Page.MovieYear`. (Nebo jiné názvy, které je určena k ukládání informací.) Jediným požadavkem – což je pravděpodobně zřejmé – je, že budete muset použít stejné názvy v obsahu stránky a rozložení stránky.
> 
> Informace o předávání pomocí `Page` objektu se neomezuje na pouze text se zobrazí na stránce rozložení. Můžete předat hodnotu na stránku rozložení a pak použít hodnotu rozhodnout, jestli se mají zobrazovat oddíl na stránce kódu ve stránce rozložení co *.css* soubor použít a tak dále. Předáním hodnoty `Page` objektu jsou stejně jako jakékoli jiné hodnoty, které použijete v kódu. Stačí je, že hodnoty pocházejí z obsahu stránky a jsou předány na stránku rozložení.


Otevřít *AddMovie.cshtml* stránce a přidá řádek do horní části kódu, který obsahuje název *AddMovie.cshtml* stránky:

[!code-csharp[Main](layouts/samples/sample10.cs)]

Spustit *AddMovie.cshtml* stránky. Zobrazí se nový název:

![Na kartě prohlížeče, který je zobrazen titulek "přidat filmy vytvářet dynamicky](layouts/_static/image10.png)

## <a name="updating-the-remaining-pages-to-use-the-layout"></a>Aktualizuje se zbývající stránky rozložení

Teď můžete dokončit zbývající stránky na webu tak, aby používaly nové rozložení. Otevřít *EditMovie.cshtml* a *DeleteMovie.cshtml* následně a provést stejné změny v každém.

Přidáte řádek kódu, který odkazuje na stránku rozložení:

[!code-csharp[Main](layouts/samples/sample11.cs)]

Přidejte řádek nastavit nadpis stránky:

[!code-csharp[Main](layouts/samples/sample12.cs)]

nebo:

[!code-csharp[Main](layouts/samples/sample13.cs)]

Odeberte všechny nadbytečné kód HTML – v podstatě, ponechte pouze bity, které jsou uvnitř `<body>` – element (plus bloku kódu v horní části).

Změnit `<h1>` elementu do `<h2>` elementu.

Pokud jste provedli tyto změny, otestujte a ujistěte se, že se správně zobrazuje a zda je správný název.

## <a name="parting-thoughts-about-layout-pages"></a>Závěrečný myšlenky o rozložení stránky

V tomto kurzu jste vytvořili  *\_Layout.cshtml* stránce a použít `RenderBody` metoda sloučit obsah z jiné stránky. To je základní vzor pro použití rozložení na webových stránkách.

Rozložení stránky mají další funkce, které jsme neměli abychom ji tady pokryli. Například je možné vnořovat rozložení stránek – jednu stránku rozložení může odkazovat na zase jiný. Vnořené rozložení může být užitečné, pokud pracujete s dílčí části objektů lokality, které vyžadují různá rozložení. Můžete také použít další metody (například `RenderSection`) k nastavení s názvem části na stránce rozložení.

Kombinace stránkami rozložení a *.css* soubory jsou výkonné. Jak uvidíte v dalším sérii, ve službě WebMatrix můžete vytvořit web založený na *šablony*, kterým získáte web, který obsahuje předem připravených funkce v ní. Šablony velice rozložení stránky a šablony stylů CSS vytvářet weby, které vypadají skvěle a, které mají funkce, jako jsou nabídky. Zde je snímek domovské stránce webu na základě šablony, zobrazuje funkce, které používají rozložení stránky a šablony stylů CSS:

![Rozložení vytvořené šablony webu služby WebMatrix zobrazující záhlaví, navigační oblast, oblasti obsahu, volitelný oddíl a odkazy přihlášení](layouts/_static/image11.png)

## <a name="complete-listing-for-movie-page-updated-to-use-a-layout-page"></a>Úplný seznam pro stránku Movie (aktualizované na používání stránku rozložení)

[!code-cshtml[Main](layouts/samples/sample14.cshtml)]

## <a name="complete-page-listing-for-add-movie-page-updated-for-layout"></a>Dokončení stránku se seznamem pro přidání stránky Movie (aktualizováno pro rozložení)

[!code-cshtml[Main](layouts/samples/sample15.cshtml)]

## <a name="complete-page-listing-for-delete-movie-page-updated-for-layout"></a>Úplný výpis stránky pro stránku odstranit Movie (aktualizováno pro rozložení)

[!code-cshtml[Main](layouts/samples/sample16.cshtml)]

## <a name="complete-page-listing-for-edit-movie-page-updated-for-layout"></a>Úplný výpis stránky pro stránku video upravit (aktualizováno pro rozložení)

[!code-cshtml[Main](layouts/samples/sample17.cshtml)]

## <a name="coming-up-next"></a>Chystá se další

V dalším kurzu se dozvíte, jak publikovat svůj web na Internetu, všichni můžou zobrazit.

## <a name="additional-resources"></a>Další prostředky

- [Vytvoření konzistentního vypadat](https://go.microsoft.com/fwlink/?LinkID=202891) – článek, který obsahuje některé další podrobné informace o práci s rozložením. Také popisuje, jak předat hodnotu stránku rozložení, který zobrazí nebo skryje část obsahu.
- [Vnořené rozložení stránky s jádrem Razor](http://www.mikesdotnetting.com/Article/164/Nested-Layout-Pages-with-Razor) – Mike Brind blogy příklad toho, jak lze vnořit rozložení stránky. (Včetně stažení stránky.)

> [!div class="step-by-step"]
> [Předchozí](deleting-data.md)
> [další](publishing.md)

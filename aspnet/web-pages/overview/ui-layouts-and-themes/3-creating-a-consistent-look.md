---
uid: web-pages/overview/ui-layouts-and-themes/3-creating-a-consistent-look
title: Vytvoření konzistentního rozložení v rozhraní ASP.NET Web Pages servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Chcete-li efektivnější vytvořit webové stránky pro váš web, můžete vytvořit opakovaně použitelné bloky obsahu (jako záhlaví a zápatí) u vašeho webu a jazyka c je...
ms.author: riande
ms.date: 03/10/2014
ms.assetid: d7bd001b-6db2-4422-9b78-f3d08b743b00
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/3-creating-a-consistent-look
msc.type: authoredcontent
ms.openlocfilehash: 83aef41c0baaeca6a25e09b4ea797ce9ee963a85
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021544"
---
<a name="creating-a-consistent-layout-in-aspnet-web-pages-razor-sites"></a>Vytvoření konzistentního rozložení v lokalitách rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak můžete pomocí rozložení stránky na webu rozhraní ASP.NET Web Pages (Razor) k vytvoření opakovaně použitelné bloky obsahu (jako záhlaví a zápatí) a k vytvoření konzistentního vzhledu všech stránek na webu.
> 
> **Co se dozvíte:** 
> 
> - Jak vytvářet opakovaně použitelné bloky obsahu jako záhlaví a zápatí.
> - Postup vytvoření konzistentního vzhledu všech stránek ve vaší lokalitě pomocí rozložení.
> - Jak předávat data v době běhu na stránku rozložení.
> 
> Toto jsou funkce technologie ASP.NET v následujícím článku:
> 
> - Bloky obsahu, které jsou soubory, které obsahují obsah ve formátu HTML má být vložen do více stránek.
> - Rozložení stránky, které jsou stránky, které obsahují obsah ve formátu HTML, který může být sdílen stránky na webu.
> - `RenderPage`, `RenderBody`, A `RenderSection` metody, které zjistit, kam chcete vložit elementy stránek technologie ASP.NET.
> - `PageData` Slovník, který umožňuje sdílet data mezi bloky obsahu a rozložení stránky.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


## <a name="about-layout-pages"></a>Informace o rozložení stránky

Mnohé weby mají obsah, který se zobrazí na každé stránce, jako je záhlaví a zápatí nebo pole, který uživatele informuje, že jste přihlášení. Technologie ASP.NET umožňuje vytvořit samostatný soubor s bloku s obsahem, který může obsahovat text, značek a kódu, stejně jako běžné webové stránce. Pak můžete vložit blok s obsahem na jiných stránkách na webu, kam chcete informace zobrazit. Díky tomu není nutné zkopírovat a vložit stejný obsah do každé stránky. Vytvoření společného obsahu, jako je to také usnadňuje rychlé k aktualizaci vašeho webu. Pokud potřebujete změnit obsah, můžete aktualizovat jenom jeden soubor a změny se projeví všude, kde byl vložen obsah.

Následující diagram znázorňuje, jak obsah blokuje práce. Když prohlížeč požádá o stránku z webového serveru, ASP.NET vloží bloky obsahu v místě, kde `RenderPage` metoda je volána na hlavní stránce. Na stránce dokončení (sloučení) se pak posílají do prohlížeče.

![Koncepční diagram znázorňující, jak metoda RenderPage vloží odkazované stránky do aktuální stránky.](3-creating-a-consistent-look/_static/image1.jpg)

V tomto postupu vytvoříte stránku, která odkazuje na dva bloky obsahu (záhlaví nebo zápatí), které se nacházejí v samostatných souborech. Tyto stejné bloky obsahu můžete použít na libovolné stránce na webu. Jakmile budete hotovi, získáte na stránce takto:

![Snímek obrazovky zobrazující stránku v prohlížeči, který je výsledkem spuštění stránky, která obsahuje volání metodě RenderPage.](3-creating-a-consistent-look/_static/image2.jpg)

1. V kořenové složce vašeho webu, vytvořte soubor s názvem *Index.cshtml*.
2. Nahraďte existující kód následujícím kódem:

    [!code-html[Main](3-creating-a-consistent-look/samples/sample1.html)]
3. V kořenové složce vytvořte složku s názvem *Shared*.

    > [!NOTE]
    > Se obvykle používá k ukládání souborů, které jsou odkazy sdíleny mezi webové stránky ve složce s názvem *Shared*.
4. V *Shared* složce vytvořte soubor s názvem  *\_Header.cshtml*.
5. Jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-html[Main](3-creating-a-consistent-look/samples/sample2.html)]

    Všimněte si, že je název souboru  *\_Header.cshtml*, podtržítko (\_) jako předponu. ASP.NET nebude lze do prohlížeče odeslat stránku, pokud jeho název začíná podtržítkem. To zabrání uživatelům požaduje (neúmyslně nebo jinak) tyto stránky přímo. Je vhodné použít na název stránky, které mají bloky obsahu v nich, podtržítko, protože nechcete skutečně uživatelé mít možnost požádat o tyto stránky &#8212; existují výhradně má být vložen do jiné stránky.
6. V *Shared* složce vytvořte soubor s názvem  *\_Footer.cshtml* a nahraďte obsah následujícím kódem:

    [!code-html[Main](3-creating-a-consistent-look/samples/sample3.html)]
7. V *Index.cshtml* stránce, přidejte dvě volání `RenderPage` způsob, jak je znázorněno zde:

    [!code-html[Main](3-creating-a-consistent-look/samples/sample4.html)]

    To ukazuje, jak vložit blok s obsahem do webové stránky. Volání `RenderPage` metoda a předejte mu název souboru, jejíž obsah chcete vložit v daném okamžiku. Sem vkládáte obsah  *\_Header.cshtml* a  *\_Footer.cshtml* soubory do *Index.cshtml* souboru.
8. Spustit *Index.cshtml* stránku v prohlížeči. (V nástroji WebMatrix, v **soubory** pracovní prostor, klikněte pravým tlačítkem na soubor a pak vyberte **spustit v prohlížeči**.)
9. V prohlížeči zobrazte zdroj stránky. (Například v aplikaci Internet Explorer, klikněte pravým tlačítkem na stránce a potom klikněte na tlačítko **zobrazit zdroj**.)

    To umožňuje zobrazit kódu webové stránky, které je odesláno prohlížeči, který kombinuje značky index stránky s bloky obsahu. Následující příklad ukazuje na stránku zdroj, který je vykreslen pro *Index.cshtml*. Volání `RenderPage` , který jste vložili do *Index.cshtml* bylo nahrazeno tématem skutečný obsah souborů záhlaví a zápatí.

    [!code-html[Main](3-creating-a-consistent-look/samples/sample5.html)]

## <a name="creating-a-consistent-look-using-layout-pages"></a>Vytvoření konzistentního vzhledu pomocí rozložení stránek

Zatím jste viděli, že je snadné zahrnutí stejného obsahu na více stránkách. Více strukturovaný přístup k vytvoření konzistentního vzhledu pro lokalitu, je použití rozložení stránky. Stránka rozložení definuje strukturu webové stránky, ale neobsahuje žádný skutečný obsah. Po vytvoření rozložení stránek, můžete vytvořit webové stránky, které obsahují obsah a připojit je ke stránce rozložení. Při tyto stránky se zobrazí, budete mít ve formátu podle rozložení stránky. (V tomto smyslu stránku rozložení funguje jako šablony pro obsah, který je definován v jiné stránky.)

Stránka rozložení se stejně jako libovolnou stránku HTML s tím rozdílem, že obsahuje volání `RenderBody` metody. Pozice `RenderBody` metody ve stránce rozložení určuje, kde budou zahrnuty informace z obsahu stránky.

Následující diagram ukazuje, jak obsahu stránky a rozložení stránky jsou zkombinované v době běhu k vytvoření konečného webové stránky. Prohlížeč požádá o stránku obsahu. Stránka obsahu obsahuje kód, který určuje požadovanou stránku rozložení pro stránky strukturu. Na stránce rozložení se vloží obsah v místě, kde `RenderBody` metoda je volána. Obsahu bloky lze také vložit do rozložení stránky voláním `RenderPage` metodu, tak, jak jste to udělali v předchozí části. Po dokončení webové stránky je odesláno prohlížeči.

![Snímek obrazovky zobrazující stránku v prohlížeči, který je výsledkem spuštění stránky, která obsahuje volání metodě RenderBody.](3-creating-a-consistent-look/_static/image3.jpg)

Následující postup ukazuje, jak vytvořit rozložení stránky obsahu stránky a odkaz na něj.

1. V *Shared* složku vašeho webu, vytvořte soubor s názvem  *\_Layout1.cshtml*.
2. Jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-html[Main](3-creating-a-consistent-look/samples/sample6.html)]

    Můžete použít `RenderPage` metody ve stránce rozložení pro vložení bloky obsahu. Rozložení stránky může obsahovat pouze jedno volání `RenderBody` metody.
3. V *Shared* složce vytvořte soubor s názvem  *\_Header2.cshtml* a jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-html[Main](3-creating-a-consistent-look/samples/sample7.html)]
4. V kořenové složce vytvořte novou složku s názvem *styly*.
5. V *styly* složce vytvořte soubor s názvem *Site.css* a přidejte následující definice:

    [!code-css[Main](3-creating-a-consistent-look/samples/sample8.css)]

    Tyto definice stylů se tady jenom zobrazit použití šablony stylů se stránkami rozložení. Pokud chcete, můžete definovat vlastní styly pro tyto elementy.
6. V kořenové složce vytvořte soubor s názvem *Content1.cshtml* a jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample9.cshtml)]

    Toto je stránka, která bude používat stránku rozložení. Blok kódu v horní části stránky označuje stránce rozložení, které se má použít pro formátování tohoto obsahu.
7. Spustit *Content1.cshtml* v prohlížeči. Na vykreslené stránce používá formát a šablony stylů definovaná v  *\_Layout1.cshtml* a text (obsah) definované v *Content1.cshtml*.

    ![[image]](3-creating-a-consistent-look/_static/image4.jpg)

    Krok 6 a vytvořte další stránky obsahu, které pak můžete sdílet stejnou stránku rozložení, můžete opakovat.

    > [!NOTE]
    > Můžete nastavit svůj web tak, aby může automaticky použít stejnou stránku rozložení pro všechny stránky obsahu ve složce. Podrobnosti najdete v tématu [přizpůsobení chování v celém webu pro webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202906).

## <a name="designing-layout-pages-that-have-multiple-content-sections"></a>Návrh rozložení stránky, které mají více oddíly obsahu.

Stránka obsahu může mít více oddílů, což je užitečné, pokud chcete použít rozložení, které mají více oblastí s obsahem replaceable. Na stránce obsahu dáte každý oddíl jedinečný název. (Výchozí sekcí zbývá nepojmenované.) Na stránce rozložení přidáte `RenderBody` metodu k určení, kde by se měla zobrazit v části nepojmenované (výchozí). Potom přidáte jako samostatné `RenderSection` metody za účelem vykreslení jednotlivě pojmenovaných oddílů.

Následující diagram znázorňuje způsob, jakým zpracovává obsah, který je rozdělený do několika částí technologie ASP.NET. Každý pojmenovaný oddíl je obsažen v části bloku na stránce obsahu. (Se nazývají `Header` a `List` v příkladu.) Rozhraní framework vloží části obsahu do stránky rozložení v místě, kde `RenderSection` metoda je volána. Nepojmenované (výchozí) oddíl disku do mechaniky v místě, kde `RenderBody` metoda je volána, protože jste viděli již dříve.

![Koncepční diagram znázorňující, jak metoda RenderSection vloží odkazy na oddíly na aktuální stránku.](3-creating-a-consistent-look/_static/image5.jpg)

Tento postup ukazuje, jak vytvořit stránku obsahu, který má více oddílů obsahu a jak lze vykreslit pomocí rozložení stránky, která podporuje víc oddílů obsahu.

1. V *Shared* složce vytvořte soubor s názvem  *\_Layout2.cshtml*.
2. Jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-html[Main](3-creating-a-consistent-look/samples/sample10.html)]

    Můžete použít `RenderSection` metody vykreslit záhlaví a seznam oddílů.
3. V kořenové složce vytvořte soubor s názvem *Content2.cshtml* a jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample11.cshtml)]

    Tato stránka obsahu obsahuje blok kódu v horní části stránky. Každý pojmenovaný oddíl je obsažen v části bloku. Zbývající části stránky obsahuje výchozí (nepojmenované) části obsahu.
4. Spustit *Content2.cshtml* v prohlížeči.

    ![Snímek obrazovky zobrazující stránku v prohlížeči, který je výsledkem spuštění stránky, která obsahuje volání metodě RenderSection.](3-creating-a-consistent-look/_static/image6.jpg)

## <a name="making-content-sections-optional"></a>Provádění nepovinné oddíly obsahu.

Za normálních okolností oddíly, které jste vytvořili na stránce obsahu se musí shodovat oddíly, které jsou definovány na stránce rozložení. Chyby můžete získat, pokud dojde k některé z následujících akcí:

- Stránka obsahu obsahuje oddíl, který nemá žádný odpovídající oddíl na stránce rozložení.
- Stránka rozložení obsahuje oddíl, který není k dispozici žádný obsah.
- Stránka rozložení obsahuje volání metody, které se pokoušejí vykreslení stejné části více než jednou.

Však můžete přepsat toto chování pro pojmenovaný oddíl deklarováním oddílu, který má být volitelné na stránce rozložení. To vám umožňuje definovat více obsahu stránek, které sdílejí stránku rozložení, ale mohou nebo nemusí mít obsah pro určitou část.

1. Otevřít *Content2.cshtml* a odeberte následující části:

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample12.cshtml)]
2. Uložit na stránku a pak ho spusťte v prohlížeči. Chybová zpráva se zobrazí, protože obsah stránky neposkytuje obsah pro oddíl definovaný v rozložení stránky, konkrétně oddíl hlavičky.

    ![Snímek obrazovky, který se zobrazuje chyba, ke které dojde při spuštění stránky, která volá metodu RenderSection, ale odpovídající oddíl není k dispozici.](3-creating-a-consistent-look/_static/image7.jpg)
3. V *Shared* složku, otevřete  *\_Layout2.cshtml* stránce a nahraďte tento řádek:

    [!code-javascript[Main](3-creating-a-consistent-look/samples/sample13.js)]

    následujícím kódem:

    [!code-javascript[Main](3-creating-a-consistent-look/samples/sample14.js)]

    Jako alternativu můžete na předchozí řádek kódu nahradit následující blok kódu, který vytvoří stejné výsledky:

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample15.cshtml)]
4. Spustit *Content2.cshtml* stránku v prohlížeči znovu. (Pokud stále máte tuto stránku otevřít v prohlížeči, můžete jen aktualizovat ji.) Tentokrát stránka se zobrazí s žádná chyba, i když nemá žádné hlavičky.

## <a name="passing-data-to-layout-pages"></a>Předání dat stránkám rozložení

Můžete mít data definovaná v obsahu stránky, které potřebujete k odkazování na stránce rozložení. Pokud ano, je potřeba předat data z obsahu stránky na stránku rozložení. Například můžete chtít zobrazit stav přihlášení uživatele, nebo můžete chtít zobrazit nebo skrýt oblasti obsahu, které jsou založeny na vstup uživatele.

Předání dat z obsahu stránky pro stránku rozložení, hodnoty je možné uspořádat do `PageData` vlastnost obsahu stránky. `PageData` Vlastnost je kolekce dvojic název/hodnota, které obsahují data, která chcete předávat mezi stránkami. Na stránce rozložení, pak můžete přečíst hodnot z `PageData` vlastnost.

Tady je jiného diagramu. Tento ukazuje, jak můžete používat ASP.NET `PageData` vlastnost k předání hodnot z obsahu stránky na stránku rozložení. Po zahájení sestavování webové stránky ASP.NET vytvoří `PageData` kolekce. Na stránce obsahu píšete kód k vložení dat `PageData` kolekce. Hodnoty v `PageData` kolekci lze rovněž přistupovat pomocí jiné části na stránce obsahu nebo další bloky obsahu.

![Koncepční diagram, který ukazuje, jak naplnit PageData slovníku a předávat tyto informace ke stránce rozložení stránky obsahu.](3-creating-a-consistent-look/_static/image8.jpg)

Následující postup ukazuje, jak předat data z obsahu stránky pro stránku rozložení. Při spuštění stránky se zobrazí tlačítko, které umožňuje uživateli skrýt nebo zobrazit seznam, který je definován v rozložení stránky. Když uživatelé kliknou na tlačítko, nastaví hodnotu true nebo false (Boolean) `PageData` vlastnost. Stránka rozložení přečte tuto hodnotu a pokud je hodnota false, skryje seznamu. Hodnota se také používá na stránce obsahu k určení, jestli se má zobrazit **skrýt seznam** tlačítko nebo **zobrazit seznam** tlačítko.

![[image]](3-creating-a-consistent-look/_static/image9.jpg)

1. V kořenové složce vytvořte soubor s názvem *Content3.cshtml* a jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample16.cshtml)]

    Ukládá dva druhy dat v kódu `PageData` vlastnost &#8212; název webové stránky a true nebo false určující, zda chcete-li zobrazit seznam.

    Všimněte si, že technologie ASP.NET umožňuje vložit kód HTML do stránky podmíněně pomocí bloku kódu. Například `if/else` blok v těle stránky určuje, který formulář pro zobrazení v závislosti na tom, zda `PageData["ShowList"]` je nastavena na hodnotu true.
2. V *Shared* složce vytvořte soubor s názvem  *\_Layout3.cshtml* a jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample17.cshtml)]

    Stránka rozložení obsahuje výraz v `<title>` element, který získá hodnotu názvu `PageData` vlastnost. Využívá také `ShowList` hodnotu `PageData` a určí, jestli se má zobrazit seznam blok s obsahem.
3. V *Shared* složce vytvořte soubor s názvem  *\_List.cshtml* a jakýkoli existující obsah nahraďte následujícím kódem:

    [!code-html[Main](3-creating-a-consistent-look/samples/sample18.html)]
4. Spustit *Content3.cshtml* stránku v prohlížeči. Zobrazí se na stránce se seznamem na levé straně stránky viditelná a **skrýt seznam** tlačítko dole.

    ![Snímek obrazovky zobrazující stránku, která obsahuje seznam a tlačítko s textem "Skrýt seznam".](3-creating-a-consistent-look/_static/image10.jpg)
5. Klikněte na tlačítko **skrýt seznam**. V seznamu zmizí a tlačítko se změní na **zobrazit seznam**.

    ![Snímek obrazovky zobrazující stránku, která se nenachází v seznamu a tlačítko, které se říká zobrazit seznam.](3-creating-a-consistent-look/_static/image11.jpg)
6. Klikněte na tlačítko **zobrazit seznam** tlačítko a v seznamu se zobrazí znovu.

## <a name="additional-resources"></a>Další prostředky


[Přizpůsobení chování v celém webu pro webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202906)

---
uid: web-pages/overview/ui-layouts-and-themes/9-working-with-images
title: Práce s obrázky na webu technologie ASP.NET Web Pages (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola ukazuje, jak přidat, zobrazit a pracovat s imagí (Změna velikosti, Převrátit na ose a přidávat vodoznaky) na vašem webu.
ms.author: riande
ms.date: 02/20/2014
ms.assetid: 778c4e58-4372-4d25-bab9-aec4a8d8e38d
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/9-working-with-images
msc.type: authoredcontent
ms.openlocfilehash: 7536f71eb9afce9d7c8bb7e4d6326d280658c27b
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021713"
---
<a name="working-with-images-in-an-aspnet-web-pages-razor-site"></a>Práce s obrázky na webu rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek ukazuje, jak přidat, zobrazit a pracovat s imagí (Změna velikosti, Převrátit na ose a přidávat vodoznaky) na webu rozhraní ASP.NET Web Pages (Razor).
> 
> Co se dozvíte:
> 
> - Postup přidání obrázku na stránku dynamicky.
> - Jak chcete, aby uživatelé nahrát obrázek.
> - Postup pro změnu velikosti obrázku.
> - Jak překlopit nebo otočení obrázku.
> - Postup přidání vodoznaku do bitové kopie.
> - Jak použít obraz jako vodoznak.
> 
> Toto jsou ASP.NET programování funkcí představených v následujícím článku:
> 
> - `WebImage` Pomocné rutiny.
> - `Path` Objektu, který poskytuje metody, které umožňují pracovat s názvy souborů a cestu.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Služba WebMatrix 2
>   
> 
> V tomto kurzu funguje taky pomocí služby WebMatrix 3.


<a id="Adding_an_Image"></a>
## <a name="adding-an-image-to-a-web-page-dynamically"></a>Dynamické přidání obrázku na webovou stránku

Image můžete přidat na váš web a na jednotlivých stránkách, zatímco vyvíjíte web. Můžete taky umožnit uživatelům odeslat Image, které může být užitečná pro úkoly, jako je, aby přidat profilové fotky.

Pokud chcete zobrazit na stránce bitovou kopii je již k dispozici na webu, použijete HTML `<img>` element následujícím způsobem:

[!code-html[Main](9-working-with-images/samples/sample1.html)]

V některých případech však musíte být schopni zobrazit obrázky dynamicky &#8212; tedy si nejste jisti, co obrázek zobrazit, dokud je stránka je spuštěná.

Postup v této části ukazuje, jak zobrazit obrázek v reálném čase, kdy uživatelé zadat název souboru obrázku ze seznamu názvy imagí. Název bitové kopie, vyberte z rozevíracího seznamu a při odesílání stránky, se zobrazí obrázek, který se vybral.

![[Obrázek] ](9-working-with-images/_static/image1.jpg "ch9images 1.jpg")

1. V nástroji WebMatrix vytvoření nového webu.
2. Přidejte novou stránku s názvem *DynamicImage.cshtml*.
3. V kořenové složce webu, přidejte novou složku a pojmenujte ho *image*.
4. Přidejte čtyři imagí *imagí* složky, které jste právě vytvořili. (Žádné obrázky máte po ruce budou dělat, ale by se měl vejít na stránku.) Přejmenovat imagí *Photo1.jpg*, *Photo2.jpg*, *Photo3.jpg*, a *Photo4.jpg*. (Nebudete používat *Photo4.jpg* v tomto postupu, ale použijete je později v tomto článku.)
5. Ověřte, že čtyři obrázky nejsou označeny jako jen pro čtení.
6. Nahraďte existující obsah na stránce s následujícími možnostmi:

    [!code-cshtml[Main](9-working-with-images/samples/sample2.cshtml)]

    Text na stránce má rozevíracího seznamu ( `<select>` prvek), který je pojmenován `photoChoice`. Seznam obsahuje tři možnosti a `value` atribut jednotlivých možností v seznamu má název některou k imagí, které jste umístili do *imagí* složky. V podstatě seznamu umožní uživateli vybrat popisný název, jako je &quot;fotografii 1&quot;, a poté předá *.jpg* název souboru při odeslání stránky.

    V kódu, můžete získat výběru uživatele (jinými slovy, název souboru obrázku) ze seznamu načtením `Request["photoChoice"]`. Nejprve zobrazí, pokud je výběr vůbec. Pokud existuje, sestavit cestu k obrázku, který se skládá z názvu složky pro obrázky a název souboru obrázku uživatele. (Pokud jste se pokusili vytvořit cestu, ale došlo ustanovení `Request["photoChoice"]`, by dojde k chybě.) Výsledkem je relativní cesta takto:

    *Image/Photo1.jpg*

    Cesta je uložena v proměnné s názvem `imagePath` , kterou budete potřebovat později na stránce.

    V textu, k dispozici je také `<img>` element, který se používá k zobrazení obrázku, který uživatel vybere. `src` Atribut není nastavena na název souboru nebo adresu URL, jako by tomu zobrazíte statický prvek. Místo toho je nastavena na `@imagePath`, což znamená, že získá svou hodnotu z cesty, které jste nastavili v kódu.

    Při prvním spuštění stránky, ale neexistuje žádný obrázek se zobrazí, protože uživatel nemá vybrané nic. To obvykle znamená, že `src` atribut budou prázdné a image se zobrazují jako červené &quot;x&quot; (nebo cokoli, co prohlížeče vykreslí, pokud nemůže najít image). Chcete-li tomu zabránit, kam si ukládáte `<img>` prvek v `if` blok, který testuje, pokud chcete zobrazit, jestli `imagePath` proměnná má něco v ní. Pokud uživatel proveden výběr, `imagePath` obsahuje cestu. Pokud uživatel nebyl vyberte bitovou kopii nebo pokud je to první stránka se zobrazí, `<img>` prvek není ještě vykreslen.
7. Uložte soubor a spuštění stránky v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.)
8. Vyberte bitovou kopii z rozevíracího seznamu a klikněte na **Ukázkový obrázek**. Ujistěte se, že se zobrazuje různé obrázky pro různé možnosti.

<a id="Uploading_an_Image"></a>
## <a name="uploading-an-image"></a>Nahrání Image

Předchozí příklad ukázal, jak zobrazit obrázek dynamicky, ale to šlo jenom s imagí, které již byly na vašem webu. Tento postup ukazuje, jak chcete, aby uživatelé nahrát obrázek, který se následně zobrazí na stránce. V technologii ASP.NET, můžete pracovat s obrázky v reálném čase pomocí `WebImage` pomocné rutiny, která obsahuje metody, které umožňují vytváření, manipulaci s a uložte imagí. `WebImage` Pomocné rutiny podporuje všechny běžné webové image typy souborů, včetně *.jpg*, *.png*, a *.bmp*. V tomto článku budete používat *.jpg* bitové kopie, ale můžete použít některé typy obrázků.

![[Obrázek] ](9-working-with-images/_static/image2.jpg "ch9images 2.jpg")

1. Přidejte novou stránku a pojmenujte ho *UploadImage.cshtml*.
2. Nahraďte existující obsah na stránce s následujícími možnostmi: 

    [!code-cshtml[Main](9-working-with-images/samples/sample3.cshtml)]

    Má tělo text `<input type="file">` element, který umožňuje uživateli vybrat soubor k odeslání. Kliknutím na **odeslat**, odeslání souboru, vyberou se spolu s formuláři.

    K získání nahraného obrázku, použijete `WebImage` pomocné rutiny, která má celou řadu užitečných metod pro práci s obrázky. Konkrétně byste měli použít `WebImage.GetImageFromRequest` získání nahraného obrázku (pokud existuje) a uloží je do proměnné s názvem `photo`.

    Spoustu práce v tomto příkladu zahrnuje získání a nastavení názvy souborů a cestu. Tento problém je, že chcete získat název (a pouze název) bitovou kopii, která uživatel nahrál a pak vytvořte novou cestu pro budete k uložení image. Protože uživatelé potenciálně mohou nahrávat více bitových kopií, které mají stejný název, vám hodně zvláštní kód vytvořit jedinečné názvy a ujistěte se, že uživatelé nepřepisujte stávající obrázky.

    Pokud obrázek skutečně odeslal (test `if (photo != null)`), získat název bitové kopie z této image `FileName` vlastnost. Když uživatel odešle obrázek, `FileName` obsahuje původní jméno uživatele, který obsahuje cestu z počítače uživatele. To může vypadat takto:

    *C:\Users\Joe\Pictures\SamplePhoto1.jpg*

    Tyto cesty informace, ale nechcete &#8212; je vhodné skutečného názvu souboru (*SamplePhoto1.jpg*). Můžete odstranit pouze soubor pomocí cesty s použitím `Path.GetFileName` metoda takto:

    [!code-csharp[Main](9-working-with-images/samples/sample4.cs)]

    Potom vytvoříte nový jedinečný název souboru tak, že přidáte identifikátor GUID k původnímu názvu. (Další informace o identifikátory GUID najdete v tématu [o identifikátory GUID](#SB_AboutGUIDs) dále v tomto článku.) Pak vytvoříte úplnou cestu, která vám pomůže se tento obrázek uloží. Uložení cesta se skládá z nový název souboru, složky (imagí) a aktuální umístění webu.

    > [!NOTE]
    > Aby se váš kód pro uložení souborů v *imagí* složce aplikace potřebuje pro čtení i zápis oprávnění pro tuto složku. Ve svém vývojovém počítači to není obvykle problém. Při publikování webu poskytovatele hostingu webový server, může však muset explicitně nastavena patřičná oprávnění. Pokud tento kód spustit na serveru pro poskytovatele hostingu a dojde k chybám, obraťte se na poskytovatele hostingu a zjistěte, jak nastavit tato oprávnění.

    A konečně, předáte Uložit cestu k `Save` metodu `WebImage` pomocné rutiny. Toto uloží nahraný obrázek pod jejím novým názvem. Uložení metoda vypadá takto: `photo.Save(@"~\" + imagePath)`. Úplná cesta je připojen na `@"~\"`, což je aktuální umístění webu. (Informace o tom, `~` operátoru, naleznete v tématu [Úvod do ASP.NET webové programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890#ID_WorkingWithFileAndFolderPaths).)

    Stejně jako v předchozím příkladu obsahuje tělo stránky `<img>` elementu a zobrazte obrázek. Pokud `imagePath` nastavil, `<img>` prvek je vykreslovaný a jeho `src` atribut je nastaven na `imagePath` hodnotu.
3. Spuštění stránky v prohlížeči.
4. Nahrání image a ujistěte se, že se zobrazí na stránce.
5. Na vašem webu, otevřete *imagí* složky. Uvidíte, že nový soubor byl přidán jejichž název souboru by měl vypadat asi takto: 

    *45ea4527-7ddd-4965-b9ca-c6444982b342\_MyPhoto.png*

    Toto je obrázek, který jste nahráli s předponou názvu identifikátor GUID. (Vlastní soubor bude mít jiný identifikátor GUID a pravděpodobně se jmenuje něco jiného než *MyPhoto.png*.)

> [!TIP] 
> 
> <a id="SB_AboutGUIDs"></a>
> ### <a name="about-guids"></a>O identifikátory GUID
> 
> Identifikátor GUID (globálně jedinečný Identifikátor) je identifikátor, který je vykreslen obvykle ve formátu tímto způsobem: `936DA01F-9ABD-4d9d-80C7-02AF85C822A8`. Čísla a písmena (A-F) se liší pro každý identifikátor GUID, ale všechny se řídí vzor používání skupin 8-4-4-4-12 znaků. (Technicky vzato identifikátor GUID je bajtů / 12816bitové číslo.) Když budete potřebovat identifikátor GUID, můžete volat specializovaný kód, který pro vás vytvoří identifikátor GUID. Za identifikátory GUID spočívá v tom, mezi obrovském množství číslo (3.4 x 10<sup>38</sup>) a algoritmus pro generování výsledné číslo je prakticky zaručeně jednoho typu. Identifikátory GUID proto jsou dobrým způsobem, jak generovat názvy pro takové věci, pokud je třeba zaručit, že nebudete používat se stejným názvem dvakrát. Nevýhodou, samozřejmě, je, že identifikátory GUID nejsou zejména uživatelsky přívětivější, takže mají tendenci se dá použít při název se používá pouze v kódu.


<a id="Resizing_an_Image"></a>
## <a name="resizing-an-image"></a>Změna velikosti obrázku

Pokud váš web přijímá Image od uživatele, můžete chtít změnit velikost bitové kopie, před zobrazení nebo je uložíte. Můžete znovu použít `WebImage` pomocné rutiny pro tento.

Tento postup ukazuje, jak změnit velikost obrázku vytvořit miniaturu a poté uložit miniaturu a původní obrázek na webu. Zobrazit miniaturu na stránce a hypertextový odkaz můžete přesměrovat uživatele na plnou velikost obrázku.

![[Obrázek] ](9-working-with-images/_static/image3.jpg "ch9images 3.jpg")

1. Přidejte novou stránku s názvem *Thumbnail.cshtml*.
2. V *image* složce vytvořte podsložku s názvem *thumbs*.
3. Nahraďte existující obsah na stránce s následujícími možnostmi: 

    [!code-cshtml[Main](9-working-with-images/samples/sample5.cshtml)]

    Tento kód je podobný kód z předchozího příkladu. Rozdíl spočívá v tom, že tento kód uloží obrázek dvakrát, jednou za normálních okolností a jednou po vytvoření Miniatura bitovou kopii. Nejprve získejte nahraného obrázku a uložte ho do *imagí* složky. Pak vytvoříte novou cestu pro miniaturu. K samotnému vytvoření miniatury, volání `WebImage` pomocné rutiny `Resize` metodu pro vytvoření bitové kopie 60 – podle 60 pixelů. Příklad ukazuje, jak zachovat poměr stran a jak můžete zabránit bitovou kopii z se zvětší (v případě novou velikost by ve skutečnosti zvětšit na obrázku). Obrázek se změněnou velikostí se pak uloží do *thumbs* podsložky.

    Na konci značky, můžete použít stejné `<img>` element s dynamické `src` atribut, který jste viděli v předchozích příkladech má podmíněně zobrazit obrázek. V takovém případě můžete zobrazit miniatury. Můžete také použít `<a>` prvek, který chcete vytvořit hypertextový odkaz na velké objemy verzi image. Stejně jako u `src` atribut `<img>` prvku nastavíte `href` atribut `<a>` element dynamicky na cokoli, co je v `imagePath`. Pokud chcete mít jistotu, že cesta může fungovat jako adresu URL, předáte `imagePath` k `Html.AttributeEncode` metodu, která převede vyhrazené znaky v cestě na znaky, které jsou v pořádku v adrese URL.
4. Spuštění stránky v prohlížeči.
5. Odeslat fotografii a ověřte, jestli se zobrazují na miniaturu.
6. Klikněte na miniaturu a chcete obrázek reklamy zobrazit.
7. V *image* a *Image/thumbs*, mějte na paměti, že se přidaly nové soubory.

<a id="Rotating_and_Flipping"></a>
## <a name="rotating-and-flipping-an-image"></a>Otočení a překlopení obrázku

`WebImage` Pomocník také umožňuje Převrátit a otáčení obrázků. Tento postup ukazuje, jak získat obrázek ze serveru, Převrátit obrázek vzhůru nohama (svisle), uložte ho a pak zobrazí na stránce přetočený obrázek. V tomto příkladu jste právě pomocí souboru již na serveru (*Photo2.jpg*). V reálné aplikaci by pravděpodobně Převrátit obrázek jejíž název je získat dynamicky, stejně jako v předchozích příkladech.

![[Obrázek] ](9-working-with-images/_static/image4.jpg "ch9images 4.jpg")

1. Přidejte novou stránku s názvem *FlipImage.cshtml*.
2. Nahraďte existující obsah na stránce s následujícími možnostmi: 

    [!code-cshtml[Main](9-working-with-images/samples/sample6.cshtml)]

    Tento kód použije `WebImage` pomocné rutiny, chcete-li získat obrázek ze serveru. Vytvořit cestu k obrázku pomocí stejného postupu, který jste použili v předchozích příkladech pro ukládání obrázků a předat tuto cestu při vytváření image pomocí `WebImage`:

    [!code-javascript[Main](9-working-with-images/samples/sample7.js)]

    Pokud je nalezen bitovou kopii, vytvořit novou cestu a název souboru, stejně jako v předchozích příkladech. Převrátit obrázek, zavolejte `FlipVertical` metoda a pak uložte bitovou kopii znovu.

    Image se znovu zobrazí na stránce pomocí `<img>` křížkem `src` atribut nastaven na `imagePath`.
3. Spuštění stránky v prohlížeči. Obrázek pro *Photo2.jpg* vzhůru nohama se zobrazí.
4. Aktualizujte stránku, nebo požádat o stránku znovu, abyste viděli, že bitová kopie je přetočený pravé straně se znovu.

Otočí obrázek, použijte stejný kód, s výjimkou, že namísto volání metody `FlipVertical` nebo `FlipHorizontal`, volání `RotateLeft` nebo `RotateRight`.

<a id="Adding_a_Watermark"></a>
## <a name="adding-a-watermark-to-an-image"></a>Přidání vodoznaku do Image

Když přidáte obrázků na váš web, můžete chtít přidat vodoznak do bitové kopie, předtím, než ji uložit nebo zobrazit na stránce. Lidé často používají vodoznaky, chcete-li přidat informace o autorských právech pro bitovou kopii nebo inzerovat název své firmy.

![[Obrázek] ](9-working-with-images/_static/image5.jpg "ch9images 5.jpg")

1. Přidejte novou stránku s názvem *Watermark.cshtml*.
2. Nahraďte existující obsah na stránce s následujícími možnostmi: 

    [!code-cshtml[Main](9-working-with-images/samples/sample8.cshtml)]

    Tento kód je stejně jako kód v *FlipImage.cshtml* stránky z předchozí (i když se v tuto chvíli se používá *Photo3.jpg* souboru). Chcete-li přidat vodoznak, zavolejte `WebImage` pomocné rutiny `AddTextWatermark` metoda před uložením na obrázku. Při volání funkce `AddTextWatermark`, předáte text &quot;Můj vodoznak&quot;, nastavit barvu písma na žlutou a nastavte na Arial rodiny písem. (I když ho tady není ukázaný, `WebImage` Pomocník také umožňuje určit krytí a rodinu písem a velikost písma a pozice vodoznakového textu.) Při ukládání obrázku nesmí být jen pro čtení.

    Jak už víte, před, obrázek se zobrazí na stránce pomocí `<img>` element s atributem src nastaven na hodnotu `@imagePath`.
3. Spuštění stránky v prohlížeči. Všimněte si, že text "Moje vodoznak" v pravém dolním rohu obrázku.

<a id="Using_an_Image_as_a_Watermark"></a>
## <a name="using-an-image-as-a-watermark"></a>Pomocí Image jako vodoznak

Namísto použití text pro mez, můžete použít jiný obrázek. Uživatelé někdy použít jako vodoznak Image, jako je logo společnosti, nebo použít vodoznak namísto textu pro informace o autorských právech.

![[Obrázek] ](9-working-with-images/_static/image6.jpg "ch9images 6.jpg")

1. Přidejte novou stránku s názvem *ImageWatermark.cshtml*.
2. Přidání obrázku, který má *image* složky, můžete použít jako logo a přejmenovat *MyCompanyLogo.jpg*. Tato bitová kopie by měl být bitovou kopii, které můžete jasně vidíte, když je nastaven na 80 pixelů na šířku a 20 pixelů na výšku.
3. Nahraďte existující obsah na stránce s následujícími možnostmi: 

    [!code-cshtml[Main](9-working-with-images/samples/sample9.cshtml)]

    Toto je další variantou na kód v předchozích příkladech. V tomto případě voláte `AddImageWatermark` přidání vodoznaku do target image (*Photo3.jpg*) před uložením na obrázku. Při volání `AddImageWatermark`, nastavte jeho šířku na 80 pixelů a výšku na 20 pixelů. *MyCompanyLogo.jpg* image je vodorovného zarovnání v centru a v dolní části obrázku cílové svisle zarovnané. Neprůhlednost nastavená na 100 % a je odsazení nastaveno na 10 pixelů. Pokud vodoznaku je větší než cílové image, nic nestane. Pokud je větší než cílové image vodoznaku a nastavte odsazení pro image mez nula, je ignorován vodoznak.

    Jako před tím zobrazíte pomocí bitové kopie `<img>` elementu a dynamické `src` atribut.
4. Spuštění stránky v prohlížeči. Všimněte si, že vodoznaku se zobrazí v dolní části hlavní image.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


[Práce se soubory na webu rozhraní ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202896)

[Úvod do programování pomocí syntaxe Razor rozhraní ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkID=251587)

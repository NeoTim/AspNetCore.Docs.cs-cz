---
uid: web-pages/overview/ui-layouts-and-themes/displaying-maps-in-an-aspnet-web-pages-site
title: Zobrazení mapy v ASP.NET Web Pages lokality (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek vysvětluje, jak zobrazit interaktivní mapy na stránkách na webu rozhraní ASP.NET Web Pages (Razor) podle mapování služby poskytované Bing, Google, Ma...
ms.author: riande
ms.date: 02/20/2014
ms.assetid: b5c268dd-ca6a-4562-b94c-a220fcf01f58
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/displaying-maps-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: cde27c54b11ee91b193dffd61e3a354c6cf2449a
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020985"
---
<a name="displaying-maps-in-an-aspnet-web-pages-razor-site"></a>Zobrazení map na webu rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak se zobrazí na stránkách na webu rozhraní ASP.NET Web Pages (Razor) podle mapování služby poskytované Bing, Google, Yahoo a MapQuest interaktivní mapy.
> 
> Co se dozvíte:
> 
> - Postup generování mapování na základě adresy.
> - Postup generování mapy založené na zeměpisné šířky a délky.
> - Jak se zaregistrovat vývojářský účet mapy Bing a získat klíč pro použití službou mapy Bing.
> 
> Toto je funkce technologie ASP.NET zavedené v následujícím článku:
> 
> - `Maps` Pomocné rutiny.
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


Na webových stránkách, můžete zobrazit mapování na stránce pomocí `Maps` pomocné rutiny. Můžete generovat mapy založené na adresu nebo na sadu souřadnice zeměpisné šířky a délky. `Maps` Umožňuje volání do mapy Oblíbené moduly včetně Bing, Google, Yahoo a MapQuest třídy.

Kroky pro přidání mapování do stránky jsou stejné bez ohledu na to, které moduly mapy volání. Stačí přidat odkaz na soubor jazyka JavaScript, která je k dispozici metody k zobrazení mapy, a pak zavolat metody `Maps` pomocné rutiny.

Zvolte service pro mapy, podle níž `Maps` pomocnou metodu použijete. Můžete použít některý z těchto:

- `Maps.GetBingHtml`
- `Maps.GetGoogleHtml`
- `Maps.GetYahooHtml`
- `Maps.GetMapQuestHtml`

## <a name="installing-the-pieces-you-need"></a>Instalace částí, které potřebujete

K zobrazení mapy, je třeba tyto údaje:

- `Maps` Pomocné rutiny. Ve verzi 2 knihovnu ASP.NET Web Helpers je tohoto pomocníka. Pokud jste ještě nepřidali knihovny, můžete ho nainstalovat na vašem webu jako balíček NuGet. Podrobnosti najdete v tématu [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372). (V galerii, vyhledejte `microsoft-web-helpers` balíčku.)
- Knihovna jQuery. Některé šablony webu služby WebMatrix již zahrnují knihovny jQuery v jejich *skript* složek. Pokud tyto knihovny, si můžete stáhnout nejnovější verze knihovny jQuery přímo [jQuery.org](http://jQuery.org) lokality. Nebo můžete vytvořit nový web pomocí šablony (třeba **Starter Site** šablony) a zkopírujte soubory jQuery z této lokality k aktuální lokalitě.

Nakonec pokud chcete použít služby mapy Bing, musíte nejdřív vytvořit účet (zdarma) a získat klíč. Pokud chcete získat klíče, postupujte takto:

1. Vytvoření účtu na [vývojářský účet pro mapy Bing](https://www.microsoft.com/maps/developers/web.aspx). Musíte mít účet Microsoft (Windows Live ID) i.

    Můžete určit, že chcete použít klíč pro **vyhodnocování a testování**. Pokud testujete funkci mapování na vlastním počítači pomocí služby WebMatrix a služby IIS Express, přejděte **lokality** pracovního prostoru a poznamenejte si adresu URL vašeho webu (například `http://localhost:50408`, i když vaše číslo portu bude pravděpodobně lišit). To může být použito *localhost* adresu jako web při registraci.
2. Poté, co jste se zaregistrovali účet, přejděte na účet Center pro mapy Bing a klikněte na tlačítko **vytvořit nebo zobrazení klíče**:

    ![mapování-2](displaying-maps-in-an-aspnet-web-pages-site/_static/image1.png)
3. Záznam klíč, který vytvoří Bingu.

## <a name="creating-a-map-based-on-an-address-using-google"></a>Vytváří se mapování na základě adresy (pomocí Google)

Následující příklad ukazuje, jak vytvořit stránku, která vykreslí mapu, na základě adresy. Tento příklad ukazuje, jak používat Google Maps.

1. Vytvořte soubor s názvem *MapAddress.cshtml* v kořenové složce webu. Tato stránka bude generovat mapy založené na adresu, která mu předáte.
2. Zkopírujte následující kód do souboru, přepisování stávajícího obsahu.

    [!code-cshtml[Main](displaying-maps-in-an-aspnet-web-pages-site/samples/sample1.cshtml)]

    Všimněte si, že následující funkce stránky:

    - `<script>` Prvek `<head>` elementu. V tomto příkladu `<script>` elementu odkazy *jquery 1.6.4.min.js* soubor, který se nachází minifikovaný (komprimované) verzi knihovny jQuery verze 1.6.4. Všimněte si, že odkaz předpokládá, že *js* soubor se *skripty* složku vašeho webu. 

        > [!NOTE]
        > Pokud používáte jinou verzi knihovny jQuery, ujistěte se, že jste správně přejdete tuto verzi.
    - Volání `@Maps.GetGoogleHtml` těla stránky. Pokud chcete namapovat adresu, musíte předat řetězec adresy. Metody pro ostatní moduly mapy fungují podobným způsobem (`@Maps.GetYahooHtml`, `@Maps.GetMapQuestHtml`).
3. Spustit na stránku a zadejte adresu. Na stránce zobrazí mapu založeny na mapách Googlu, která zobrazuje umístění, které jste zadali.

     ![mapování 1](displaying-maps-in-an-aspnet-web-pages-site/_static/image2.png)

## <a name="creating-a-map-based-on-latitude-and-longitude-coordinates-using-bing"></a>Vytvoření mapy založené na zeměpisné šířce a délce koordinuje (pomocí Bing)

Tento příklad ukazuje způsob vytvoření mapy podle souřadnic. Tento příklad ukazuje, jak pomocí služby mapy Bing a jak zahrnovat klíč Bingu. (Můžete vytvořit mapy podle souřadnic také pomocí jiné mapování modulů bez použití klíče Bingu.)

1. Vytvořte soubor s názvem *MapCoordinates.cshtml* v kořenové složce serveru a nahradit existující obsah s následujícím kódem a značky:

    [!code-cshtml[Main](displaying-maps-in-an-aspnet-web-pages-site/samples/sample2.cshtml)]
2. Nahraďte `your-key-here` s klíčem služby mapy Bing, který jste vygenerovali dříve.
3. Spustit *MapCoordinates.cshtml* stránce zadejte zeměpisné šířky a délky a potom klikněte na tlačítko **mapy ho!** tlačítko. (Pokud si nejste jisti všechny souřadnice, zkuste následující postup. Toto je umístění v campusech Microsoft Redmond.)

   - Zeměpisná šířka: 47.6781005859375
   - Zeměpisná délka:-122.158317565918

     Na stránce se zobrazí pomocí souřadnic, které jste zadali.

     ![mapování 3](displaying-maps-in-an-aspnet-web-pages-site/_static/image3.png)

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


[Reference k rozhraní API Microsoft.Maps](https://msdn.microsoft.com/library/gg427611.aspx)

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
<a name="displaying-maps-in-an-aspnet-web-pages-razor-site"></a><span data-ttu-id="a8c76-103">Zobrazení map na webu rozhraní ASP.NET Web Pages (Razor)</span><span class="sxs-lookup"><span data-stu-id="a8c76-103">Displaying Maps in an ASP.NET Web Pages (Razor) Site</span></span>
====================
<span data-ttu-id="a8c76-104">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="a8c76-104">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="a8c76-105">Tento článek vysvětluje, jak se zobrazí na stránkách na webu rozhraní ASP.NET Web Pages (Razor) podle mapování služby poskytované Bing, Google, Yahoo a MapQuest interaktivní mapy.</span><span class="sxs-lookup"><span data-stu-id="a8c76-105">This article explains how to display interactive maps on pages in an ASP.NET Web Pages (Razor) website based on mapping services provided by Bing, Google, MapQuest, and Yahoo.</span></span>
> 
> <span data-ttu-id="a8c76-106">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="a8c76-106">What you'll learn:</span></span>
> 
> - <span data-ttu-id="a8c76-107">Postup generování mapování na základě adresy.</span><span class="sxs-lookup"><span data-stu-id="a8c76-107">How to generate a map based on an address.</span></span>
> - <span data-ttu-id="a8c76-108">Postup generování mapy založené na zeměpisné šířky a délky.</span><span class="sxs-lookup"><span data-stu-id="a8c76-108">How to generate a map based on latitude and longitude coordinates.</span></span>
> - <span data-ttu-id="a8c76-109">Jak se zaregistrovat vývojářský účet mapy Bing a získat klíč pro použití službou mapy Bing.</span><span class="sxs-lookup"><span data-stu-id="a8c76-109">How to register a Bing Maps Developer Account and get a key to use with Bing Maps.</span></span>
> 
> <span data-ttu-id="a8c76-110">Toto je funkce technologie ASP.NET zavedené v následujícím článku:</span><span class="sxs-lookup"><span data-stu-id="a8c76-110">This is the ASP.NET feature introduced in the article:</span></span>
> 
> - <span data-ttu-id="a8c76-111">`Maps` Pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="a8c76-111">The `Maps` helper.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="a8c76-112">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="a8c76-112">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="a8c76-113">Rozhraní ASP.NET Web Pages (Razor) 2</span><span class="sxs-lookup"><span data-stu-id="a8c76-113">ASP.NET Web Pages (Razor) 2</span></span>
> - <span data-ttu-id="a8c76-114">Služba WebMatrix 2</span><span class="sxs-lookup"><span data-stu-id="a8c76-114">WebMatrix 2</span></span>
>   
> 
> <span data-ttu-id="a8c76-115">V tomto kurzu funguje taky pomocí služby WebMatrix 3.</span><span class="sxs-lookup"><span data-stu-id="a8c76-115">This tutorial also works with WebMatrix 3.</span></span>


<span data-ttu-id="a8c76-116">Na webových stránkách, můžete zobrazit mapování na stránce pomocí `Maps` pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="a8c76-116">In Web Pages, you can display maps on a page by using `Maps` helper.</span></span> <span data-ttu-id="a8c76-117">Můžete generovat mapy založené na adresu nebo na sadu souřadnice zeměpisné šířky a délky.</span><span class="sxs-lookup"><span data-stu-id="a8c76-117">You can generate maps based either on an address or on a set of longitude and latitude coordinates.</span></span> <span data-ttu-id="a8c76-118">`Maps` Umožňuje volání do mapy Oblíbené moduly včetně Bing, Google, Yahoo a MapQuest třídy.</span><span class="sxs-lookup"><span data-stu-id="a8c76-118">The `Maps` class lets you call into popular map engines including Bing, Google, MapQuest, and Yahoo.</span></span>

<span data-ttu-id="a8c76-119">Kroky pro přidání mapování do stránky jsou stejné bez ohledu na to, které moduly mapy volání.</span><span class="sxs-lookup"><span data-stu-id="a8c76-119">The steps for adding mapping to a page are the same regardless of which of the map engines you call.</span></span> <span data-ttu-id="a8c76-120">Stačí přidat odkaz na soubor jazyka JavaScript, která je k dispozici metody k zobrazení mapy, a pak zavolat metody `Maps` pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="a8c76-120">You just add a JavaScript file reference that makes available methods to display the map, and then you call methods of the `Maps` helper.</span></span>

<span data-ttu-id="a8c76-121">Zvolte service pro mapy, podle níž `Maps` pomocnou metodu použijete.</span><span class="sxs-lookup"><span data-stu-id="a8c76-121">You choose a map service based on which `Maps` helper method you use.</span></span> <span data-ttu-id="a8c76-122">Můžete použít některý z těchto:</span><span class="sxs-lookup"><span data-stu-id="a8c76-122">You can use any of these:</span></span>

- `Maps.GetBingHtml`
- `Maps.GetGoogleHtml`
- `Maps.GetYahooHtml`
- `Maps.GetMapQuestHtml`

## <a name="installing-the-pieces-you-need"></a><span data-ttu-id="a8c76-123">Instalace částí, které potřebujete</span><span class="sxs-lookup"><span data-stu-id="a8c76-123">Installing the Pieces You Need</span></span>

<span data-ttu-id="a8c76-124">K zobrazení mapy, je třeba tyto údaje:</span><span class="sxs-lookup"><span data-stu-id="a8c76-124">To display maps, you need these pieces:</span></span>

- <span data-ttu-id="a8c76-125">`Maps` Pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="a8c76-125">The `Maps` helper.</span></span> <span data-ttu-id="a8c76-126">Ve verzi 2 knihovnu ASP.NET Web Helpers je tohoto pomocníka.</span><span class="sxs-lookup"><span data-stu-id="a8c76-126">This helper is in version 2 of the ASP.NET Web Helpers Library.</span></span> <span data-ttu-id="a8c76-127">Pokud jste ještě nepřidali knihovny, můžete ho nainstalovat na vašem webu jako balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="a8c76-127">If you haven't already added the library, you can install it in your site as a NuGet package.</span></span> <span data-ttu-id="a8c76-128">Podrobnosti najdete v tématu [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372).</span><span class="sxs-lookup"><span data-stu-id="a8c76-128">For details, see [Installing Helpers in an ASP.NET Web Pages Site](https://go.microsoft.com/fwlink/?LinkId=252372).</span></span> <span data-ttu-id="a8c76-129">(V galerii, vyhledejte `microsoft-web-helpers` balíčku.)</span><span class="sxs-lookup"><span data-stu-id="a8c76-129">(In the Gallery, search for the `microsoft-web-helpers` package.)</span></span>
- <span data-ttu-id="a8c76-130">Knihovna jQuery.</span><span class="sxs-lookup"><span data-stu-id="a8c76-130">The jQuery library.</span></span> <span data-ttu-id="a8c76-131">Některé šablony webu služby WebMatrix již zahrnují knihovny jQuery v jejich *skript* složek.</span><span class="sxs-lookup"><span data-stu-id="a8c76-131">Several of the WebMatrix site templates already include jQuery libraries in their *Script* folders.</span></span> <span data-ttu-id="a8c76-132">Pokud tyto knihovny, si můžete stáhnout nejnovější verze knihovny jQuery přímo [jQuery.org](http://jQuery.org) lokality.</span><span class="sxs-lookup"><span data-stu-id="a8c76-132">If you do not have these libraries, you can download the latest jQuery library directly from the [jQuery.org](http://jQuery.org) site.</span></span> <span data-ttu-id="a8c76-133">Nebo můžete vytvořit nový web pomocí šablony (třeba **Starter Site** šablony) a zkopírujte soubory jQuery z této lokality k aktuální lokalitě.</span><span class="sxs-lookup"><span data-stu-id="a8c76-133">Or you can create a new site using a template (for example, the **Starter Site** template) and then copy the jQuery files from that site to your current site.</span></span>

<span data-ttu-id="a8c76-134">Nakonec pokud chcete použít služby mapy Bing, musíte nejdřív vytvořit účet (zdarma) a získat klíč.</span><span class="sxs-lookup"><span data-stu-id="a8c76-134">Finally, if you want to use Bing maps, you must first create a (free) account and get a key.</span></span> <span data-ttu-id="a8c76-135">Pokud chcete získat klíče, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="a8c76-135">To get a key, follow these steps:</span></span>

1. <span data-ttu-id="a8c76-136">Vytvoření účtu na [vývojářský účet pro mapy Bing](https://www.microsoft.com/maps/developers/web.aspx).</span><span class="sxs-lookup"><span data-stu-id="a8c76-136">Create an account on the [Bing Maps Developer Account](https://www.microsoft.com/maps/developers/web.aspx).</span></span> <span data-ttu-id="a8c76-137">Musíte mít účet Microsoft (Windows Live ID) i.</span><span class="sxs-lookup"><span data-stu-id="a8c76-137">You must have a Microsoft account (Windows Live ID) as well.</span></span>

    <span data-ttu-id="a8c76-138">Můžete určit, že chcete použít klíč pro **vyhodnocování a testování**.</span><span class="sxs-lookup"><span data-stu-id="a8c76-138">You can specify that you want to use the key for **Evaluation/Test**.</span></span> <span data-ttu-id="a8c76-139">Pokud testujete funkci mapování na vlastním počítači pomocí služby WebMatrix a služby IIS Express, přejděte **lokality** pracovního prostoru a poznamenejte si adresu URL vašeho webu (například `http://localhost:50408`, i když vaše číslo portu bude pravděpodobně lišit).</span><span class="sxs-lookup"><span data-stu-id="a8c76-139">If you are testing the mapping function on your own computer using WebMatrix and IIS Express, go the **Site** workspace and note the URL of your site (for example, `http://localhost:50408`, although your port number will probably be different).</span></span> <span data-ttu-id="a8c76-140">To může být použito *localhost* adresu jako web při registraci.</span><span class="sxs-lookup"><span data-stu-id="a8c76-140">You can use this *localhost* address as the site when you register.</span></span>
2. <span data-ttu-id="a8c76-141">Poté, co jste se zaregistrovali účet, přejděte na účet Center pro mapy Bing a klikněte na tlačítko **vytvořit nebo zobrazení klíče**:</span><span class="sxs-lookup"><span data-stu-id="a8c76-141">After you have registered for an account, go to the Bing Maps Account Center and click **Create or view keys**:</span></span>

    ![mapování-2](displaying-maps-in-an-aspnet-web-pages-site/_static/image1.png)
3. <span data-ttu-id="a8c76-143">Záznam klíč, který vytvoří Bingu.</span><span class="sxs-lookup"><span data-stu-id="a8c76-143">Record the key that Bing creates.</span></span>

## <a name="creating-a-map-based-on-an-address-using-google"></a><span data-ttu-id="a8c76-144">Vytváří se mapování na základě adresy (pomocí Google)</span><span class="sxs-lookup"><span data-stu-id="a8c76-144">Creating a Map Based on an Address (Using Google)</span></span>

<span data-ttu-id="a8c76-145">Následující příklad ukazuje, jak vytvořit stránku, která vykreslí mapu, na základě adresy.</span><span class="sxs-lookup"><span data-stu-id="a8c76-145">The following example shows how to create a page that renders a map based on an address.</span></span> <span data-ttu-id="a8c76-146">Tento příklad ukazuje, jak používat Google Maps.</span><span class="sxs-lookup"><span data-stu-id="a8c76-146">This example shows how to use Google Maps.</span></span>

1. <span data-ttu-id="a8c76-147">Vytvořte soubor s názvem *MapAddress.cshtml* v kořenové složce webu.</span><span class="sxs-lookup"><span data-stu-id="a8c76-147">Create a file named *MapAddress.cshtml* in the root of the site.</span></span> <span data-ttu-id="a8c76-148">Tato stránka bude generovat mapy založené na adresu, která mu předáte.</span><span class="sxs-lookup"><span data-stu-id="a8c76-148">This page will generate a map based on an address that you pass to it.</span></span>
2. <span data-ttu-id="a8c76-149">Zkopírujte následující kód do souboru, přepisování stávajícího obsahu.</span><span class="sxs-lookup"><span data-stu-id="a8c76-149">Copy the following code into the file, overwriting the existing content.</span></span>

    [!code-cshtml[Main](displaying-maps-in-an-aspnet-web-pages-site/samples/sample1.cshtml)]

    <span data-ttu-id="a8c76-150">Všimněte si, že následující funkce stránky:</span><span class="sxs-lookup"><span data-stu-id="a8c76-150">Notice the following features of the page:</span></span>

    - <span data-ttu-id="a8c76-151">`<script>` Prvek `<head>` elementu.</span><span class="sxs-lookup"><span data-stu-id="a8c76-151">The `<script>` element in the `<head>` element.</span></span> <span data-ttu-id="a8c76-152">V tomto příkladu `<script>` elementu odkazy *jquery 1.6.4.min.js* soubor, který se nachází minifikovaný (komprimované) verzi knihovny jQuery verze 1.6.4.</span><span class="sxs-lookup"><span data-stu-id="a8c76-152">In the example, the `<script>` element references the *jquery-1.6.4.min.js* file, which is a minified (compressed) version of the jQuery library, version 1.6.4.</span></span> <span data-ttu-id="a8c76-153">Všimněte si, že odkaz předpokládá, že *js* soubor se *skripty* složku vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="a8c76-153">Note that the reference assumes that the *.js* file is in the *Scripts* folder of your site.</span></span> 

        > [!NOTE]
        > <span data-ttu-id="a8c76-154">Pokud používáte jinou verzi knihovny jQuery, ujistěte se, že jste správně přejdete tuto verzi.</span><span class="sxs-lookup"><span data-stu-id="a8c76-154">If you're using a different version of the jQuery library, just make sure that you're pointing to that version correctly.</span></span>
    - <span data-ttu-id="a8c76-155">Volání `@Maps.GetGoogleHtml` těla stránky.</span><span class="sxs-lookup"><span data-stu-id="a8c76-155">The call to the `@Maps.GetGoogleHtml` in the body of the page.</span></span> <span data-ttu-id="a8c76-156">Pokud chcete namapovat adresu, musíte předat řetězec adresy.</span><span class="sxs-lookup"><span data-stu-id="a8c76-156">To map an address, you must pass an address string.</span></span> <span data-ttu-id="a8c76-157">Metody pro ostatní moduly mapy fungují podobným způsobem (`@Maps.GetYahooHtml`, `@Maps.GetMapQuestHtml`).</span><span class="sxs-lookup"><span data-stu-id="a8c76-157">The methods for the other map engines work in a similar way (`@Maps.GetYahooHtml`, `@Maps.GetMapQuestHtml`).</span></span>
3. <span data-ttu-id="a8c76-158">Spustit na stránku a zadejte adresu.</span><span class="sxs-lookup"><span data-stu-id="a8c76-158">Run the page and enter an address.</span></span> <span data-ttu-id="a8c76-159">Na stránce zobrazí mapu založeny na mapách Googlu, která zobrazuje umístění, které jste zadali.</span><span class="sxs-lookup"><span data-stu-id="a8c76-159">The page displays a map, based on Google Maps, that shows the location that you specified.</span></span>

     ![mapování 1](displaying-maps-in-an-aspnet-web-pages-site/_static/image2.png)

## <a name="creating-a-map-based-on-latitude-and-longitude-coordinates-using-bing"></a><span data-ttu-id="a8c76-161">Vytvoření mapy založené na zeměpisné šířce a délce koordinuje (pomocí Bing)</span><span class="sxs-lookup"><span data-stu-id="a8c76-161">Creating a Map Based on Latitude and Longitude Coordinates (Using Bing)</span></span>

<span data-ttu-id="a8c76-162">Tento příklad ukazuje způsob vytvoření mapy podle souřadnic.</span><span class="sxs-lookup"><span data-stu-id="a8c76-162">This example shows how to create a map based on coordinates.</span></span> <span data-ttu-id="a8c76-163">Tento příklad ukazuje, jak pomocí služby mapy Bing a jak zahrnovat klíč Bingu.</span><span class="sxs-lookup"><span data-stu-id="a8c76-163">This example shows how to use Bing maps and how to include your Bing key.</span></span> <span data-ttu-id="a8c76-164">(Můžete vytvořit mapy podle souřadnic také pomocí jiné mapování modulů bez použití klíče Bingu.)</span><span class="sxs-lookup"><span data-stu-id="a8c76-164">(You can create a map based on coordinates using the other map engines also, without using a Bing key.)</span></span>

1. <span data-ttu-id="a8c76-165">Vytvořte soubor s názvem *MapCoordinates.cshtml* v kořenové složce serveru a nahradit existující obsah s následujícím kódem a značky:</span><span class="sxs-lookup"><span data-stu-id="a8c76-165">Create a file named *MapCoordinates.cshtml* in the root of the site and replace the existing content with the following code and markup:</span></span>

    [!code-cshtml[Main](displaying-maps-in-an-aspnet-web-pages-site/samples/sample2.cshtml)]
2. <span data-ttu-id="a8c76-166">Nahraďte `your-key-here` s klíčem služby mapy Bing, který jste vygenerovali dříve.</span><span class="sxs-lookup"><span data-stu-id="a8c76-166">Replace `your-key-here` with the Bing Maps key that you generated earlier.</span></span>
3. <span data-ttu-id="a8c76-167">Spustit *MapCoordinates.cshtml* stránce zadejte zeměpisné šířky a délky a potom klikněte na tlačítko **mapy ho!**</span><span class="sxs-lookup"><span data-stu-id="a8c76-167">Run the *MapCoordinates.cshtml* page, enter latitude and longitude coordinates, and then click the **Map It!**</span></span> <span data-ttu-id="a8c76-168">tlačítko.</span><span class="sxs-lookup"><span data-stu-id="a8c76-168">button.</span></span> <span data-ttu-id="a8c76-169">(Pokud si nejste jisti všechny souřadnice, zkuste následující postup.</span><span class="sxs-lookup"><span data-stu-id="a8c76-169">(If you don't know any coordinates, try the following.</span></span> <span data-ttu-id="a8c76-170">Toto je umístění v campusech Microsoft Redmond.)</span><span class="sxs-lookup"><span data-stu-id="a8c76-170">This is a location on the Microsoft Redmond campus.)</span></span>

   - <span data-ttu-id="a8c76-171">Zeměpisná šířka: 47.6781005859375</span><span class="sxs-lookup"><span data-stu-id="a8c76-171">Latitude: 47.6781005859375</span></span>
   - <span data-ttu-id="a8c76-172">Zeměpisná délka:-122.158317565918</span><span class="sxs-lookup"><span data-stu-id="a8c76-172">Longitude: -122.158317565918</span></span>

     <span data-ttu-id="a8c76-173">Na stránce se zobrazí pomocí souřadnic, které jste zadali.</span><span class="sxs-lookup"><span data-stu-id="a8c76-173">The page is displayed using the coordinates that you specified.</span></span>

     ![mapování 3](displaying-maps-in-an-aspnet-web-pages-site/_static/image3.png)

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a><span data-ttu-id="a8c76-175">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="a8c76-175">Additional Resources</span></span>


[<span data-ttu-id="a8c76-176">Reference k rozhraní API Microsoft.Maps</span><span class="sxs-lookup"><span data-stu-id="a8c76-176">Microsoft.Maps API Reference</span></span>](https://msdn.microsoft.com/library/gg427611.aspx)

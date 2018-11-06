---
uid: web-pages/overview/getting-started/13-adding-social-networking-to-your-web-site
title: Přidání sociálních sítí na rozhraní ASP.NET Web Pages servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola vysvětluje, jak integrovat svůj web pomocí služby pro sociální sítě. V této kapitole se dozvíte, jak umožnit lidem/odkazu na záložku webu...
ms.author: riande
ms.date: 02/21/2014
ms.assetid: 03c342f9-b35c-4d7c-b9ed-cd9aaaffedb6
msc.legacyurl: /web-pages/overview/getting-started/13-adding-social-networking-to-your-web-site
msc.type: authoredcontent
ms.openlocfilehash: d2b970e7b80e4129d0a912f648f9c4a54df531b2
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021154"
---
<a name="adding-social-networking-to-aspnet-web-pages-razor-sites"></a><span data-ttu-id="1fc6b-104">Přidání sociálních sítí pro ASP.NET Web Pages servery (Razor)</span><span class="sxs-lookup"><span data-stu-id="1fc6b-104">Adding Social Networking to ASP.NET Web Pages (Razor) Sites</span></span>
====================
<span data-ttu-id="1fc6b-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="1fc6b-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="1fc6b-106">Tento článek vysvětluje postup přidání sociálních sítí odkazy pro Facebook, Twitter, Reddit a Digg na stránky na webu rozhraní ASP.NET Web Pages (Razor) a jak zahrnout Twitterové kanály, karty hráče Xbox a Gravatar obrázků.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-106">This article explains how to add social networking links for Facebook, Twitter, Reddit, and Digg to pages in an ASP.NET Web Pages (Razor) website, and how to include Twitter feeds, Xbox gamer cards, and Gravatar images.</span></span>
> 
> <span data-ttu-id="1fc6b-107">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="1fc6b-107">What you'll learn:</span></span>
> 
> - <span data-ttu-id="1fc6b-108">Jak umožnit lidem záložku nebo propojení vaší lokality.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-108">How to let people bookmark/link your site.</span></span>
> - <span data-ttu-id="1fc6b-109">Jak přidat informační kanál sítě Twitter.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-109">How to add a Twitter feed.</span></span>
> - <span data-ttu-id="1fc6b-110">Postup přidání Facebook **jako** tlačítko na stránky.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-110">How to add a Facebook **Like** button to pages.</span></span>
> - <span data-ttu-id="1fc6b-111">Jak lze vykreslit Gravatar.com imagí.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-111">How to render Gravatar.com images.</span></span>
> - <span data-ttu-id="1fc6b-112">Jak zobrazit pomocí karty hráče Xbox ve vaší lokalitě.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-112">How to display an Xbox gamer card on your site.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="1fc6b-113">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="1fc6b-113">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="1fc6b-114">Rozhraní ASP.NET Web Pages (Razor) 2</span><span class="sxs-lookup"><span data-stu-id="1fc6b-114">ASP.NET Web Pages (Razor) 2</span></span>
> - <span data-ttu-id="1fc6b-115">Technologie ASP.NET webové pomocné knihovny (balíček NuGet)</span><span class="sxs-lookup"><span data-stu-id="1fc6b-115">ASP.NET Web Helper Library (NuGet package)</span></span>
>   
> 
> <span data-ttu-id="1fc6b-116">V tomto kurzu funguje taky s 3 webových stránek ASP.NET, s výjimkou částí, které používají knihovnu ASP.NET Web pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-116">This tutorial also works with ASP.NET Web Pages 3, except for parts that use the ASP.NET Web Helper Library.</span></span>


<a id="Linking_Your_Website"></a>
## <a name="linking-your-website-on-social-networking-sites"></a><span data-ttu-id="1fc6b-117">Propojení svůj web na weby sociálních sítí</span><span class="sxs-lookup"><span data-stu-id="1fc6b-117">Linking Your Website on Social Networking Sites</span></span>

<span data-ttu-id="1fc6b-118">Pokud se lidé, jako je něco na webu, často chtějí sdílet s přáteli.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-118">If people like something on your site, they often want to share it with friends.</span></span> <span data-ttu-id="1fc6b-119">Můžete provést to snadno zobrazením piktogramů (ikon), které lze kliknout na sdílet stránku na Digg, Reddit, Facebook, Twitter nebo podobné servery.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-119">You can make this easy by displaying glyphs (icons) that people can click to share a page on Digg, Reddit, Facebook, Twitter, or similar sites.</span></span>

<span data-ttu-id="1fc6b-120">Chcete-li zobrazit tyto piktogramy, přidejte `LinkSharecode` pomocné rutiny na stránku.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-120">To display these glyphs, add the `LinkSharecode` helper to a page.</span></span> <span data-ttu-id="1fc6b-121">Uživatelé, kteří navštíví stránku můžete kliknout na jednotlivé glyfů.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-121">People who visit your page can click an individual glyph.</span></span> <span data-ttu-id="1fc6b-122">Pokud mají účet s sociální síťové lokality, se pak odeslat odkaz na stránku dané lokality.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-122">If they have an account with that social networking site, they can then post a link to your page on that site.</span></span>

![Obrázek 1](13-adding-social-networking-to-your-web-site/_static/image1.jpg)

1. <span data-ttu-id="1fc6b-124">Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud už jste nepřidali ní – vytvoření stránky s názvem *ListLinkShare.cshtml* a přidat Následující kód:</span><span class="sxs-lookup"><span data-stu-id="1fc6b-124">Add the ASP.NET Web Helpers Library to your website as described in [Installing Helpers in an ASP.NET Web Pages Site](https://go.microsoft.com/fwlink/?LinkId=252372), if you haven't already added it.- Create a page named *ListLinkShare.cshtml* and add the following markup:</span></span>

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample1.cshtml)]

    <span data-ttu-id="1fc6b-125">V tomto příkladu když `LinkShare` spuštění pomocné rutiny, název stránky je předán jako parametr, který pak předá název stránky na sociálních sítí Web.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-125">In this example, when the `LinkShare` helper runs, the page title is passed as a parameter, which in turn passes the page title to the social networking site.</span></span> <span data-ttu-id="1fc6b-126">Ale mohli předat v libovolný řetězec, který chcete.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-126">However, you could pass in any string you want.</span></span> <span data-ttu-id="1fc6b-127">Tento příklad také určuje, jaké weby sociálních sítí mají být zahrnuty do seznamu.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-127">This example also specifies which social networking sites to include in the list.</span></span> <span data-ttu-id="1fc6b-128">Můžete zadat sociálních sítí, které se vztahují na váš web.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-128">You can specify the social networking sites that are relevant to your site.</span></span>
2. <span data-ttu-id="1fc6b-129">Spustit *ListLinkShare.cshtml* stránku v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-129">Run the *ListLinkShare.cshtml* page in a browser.</span></span> <span data-ttu-id="1fc6b-130">(Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.)</span><span class="sxs-lookup"><span data-stu-id="1fc6b-130">(Make sure the page is selected in the **Files** workspace before you run it.)</span></span>
3. <span data-ttu-id="1fc6b-131">Klikněte na tlačítko glyfů pro jednu z nich, které jste zaregistrovaní na.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-131">Click a glyph for one of the sites that you're signed up for.</span></span> <span data-ttu-id="1fc6b-132">Odkaz přejdete na stránku na webu vybrané sociálních sítí, kde můžete sdílet odkaz.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-132">The link takes you to the page on the selected social network site where you can share a link.</span></span> <span data-ttu-id="1fc6b-133">Například pokud kliknete na odkaz Reddit, potom se přesunete na `submit to reddit` stránky na webu Reddit.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-133">For example, if you click the Reddit link, you're taken to the `submit to reddit` page on the Reddit website.</span></span>

     ![Obrázek 2](13-adding-social-networking-to-your-web-site/_static/image2.jpg)

<a id="Adding_a_Twitter_Feed"></a>
## <a name="adding-a-twitter-feed"></a><span data-ttu-id="1fc6b-135">Přidání na twitteru přes kanál</span><span class="sxs-lookup"><span data-stu-id="1fc6b-135">Adding a Twitter Feed</span></span>

<span data-ttu-id="1fc6b-136">Informace o použití pomocné rutiny na Twitteru, který je kompatibilní s aktuální verzí rozhraní Twitter API najdete v tématu [Pomocník Twitter](../ui-layouts-and-themes/twitter-helper.md).</span><span class="sxs-lookup"><span data-stu-id="1fc6b-136">For information about using a Twitter helper that is compatible with the current version of the Twitter API, see [Twitter helper](../ui-layouts-and-themes/twitter-helper.md).</span></span> <span data-ttu-id="1fc6b-137">Tento příklad ukazuje, jak psát vlastní pomocné rutiny, můžete snadno opakovaně použít kód z mnoho stránek.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-137">This example shows how to write your own helper so you can easily reuse the code from many pages.</span></span>

<a id="Displaying_a_Facebook_Button"></a>
## <a name="displaying-a-facebook-quotlikequot-button"></a><span data-ttu-id="1fc6b-138">Zobrazení Facebook &quot;jako&quot; tlačítko</span><span class="sxs-lookup"><span data-stu-id="1fc6b-138">Displaying a Facebook &quot;Like&quot; Button</span></span>

<span data-ttu-id="1fc6b-139">V některých případech je nejlepší možnost získat kód přímo ze sociálních sítí poskytovatele spíše než spoléhání se na pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-139">In some cases, your best option is to get the code directly from the social networking provider rather than relying on a helper.</span></span> <span data-ttu-id="1fc6b-140">To platí zejména pokud poskytovateli sociální sítě aktualizuje jeho možnosti rychleji, než se aktualizuje pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-140">This is especially true if the social network provider updates its options more quickly than the helper is updated.</span></span>

<span data-ttu-id="1fc6b-141">Přidávání funkcí Facebooku (jako je tlačítko) do vaší lokality, můžete načíst fragmentů kódu z [developers.facebook.com](https://developers.facebook.com/) lokality.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-141">To add Facebook features (such as the Like button) to your site, you can retrieve code snippets from the [developers.facebook.com](https://developers.facebook.com/) site.</span></span> <span data-ttu-id="1fc6b-142">Na stránce Facebooku pomocí svých nástrojů se ke generování fragmentu kódu, které se týkají vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-142">On the Facebook site, you use their tools to generate a code snippet that is relevant to your site.</span></span>

<span data-ttu-id="1fc6b-143">Následující zvýrazněný kód je kód, který byl načten z nástroje jako tlačítko na webu developers.facebook.com.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-143">The following highlighted code is the code that was retrieved from the Like Button tool on the developers.facebook.com site.</span></span> <span data-ttu-id="1fc6b-144">Je nutné zadat vlastní ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-144">You must provide your own app ID.</span></span>

[!code-html[Main](13-adding-social-networking-to-your-web-site/samples/sample2.html?highlight=7-14,16-17)]

<a id="Rendering_a_Gravatar_Image"></a>
## <a name="rendering-a-gravatar-image"></a><span data-ttu-id="1fc6b-145">Vykreslování obrázků Gravatar</span><span class="sxs-lookup"><span data-stu-id="1fc6b-145">Rendering a Gravatar Image</span></span>

<span data-ttu-id="1fc6b-146">A *Gravatar* ( &quot;globálně uznávané avatar&quot;) je obrázek, který může sloužit jako svou miniaturu na více webech &#8212; tedy image, která představuje vám.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-146">A *Gravatar* (a &quot;globally recognized avatar&quot;) is an image that can be used on multiple websites as your avatar &#8212; that is, an image that represents you.</span></span> <span data-ttu-id="1fc6b-147">Například můžete Gravatar identifikaci osoby v příspěvku fóra, do komentáře na blogu a tak dále.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-147">For example, a Gravatar can identify a person in a forum post, in a blog comment, and so on.</span></span> <span data-ttu-id="1fc6b-148">(Můžete zaregistrovat na webu Gravatar na vlastní Gravatar [ http://www.gravatar.com/ ](http://www.gravatar.com/).) Pokud chcete zobrazit obrázky vedle jména nebo e-mailové adresy lidí na vašem webu, můžete použít Gravatar pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-148">(You can register your own Gravatar at the Gravatar website at [http://www.gravatar.com/](http://www.gravatar.com/).) If you want to display images next to people's names or email addresses on your website, you can use the Gravatar helper.</span></span>

<span data-ttu-id="1fc6b-149">V tomto příkladu používáte jeden Gravatar, který představuje sami.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-149">In this example, you're using a single Gravatar that represents yourself.</span></span> <span data-ttu-id="1fc6b-150">Dalším způsobem, jak používat Gravatar je umožnit lidem, zadejte jeho adresu Gravatar po jejich registraci ve vaší lokalitě.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-150">Another way to use a Gravatar is to let people specify their Gravatar address when they register on your site.</span></span> <span data-ttu-id="1fc6b-151">(Můžete zjistěte, jak umožnit lidem zaregistrovat v [přidání zabezpečení a s členstvím na server webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202904).) Pokaždé, když zobrazíte informace pro tohoto uživatele, můžete přidat jenom Gravatar kde zobrazit uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-151">(You can learn how to let people register in [Adding Security and Membership to an ASP.NET Web Pages Site](https://go.microsoft.com/fwlink/?LinkId=202904).) Then whenever you display information for that user, you can just add the Gravatar to where you display the user's name.</span></span>

1. <span data-ttu-id="1fc6b-152">Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste tak již neučinili.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-152">Add the ASP.NET Web Helpers Library to your website as described in [Installing Helpers in an ASP.NET Web Pages Site](https://go.microsoft.com/fwlink/?LinkId=252372), if you haven't already.</span></span>
2. <span data-ttu-id="1fc6b-153">Vytvoření nové webové stránky s názvem *Gravatar.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-153">Create a new web page named *Gravatar.cshtml*.</span></span>
3. <span data-ttu-id="1fc6b-154">Přidejte následující kód do souboru:</span><span class="sxs-lookup"><span data-stu-id="1fc6b-154">Add the following markup to the file:</span></span> 

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample3.cshtml)]

    <span data-ttu-id="1fc6b-155">`Gravatar.GetHtml` Metoda zobrazí obrázek Gravatar na stránce.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-155">The `Gravatar.GetHtml` method displays the Gravatar image on the page.</span></span> <span data-ttu-id="1fc6b-156">Chcete-li změnit velikost bitové kopie, může obsahovat číslo jako druhý parametr.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-156">To change the size of the image, you can include a number as a second parameter.</span></span> <span data-ttu-id="1fc6b-157">Výchozí velikost je 80.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-157">The default size is 80.</span></span> <span data-ttu-id="1fc6b-158">Čísla menší než 80 vytvořit bitovou kopii menší.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-158">Numbers less than 80 make the image smaller.</span></span> <span data-ttu-id="1fc6b-159">Čísla větší než 80 zvětšit na obrázku.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-159">Numbers greater than 80 make the image larger.</span></span>
4. <span data-ttu-id="1fc6b-160">V `Gravatar.GetHtml` metody, nahradí `<Your Gravatar account here>` e-mailovou adresou, který používáte pro svůj účet Gravatar.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-160">In the `Gravatar.GetHtml` methods, replace `<Your Gravatar account here>` with the email address that you use for your Gravatar account.</span></span> <span data-ttu-id="1fc6b-161">(Pokud nemáte účet Gravatar, můžete použít e-mailovou adresu osoby, která nepodporuje.)</span><span class="sxs-lookup"><span data-stu-id="1fc6b-161">(If you don't have a Gravatar account, you can use the email address of someone who does.)</span></span>
5. <span data-ttu-id="1fc6b-162">Na stránce spusťte v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-162">Run the page in your browser.</span></span> <span data-ttu-id="1fc6b-163">Na stránce se zobrazí dvě bitové kopie Gravatar e-mailových adres, který jste zadali.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-163">The page displays two Gravatar images for the email address you specified.</span></span> <span data-ttu-id="1fc6b-164">Druhý obrázek je menší než první.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-164">The second image is smaller than the first.</span></span> 

    ![Obrázek 4](13-adding-social-networking-to-your-web-site/_static/image3.jpg)

<a id="Displaying_an_Xbox_Gamer_Card"></a>
## <a name="displaying-an-xbox-gamer-card"></a><span data-ttu-id="1fc6b-166">Zobrazení karty, která hráče Xbox</span><span class="sxs-lookup"><span data-stu-id="1fc6b-166">Displaying an Xbox Gamer Card</span></span>

<span data-ttu-id="1fc6b-167">Když uživatelé hry pro Microsoft Xbox online, každý uživatel má jedinečné ID.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-167">When people play Microsoft Xbox games online, each user has a unique ID.</span></span> <span data-ttu-id="1fc6b-168">Statistiky jsou udržovány pro oba hráči v podobě hráče karty, která zobrazuje jejich reputace, skóre hráče a nedávno hrají hry.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-168">Statistics are kept for each player in the form of a gamer card, which shows their reputation, gamer score, and recently played games.</span></span> <span data-ttu-id="1fc6b-169">Pokud jste hráče Xbox, můžete zobrazit hráče karty na stránkách na webu pomocí `GamerCard` pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-169">If you're an Xbox gamer, you can show your gamer card on pages in your site by using the `GamerCard` helper.</span></span>

1. <span data-ttu-id="1fc6b-170">Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste tak již neučinili.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-170">Add the ASP.NET Web Helpers Library to your website as described in [Installing Helpers in an ASP.NET Web Pages Site](https://go.microsoft.com/fwlink/?LinkId=252372), if you haven't already.</span></span>
2. <span data-ttu-id="1fc6b-171">Vytvoří novou stránku s názvem *XboxGamer.cshtml* a přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-171">Create a new page named *XboxGamer.cshtml* and add the following markup.</span></span>

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample4.cshtml)]

    <span data-ttu-id="1fc6b-172">Můžete použít `GamerCard.GetHtml` vlastnosti a určit tak alias pro hráče karty, který se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-172">You use the `GamerCard.GetHtml` property to specify the alias for the gamer card to be displayed.</span></span>
3. <span data-ttu-id="1fc6b-173">Na stránce spusťte v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-173">Run the page in your browser.</span></span> <span data-ttu-id="1fc6b-174">Na stránce se zobrazí karta hráče Xbox, který jste zadali.</span><span class="sxs-lookup"><span data-stu-id="1fc6b-174">The page displays the Xbox gamer card that you specified.</span></span>

    ![Obrázek 5](13-adding-social-networking-to-your-web-site/_static/image4.jpg)

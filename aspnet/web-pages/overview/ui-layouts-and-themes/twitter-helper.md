---
uid: web-pages/overview/ui-layouts-and-themes/twitter-helper
title: Rozhraní ASP.NET Web Pages pomocná rutina twitteru | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto tématu a aplikace ukazují, jak přidat do projektu služby WebMatrix 3 Pomocník Twitter. Obsahuje kód Pomocník Twitter a ukazuje způsob volání pomocné rutiny...
ms.author: riande
ms.date: 11/26/2018
ms.assetid: c1a1244e-b9c8-42e6-a00b-8456a4ec027c
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/twitter-helper
msc.type: authoredcontent
ms.openlocfilehash: fabe9f2b84d278a766dc8d8b7bfc00e1eb967127
ms.sourcegitcommit: 710fc5fcac258cc8415976dc66bdb355b3e061d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52299427"
---
<a name="twitter-helper-with-aspnet-web-pages"></a><span data-ttu-id="0f591-104">Pomocná rutina Twitteru na webových stránkách ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0f591-104">Twitter Helper with ASP.NET Web Pages</span></span>
====================
<span data-ttu-id="0f591-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="0f591-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0f591-106">Pomocné rutiny twitteru jsou zastaralé.</span><span class="sxs-lookup"><span data-stu-id="0f591-106">Twitter Helpers are obsolete.</span></span> <span data-ttu-id="0f591-107">Na Twitteru nejnovější zapojení nástroje pro weby, naleznete v tématu [Twitter pro Websites přehled](https://developer.twitter.com/en/docs/twitter-for-websites/overview).</span><span class="sxs-lookup"><span data-stu-id="0f591-107">For Twitter's latest engagement tools for websites, see [Twitter for Websites Overview](https://developer.twitter.com/en/docs/twitter-for-websites/overview).</span></span>

> <span data-ttu-id="0f591-108">V tomto tématu a aplikace ukazují, jak přidat do projektu služby WebMatrix 3 Pomocník Twitter.</span><span class="sxs-lookup"><span data-stu-id="0f591-108">This topic and application show how to add a Twitter Helper to your WebMatrix 3 project.</span></span> <span data-ttu-id="0f591-109">Obsahuje kód Pomocník Twitter a ukazuje, jak volat metodu helper.</span><span class="sxs-lookup"><span data-stu-id="0f591-109">It contains the Twitter Helper code and shows how to call the helper methods.</span></span>
> 
> <span data-ttu-id="0f591-110">Tento kód pro soubor Twitter.cshtml vyvinula společnost **Tian Pan** společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0f591-110">This code for the Twitter.cshtml file was developed by **Tian Pan** of Microsoft.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="0f591-111">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="0f591-111">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="0f591-112">Webové stránky ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="0f591-112">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="0f591-113">V tomto kurzu se také pracuje s ASP.NET Web Pages 2.</span><span class="sxs-lookup"><span data-stu-id="0f591-113">This tutorial also works with ASP.NET Web Pages 2.</span></span>


## <a name="introduction"></a><span data-ttu-id="0f591-114">Úvod</span><span class="sxs-lookup"><span data-stu-id="0f591-114">Introduction</span></span>

<span data-ttu-id="0f591-115">Toto téma ukazuje, jak přidat Pomocník Twitter pro vaši aplikaci a pomocí syntaxe Razor pro volání metody helper.</span><span class="sxs-lookup"><span data-stu-id="0f591-115">This topic demonstrates how to add a Twitter Helper to your application and use Razor syntax to call the helper methods.</span></span> <span data-ttu-id="0f591-116">Pomocník Twitter umožňuje snadno začlenit tlačítka Twitteru a pomůcky ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0f591-116">The Twitter Helper makes it easy to incorporate Twitter buttons and widgets in your application.</span></span> <span data-ttu-id="0f591-117">Chcete-li pomocí widgetu Twitter, jako je například timeline uživatele nebo výsledky hledání pro hashtagu, musíte nejdřív vytvořit [widgetů na Twitteru](https://twitter.com/settings/widgets).</span><span class="sxs-lookup"><span data-stu-id="0f591-117">To use a Twitter widget, such as a user's timeline or the search results for a hashtag, you must first create the [widget on Twitter](https://twitter.com/settings/widgets).</span></span> <span data-ttu-id="0f591-118">Po vytvoření vaší widgetů, zobrazí se id widgetu. Toto id widgetu předat jako parametr při volání pomocné metody, které ukazují widgetu.</span><span class="sxs-lookup"><span data-stu-id="0f591-118">After creating your widget, you will receive a widget id. You pass this widget id as a parameter when calling the helper methods that show widget.</span></span>

<span data-ttu-id="0f591-119">Toto téma bylo napsáno pro verzi 1.1 rozhraní Twitter API.</span><span class="sxs-lookup"><span data-stu-id="0f591-119">This topic was written for version 1.1 of the Twitter API.</span></span> <span data-ttu-id="0f591-120">Přímo do projektu přidáte kód Pomocník Twitter, můžete aktualizovat kód pomocného objektu, pokud se změní rozhraní Twitter API.</span><span class="sxs-lookup"><span data-stu-id="0f591-120">By directly adding the Twitter Helper code to your project, you can update the helper code if the Twitter API changes.</span></span>

<span data-ttu-id="0f591-121">Informace o instalaci služby WebMatrix najdete v tématu [Úvod do ASP.NET Web Pages 2 – Začínáme](../getting-started/introducing-aspnet-web-pages-2/getting-started.md).</span><span class="sxs-lookup"><span data-stu-id="0f591-121">For information about installing WebMatrix, see [Introducing ASP.NET Web Pages 2 - Getting Started](../getting-started/introducing-aspnet-web-pages-2/getting-started.md).</span></span>

## <a name="add-twitter-helper-to-your-project"></a><span data-ttu-id="0f591-122">Pomocník Twitter přidat do projektu</span><span class="sxs-lookup"><span data-stu-id="0f591-122">Add Twitter Helper to your project</span></span>

<span data-ttu-id="0f591-123">Chcete-li přidat Pomocník Twitter, nejprve přidejte složku s názvem **aplikace\_kód** do projektu.</span><span class="sxs-lookup"><span data-stu-id="0f591-123">To add the Twitter Helper, first, add a folder named **App\_Code** to your project.</span></span> <span data-ttu-id="0f591-124">Vytvořte soubor s názvem **Twitter.cshtml**.</span><span class="sxs-lookup"><span data-stu-id="0f591-124">Then, create a file named **Twitter.cshtml**.</span></span>

![Složku App_Code](twitter-helper/_static/image1.png)

<span data-ttu-id="0f591-126">Nahraďte kód v Twitter.cshtml následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="0f591-126">Replace the default code in Twitter.cshtml with the following code.</span></span>

[!code-cshtml[Main](twitter-helper/samples/sample1.cshtml)]

## <a name="call-twitter-methods-from-your-web-pages"></a><span data-ttu-id="0f591-127">Volání metody Twitteru z webových stránek</span><span class="sxs-lookup"><span data-stu-id="0f591-127">Call Twitter methods from your web pages</span></span>

<span data-ttu-id="0f591-128">Následující příklad ukazuje způsob použití metody Pomocník Twitter ze stránky ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="0f591-128">The following example shows how to use the Twitter Helper methods from a page in your project.</span></span> <span data-ttu-id="0f591-129">Ve vašem projektu můžete k nahrazení hodnoty parametrů s hodnotami, které jsou relevantní pro vaše potřeby.</span><span class="sxs-lookup"><span data-stu-id="0f591-129">In your project, you will want to replace the parameter values with values that are relevant to your needs.</span></span> <span data-ttu-id="0f591-130">ID zadané widgetu můžete prozkoumat, jak fungují metody, ale budete chtít vytvořit vlastní pomůcky pro váš projekt.</span><span class="sxs-lookup"><span data-stu-id="0f591-130">You can use the provided widget ids to explore how the methods work, but you will want to generate your own widgets for your project.</span></span>

<span data-ttu-id="0f591-131">Ne všechny parametry uvedené níže jsou požadovány.</span><span class="sxs-lookup"><span data-stu-id="0f591-131">Not all of the parameters shown below are required.</span></span> <span data-ttu-id="0f591-132">Volitelné parametry umožňují přizpůsobit, jak se zobrazí tlačítko nebo widgetu.</span><span class="sxs-lookup"><span data-stu-id="0f591-132">The optional parameters are used to customize how the button or widget is displayed.</span></span> <span data-ttu-id="0f591-133">Například použijte tlačítko pouze vyžaduje uživatelské jméno dodržovat, ale tento příklad ukazuje, jak zahrnují počet sledujících a jak určit velikost tlačítka a jazyk.</span><span class="sxs-lookup"><span data-stu-id="0f591-133">For example, the Follow Button only requires the user name to follow, but the example shows how to include the number of followers, and how specify the size of the button and the language.</span></span>

[!code-html[Main](twitter-helper/samples/sample2.html)]

## <a name="see-the-results"></a><span data-ttu-id="0f591-134">Zobrazit výsledky</span><span class="sxs-lookup"><span data-stu-id="0f591-134">See the results</span></span>

<span data-ttu-id="0f591-135">Ve výše uvedeném kódu vytvoří následující tlačítka a pomůcky.</span><span class="sxs-lookup"><span data-stu-id="0f591-135">The above code produces the following buttons and widgets.</span></span> <span data-ttu-id="0f591-136">Tato tlačítka a pomůcky jsou plně funkční, není snímky obrazovky.</span><span class="sxs-lookup"><span data-stu-id="0f591-136">These buttons and widgets are fully-functional, not screenshots.</span></span> <span data-ttu-id="0f591-137">Je zobrazeno tlačítko postupujte ve španělštině, protože parametr jazyka byla nastavena na hodnotu **es**.</span><span class="sxs-lookup"><span data-stu-id="0f591-137">The Follow Button is displayed in Spanish because the language parameter was set to **es**.</span></span>

### <a name="follow-button"></a><span data-ttu-id="0f591-138">Použijte tlačítko</span><span class="sxs-lookup"><span data-stu-id="0f591-138">Follow Button</span></span>

<span data-ttu-id="0f591-139">[Postupujte podle @aspnet)](https://twitter.com/aspnet)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + '://platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore(js, fjs); } }(document, 'script', 'twitter-wjs');</script>`</span><span class="sxs-lookup"><span data-stu-id="0f591-139">[Follow @aspnet)](https://twitter.com/aspnet)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + '://platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore(js, fjs); } }(document, 'script', 'twitter-wjs');</script>`</span></span>

### <a name="tweet-button"></a><span data-ttu-id="0f591-140">Tlačítko tweetu</span><span class="sxs-lookup"><span data-stu-id="0f591-140">Tweet Button</span></span>

<span data-ttu-id="0f591-141">[Tweet](https://twitter.com/share)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + '://platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore(js, fjs); } }(document, 'script', 'twitter-wjs');</script>`</span><span class="sxs-lookup"><span data-stu-id="0f591-141">[Tweet](https://twitter.com/share)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + '://platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore(js, fjs); } }(document, 'script', 'twitter-wjs');</script>`</span></span>

### <a name="user-timeline-profile"></a><span data-ttu-id="0f591-142">Časová osa uživatele (profil)</span><span class="sxs-lookup"><span data-stu-id="0f591-142">User Timeline (Profile)</span></span>

<span data-ttu-id="0f591-143">[Tweety podle @aspnet](https://twitter.com/aspnet)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`</span><span class="sxs-lookup"><span data-stu-id="0f591-143">[Tweets by @aspnet](https://twitter.com/aspnet)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`</span></span>

### <a name="favorites"></a><span data-ttu-id="0f591-144">Oblíbené položky</span><span class="sxs-lookup"><span data-stu-id="0f591-144">Favorites</span></span>

<span data-ttu-id="0f591-145">[Oblíbené Tweety podle @Microsoft](https://twitter.com/Microsoft/favorites)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`</span><span class="sxs-lookup"><span data-stu-id="0f591-145">[Favorite Tweets by @Microsoft](https://twitter.com/Microsoft/favorites)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`</span></span>

### <a name="list"></a><span data-ttu-id="0f591-146">Seznam</span><span class="sxs-lookup"><span data-stu-id="0f591-146">List</span></span>

<span data-ttu-id="0f591-147">[Tweetuje z @Microsoft/MS \_příjemce\_pruhy](https://twitter.com/microsoft/ms-consumer-brands/)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`</span><span class="sxs-lookup"><span data-stu-id="0f591-147">[Tweets from @Microsoft/MS\_Consumer\_Bands](https://twitter.com/microsoft/ms-consumer-brands/)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`</span></span>

### <a name="search"></a><span data-ttu-id="0f591-148">Hledat</span><span class="sxs-lookup"><span data-stu-id="0f591-148">Search</span></span>

<span data-ttu-id="0f591-149">[Tweetuje o &quot;#asp.net&quot;](https://twitter.com/search?q=%23asp.net)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`</span><span class="sxs-lookup"><span data-stu-id="0f591-149">[Tweets about &quot;#asp.net&quot;](https://twitter.com/search?q=%23asp.net)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`</span></span>

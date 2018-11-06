---
uid: web-pages/overview/performance-and-traffic/14-analyzing-traffic
title: Sledování návštěvníka informace (Analytics) pro webové stránky ASP.NET (Razor) lokality | Dokumentace Microsoftu
author: Rick-Anderson
description: Poté, co jste získali váš web bude, můžete analyzovat provoz vašeho webu.
ms.author: riande
ms.date: 02/17/2014
ms.assetid: 360bc6e1-84c5-4b8e-a84c-ea48ab807aa4
msc.legacyurl: /web-pages/overview/performance-and-traffic/14-analyzing-traffic
msc.type: authoredcontent
ms.openlocfilehash: 57e6a0d4681f147faa5e9ca3b6ed0ef287d6a381
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020881"
---
<a name="tracking-visitor-information-analytics-for-an-aspnet-web-pages-razor-site"></a><span data-ttu-id="9e614-103">Sledování návštěvníka informace (Analytics) pro ASP.NET Web Pages (Razor)</span><span class="sxs-lookup"><span data-stu-id="9e614-103">Tracking Visitor Information (Analytics) for an ASP.NET Web Pages (Razor) Site</span></span>
====================
<span data-ttu-id="9e614-104">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="9e614-104">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="9e614-105">Tento článek popisuje způsob použití pomocné rutiny pro přidání analýzy webů na stránky na webu rozhraní ASP.NET Web Pages (Razor).</span><span class="sxs-lookup"><span data-stu-id="9e614-105">This article describes how to use a helper to add website analytics to pages in an ASP.NET Web Pages (Razor) website.</span></span>
> 
> <span data-ttu-id="9e614-106">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="9e614-106">What you'll learn:</span></span>
> 
> - <span data-ttu-id="9e614-107">Jak odeslat informace o provozu webu poskytovatele analýz.</span><span class="sxs-lookup"><span data-stu-id="9e614-107">How to send information about your website traffic to an analytics provider.</span></span>
> 
> <span data-ttu-id="9e614-108">Toto jsou ASP.NET programování funkcí představených v následujícím článku:</span><span class="sxs-lookup"><span data-stu-id="9e614-108">These are the ASP.NET programming features introduced in the article:</span></span>
> 
> - <span data-ttu-id="9e614-109">`Analytics` Pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="9e614-109">The `Analytics` helper.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="9e614-110">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="9e614-110">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="9e614-111">Rozhraní ASP.NET Web Pages (Razor) 2</span><span class="sxs-lookup"><span data-stu-id="9e614-111">ASP.NET Web Pages (Razor) 2</span></span>
> - <span data-ttu-id="9e614-112">Knihovnu ASP.NET Web Helpers (balíček NuGet)</span><span class="sxs-lookup"><span data-stu-id="9e614-112">ASP.NET Web Helpers Library (NuGet package)</span></span>


<span data-ttu-id="9e614-113">Analýza je obecný termín pro technologie, která měří provoz na vašem webu to vám umožní pochopit, jak ostatní používají Web.</span><span class="sxs-lookup"><span data-stu-id="9e614-113">Analytics is a general term for technology that measures traffic on your website so you can understand how people use the site.</span></span> <span data-ttu-id="9e614-114">Mnoho služeb analytics jsou k dispozici, včetně služby od Google, Yahoo, StatCounter a dalších.</span><span class="sxs-lookup"><span data-stu-id="9e614-114">Many analytics services are available, including services from Google, Yahoo, StatCounter, and others.</span></span>

<span data-ttu-id="9e614-115">Analýzy způsob, jak funguje je, že zaregistrujete účet s tímto poskytovatelem analytics, ve kterém zaregistrujete lokalitu, které chcete sledovat. Zprostředkovatel odešle fragment kódu jazyka JavaScript, který obsahuje ID nebo kód pro váš účet sledování.</span><span class="sxs-lookup"><span data-stu-id="9e614-115">The way analytics works is that you sign up for an account with the analytics provider, where you register the site that you want to track. The provider sends you a snippet of JavaScript code that includes an ID or tracking code for your account.</span></span> <span data-ttu-id="9e614-116">Přidejte javascriptový fragment kódu na webové stránky na webu, který chcete sledovat. (Obvykle přidáte fragment analytics zápatí ani rozložení stránky nebo jiné kódování HTML, který se zobrazí na každé stránce ve vaší lokalitě.) Pokud uživatelé požadují stránku, která obsahuje jeden z těchto fragmenty kódu jazyka JavaScript, fragment kódu odešle do poskytovatele analýz, který zaznamenává detailní informace o stránce informace o aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="9e614-116">You add the JavaScript snippet to the web pages on the site that you want to track. (You typically add the analytics snippet to a footer or layout page or other HTML markup that appears on every page in your site.) When users request a page that contains one of these JavaScript snippets, the snippet sends information about the current page to the analytics provider, who records various details about the page.</span></span>

<span data-ttu-id="9e614-117">Pokud chcete podívejte se na vaše Statistika webu přihlásíte webu poskytovatele analýz.</span><span class="sxs-lookup"><span data-stu-id="9e614-117">When you want to have a look at your site statistics, you log into the analytics provider's website.</span></span> <span data-ttu-id="9e614-118">Pak můžete zobrazit nejrůznější sestavy o vašem webu, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="9e614-118">You can then view all sorts of reports about your site, like:</span></span>

- <span data-ttu-id="9e614-119">Počet zobrazení stránek pro jednotlivé stránky.</span><span class="sxs-lookup"><span data-stu-id="9e614-119">The number of page views for individual pages.</span></span> <span data-ttu-id="9e614-120">Znamená to (přibližně) kolik návštěvníků webu a které stránky na webu jsou ta úplně nejoblíbenější.</span><span class="sxs-lookup"><span data-stu-id="9e614-120">This tells you (roughly) how many people are visiting the site, and which pages on your site are the most popular.</span></span>
- <span data-ttu-id="9e614-121">Jak dlouho strávit lidí na konkrétní stránky.</span><span class="sxs-lookup"><span data-stu-id="9e614-121">How long people spend on specific pages.</span></span> <span data-ttu-id="9e614-122">To se dozvíte, například, jestli je domovská stránka udržovat zájmu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="9e614-122">This can tell you things like whether your home page is keeping people's interest.</span></span>
- <span data-ttu-id="9e614-123">Jaké weby uživatelé byli před navštívili vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="9e614-123">What sites people were on before they visited your site.</span></span> <span data-ttu-id="9e614-124">To vám pomůže pochopit, jestli je provoz přicházející z odkazy z prohledávání a tak dále.</span><span class="sxs-lookup"><span data-stu-id="9e614-124">This helps you understand whether your traffic is coming from links, from searches, and so on.</span></span>
- <span data-ttu-id="9e614-125">Když uživatelé, kteří navštíví váš web a jak dlouho zůstávají.</span><span class="sxs-lookup"><span data-stu-id="9e614-125">When people visit your site and how long they stay.</span></span>
- <span data-ttu-id="9e614-126">Které země jsou návštěvnících z.</span><span class="sxs-lookup"><span data-stu-id="9e614-126">What countries your visitors are from.</span></span>
- <span data-ttu-id="9e614-127">Jaké prohlížeče a operační systémy návštěvnících používají.</span><span class="sxs-lookup"><span data-stu-id="9e614-127">What browsers and operating systems your visitors are using.</span></span>

    ![Ch14traffic-1](14-analyzing-traffic/_static/image1.jpg)

## <a name="using-a-helper-to-add-analytics-to-a-page"></a><span data-ttu-id="9e614-129">Použití pomocné rutiny na Analytics přidat na stránku</span><span class="sxs-lookup"><span data-stu-id="9e614-129">Using a Helper to Add Analytics to a Page</span></span>

<span data-ttu-id="9e614-130">Rozhraní ASP.NET Web Pages zahrnuje několik pomocných rutin analytics (`Analytics.GetGoogleHtml`, `Analytics.GetYahooHtml`, a `Analytics.GetStatCounterHtml`), které usnadňují správu fragmenty kódu jazyka JavaScript používá pro účely analýzy.</span><span class="sxs-lookup"><span data-stu-id="9e614-130">ASP.NET Web Pages includes several analytics helpers (`Analytics.GetGoogleHtml`, `Analytics.GetYahooHtml`, and `Analytics.GetStatCounterHtml`) that make it easy to manage the JavaScript snippets used for analytics.</span></span> <span data-ttu-id="9e614-131">Místo tím, jak a kde do kódu jazyka JavaScript, vše, co musíte udělat, je přidání pomocné rutiny na stránku.</span><span class="sxs-lookup"><span data-stu-id="9e614-131">Instead of figuring out how and where to put the JavaScript code, all you have to do is add the helper to a page.</span></span> <span data-ttu-id="9e614-132">Jediná informace, které je potřeba zadat je název účtu, ID nebo sledování kódu.</span><span class="sxs-lookup"><span data-stu-id="9e614-132">The only information you need to provide is your account name, ID, or tracking code.</span></span> <span data-ttu-id="9e614-133">(Pro StatCounter, budete také muset poskytnout pár dalších hodnot.)</span><span class="sxs-lookup"><span data-stu-id="9e614-133">(For StatCounter, you also have to provide a few additional values.)</span></span>

<span data-ttu-id="9e614-134">V tomto postupu vytvoříte stránku rozložení, který používá `GetGoogleHtml` pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="9e614-134">In this procedure, you'll create a layout page that uses the `GetGoogleHtml` helper.</span></span> <span data-ttu-id="9e614-135">Pokud již máte účet s jednou z jiné poskytovatele analytických služeb, můžete místo toho použijte tento účet a provádět lehké úpravy podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="9e614-135">If you already have an account with one of the other analytics providers, you can use that account instead and make slight adjustments as needed.</span></span>

> [!NOTE]
> <span data-ttu-id="9e614-136">Když vytvoříte účet analytics, je zaregistrovat adresu URL webu, který chcete sledovat.</span><span class="sxs-lookup"><span data-stu-id="9e614-136">When you create an analytics account, you register the URL of the site that you want to be tracking.</span></span> <span data-ttu-id="9e614-137">Pokud testujete všechno, co v místním počítači, můžete nebude sledovat skutečný provoz (pouze provoz je můžete), tak nebude možné zaznamenat a zobrazit statistiku serveru.</span><span class="sxs-lookup"><span data-stu-id="9e614-137">If you're testing everything on your local computer, you won't be tracking actual traffic (the only traffic is you), so you won't be able to record and view site statistics.</span></span> <span data-ttu-id="9e614-138">Ale tento postup ukazuje, jak přidat analytics pomocné rutiny na stránku.</span><span class="sxs-lookup"><span data-stu-id="9e614-138">But this procedure shows how you add an analytics helper to a page.</span></span> <span data-ttu-id="9e614-139">Při publikování webu živého webu bude odesílat informace do vašeho zprostředkovatele datové analýzy.</span><span class="sxs-lookup"><span data-stu-id="9e614-139">When you publish your site, the live site will send information to your analytics provider.</span></span>


1. <span data-ttu-id="9e614-140">Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste to ještě nepřidali.</span><span class="sxs-lookup"><span data-stu-id="9e614-140">Add the ASP.NET Web Helpers Library to your website as described in [Installing Helpers in an ASP.NET Web Pages Site](https://go.microsoft.com/fwlink/?LinkId=252372), if you haven't already added it.</span></span>
2. <span data-ttu-id="9e614-141">Vytvoření účtu pomocí Google Analytics a poznamenejte název účtu.</span><span class="sxs-lookup"><span data-stu-id="9e614-141">Create an account with Google Analytics and record the account name.</span></span>
3. <span data-ttu-id="9e614-142">Vytvoření rozložení stránky s názvem *Analytics.cshtml* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="9e614-142">Create a layout page named *Analytics.cshtml* and add the following markup:</span></span>

    [!code-cshtml[Main](14-analyzing-traffic/samples/sample1.cshtml)]

    > [!NOTE]
    > <span data-ttu-id="9e614-143">Je nutné umístit volání `Analytics` pomocné rutiny v těle webové stránky (před `</body>` značky).</span><span class="sxs-lookup"><span data-stu-id="9e614-143">You must place the call to the `Analytics` helper in the body of your web page (before the `</body>` tag).</span></span> <span data-ttu-id="9e614-144">V opačném případě nebude prohlížeče spusťte skript.</span><span class="sxs-lookup"><span data-stu-id="9e614-144">Otherwise, the browser will not run the script.</span></span>

    <span data-ttu-id="9e614-145">Pokud používáte jiný analytics poskytovatele, použijte jednu z následujících pomocné rutiny:</span><span class="sxs-lookup"><span data-stu-id="9e614-145">If you're using a different analytics provider, use one of the following helpers instead:</span></span>

    - <span data-ttu-id="9e614-146">(Yahoo) `@Analytics.GetYahooHtml("myaccount")`</span><span class="sxs-lookup"><span data-stu-id="9e614-146">(Yahoo) `@Analytics.GetYahooHtml("myaccount")`</span></span>
    - <span data-ttu-id="9e614-147">(StatCounter) `@Analytics.GetStatCounterHtml("project", "security")`</span><span class="sxs-lookup"><span data-stu-id="9e614-147">(StatCounter) `@Analytics.GetStatCounterHtml("project", "security")`</span></span>
4. <span data-ttu-id="9e614-148">Nahraďte `myaccount` s názvem účtu, ID nebo sledování kód, který jste vytvořili v kroku 1.</span><span class="sxs-lookup"><span data-stu-id="9e614-148">Replace `myaccount` with the name of the account, ID, or tracking code that you created in step 1.</span></span>
5. <span data-ttu-id="9e614-149">Spuštění stránky v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9e614-149">Run the page in the browser.</span></span> <span data-ttu-id="9e614-150">(Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.)</span><span class="sxs-lookup"><span data-stu-id="9e614-150">(Make sure the page is selected in the **Files** workspace before you run it.)</span></span>
6. <span data-ttu-id="9e614-151">V prohlížeči zobrazte zdroj stránky.</span><span class="sxs-lookup"><span data-stu-id="9e614-151">In the browser, view the page source.</span></span> <span data-ttu-id="9e614-152">Budete moct zobrazit vykreslený analýzy kódu:</span><span class="sxs-lookup"><span data-stu-id="9e614-152">You'll be able to see the rendered analytics code:</span></span>

    [!code-html[Main](14-analyzing-traffic/samples/sample2.html)]
7. <span data-ttu-id="9e614-153">Přihlášení na web Google Analytics a prozkoumejte statistiky pro váš web.</span><span class="sxs-lookup"><span data-stu-id="9e614-153">Log onto the Google Analytics site and examine the statistics for your site.</span></span> <span data-ttu-id="9e614-154">Pokud spouštíte na živý web na stránku, uvidíte položku, která protokoluje najdete na stránce.</span><span class="sxs-lookup"><span data-stu-id="9e614-154">If you're running the page on a live site, you see an entry that logs the visit to your page.</span></span>

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a><span data-ttu-id="9e614-155">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="9e614-155">Additional Resources</span></span>

- [<span data-ttu-id="9e614-156">Web Google Analytics</span><span class="sxs-lookup"><span data-stu-id="9e614-156">Google Analytics site</span></span>](https://www.google.com/analytics/)
- [<span data-ttu-id="9e614-157">Yahoo! Web Analytics lokality</span><span class="sxs-lookup"><span data-stu-id="9e614-157">Yahoo! Web Analytics site</span></span>](http://help.yahoo.com/l/us/yahoo/ywa/)
- [<span data-ttu-id="9e614-158">StatCounter lokality</span><span class="sxs-lookup"><span data-stu-id="9e614-158">StatCounter site</span></span>](http://statcounter.com/)

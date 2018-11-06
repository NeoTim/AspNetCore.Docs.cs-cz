---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/publishing
title: Představení rozhraní ASP.NET Web Pages – publikování webu pomocí služby WebMatrix | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento kurz je finální pokračování v této sérii kurzů, který představuje rozhraní ASP.NET Web Pages a Microsoft WebMatrix. Popisuje, jak publikovat svůj web t...
ms.author: riande
ms.date: 05/28/2015
ms.assetid: 7e85c70e-1a88-4408-8b3d-29611c7713ed
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/publishing
msc.type: authoredcontent
ms.openlocfilehash: fe196e5db8fd1cecbe84b2eb970939303f9313d1
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021453"
---
<a name="introducing-aspnet-web-pages---publishing-a-site-by-using-webmatrix"></a><span data-ttu-id="e957c-104">Úvod do webových stránek ASP.NET – publikování webu pomocí Webmatrixu</span><span class="sxs-lookup"><span data-stu-id="e957c-104">Introducing ASP.NET Web Pages - Publishing a Site by Using WebMatrix</span></span>
====================
<span data-ttu-id="e957c-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="e957c-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="e957c-106">Tento kurz je finální pokračování v této sérii kurzů, který představuje rozhraní ASP.NET Web Pages a Microsoft WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="e957c-106">This tutorial is the final installment in the tutorial set that introduces ASP.NET Web Pages and Microsoft WebMatrix.</span></span> <span data-ttu-id="e957c-107">Popisuje, jak publikovat svůj web na Internetu, ostatní mohli pracovat s ním.</span><span class="sxs-lookup"><span data-stu-id="e957c-107">It discusses how to publish your site to the Internet so that others can work with it.</span></span> <span data-ttu-id="e957c-108">Předpokládá, že jste dokončili řady prostřednictvím [vytváření konzistentní vzhled pro weby technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251585).</span><span class="sxs-lookup"><span data-stu-id="e957c-108">It assumes you have completed the series through [Creating a Consistent Look for ASP.NET Web Pages Sites](https://go.microsoft.com/fwlink/?LinkId=251585).</span></span>
> 
> <span data-ttu-id="e957c-109">Dozvíte se víc o publikování vašeho webu pomocí:</span><span class="sxs-lookup"><span data-stu-id="e957c-109">You'll learn how to publish your site using:</span></span>
> 
> - <span data-ttu-id="e957c-110">Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="e957c-110">Microsoft Azure</span></span>
> - <span data-ttu-id="e957c-111">Webové hostingové společnosti</span><span class="sxs-lookup"><span data-stu-id="e957c-111">Web Hosting Company</span></span>


## <a name="about-publishing-your-site"></a><span data-ttu-id="e957c-112">Publikování webu</span><span class="sxs-lookup"><span data-stu-id="e957c-112">About Publishing Your Site</span></span>

<span data-ttu-id="e957c-113">Až dosud jste provedli všechny práce na místním počítači, včetně testování stránek.</span><span class="sxs-lookup"><span data-stu-id="e957c-113">Up to now, you've done all your work on a local computer, including testing your pages.</span></span> <span data-ttu-id="e957c-114">Ke spuštění vaší<em>.cshtml</em> stránky, využili jste webový server, která je integrovaná do služby WebMatrix, konkrétně služby IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e957c-114">To run your<em>.cshtml</em> pages, you've used the web server that's built into WebMatrix, namely IIS Express.</span></span> <span data-ttu-id="e957c-115">Můžete ale samozřejmě nikdo můžete zobrazit na webu, který jste vytvořili s výjimkou je.</span><span class="sxs-lookup"><span data-stu-id="e957c-115">But of course no one can see the site you've created except you.</span></span> <span data-ttu-id="e957c-116">Informovat tak ostatní uživatele práci s webem, je nutné ji publikovat na Internetu.</span><span class="sxs-lookup"><span data-stu-id="e957c-116">To let others work with your site, you have to publish it to the Internet.</span></span>

<span data-ttu-id="e957c-117">Pokud již máte přístup k veřejné webový server, publikování znamená, že budete muset mít s účtem *cloudovou platformu* nebo *poskytovatele hostingu*.</span><span class="sxs-lookup"><span data-stu-id="e957c-117">Unless you have access to a public web server already, publishing means that you have to have an account with a *cloud platform* or a *hosting provider*.</span></span> <span data-ttu-id="e957c-118">Cloudové platformy, jako je například Microsoft Azure poskytuje infrastrukturu na vyžádání pro vaše aplikace.</span><span class="sxs-lookup"><span data-stu-id="e957c-118">A cloud platform, such as Microsoft Azure, provides on-demand infrastructure for your applications.</span></span> <span data-ttu-id="e957c-119">Poskytovatel hostingu je společnost, která vlastní servery veřejně přístupný webový a, který bude poskytovat do nájmu můžete místo pro váš web.</span><span class="sxs-lookup"><span data-stu-id="e957c-119">A hosting provider is a company that owns publicly accessible web servers and that will rent you space for your site.</span></span> <span data-ttu-id="e957c-120">Spuštění z za pár dolarů za měsíc (nebo dokonce bezplatná) plány hostování pro malé weby na stovky dolarů za měsíc pro komerční websites velkého rozsahu.</span><span class="sxs-lookup"><span data-stu-id="e957c-120">Hosting plans run from a few dollars a month (or even free) for small sites to many hundreds of dollars a month for high-volume commercial websites.</span></span>

> [!NOTE]
> <span data-ttu-id="e957c-121">Můžete mít přístup public webový server prostřednictvím poskytovatele internetových služeb (ISP), který použijete k získání doma internetové služby.</span><span class="sxs-lookup"><span data-stu-id="e957c-121">You might have access to a public web server via the internet service provider (ISP) that you use to get internet service at home.</span></span> <span data-ttu-id="e957c-122">Váš poskytovatel hostingu však musí podporovat rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="e957c-122">However, your hosting provider must support ASP.NET Web Pages.</span></span> <span data-ttu-id="e957c-123">Není mnoho poskytovatelů internetových služeb, ale je vždy vhodné kontrolu.</span><span class="sxs-lookup"><span data-stu-id="e957c-123">Many ISPs don't, but it's always worth checking.</span></span>


<span data-ttu-id="e957c-124">V tomto kurzu dáme vám přehled o tom, jak publikovat.</span><span class="sxs-lookup"><span data-stu-id="e957c-124">In this tutorial, we'll give you an overview of how to publish.</span></span> <span data-ttu-id="e957c-125">Není praktické poskytnout přesné informace pro všechno, protože proces se trochu liší pro každý poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="e957c-125">It's not practical to provide exact details for everything, because the process differs a bit for every hosting provider.</span></span> <span data-ttu-id="e957c-126">Ale získáte představu o tom, jak tento proces funguje.</span><span class="sxs-lookup"><span data-stu-id="e957c-126">But you'll get a good idea of how the process works.</span></span>

<span data-ttu-id="e957c-127">Tento kurz obsahuje čtyři části:</span><span class="sxs-lookup"><span data-stu-id="e957c-127">This tutorial contains four sections:</span></span>

1. [<span data-ttu-id="e957c-128">Nastavení výchozí stránky</span><span class="sxs-lookup"><span data-stu-id="e957c-128">Setting up the default page</span></span>](#defaultpage)
2. <span data-ttu-id="e957c-129">Publikování (zvolte jednu z následujících)</span><span class="sxs-lookup"><span data-stu-id="e957c-129">Publishing (choose one of the following)</span></span>  
 <span data-ttu-id="e957c-130">a.</span><span class="sxs-lookup"><span data-stu-id="e957c-130">a.</span></span> [<span data-ttu-id="e957c-131">Publikování webu ve službě Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="e957c-131">Publishing Your Site to Microsoft Azure</span></span>](#azure)  
 <span data-ttu-id="e957c-132">b.</span><span class="sxs-lookup"><span data-stu-id="e957c-132">b.</span></span> [<span data-ttu-id="e957c-133">Publikování webu na webu firma poskytující Hosting</span><span class="sxs-lookup"><span data-stu-id="e957c-133">Publishing Your Site to a Web Hosting Company</span></span>](#host)
3. [<span data-ttu-id="e957c-134">Aktualizuje se živého webu: opětovné publikování</span><span class="sxs-lookup"><span data-stu-id="e957c-134">Updating the Live Site: Republishing</span></span>](#update)

<a id="defaultpage"></a>
## <a name="setting-up-the-default-page"></a><span data-ttu-id="e957c-135">Nastavení výchozí stránky</span><span class="sxs-lookup"><span data-stu-id="e957c-135">Setting up the default page</span></span>

<span data-ttu-id="e957c-136">Když uživatel přejde na základní adresu pro web, se uživateli zobrazí výchozí stránka pro váš web.</span><span class="sxs-lookup"><span data-stu-id="e957c-136">When a user navigates to the base address for your web site, the default page for your site is displayed to the user.</span></span> <span data-ttu-id="e957c-137">Například pokud Default.htm je nastaven jako výchozí stránku webu na www.contoso.com, pak přejdete do <strong>www.contoso.com</strong> je stejný jako přejdete na <strong>www.contoso.com/Default.htm</strong>.</span><span class="sxs-lookup"><span data-stu-id="e957c-137">For example, when Default.htm is set as the default page for the site at www.contoso.com, then navigating to <strong>www.contoso.com</strong> is the same as navigating to <strong>www.contoso.com/Default.htm</strong>.</span></span>

<span data-ttu-id="e957c-138">V současné době je váš web používá **stránku Default.cshtml** jako výchozí stránky.</span><span class="sxs-lookup"><span data-stu-id="e957c-138">Currently, your site uses **Default.cshtml** as the default page.</span></span> <span data-ttu-id="e957c-139">Tato stránka je v pořádku pro výchozí stránku, ale v tomto kurzu jste nepřidali žádný obsah na této stránce tak, že by se zobrazily prázdnou stránku.</span><span class="sxs-lookup"><span data-stu-id="e957c-139">This page is fine for your default page, but in this tutorial you have not added any content to that page so it would display a blank page.</span></span> <span data-ttu-id="e957c-140">Otevřete stránku Default.cshtml a nahraďte obsah následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="e957c-140">Open Default.cshtml and replace the content with the following code.</span></span>

[!code-cshtml[Main](publishing/samples/sample1.cshtml)]

<span data-ttu-id="e957c-141">Váš web je nyní připraveno k publikaci.</span><span class="sxs-lookup"><span data-stu-id="e957c-141">Now your site is ready for publication.</span></span> <span data-ttu-id="e957c-142">Nejprve se zobrazí postup nasazení webu do Azure a jak ji nasadit do webové firma poskytující hosting.</span><span class="sxs-lookup"><span data-stu-id="e957c-142">First, you will see how to deploy the site to Azure, and then how to deploy it to a web hosting company.</span></span> <span data-ttu-id="e957c-143">Funguje kterákoli z nich možností pro webové stránky a je potřeba jenom použijte jednu z možností nasazení.</span><span class="sxs-lookup"><span data-stu-id="e957c-143">Either option works for your web site, and you only need to follow one of the deployment options.</span></span>

<a id="azure"></a>
## <a name="publishing-your-site-to-microsoft-azure"></a><span data-ttu-id="e957c-144">Publikování webu ve službě Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="e957c-144">Publishing Your Site to Microsoft Azure</span></span>

<span data-ttu-id="e957c-145">V tomto kurzu se nejprve ukazují, jak nasadit svůj web do Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="e957c-145">This tutorial will first show you how to deploy your site to Microsoft Azure.</span></span> <span data-ttu-id="e957c-146">Když se přihlásíte pomocí účtu Microsoft, můžete vytvořit až 10 bezplatných webů v Azure.</span><span class="sxs-lookup"><span data-stu-id="e957c-146">By signing in with a Microsoft account, you can create up to 10 free sites on Azure.</span></span> <span data-ttu-id="e957c-147">Tyto bezplatné lokality poskytují pohodlný způsob, jak testovat svoje weby.</span><span class="sxs-lookup"><span data-stu-id="e957c-147">These free sites provide a convenient way to test your sites.</span></span> <span data-ttu-id="e957c-148">Vždy můžete odstranit tento příklad web později, abyste se vyhnuli použití všech bezplatných webů.</span><span class="sxs-lookup"><span data-stu-id="e957c-148">You can always delete this example site later to avoid using all of your free sites.</span></span> <span data-ttu-id="e957c-149">Během několika minut můžete vytvořit Bezplatný zkušební účet.</span><span class="sxs-lookup"><span data-stu-id="e957c-149">You can create a free trial account in just a couple of minutes.</span></span> <span data-ttu-id="e957c-150">Podrobnosti najdete v tématu [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).</span><span class="sxs-lookup"><span data-stu-id="e957c-150">For details, see [Azure Free Trial](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).</span></span>

<span data-ttu-id="e957c-151">Na pásu karet nástroje WebMatrix, klikněte na tlačítko **publikovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e957c-151">In the WebMatrix ribbon, click the **Publish** button.</span></span>

!["Publikovat" tlačítko na pásu karet nástroje WebMatrix](publishing/_static/image1.png)

<span data-ttu-id="e957c-153">**Publikovat web** se zobrazí dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="e957c-153">The **Publish Your Site** dialog box is displayed.</span></span> <span data-ttu-id="e957c-154">Pokud nejste přihlášeni k účtu Microsoft, bude obsahovat dialogových oken **Začínáme s Azure** odkaz.</span><span class="sxs-lookup"><span data-stu-id="e957c-154">If you have not signed in to your Microsoft account, the dialog box will contain a **Get started with Azure** link.</span></span> <span data-ttu-id="e957c-155">Kliknutím na tento odkaz.</span><span class="sxs-lookup"><span data-stu-id="e957c-155">Click this link.</span></span>

![Publikování webu](publishing/_static/image2.png)

<span data-ttu-id="e957c-157">Pokud nejste přihlášeni k účtu Microsoft, budete mít znovu příležitost k přihlášení.</span><span class="sxs-lookup"><span data-stu-id="e957c-157">If you have not signed in to a Microsoft account, you are again given the opportunity to sign in.</span></span> <span data-ttu-id="e957c-158">Musíte se přihlásit k účtu Microsoft k publikování webu v Azure.</span><span class="sxs-lookup"><span data-stu-id="e957c-158">You must sign in to a Microsoft account to publish your site on Azure.</span></span>

![Přihlásit se](publishing/_static/image3.png)

<span data-ttu-id="e957c-160">Po přihlášení ke svému účtu Microsoft, dialogové okno obsahuje odkazy na vytvoření nového webu v Azure nebo se připojit k jednomu z existující weby v Azure.</span><span class="sxs-lookup"><span data-stu-id="e957c-160">After signing in to your Microsoft account, the dialog box contains links to create a new site on Azure or connect to one of your existing sites on Azure.</span></span>

![Vytvoří nový web.](publishing/_static/image4.png)

<span data-ttu-id="e957c-162">Vyberte **vytvoření nového webu**.</span><span class="sxs-lookup"><span data-stu-id="e957c-162">Select **Create a new site**.</span></span>

<span data-ttu-id="e957c-163">Pokud název vašeho projektu **WebPagesMovies**, bude mít výchozí název pro svůj web **webpagesmovies.azurewebsites.net**.</span><span class="sxs-lookup"><span data-stu-id="e957c-163">If you named your project **WebPagesMovies**, the default name for your site will be **webpagesmovies.azurewebsites.net**.</span></span> <span data-ttu-id="e957c-164">Tento výchozí název je s největší pravděpodobností není k dispozici, je určeno červený vykřičník.</span><span class="sxs-lookup"><span data-stu-id="e957c-164">This default name is most likely not available, as indicated by the red exclamation mark.</span></span>

![název výchozí webové stránky](publishing/_static/image5.png)

<span data-ttu-id="e957c-166">Změnit název serveru, který je k dispozici a vyberte umístění, které je blízko vaší polohy.</span><span class="sxs-lookup"><span data-stu-id="e957c-166">Change the site name to something that is available, and select a location that is close to your location.</span></span>

![název změněné lokality](publishing/_static/image6.png)

<span data-ttu-id="e957c-168">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="e957c-168">Click **OK**.</span></span>

<span data-ttu-id="e957c-169">Služba WebMatrix performss testu k určení, zda je server kompatibilní s vaší lokality.</span><span class="sxs-lookup"><span data-stu-id="e957c-169">WebMatrix performss a test to determine if the server is compatible with your site.</span></span>

![testování kompatibility](publishing/_static/image7.png)

<span data-ttu-id="e957c-171">Vyberte **pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="e957c-171">Select **Continue**.</span></span>

<span data-ttu-id="e957c-172">Výsledky testování kompatibility se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="e957c-172">The results of the compatibility test are displayed.</span></span>

![výsledek kompatibility](publishing/_static/image8.png)

<span data-ttu-id="e957c-174">Vyberte **pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="e957c-174">Select **Continue**.</span></span>

<span data-ttu-id="e957c-175">Služba WebMatrix zobrazí soubory a databáze, které bude publikována do lokality.</span><span class="sxs-lookup"><span data-stu-id="e957c-175">WebMatrix displays the files and databases that will be published to the site.</span></span> <span data-ttu-id="e957c-176">Protože při prvním publikování webu, jsou uvedeny všechny soubory.</span><span class="sxs-lookup"><span data-stu-id="e957c-176">Since this is the first time you are publishing the site, all of the files are listed.</span></span> <span data-ttu-id="e957c-177">Zrušte zaškrtnutí políčka soubor, který není připravená k publikování.</span><span class="sxs-lookup"><span data-stu-id="e957c-177">You can uncheck a file that is not ready to be published.</span></span> <span data-ttu-id="e957c-178">V následující publikace se zobrazí pouze soubory, které se změnily.</span><span class="sxs-lookup"><span data-stu-id="e957c-178">In the subsequent publications, only the files that have changed will be displayed.</span></span> <span data-ttu-id="e957c-179">Zobrazit [aktualizace živého webu: opětovné publikování](#update).</span><span class="sxs-lookup"><span data-stu-id="e957c-179">See [Updating the Live Site: Republishing](#update).</span></span>

![Náhled publikování](publishing/_static/image9.png)

<span data-ttu-id="e957c-181">Vyberte **pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="e957c-181">Select **Continue**.</span></span>

<span data-ttu-id="e957c-182">Po nasazení webu do Azure, zobrazí se zpráva, která označuje, že dokončení nasazení.</span><span class="sxs-lookup"><span data-stu-id="e957c-182">After the site has been deployed to Azure, a message is displayed that indicates the deployment has completed.</span></span>

![publikování dokončeno](publishing/_static/image10.png)

<span data-ttu-id="e957c-184">Web a databáze byly publikovány do Azure a jsou teď dostupné pro veřejnost.</span><span class="sxs-lookup"><span data-stu-id="e957c-184">Your site and database have been published to Azure, and are now available to the public.</span></span> <span data-ttu-id="e957c-185">Klikněte na odkaz ve zprávě označující publikování dokončí a zobrazí se vám teď váš web.</span><span class="sxs-lookup"><span data-stu-id="e957c-185">Click the link in the message indicating publishing has completed, and you will now see your deployed site.</span></span> <span data-ttu-id="e957c-186">Vy nebo každý, kdo má přístup k Internetu, můžete přidat nebo upravit záznamy v databázi.</span><span class="sxs-lookup"><span data-stu-id="e957c-186">You or anyone with Internet access can add or modify records in the database.</span></span>

![](publishing/_static/image11.png)

<a id="host"></a>
## <a name="publishing-your-site-to-a-web-hosting-company"></a><span data-ttu-id="e957c-187">Publikování webu na webu firma poskytující Hosting</span><span class="sxs-lookup"><span data-stu-id="e957c-187">Publishing Your Site to a Web Hosting Company</span></span>

<span data-ttu-id="e957c-188">Pokud se rozhodnete nebude publikován do Azure, můžete místo toho publikování webu pro webové hostování společnosti.</span><span class="sxs-lookup"><span data-stu-id="e957c-188">If you decide to not publish to Azure, you can instead publish your site to a web hosting company.</span></span>

<span data-ttu-id="e957c-189">Klikněte na tlačítko **najít webhosting** odkaz.</span><span class="sxs-lookup"><span data-stu-id="e957c-189">Click the **Find web hosting** link.</span></span>

![Tlačítka 'Najít hostování webů' v dialogovém okně Nastavení publikování](publishing/_static/image12.png)

<span data-ttu-id="e957c-191">Přejděte na stránku na webu společnosti Microsoft, který obsahuje seznam poskytovatelé hostitelských služeb, které podporují ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e957c-191">You go to a page on the Microsoft site that lists hosting providers that support ASP.NET.</span></span>

![Stránka na webu Microsoftu, který obsahuje seznam poskytovatelů hostingu](publishing/_static/image13.png)

<span data-ttu-id="e957c-193">Samozřejmě může být obtížné zjistit, teď právě jaké funkce hostování může vyžadovat v dlouhodobém horizontu.</span><span class="sxs-lookup"><span data-stu-id="e957c-193">Obviously, it can be difficult to know now exactly what hosting features you might require over the long term.</span></span> <span data-ttu-id="e957c-194">Tady je několik věcí, které byste měli zvážit:</span><span class="sxs-lookup"><span data-stu-id="e957c-194">Here are a couple of things to consider:</span></span>

- <span data-ttu-id="e957c-195">Pro účely WebPagesMovies lokality nemusíte mít samostatné doplněk pro SQL Server, který často velmi náklady.</span><span class="sxs-lookup"><span data-stu-id="e957c-195">For purposes of the WebPagesMovies site, you don't have to have a separate add-on for SQL Server, which often costs extra.</span></span> <span data-ttu-id="e957c-196">Ve vaší lokalitě používáte SQL Server Compact Edition, který je samostatný.</span><span class="sxs-lookup"><span data-stu-id="e957c-196">In your site, you're using SQL Server Compact Edition, which is self-contained.</span></span> <span data-ttu-id="e957c-197">Přístup k SQL serveru však může být zapotřebí pro některé budoucí webu práce, kterou děláte.</span><span class="sxs-lookup"><span data-stu-id="e957c-197">However, you might need SQL Server access for some future website work you do.</span></span> <span data-ttu-id="e957c-198">Pokud se domníváte, že máte, ujistěte se, že funkce SQL serveru můžete později přidat.</span><span class="sxs-lookup"><span data-stu-id="e957c-198">If you think you might, make sure that you can add SQL Server capability later.</span></span>
- <span data-ttu-id="e957c-199">Zkontrolujte, zda poskytovatel hostingu podporuje protokol publikování nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="e957c-199">Check whether the hosting provider supports the Web Deploy publishing protocol.</span></span> <span data-ttu-id="e957c-200">Můžete publikovat pomocí protokolu FTP, ale je pohodlnější službu Web Deploy používat.</span><span class="sxs-lookup"><span data-stu-id="e957c-200">You can publish by using FTP protocol, but it's more convenient to use Web Deploy.</span></span>

<span data-ttu-id="e957c-201">Některé servery nabízejí bezplatné zkušební období.</span><span class="sxs-lookup"><span data-stu-id="e957c-201">Some sites offer a free trial period.</span></span> <span data-ttu-id="e957c-202">Bezplatnou zkušební verzi je dobrým způsobem, jak opakujte publikování a hostování při budete stále experimentování s nástrojem WebMatrix a webové stránky ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e957c-202">A free trial is a good way to try publishing and hosting while you're still experimenting with WebMatrix and ASP.NET Web Pages.</span></span>

<span data-ttu-id="e957c-203">Vyberte si ten, který vám vyhovuje.</span><span class="sxs-lookup"><span data-stu-id="e957c-203">Pick one that you like.</span></span> <span data-ttu-id="e957c-204">Pro účely tohoto kurzu jsme vybrali DiscountASP.NET, protože když jsme vytvářeli kurzu, měli této společnosti v rámci propagační akce, která lidem hostovat lokalitu zdarma po dobu několika měsíců.</span><span class="sxs-lookup"><span data-stu-id="e957c-204">For this tutorial, we selected DiscountASP.NET, because while we were creating the tutorial, that company had a promotion that let people host a site free for a few months.</span></span>

> [!NOTE]
> <span data-ttu-id="e957c-205">Námi zvolený prostřednictvím poskytovatele hostitelských služeb pro účely tohoto kurzu, neměly by být vykládány jako o potvrzení této společnosti přes jakýkoli jiný.</span><span class="sxs-lookup"><span data-stu-id="e957c-205">Our choice of a hosting provider for this tutorial shouldn't be interpreted as an endorsement of that company over any other.</span></span> <span data-ttu-id="e957c-206">Ale jsme měli vybrat jednu pro obrázek a DiscountASP.NET je jednou z mnoha společnosti, které podporuje rozhraní ASP.NET Web Pages a protokolu Webdeploy publikovat.</span><span class="sxs-lookup"><span data-stu-id="e957c-206">But we had to pick one for illustration, and DiscountASP.NET is one of the many companies that supports ASP.NET Web Pages and the Web Deploy protocol for publishing.</span></span>


<span data-ttu-id="e957c-207">Obvykle poté, co jste se zaregistrovali u poskytovatele hostingu, společnost vám pošle e-mailu, který obsahuje uživatelské jméno a heslo, adresu URL webového serveru a tak dále.</span><span class="sxs-lookup"><span data-stu-id="e957c-207">Typically, after you've signed up with the hosting provider, the company sends you an email that contains a user name and password, the URL of the web server, and so on.</span></span> <span data-ttu-id="e957c-208">Pokud hostovací společnost podporuje protokolu Webdeploy, se může odeslat je soubor, který obsahuje nastavení publikování, nebo můžete stáhnout z Internetu.</span><span class="sxs-lookup"><span data-stu-id="e957c-208">If the hosting company supports Web Deploy protocol, they might send you a file that contains publish settings, or let you download one.</span></span> <span data-ttu-id="e957c-209">Soubor nastavení publikování zjednodušuje proces za vás.</span><span class="sxs-lookup"><span data-stu-id="e957c-209">A publish settings file simplifies the process for you.</span></span>

<span data-ttu-id="e957c-210">Pokud jste se zaregistrovali a jste připraveni publikovat, klikněte na tlačítko **publikovat** tlačítko na pásu karet nástroje WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="e957c-210">When you've signed up and are ready to publish, click the **Publish** button in the WebMatrix ribbon.</span></span> <span data-ttu-id="e957c-211">**Nastavení publikování** se zobrazí dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="e957c-211">The **Publish Settings** dialog box is displayed.</span></span>

<span data-ttu-id="e957c-212">V případě poskytovatele hostingu odeslaný soubor nastavení publikování, klikněte na tlačítko **importovat nastavení publikování** propojit a naimportujte ho.</span><span class="sxs-lookup"><span data-stu-id="e957c-212">If the hosting provider sent you a publish settings file, click the **Import publish settings** link and import the file.</span></span> <span data-ttu-id="e957c-213">Pokud nemáte soubor nastavení publikování, vyplňte pole pomocí hodnot, které hostingové společnosti vám poslali e-mailem.</span><span class="sxs-lookup"><span data-stu-id="e957c-213">If you don't have a publish settings file, fill in the fields by using the values that the hosting company sent you in email.</span></span> <span data-ttu-id="e957c-214">Co **nastavení publikování** dialogové okno může vypadat po dokončení:</span><span class="sxs-lookup"><span data-stu-id="e957c-214">Here's what the **Publish Settings** dialog box might look like when you're done:</span></span>

![Nastavení publikování vyplněna v dialogovém okně "nastavení publikování.](publishing/_static/image14.png)

<span data-ttu-id="e957c-216">Klikněte na tlačítko **ověřit připojení**.</span><span class="sxs-lookup"><span data-stu-id="e957c-216">Click **Validate Connection**.</span></span> <span data-ttu-id="e957c-217">Pokud všechno, co je v pořádku, dialogové okno sestavy **úspěšně připojila**, což znamená, že může komunikovat se serverem poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="e957c-217">If everything is ok, the dialog box reports **Connected successfully**, which means it can communicate with the hosting provider's server.</span></span>

![Úspěch zprávu při publikování se používají správná nastavení](publishing/_static/image15.png)

<span data-ttu-id="e957c-219">Pokud je nějaký problém, služba WebMatrix provede nejlépe, říct, že problém je:</span><span class="sxs-lookup"><span data-stu-id="e957c-219">If there's a problem, WebMatrix does its best to tell you what the problem is:</span></span>

![Chybová zpráva, pokud je nějaký problém s nastavením publikování](publishing/_static/image16.png)

<span data-ttu-id="e957c-221">Klikněte na tlačítko **Uložit** uložte nastavení.</span><span class="sxs-lookup"><span data-stu-id="e957c-221">Click **Save** to save your settings.</span></span> <span data-ttu-id="e957c-222">Služba WebMatrix nabízí proveďte test, abyste měli jistotu, že můžete správně komunikují s lokalitou hostingu:</span><span class="sxs-lookup"><span data-stu-id="e957c-222">WebMatrix offers to perform a test to make sure that it can communicate correctly with the hosting site:</span></span>

![Zpráva k provedení testu se proces publikování nabídky](publishing/_static/image17.png)

<span data-ttu-id="e957c-224">Klikněte na tlačítko **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e957c-224">Click **Yes**.</span></span> <span data-ttu-id="e957c-225">Služba WebMatrix některé ukázkové soubory nahraje do poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="e957c-225">WebMatrix uploads some sample files to the hosting provider.</span></span> <span data-ttu-id="e957c-226">Po dokončení testování kompatibility se službou WebMatrix hlásí výsledky:</span><span class="sxs-lookup"><span data-stu-id="e957c-226">When the compatibility test is done, WebMatrix reports the results:</span></span>

![Výsledky testu pro publikování](publishing/_static/image18.png)

<span data-ttu-id="e957c-228">Pokud jste připravení, pokračujte a klepněte na tlačítko **pokračovat** skutečném zahájíte proces publikování.</span><span class="sxs-lookup"><span data-stu-id="e957c-228">If you're ready to go, go ahead and click **Continue** to start the publish process for real.</span></span> <span data-ttu-id="e957c-229">Služba WebMatrix přijde na to, co soubory jsou ve vaší lokalitě a jsou už na hostitelském serveru (ihned, žádné) a nabízí přehled procesu publikování:</span><span class="sxs-lookup"><span data-stu-id="e957c-229">WebMatrix figures out what files are in your site and are already on the host server (right now, none) and gives you a preview of the publish process:</span></span>

![Ve verzi Preview, jaké soubory, které odešlete proces publikování](publishing/_static/image19.png)

<span data-ttu-id="e957c-231">Seznam souborů k publikování, které zahrnuje webové stránky, které jste vytvořili jako *Movies.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e957c-231">The list of files to publish includes the web pages that you've created like *Movies.cshtml*.</span></span> <span data-ttu-id="e957c-232">Seznam obsahuje také soubory pro pomocné funkce, které jste nainstalovali, soubory, které chcete spustit SQL Server Compact Edition pro vaši databázi a tak dále.</span><span class="sxs-lookup"><span data-stu-id="e957c-232">The list also includes files for helpers that you've installed, the files to run SQL Server Compact Edition for your database, and so on.</span></span> <span data-ttu-id="e957c-233">V důsledku toho počáteční procesu publikování může být významné.</span><span class="sxs-lookup"><span data-stu-id="e957c-233">As a result, the initial publish process can be substantial.</span></span>

<span data-ttu-id="e957c-234">Klikněte na tlačítko **pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="e957c-234">Click **Continue**.</span></span> <span data-ttu-id="e957c-235">Služba WebMatrix zkopíruje soubory na server poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="e957c-235">WebMatrix copies your files to the hosting provider's server.</span></span> <span data-ttu-id="e957c-236">Po dokončení, výsledky jsou uvedeny ve stavovém řádku:</span><span class="sxs-lookup"><span data-stu-id="e957c-236">When it's done, the results are reported in the status bar:</span></span>

![Stav panelu zprávy, když proces publikování byl úspěšně dokončen.](publishing/_static/image20.png)

<span data-ttu-id="e957c-238">Pokud chcete zobrazit živého webu, klikněte na odkaz ve stavovém řádku.</span><span class="sxs-lookup"><span data-stu-id="e957c-238">To see your live site, click the link in the status bar.</span></span> <span data-ttu-id="e957c-239">Přidat *filmy* na adresu URL, a zobrazí se vám *Movies.cshtml* soubor, který jste vytvořili:</span><span class="sxs-lookup"><span data-stu-id="e957c-239">Add *Movies* to the URL, and you'll see the *Movies.cshtml* file that you created:</span></span>

![Živý web stránkou filmy](publishing/_static/image21.png)

<a id="update"></a>
## <a name="updating-the-live-site-republishing"></a><span data-ttu-id="e957c-241">Aktualizuje se živého webu: opětovné publikování</span><span class="sxs-lookup"><span data-stu-id="e957c-241">Updating the Live Site: Republishing</span></span>

<span data-ttu-id="e957c-242">Po publikování vaší lokality (do Azure nebo webhosting společnosti), jsou dvě kopie &mdash; verze v počítači a verze na poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="e957c-242">Once you've published your site (to either Azure or a web hosting company), there are two copies of it &mdash; the version on your computer and the version on the service provider.</span></span> <span data-ttu-id="e957c-243">Pravděpodobně budete chtít pokračovat ve vývoji webu (Pokud nic jiného, další sérii kurzů v rámci).</span><span class="sxs-lookup"><span data-stu-id="e957c-243">You'll probably want to continue developing the site (if nothing else, as part of the next tutorial set).</span></span> <span data-ttu-id="e957c-244">Když použijete, budete muset znovu publikovat svůj web, aby bylo možné kopírovat změny z vašeho počítače k poskytovateli služby.</span><span class="sxs-lookup"><span data-stu-id="e957c-244">When you do, you have to republish your site in order to copy changes from your computer to the service provider.</span></span> <span data-ttu-id="e957c-245">Proces publikování ve Webmatrixu můžete zjistit, co byly změněné soubory na váš web a publikovat pouze tyto soubory.</span><span class="sxs-lookup"><span data-stu-id="e957c-245">The publish process in WebMatrix can determine what files have changed on your site and publish just those files.</span></span>

<span data-ttu-id="e957c-246">Chcete-li zjistit, jak funguje znovu publikovat, otevřete *Movies.cshtml* webu, proveďte nějakou změnu (krátkodobé používání) a pak soubor uložte.</span><span class="sxs-lookup"><span data-stu-id="e957c-246">To see how republishing works, open the *Movies.cshtml* site, make some small change, and then save the file.</span></span> <span data-ttu-id="e957c-247">Například změnit nadpis `Movies - Updated`.</span><span class="sxs-lookup"><span data-stu-id="e957c-247">For example, change the title to `Movies - Updated`.</span></span>

<span data-ttu-id="e957c-248">Klikněte na tlačítko **publikovat** tlačítko na pásu karet.</span><span class="sxs-lookup"><span data-stu-id="e957c-248">Click the **Publish** button in the ribbon.</span></span> <span data-ttu-id="e957c-249">Služba WebMatrix Určuje, co se změnilo a zobrazí náhled souborů, které budete publikovat.</span><span class="sxs-lookup"><span data-stu-id="e957c-249">WebMatrix determines what's changed and shows you a preview of the files it will publish.</span></span>

![Dialogové okno "Publikovat" zobrazující změněné soubory připravené k opětovné publikování](publishing/_static/image22.png)

> [!IMPORTANT] 
> 
> <span data-ttu-id="e957c-251">Ve výchozím nastavení, služba WebMatrix publikuje vaši databázi (*SDF* souboru) pouze při prvním publikování webu.</span><span class="sxs-lookup"><span data-stu-id="e957c-251">By default, WebMatrix publishes your database (*.sdf* file) only the first time you publish the site.</span></span> <span data-ttu-id="e957c-252">Jakmile se vaše lokalita je publikována a uživatelé interagují s webem, databázi na živém webu má obvykle reálná data lokality.</span><span class="sxs-lookup"><span data-stu-id="e957c-252">Once your site is published and people are interacting with the website, the database on the live site typically has the site's real data.</span></span> <span data-ttu-id="e957c-253">Je nutné přepsat živé databáze s velmi opatrní *SDF* soubor, který je v počítači, který obvykle obsahuje pouze testovací data.</span><span class="sxs-lookup"><span data-stu-id="e957c-253">You have to be very careful not to overwrite the live database with the *.sdf* file that's on your computer, which usually contains only test data.</span></span> <span data-ttu-id="e957c-254">To je důvod, proč se zobrazí upozornění **publikování se přepíšou všechny vzdálené databáze**, a proč zaškrtnutí políčka *WebPagesMovies.sdf* ve výchozím nastavení zaškrtnuto.</span><span class="sxs-lookup"><span data-stu-id="e957c-254">That's why you see the warning **Publishing will overwrite any remote databases**, and why the check box for *WebPagesMovies.sdf* is cleared by default.</span></span>


<span data-ttu-id="e957c-255">Klikněte na tlačítko **pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="e957c-255">Click **Continue**.</span></span> <span data-ttu-id="e957c-256">Služba WebMatrix publikuje změněných souborů a zobrazí zprávu o úspěšném dokončení stejně, jako kdyby poprvé, kterou jste publikovali.</span><span class="sxs-lookup"><span data-stu-id="e957c-256">WebMatrix publishes the changed files and shows you a success message, like it did the first time you published.</span></span>

<span data-ttu-id="e957c-257">Přejít do živého webu (můžete kliknout na odkaz v zpráva o úspěchu Pokud je stále zobrazena) a ověřte, že vaše změny se publikoval.</span><span class="sxs-lookup"><span data-stu-id="e957c-257">Go to the live site (you can click the link in the success message if it's still showing) and verify that your change has been published.</span></span>

> [!TIP] 
> 
> <span data-ttu-id="e957c-258">**Vzdálené úpravy souborů**</span><span class="sxs-lookup"><span data-stu-id="e957c-258">**Editing Files Remotely**</span></span>
> 
> <span data-ttu-id="e957c-259">Jako alternativu k změně webu a pak znovu publikovat můžete upravit vzdálené soubory přímo ve službě WebMatrix.</span><span class="sxs-lookup"><span data-stu-id="e957c-259">As an alternative to changing your site and then republishing, you can edit remote files directly in WebMatrix.</span></span> <span data-ttu-id="e957c-260">V tomto scénáři otevřete soubor, který je u poskytovatelů služeb a služba WebMatrix stáhne kopii jej můžete upravit.</span><span class="sxs-lookup"><span data-stu-id="e957c-260">In this scenario, you open a file that's on the service provider, and WebMatrix downloads a copy of it for you to edit.</span></span> <span data-ttu-id="e957c-261">Pokaždé, když se soubor uložíte, služba WebMatrix odešle změny do lokality.</span><span class="sxs-lookup"><span data-stu-id="e957c-261">Every time you save the file, WebMatrix sends the changes to the site.</span></span>
> 
> <span data-ttu-id="e957c-262">Vzdálené úpravy je snadný způsob, jak provádět změny živého webu.</span><span class="sxs-lookup"><span data-stu-id="e957c-262">Remote editing is an easy way to make changes to your live site.</span></span> <span data-ttu-id="e957c-263">Změny provedené tímto způsobem se však nejsou synchronizované s souborů ve vaší místní lokalitě.</span><span class="sxs-lookup"><span data-stu-id="e957c-263">However, the changes you make this way aren't synchronized with the files in your local site.</span></span> <span data-ttu-id="e957c-264">K synchronizaci místních souborů s vzdálené lokality, může stahování vzdálených souborů.</span><span class="sxs-lookup"><span data-stu-id="e957c-264">To synchronize the local files with the remote site, you can download the remote files.</span></span> <span data-ttu-id="e957c-265">Tento proces funguje stejně jako publikování, s výjimkou v opačném pořadí.</span><span class="sxs-lookup"><span data-stu-id="e957c-265">This process works much like publishing, except in reverse.</span></span>
> 
> <span data-ttu-id="e957c-266">Nebude popisujeme více o vzdálené úpravy a stáhnout vzdálené zařízení WebMatrix tady.</span><span class="sxs-lookup"><span data-stu-id="e957c-266">We won't describe more about the remote-editing and remote-download facilities of WebMatrix here.</span></span> <span data-ttu-id="e957c-267">Jsou velmi užitečné, pokud máte více lidem pracovat na stejném místě na různých počítačích.</span><span class="sxs-lookup"><span data-stu-id="e957c-267">They're quite useful if multiple people have to work on the same site on different computers.</span></span> <span data-ttu-id="e957c-268">Další informace najdete v tématu [publikování a úprava vzdálené lokality pomocí služby WebMatrix 2 Beta](https://go.microsoft.com/fwlink/?LinkId=251591).</span><span class="sxs-lookup"><span data-stu-id="e957c-268">For more information, see [Publish and Edit a Remote Site with WebMatrix 2 Beta](https://go.microsoft.com/fwlink/?LinkId=251591).</span></span>


## <a name="additional-resources"></a><span data-ttu-id="e957c-269">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="e957c-269">Additional Resources</span></span>

- <span data-ttu-id="e957c-270">[Fórum služby WebMatrix rozhraní ASP.NET Web Pages ASP.NET](https://forums.asp.net/1224.aspx/1?WebMatrix+and+ASP+NET+Web+Pages)skvělým místem pro zveřejňování otázky a odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e957c-270">[ASP.NET WebMatrix ASP.NET Web Pages forum](https://forums.asp.net/1224.aspx/1?WebMatrix+and+ASP+NET+Web+Pages), a great place to post questions and get answers.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="e957c-271">Předchozí</span><span class="sxs-lookup"><span data-stu-id="e957c-271">Previous</span></span>](layouts.md)

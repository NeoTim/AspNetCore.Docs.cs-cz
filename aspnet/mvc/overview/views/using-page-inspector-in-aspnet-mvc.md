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
<a name="using-page-inspector-in-aspnet-mvc"></a><span data-ttu-id="2b88d-104">Použití Page Inspectoru v ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="2b88d-104">Using Page Inspector in ASP.NET MVC</span></span>
====================
<span data-ttu-id="2b88d-105">podle Tim Ammann</span><span class="sxs-lookup"><span data-stu-id="2b88d-105">by Tim Ammann</span></span>

> <span data-ttu-id="2b88d-106">Nástroj Page Inspector v sadě Visual Studio 2012 je nástroj pro vývoj webů pomocí integrovaného prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2b88d-106">Page Inspector in Visual Studio 2012 is a web development tool with an integrated browser.</span></span> <span data-ttu-id="2b88d-107">Vybrat jakýkoli element ve integrovaného prohlížeče a nástroj Page Inspector okamžitě zvýrazní zdroje a šablon stylů CSS prvku.</span><span class="sxs-lookup"><span data-stu-id="2b88d-107">Select any element in the integrated browser, and Page Inspector instantly highlights the element's source and CSS.</span></span> <span data-ttu-id="2b88d-108">Můžete procházet všechna zobrazení MVC, rychle najít zdroje vykreslované značky a použít nástroje prohlížeče přímo v prostředí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2b88d-108">You can browse any MVC view, quickly find the sources of rendered markup, and use browser tools right within the Visual Studio environment.</span></span>
> 
> [<span data-ttu-id="2b88d-109">Podívejte se na Video</span><span class="sxs-lookup"><span data-stu-id="2b88d-109">Watch the Video</span></span>](../../videos/mvc-4/using-page-inspector-in-aspnet-mvc.md)
> 
> <span data-ttu-id="2b88d-110">Tento kurz ukazuje, jak povolit režim kontroly a rychle najít a upravit značky a šablony stylů CSS v rámci webového projektu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-110">This tutorial shows how to enable Inspection Mode, and then quickly locate and edit markup and CSS within your web project.</span></span> <span data-ttu-id="2b88d-111">V tomto kurzu použijete projektu aplikace MVC, ale můžete použít také nástroje Page Inspector pro [webových formulářů](https://go.microsoft.com/?linkid=9802001) a dalších aplikací ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="2b88d-111">The tutorial uses an MVC Project, but you can also use Page Inspector for [Web Forms](https://go.microsoft.com/?linkid=9802001) and other ASP.NET applications.</span></span>
> 
> <span data-ttu-id="2b88d-112">Tento kurz obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="2b88d-112">The tutorial has the following sections:</span></span>
> 
> - [<span data-ttu-id="2b88d-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2b88d-113">Prerequisites</span></span>](#_1_prerequisites)
> - [<span data-ttu-id="2b88d-114">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="2b88d-114">Create a Web Application</span></span>](#_2_creating_a)
> - [<span data-ttu-id="2b88d-115">Použití Page Inspectoru pro přejděte do zobrazení</span><span class="sxs-lookup"><span data-stu-id="2b88d-115">Use Page Inspector to Browse to a View</span></span>](#_3_using_page)
> - [<span data-ttu-id="2b88d-116">Povolit režim kontroly</span><span class="sxs-lookup"><span data-stu-id="2b88d-116">Enable Inspection Mode</span></span>](#_4_inspection_mode)
> - [<span data-ttu-id="2b88d-117">Použití Page Inspectoru provádět změny kódu</span><span class="sxs-lookup"><span data-stu-id="2b88d-117">Use Page Inspector to Make Changes to Markup</span></span>](#_5_using_page)
> - [<span data-ttu-id="2b88d-118">Režim kontroly a v okně HTML</span><span class="sxs-lookup"><span data-stu-id="2b88d-118">Inspection Mode and the HTML Window</span></span>](#_6_inspection_mode)
> - [<span data-ttu-id="2b88d-119">Náhled změn šablon stylů CSS v okně Styly</span><span class="sxs-lookup"><span data-stu-id="2b88d-119">Preview CSS Changes in the Styles window</span></span>](#_7_previewing_css)
> - [<span data-ttu-id="2b88d-120">Automatická synchronizace šablon stylů CSS</span><span class="sxs-lookup"><span data-stu-id="2b88d-120">CSS Auto Sync</span></span>](#css_auto_sync)
> - [<span data-ttu-id="2b88d-121">Výběr barvy šablon stylů CSS</span><span class="sxs-lookup"><span data-stu-id="2b88d-121">Using the CSS Color Picker</span></span>](#css_color_picker)
> - [<span data-ttu-id="2b88d-122">Mapování elementů dynamických stránek pro jazyk JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b88d-122">Mapping Dynamic Page Elements to JavaScript</span></span>](#map_dynamic_elements)


<a id="_prerequisites"></a><a id="_1_prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="2b88d-123">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2b88d-123">Prerequisites</span></span>

- <span data-ttu-id="2b88d-124">[Visual Studio 2012](https://www.microsoft.com/visualstudio/11) nebo [sady Visual Studio Express 2012 pro Web](https://www.microsoft.com/visualstudio/11/downloads#express-web).</span><span class="sxs-lookup"><span data-stu-id="2b88d-124">[Visual Studio 2012](https://www.microsoft.com/visualstudio/11) or [Visual Studio Express 2012 for Web](https://www.microsoft.com/visualstudio/11/downloads#express-web).</span></span>

> [!NOTE]
> <span data-ttu-id="2b88d-125">Chcete-li získat nejnovější verzi nástroje Page Inspector, použijte [instalačního programu webové platformy](https://go.microsoft.com/fwlink/?LinkId=255386) k instalaci sady Windows Azure SDK pro .NET 2.0.</span><span class="sxs-lookup"><span data-stu-id="2b88d-125">To get the latest version of Page Inspector, use [Web Platform Installer](https://go.microsoft.com/fwlink/?LinkId=255386) to install the Windows Azure SDK for .NET 2.0.</span></span>


<span data-ttu-id="2b88d-126">Nástroj Page Inspector je součástí nástroje Microsoft Web Developer Tools.</span><span class="sxs-lookup"><span data-stu-id="2b88d-126">Page Inspector is bundled with Microsoft Web Developer Tools.</span></span> <span data-ttu-id="2b88d-127">Nejnovější verze je verze 1.3.</span><span class="sxs-lookup"><span data-stu-id="2b88d-127">The latest version is 1.3.</span></span> <span data-ttu-id="2b88d-128">Zjištění verze máte, spusťte Visual Studio a vyberte **o Microsoft Visual Studio** z **pomáhají** nabídky.</span><span class="sxs-lookup"><span data-stu-id="2b88d-128">To check which version you have, run Visual Studio and select **About Microsoft Visual Studio** from the **Help** menu.</span></span>

<a id="_creating_a_web"></a><a id="_2_creating_a"></a>

## <a name="create-a-web-application"></a><span data-ttu-id="2b88d-129">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="2b88d-129">Create a Web Application</span></span>

<span data-ttu-id="2b88d-130">Nejprve vytvořte webovou aplikaci, kterou budete používat nástroj Page Inspector se.</span><span class="sxs-lookup"><span data-stu-id="2b88d-130">First, create a web application that you will use Page Inspector with.</span></span> <span data-ttu-id="2b88d-131">V sadě Visual Studio, zvolte **souboru** &gt; **nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-131">In Visual Studio, choose **File** &gt; **New Project**.</span></span> <span data-ttu-id="2b88d-132">Na levé straně rozbalte **Visual C#** vyberte **webové**a pak vyberte **webová aplikace ASP.NET MVC4**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-132">On the left, expand **Visual C#**, select **Web**, and then select **ASP.NET MVC4 Web Application**.</span></span>

![Nové aplikace ASP.NET MVC](using-page-inspector-in-aspnet-mvc/_static/image2.png)

<span data-ttu-id="2b88d-134">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-134">Click **OK**.</span></span>

<span data-ttu-id="2b88d-135">V **nového projektu ASP.NET MVC 4** dialogu **internetovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-135">In the **New ASP.NET MVC 4 Project** dialog box, select **Internet Application**.</span></span> <span data-ttu-id="2b88d-136">Ponechte **Razor** jako výchozí modul zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2b88d-136">Leave **Razor** as the default view engine.</span></span>

![Nový projekt ASP.NET MVC – Internetové aplikace.](using-page-inspector-in-aspnet-mvc/_static/image4.png)

<span data-ttu-id="2b88d-138">Aplikace se otevře v **zdroj** zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2b88d-138">The application opens in **Source** view.</span></span>

![Nové aplikace ASP.NET MVC v zobrazení zdroje](using-page-inspector-in-aspnet-mvc/_static/image6.png)

<span data-ttu-id="2b88d-140">Teď, když máte aplikaci pro práci s, můžete použít nástroj Page Inspector sloužící ke zkoumání a upravte ho.</span><span class="sxs-lookup"><span data-stu-id="2b88d-140">Now that you have an application to work with, you can use Page Inspector to examine and modify it.</span></span>

<a id="_starting_page_inspector"></a><a id="_3_using_page"></a>

## <a name="use-page-inspector-to-browse-to-a-view"></a><span data-ttu-id="2b88d-141">Použití Page Inspectoru pro přejděte do zobrazení</span><span class="sxs-lookup"><span data-stu-id="2b88d-141">Use Page Inspector to Browse to a View</span></span>

<span data-ttu-id="2b88d-142">V sadě Visual Studio 2012, je můžete klikněte pravým tlačítkem na libovolné zobrazení ve vašem projektu, vyberte **zobrazení v nástroje Page Inspector**, a nástroj Page Inspector se zjistit trasy a zobrazení stránky.</span><span class="sxs-lookup"><span data-stu-id="2b88d-142">In Visual Studio 2012, you can right-click any view in your project, select **View in Page Inspector**, and Page Inspector will figure out the route and display the page.</span></span>

<span data-ttu-id="2b88d-143">V **Průzkumníka řešení**, rozbalte **zobrazení** složku a potom **Domů** složky.</span><span class="sxs-lookup"><span data-stu-id="2b88d-143">In **Solution Explorer**, expand the **Views** folder and then the **Home** folder.</span></span> <span data-ttu-id="2b88d-144">Souboru Index.cshtml klikněte pravým tlačítkem myši a zvolte **zobrazení v nástroje Page Inspector**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-144">Right click the Index.cshtml file and choose **View in Page Inspector**.</span></span>

![Zobrazení index.cshtm ve nástroj Page Inspector](using-page-inspector-in-aspnet-mvc/_static/image8.png)

<span data-ttu-id="2b88d-146">Ve výchozím nastavení nástroj Page Inspector je ukotven jako okno na levé straně prostředí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2b88d-146">By default, Page Inspector is docked as a window on the left side of the Visual Studio environment.</span></span> <span data-ttu-id="2b88d-147">Pokud dáváte přednost, můžete ukotvit jinde, nebo zrušit ukotvení v okně.</span><span class="sxs-lookup"><span data-stu-id="2b88d-147">If you prefer, you can dock it elsewhere, or undock the window.</span></span> <span data-ttu-id="2b88d-148">Zobrazit [jak: Rozvržení a dokování Windows](https://msdn.microsoft.com/library/z4y0hsax.aspx).</span><span class="sxs-lookup"><span data-stu-id="2b88d-148">See [How to: Arrange and Dock Windows](https://msdn.microsoft.com/library/z4y0hsax.aspx).</span></span>

<span data-ttu-id="2b88d-149">Horní podokno okna nástroje Page Inspector aktuální stránky zobrazí v okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2b88d-149">The top pane of the Page Inspector window shows the current page in a browser window.</span></span> <span data-ttu-id="2b88d-150">V dolním podokně zobrazí na stránce v kódu HTML, spolu s některé karty, která umožňují kontrolovat různé aspekty stránky.</span><span class="sxs-lookup"><span data-stu-id="2b88d-150">The bottom pane shows the page in HTML markup, along with some tabs that let you inspect different aspects of the page.</span></span> <span data-ttu-id="2b88d-151">Dolní podokno je podobný [vývojářské nástroje F12 pomáhají](https://msdn.microsoft.com/ie/aa740478) v aplikaci Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="2b88d-151">The bottom pane is similar to the [F12 Developer Tools](https://msdn.microsoft.com/ie/aa740478) in Internet Explorer.</span></span>

![Aplikace ASP.NET MVC do nástroje Page Inspector](using-page-inspector-in-aspnet-mvc/_static/image10.png)

<span data-ttu-id="2b88d-153">V tomto kurzu budete používat **HTML** a **styly** karet umožňuje rychle přejít a provádět změny aplikace.</span><span class="sxs-lookup"><span data-stu-id="2b88d-153">In this tutorial, you will use the **HTML** and **Styles** tabs to navigate quickly and make changes to the application.</span></span>

<a id="_examining_(&quot;decomposing&quot;)_the"></a><a id="_inspection_mode_and"></a><a id="_4_inspection_mode"></a>

## <a name="enableinspection-mode"></a><span data-ttu-id="2b88d-154">Režim EnableInspection</span><span class="sxs-lookup"><span data-stu-id="2b88d-154">EnableInspection Mode</span></span>

<span data-ttu-id="2b88d-155">Nástroj Page Inspector převést do režimu kontroly, klikněte na tlačítko **zkontrolujte, jestli se** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="2b88d-155">To put Page Inspector into Inspection Mode, click the **Inspect** button.</span></span> <span data-ttu-id="2b88d-156">V režimu kontroly když podržíte ukazatel myši nad libovolné části na vykreslené stránce odpovídající zdrojový kód nebo kód je zvýrazněn.</span><span class="sxs-lookup"><span data-stu-id="2b88d-156">In Inspection Mode, when you hold the mouse pointer over any part of the rendered page, the corresponding source markup or code is highlighted.</span></span>

![Přepnout režim kontroly](using-page-inspector-in-aspnet-mvc/_static/image12.png)

<span data-ttu-id="2b88d-158">Nyní najeďte myší různé části stránky v rámci nástroje Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-158">Now move your mouse over different parts of the page within Page Inspector.</span></span> <span data-ttu-id="2b88d-159">Stejně jako, ukazatel myši se změní na velké znaménko plus a zvýrazní prvek pod:</span><span class="sxs-lookup"><span data-stu-id="2b88d-159">As you do, the mouse pointer changes to a large plus sign, and the element underneath is highlighted:</span></span>

![Ukazatel myši div.content obálky](using-page-inspector-in-aspnet-mvc/_static/image14.png)

<span data-ttu-id="2b88d-161">Při přesunu ukazatele myši, Visual Studio zvýrazní odpovídající syntaxe Razor ve zdrojovém souboru.</span><span class="sxs-lookup"><span data-stu-id="2b88d-161">As you move the mouse pointer, Visual Studio highlights the corresponding Razor syntax in the source file.</span></span> <span data-ttu-id="2b88d-162">Pokud prvek HTML pochází z jiného zdrojového souboru, Visual Studio automaticky otevře soubor.</span><span class="sxs-lookup"><span data-stu-id="2b88d-162">If the HTML element comes from another source file, Visual Studio automatically opens the file.</span></span>

<span data-ttu-id="2b88d-163">V nástroje Page Inspector **HTML** kartě se zobrazí kód HTML, který byl vygenerován v syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="2b88d-163">In Page Inspector, the **HTML** tab shows the HTML that was generated from the Razor syntax.</span></span> <span data-ttu-id="2b88d-164">Při přesunu ukazatele myši, jsou zvýrazněny elementů HTML.</span><span class="sxs-lookup"><span data-stu-id="2b88d-164">As you move the mouse pointer, the HTML elements are highlighted.</span></span> <span data-ttu-id="2b88d-165">**Styly** karta zobrazuje pravidla šablon stylů CSS prvku.</span><span class="sxs-lookup"><span data-stu-id="2b88d-165">The **Styles** tab shows the CSS rules for the element.</span></span>

<a id="_5_using_page"></a>

## <a name="use-page-inspector-to-make-changes-to-markup"></a><span data-ttu-id="2b88d-166">Použití Page Inspectoru provádět změny kódu</span><span class="sxs-lookup"><span data-stu-id="2b88d-166">Use Page Inspector to Make Changes to Markup</span></span>

<span data-ttu-id="2b88d-167">Nástroj Page Inspector vám umožní najít značek, jehož umístění nemusí být zřejmé.</span><span class="sxs-lookup"><span data-stu-id="2b88d-167">Page Inspector lets you find markup whose location might not be obvious.</span></span> <span data-ttu-id="2b88d-168">Potom můžete upravit značky a zobrazit výsledné změny.</span><span class="sxs-lookup"><span data-stu-id="2b88d-168">Then you can modify the markup and see the resulting changes.</span></span>

<span data-ttu-id="2b88d-169">Tento údaj zobrazíte, klikněte na tlačítko **zkontrolujte, jestli se** a sjeďte k dolnímu okraji stránky v okně nástroje Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-169">To see this, click **Inspect** and then scroll to the bottom of the page in the Page Inspector window.</span></span>

<span data-ttu-id="2b88d-170">Při přesunutí ukazatele myši do oblasti zápatí, otevře se nástroj Page Inspector \_Layout.cshtml soubor a zvýrazní část rozložení stránky, kterou jste vybrali.</span><span class="sxs-lookup"><span data-stu-id="2b88d-170">When you move the mouse pointer into the footer area, Page Inspector opens the \_Layout.cshtml file and highlights the section of the layout page that you have selected.</span></span> <span data-ttu-id="2b88d-171">Jak je vidět v zápatí je uvedené jsou definované v souboru rozložení a ne zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2b88d-171">As you can see, the footer are is defined in the layout file, and not the view itself.</span></span>

![Zápatí](using-page-inspector-in-aspnet-mvc/_static/image16.png)

<span data-ttu-id="2b88d-173">Nyní přesunout ukazatel myši nad řádek s copyrightu <a id="a"> </a>Všimněte si, že.</span><span class="sxs-lookup"><span data-stu-id="2b88d-173">Now move your mouse pointer over the line with the copyright <a id="a"></a>notice.</span></span> <span data-ttu-id="2b88d-174">V \_stránku Layout.cshtml, odpovídajícím řádku se zvýrazní.</span><span class="sxs-lookup"><span data-stu-id="2b88d-174">In the \_Layout.cshtml page, the corresponding line is highlighted.</span></span>

![Zápatí o autorských právech řádek zvýrazněný](using-page-inspector-in-aspnet-mvc/_static/image18.png)

<span data-ttu-id="2b88d-176">Přidejte nějaký text do konce řádku v \_souboru Layout.cshtml.</span><span class="sxs-lookup"><span data-stu-id="2b88d-176">Add some text to the end of the line in the \_Layout.cshtml file.</span></span>

<span data-ttu-id="2b88d-177">&lt;p&gt;&amp;kopírovat; @DateTime.Now.Year – Moje aplikace ASP.NET MVC Rocks! &lt;/p&gt;</span><span class="sxs-lookup"><span data-stu-id="2b88d-177">&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET MVC Application Rocks!&lt;/p&gt;</span></span>

<span data-ttu-id="2b88d-178">Nyní stiskněte kombinaci kláves Ctrl + Alt + Enter nebo klikněte na panel aktualizace pro zobrazení výsledků v okně prohlížeče nástroj Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-178">Now, press Ctrl+Alt+Enter or click the Update Bar to see the results in the Page Inspector browser window.</span></span>

![My ASP.NET Application Rocks!](using-page-inspector-in-aspnet-mvc/_static/image20.png)

<span data-ttu-id="2b88d-180">Může mít představit, že definované v zápatí je uvedené v Index.cshtml, ale ukázalo v \_Layout.cshtml a nástroj Page Inspector zjistila, že pro vás.</span><span class="sxs-lookup"><span data-stu-id="2b88d-180">You might have thought that the footer defined in Index.cshtml, but it turned out to be in the \_Layout.cshtml, and Page Inspector found it for you.</span></span>

<a id="_inspection_mode_and_1"></a><a id="_6_inspection_mode"></a>

## <a name="inspection-mode-and-the-html-window"></a><span data-ttu-id="2b88d-181">Režim kontroly a v okně HTML</span><span class="sxs-lookup"><span data-stu-id="2b88d-181">Inspection Mode and the HTML Window</span></span>

<span data-ttu-id="2b88d-182">V dalším kroku bude mít rychlý pohled na okno HTML a jak mapuje prvky za vás.</span><span class="sxs-lookup"><span data-stu-id="2b88d-182">Next, you will have a quick look at the HTML window and how it maps elements for you.</span></span>

<span data-ttu-id="2b88d-183">Klikněte na tlačítko **zkontrolujte, jestli se** uvést do režimu kontroly nástroje Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-183">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="2b88d-184">Klepněte na horní část stránky, která říká "logohere".</span><span class="sxs-lookup"><span data-stu-id="2b88d-184">Click the top part of the page that says "Your logohere".</span></span> <span data-ttu-id="2b88d-185">Prohlížené konkrétní element podrobněji, tak zobrazení v okně prohlížeče už mění při přesunutí ukazatele myši.</span><span class="sxs-lookup"><span data-stu-id="2b88d-185">You are examining a particular element in more detail, so the display in the browser window no longer changes as you move the mouse pointer.</span></span>

<span data-ttu-id="2b88d-186">Nyní přesunutí ukazatele myši **HTML** okna.</span><span class="sxs-lookup"><span data-stu-id="2b88d-186">Now move the mouse pointer to the **HTML** window.</span></span> <span data-ttu-id="2b88d-187">Při přesunu ukazatele myši, nástroj Page Inspector popisuje element v rámci **HTML** okno a zvýrazní odpovídající element v okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2b88d-187">As you move the mouse pointer, Page Inspector outlines the element within the **HTML** window and highlights the corresponding element in the browser window.</span></span>

![Okno HTML](using-page-inspector-in-aspnet-mvc/_static/image22.png)

<span data-ttu-id="2b88d-189">Jako předtím, otevře se nástroj Page Inspector \_Layout.cshtml souboru, který v dočasné kartu. Klikněte na tlačítko \_Layout.cshtml dočasné kartu a odpovídající značky budou zvýrazněny ve &lt;záhlaví&gt; část za vás:</span><span class="sxs-lookup"><span data-stu-id="2b88d-189">As before, Page Inspector opens the \_Layout.cshtml file for you in a temporary tab. Click the \_Layout.cshtml temporary tab, and the corresponding markup will be highlighted in the &lt;header&gt; section for you:</span></span>

![Zvýrazněná značka](using-page-inspector-in-aspnet-mvc/_static/image24.png)

<a id="_using_page_inspector"></a><a id="_7_previewing_css"></a>

## <a name="preview-css-changes-in-the-styles-window"></a><span data-ttu-id="2b88d-191">Náhled změn šablon stylů CSS v okně Styly</span><span class="sxs-lookup"><span data-stu-id="2b88d-191">Preview CSS Changes in the Styles Window</span></span>

<span data-ttu-id="2b88d-192">V dalším kroku použijete nástroj Page Inspector **styly** okna Náhled změn do šablony stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="2b88d-192">Next, you will use the Page Inspector **Styles** window to preview changes to CSS.</span></span>

<span data-ttu-id="2b88d-193">Klikněte na tlačítko **zkontrolujte, jestli se** uvést do režimu kontroly nástroje Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-193">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="2b88d-194">V okně prohlížeče nástroj Page Inspector, přesuňte ukazatel myši nad oddíl "Home Page" až do **div.content obálky** popisek se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="2b88d-194">In the Page Inspector browser window, move the mouse pointer over the "Home Page" section until the **div.content-wrapper** label appears.</span></span>

![Ukazatel myši div.content obálky](using-page-inspector-in-aspnet-mvc/_static/image26.png)

<span data-ttu-id="2b88d-196">Klikněte v části div.content obálky jednou a poté přesuňte ukazatel myši na **styly** okna.</span><span class="sxs-lookup"><span data-stu-id="2b88d-196">Click within the div.content-wrapper section once, and then move the mouse pointer to the **Styles** window.</span></span> <span data-ttu-id="2b88d-197">**Styly** okno zobrazuje všechna pravidla šablon stylů CSS u tohoto elementu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-197">The **Styles** window shows all of the CSS rules for this element.</span></span> <span data-ttu-id="2b88d-198">Posuňte se dolů najít .featured .content – obálky třídy selektor.</span><span class="sxs-lookup"><span data-stu-id="2b88d-198">Scroll down to find the .featured .content-wrapper class selector.</span></span> <span data-ttu-id="2b88d-199">Nyní zrušte zaškrtnutí políčka pro vlastnost barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="2b88d-199">Now clear the checkbox for the background-color property.</span></span>

![Barva pozadí vymazat](using-page-inspector-in-aspnet-mvc/_static/image28.png)

<span data-ttu-id="2b88d-201">Všimněte si, jak tato změna přináší náhled okamžitě v okně prohlížeče nástroj Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-201">Notice how the change previews instantly in the Page Inspector browser window.</span></span>

<span data-ttu-id="2b88d-202">Zaškrtněte toto políčko znovu, klikněte dvakrát na hodnotu vlastnosti a změní na červený.</span><span class="sxs-lookup"><span data-stu-id="2b88d-202">Select the checkbox again, then double-click the property value and change it to red.</span></span> <span data-ttu-id="2b88d-203">Změna ukazuje hned:</span><span class="sxs-lookup"><span data-stu-id="2b88d-203">The change shows immediately:</span></span>

![Barva pozadí Red](using-page-inspector-in-aspnet-mvc/_static/image30.png)

<span data-ttu-id="2b88d-205">**Styly** okno umožňuje snadno test a náhled šablon stylů CSS změní před potvrzením změn na styl listu samotný.</span><span class="sxs-lookup"><span data-stu-id="2b88d-205">The **Styles** window makes it easy to test and preview CSS changes before you commit the changes to the style sheet itself.</span></span>

<a id="css_auto_sync"></a>
## <a name="css-auto-sync"></a><span data-ttu-id="2b88d-206">Automatická synchronizace šablon stylů CSS</span><span class="sxs-lookup"><span data-stu-id="2b88d-206">CSS Auto Sync</span></span>

> [!NOTE]
> <span data-ttu-id="2b88d-207">Tato funkce vyžaduje verzi 1.3 nástroj Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-207">This feature requires version 1.3 of Page Inspector.</span></span>


<span data-ttu-id="2b88d-208">Funkce Automatická synchronizace šablon stylů CSS lze upravit přímo soubor šablony stylů CSS a podívejte se změny okamžitě v prohlížeči nástroj Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-208">The CSS Auto-Sync feature allows you to edit a CSS file directly, and see the changes immediately in the Page Inspector browser.</span></span>

<span data-ttu-id="2b88d-209">Klikněte na tlačítko **zkontrolujte, jestli se** uvést do režimu kontroly nástroje Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-209">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="2b88d-210">V prohlížeči nástroj Page Inspector, přesuňte ukazatel myši nad oddíl "Home Page" až do **div.content obálky** popisek se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="2b88d-210">In the Page Inspector browser, move the mouse pointer over the "Home Page" section until the **div.content-wrapper** label appears.</span></span> <span data-ttu-id="2b88d-211">Kliknutím vyberte tento element.</span><span class="sxs-lookup"><span data-stu-id="2b88d-211">Click once to select this element.</span></span>

<span data-ttu-id="2b88d-212">**Styly** okno zobrazuje všechna pravidla šablon stylů CSS u tohoto elementu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-212">The **Styles** window shows all of the CSS rules for this element.</span></span> <span data-ttu-id="2b88d-213">Posuňte se dolů najít .featured .content – obálky třídy selektor.</span><span class="sxs-lookup"><span data-stu-id="2b88d-213">Scroll down to find the .featured .content-wrapper class selector.</span></span> <span data-ttu-id="2b88d-214">Klikněte na ".featured .content obálku".</span><span class="sxs-lookup"><span data-stu-id="2b88d-214">Click on ".featured .content-wrapper".</span></span> <span data-ttu-id="2b88d-215">Nástroj Page Inspector otevře soubor šablony stylů CSS, která definuje tento styl (Site.css) a zvýrazní odpovídající stylu CSS.</span><span class="sxs-lookup"><span data-stu-id="2b88d-215">Page Inspector opens the CSS file that defines this style (Site.css) and highlights the corresponding CSS style.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image32.png)

<span data-ttu-id="2b88d-216">Teď změňte hodnotu `background-color` na hodnotu "red".</span><span class="sxs-lookup"><span data-stu-id="2b88d-216">Now change the value for `background-color` to "red".</span></span> <span data-ttu-id="2b88d-217">Změny se okamžitě zobrazí v prohlížeči nástroj Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-217">The change appears immediately in the Page Inspector browser.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image34.png)

<a id="css_color_picker"></a>
## <a name="using-the-css-color-picker"></a><span data-ttu-id="2b88d-218">Výběr barvy šablon stylů CSS</span><span class="sxs-lookup"><span data-stu-id="2b88d-218">Using the CSS Color Picker</span></span>

<span data-ttu-id="2b88d-219">Editor šablon stylů CSS v sadě Visual Studio 2012 obsahuje barvu ovládacího prvku pro výběr, která umožňuje jednoduše vybrat a vložit barvy.</span><span class="sxs-lookup"><span data-stu-id="2b88d-219">The CSS editor in Visual Studio 2012 has a color picker that makes it easy to choose and insert colors.</span></span> <span data-ttu-id="2b88d-220">Výběr barvy obsahuje standardní paletu barev, podporuje názvy standardních barev, hash kódy, barvy RGB, RGBA, HSL a HSLA a udržuje seznam barvy, které jste naposledy použili v dokumentu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-220">The color picker includes a standard palette of colors, supports standard color names, hash codes, RGB, RGBA, HSL, and HSLA colors, and maintains a list of the colors you've used most recently in the document.</span></span>

<span data-ttu-id="2b88d-221">V předchozí části, změnil hodnotu ovládacího prvku `background-color` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="2b88d-221">In the previous section, you changed the value of the `background-color` property.</span></span> <span data-ttu-id="2b88d-222">K vyvolání barev, umístěte kurzor po názvu vlastnosti a typ **#** nebo **rgb (**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-222">To invoke the color picker, place the insertion point after the property name and type **#** or **rgb(**.</span></span>

![Výběr pruhu barev šablon stylů CSS](using-page-inspector-in-aspnet-mvc/_static/image36.png)

<span data-ttu-id="2b88d-224">Klikněte na barvu, kterou chcete vyberte ho, nebo stisknutím klávesy se šipkou dolů a pak pomocí kláves šipka doleva a doprava procházení barvy.</span><span class="sxs-lookup"><span data-stu-id="2b88d-224">Click on a color to select it, or press the down arrow key and then use the left and right arrow keys to traverse the colors.</span></span> <span data-ttu-id="2b88d-225">Při návštěvě barvu odpovídající šestnáctková hodnota je zobrazen náhled:</span><span class="sxs-lookup"><span data-stu-id="2b88d-225">When you visit a color, the corresponding hex value is previewed:</span></span>

![Hodnota vlastnosti barvu pozadí náhledu](using-page-inspector-in-aspnet-mvc/_static/image38.png)

<span data-ttu-id="2b88d-227">Pokud pruhu barev nemá Přesná barva, kterou chcete, můžete použít v pop – seznamu pro výběr barvy.</span><span class="sxs-lookup"><span data-stu-id="2b88d-227">If the color bar doesn't have the exact color you want, you can use the color picker pop-down.</span></span> <span data-ttu-id="2b88d-228">Pokud chcete soubor otevřít, klikněte na dvojitou šipku na pravém konci pruhu barev nebo jednou nebo dvakrát klávesu šipka dolů na klávesnici.</span><span class="sxs-lookup"><span data-stu-id="2b88d-228">To open it, click the double chevron at the right end of the color bar, or press the Down Arrow once or twice on the keyboard.</span></span>

![Výběr barvy šablon stylů CSS Pop dolů](using-page-inspector-in-aspnet-mvc/_static/image40.png)

<span data-ttu-id="2b88d-230">Klikněte na barvu z svislá čára na pravé straně.</span><span class="sxs-lookup"><span data-stu-id="2b88d-230">Click a color from the vertical bar on the right.</span></span> <span data-ttu-id="2b88d-231">V hlavním okně zobrazí přechod pro tuto barvu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-231">This shows a gradient for that color in the main window.</span></span> <span data-ttu-id="2b88d-232">Zvolte barvu přímo z svislá čára stisknutím klávesy Enter nebo klikněte na tlačítko kdekoli v hlavním okně Vybrat s větší přesností.</span><span class="sxs-lookup"><span data-stu-id="2b88d-232">Choose a color directly from the vertical bar by pressing Enter, or click any point in the main window to choose with greater precision.</span></span>

<span data-ttu-id="2b88d-233">Pokud je na obrazovce počítače, který chcete použít barvu (nemusí být uvnitř uživatelské rozhraní Visual Studia), můžete zachytit její hodnotu s použitím nástroje kapátko vpravo dole.</span><span class="sxs-lookup"><span data-stu-id="2b88d-233">If there is a color on your computer screen that you want to use (it doesn't have to be inside the Visual Studio user interface), you can capture its value by using the eyedropper tool on the lower right.</span></span>

<span data-ttu-id="2b88d-234">Neprůhlednost barvu také můžete změnit přesunutím posuvníku v dolní části nástroje pro výběr barvy.</span><span class="sxs-lookup"><span data-stu-id="2b88d-234">You also can change the opacity of a color by moving the slider at the bottom of the color picker.</span></span> <span data-ttu-id="2b88d-235">Provádění, změní se barva hodnot na hodnoty RGBA, protože formát RGBA může představovat neprůhlednosti.</span><span class="sxs-lookup"><span data-stu-id="2b88d-235">Doing so changes color values to RGBA values, because the RGBA format can represent opacity.</span></span>

<span data-ttu-id="2b88d-236">Po výběru barvy, stiskněte klávesu Enter a pak zadejte středníkem k dokončení barvu pozadí položky v *Site.css* souboru.</span><span class="sxs-lookup"><span data-stu-id="2b88d-236">After you have chosen a color, press Enter, and then type a semicolon to complete the background-color entry in the *Site.css* file.</span></span>

<a id="_the_update_bar"></a>

### <a name="the-page-inspector-update-bar"></a><span data-ttu-id="2b88d-237">Kontrola aktualizace posuvník</span><span class="sxs-lookup"><span data-stu-id="2b88d-237">The Page Inspector Update Bar</span></span>

<span data-ttu-id="2b88d-238">Nástroj Page Inspector okamžitě zjistí změnu *Site.css* soubor a zobrazí výstrahu na panelu aktualizace.</span><span class="sxs-lookup"><span data-stu-id="2b88d-238">Page Inspector immediately detects the change to the *Site.css* file and displays an alert in an update bar.</span></span>

![Panel aktualizace](using-page-inspector-in-aspnet-mvc/_static/image42.png)

<span data-ttu-id="2b88d-240">Uložte všechny soubory a aktualizujte prohlížeč, nástroj Page Inspector stisknutím klávesy Ctrl + Alt + Enter nebo klikněte na panel aktualizace.</span><span class="sxs-lookup"><span data-stu-id="2b88d-240">To save all your files and refresh the Page Inspector browser, press Ctrl+Alt+Enter or click the update bar.</span></span> <span data-ttu-id="2b88d-241">Změna barvy zvýraznění se zobrazí v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="2b88d-241">The change in the highlight color appears in the browser.</span></span>

<a id="map_dynamic_elements"></a>
## <a name="mapping-dynamic-page-elements-to-javascript"></a><span data-ttu-id="2b88d-242">Mapování elementů dynamických stránek pro jazyk JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b88d-242">Mapping Dynamic Page Elements to JavaScript</span></span>

<span data-ttu-id="2b88d-243">V moderních webových aplikací elementy na stránce často dynamicky generované s použitím jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b88d-243">In modern web applications, elements in the page are often generated dynamically with JavaScript.</span></span> <span data-ttu-id="2b88d-244">To znamená, že není žádné statické značky (HTML nebo Razor), které odpovídá tyto prvky stránky.</span><span class="sxs-lookup"><span data-stu-id="2b88d-244">That means there is no static markup (either HTML or Razor) that corresponds to these page elements.</span></span>

<span data-ttu-id="2b88d-245">Verze 1.3 nástroj Page Inspector můžete nyní mapa položky, které byly dynamicky přidány na stránku zpět na odpovídající kód jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b88d-245">With version 1.3, Page Inspector can now map items that were dynamically added to the page back to the corresponding JavaScript code.</span></span> <span data-ttu-id="2b88d-246">Abychom si předvedli tuto funkci, použijeme [jedné stránky aplikace (SPA) šablony](../../../single-page-application/overview/introduction/knockoutjs-template.md).</span><span class="sxs-lookup"><span data-stu-id="2b88d-246">To demonstrate this feature, we'll use the [Single Page Application (SPA) template](../../../single-page-application/overview/introduction/knockoutjs-template.md).</span></span>

> [!NOTE]
> <span data-ttu-id="2b88d-247">Šablona jednostránková aplikace vyžaduje [ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650) aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="2b88d-247">The SPA template requires the [ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650) update.</span></span>


<span data-ttu-id="2b88d-248">V sadě Visual Studio, zvolte **souboru** &gt; **nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-248">In Visual Studio, choose **File** &gt; **New Project**.</span></span> <span data-ttu-id="2b88d-249">Na levé straně rozbalte **Visual C#** vyberte **webové**a pak vyberte **webová aplikace ASP.NET MVC4**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-249">On the left, expand **Visual C#**, select **Web**, and then select **ASP.NET MVC4 Web Application**.</span></span> <span data-ttu-id="2b88d-250">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-250">Click **OK**.</span></span>

<span data-ttu-id="2b88d-251">V **nového projektu ASP.NET MVC 4** dialogového okna, vyberte **jednostránkové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-251">In the **New ASP.NET MVC 4 Project** dialog, select **Single Page Application**.</span></span>

<span data-ttu-id="2b88d-252">V Průzkumníku řešení rozbalte **zobrazení** složku a potom **Domů** složky.</span><span class="sxs-lookup"><span data-stu-id="2b88d-252">In Solution Explorer, expand the **Views** folder and then the **Home** folder.</span></span> <span data-ttu-id="2b88d-253">Souboru Index.cshtml klikněte pravým tlačítkem myši a zvolte **zobrazení v nástroje Page Inspector**.</span><span class="sxs-lookup"><span data-stu-id="2b88d-253">Right click the Index.cshtml file and choose **View in Page Inspector**.</span></span>

<span data-ttu-id="2b88d-254">Nejprve thing, který je zobrazený v prohlížeči nástroj Page Inspector je přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="2b88d-254">The first thing that is displayed in the Page Inspector browser is a login page.</span></span> <span data-ttu-id="2b88d-255">Klikněte na "Zaregistrovat" a vytvořte uživatelské jméno a heslo.</span><span class="sxs-lookup"><span data-stu-id="2b88d-255">Click "Sign Up" and create a user name and password.</span></span> <span data-ttu-id="2b88d-256">Po registraci, aplikace vás přihlásí a vytvoří se některé ukázkové položky seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="2b88d-256">Once you sign up, the application logs you in and creates a to-do list with some sample items.</span></span>

<span data-ttu-id="2b88d-257">Klikněte na tlačítko **zkontrolujte, jestli se** uvést do režimu kontroly nástroje Page Inspector.</span><span class="sxs-lookup"><span data-stu-id="2b88d-257">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span> <span data-ttu-id="2b88d-258">V prohlížeči nástroj Page Inspector klikněte na jednu z položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="2b88d-258">In the Page Inspector browser, click on one of the to-do items.</span></span> <span data-ttu-id="2b88d-259">Všimněte si, že místo zvýrazněným modrou barvu elementu je zvýrazněn oranžově "JS" vedle názvu elementu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-259">Notice that instead of being highlighted in blue, the element is highlighted in orange, with "JS" next to the element name.</span></span> <span data-ttu-id="2b88d-260">To znamená, že element vytvořil dynamicky prostřednictvím skriptu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-260">This indicates that the element was created dynamically through script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image44.png)

<span data-ttu-id="2b88d-261">Kromě toho se zobrazí oranžové podtržení na **zásobník volání** kartu. Znamená to, že **zásobník volání** podokno má další informace o elementu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-261">In addition, an orange underline appears on the **Call Stack** tab. This indicates that the **Call Stack** pane has more information about the element.</span></span>

<span data-ttu-id="2b88d-262">Klikněte na **zásobník volání** kartu. **Zásobník volání** podokně se zobrazí zásobník volání pro volání jazyka JavaScript vytvořeného elementu.</span><span class="sxs-lookup"><span data-stu-id="2b88d-262">Click on the **Call Stack** tab. The **Call Stack** pane shows the call stack for the JavaScript call that created the element.</span></span> <span data-ttu-id="2b88d-263">Volání externí knihovny, jako je jQuery, jsou sbaleny, takže můžete snadno zobrazit volání skriptu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="2b88d-263">Calls to external libraries such as jQuery are collapsed, so that you can easily see the calls to your application script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image46.png)

<span data-ttu-id="2b88d-264">Pokud chcete zobrazit úplný zásobník volání externích knihovnách, včetně lze rozbalit uzly s popiskem "Externí knihovny":</span><span class="sxs-lookup"><span data-stu-id="2b88d-264">To see the full stack, including calls to external libraries, you can expand the nodes labeled "External Libraries":</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image48.png)

<span data-ttu-id="2b88d-265">Pokud kliknete na položku v zásobníku volání, Visual Studio otevře soubor kódu a zvýrazní odpovídající skript.</span><span class="sxs-lookup"><span data-stu-id="2b88d-265">If you click an item in the call stack, Visual Studio opens the code file and highlights the corresponding script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image50.png)

## <a name="see-also"></a><span data-ttu-id="2b88d-266">Viz také</span><span class="sxs-lookup"><span data-stu-id="2b88d-266">See Also</span></span>

<span data-ttu-id="2b88d-267">[Úvod do ASP.NET MVC 4 pomocí sady Visual Studio](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md) (webové stránky ASP.net)</span><span class="sxs-lookup"><span data-stu-id="2b88d-267">[Intro to ASP.NET MVC 4 with Visual Studio](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md) (ASP.net website)</span></span>

<span data-ttu-id="2b88d-268">[Představení nástroje Page Inspector](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) (video Channel 9)</span><span class="sxs-lookup"><span data-stu-id="2b88d-268">[Introducing Page Inspector](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) (Channel 9 video)</span></span>

<span data-ttu-id="2b88d-269">[Chybové zprávy nástroje Page Inspector](https://go.microsoft.com/?linkid=9813062) (MSDN)</span><span class="sxs-lookup"><span data-stu-id="2b88d-269">[Page Inspector Error Messages](https://go.microsoft.com/?linkid=9813062) (MSDN)</span></span>

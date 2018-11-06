---
uid: aspnet/web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio
title: Programování webových stránek technologie ASP.NET (Razor) pomocí sady Visual Studio | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento dodatek vysvětluje, jak můžete použít Visual Studio 2010 a program Visual Web Developer 2010 Express do programu rozhraní ASP.NET Web Pages se syntaxí Razor.
ms.author: riande
ms.date: 02/13/2014
ms.assetid: 0acfec5a-48f2-4766-a801-a0f426966f0a
msc.legacyurl: /web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio
msc.type: authoredcontent
ms.openlocfilehash: 5b8df17ec1021d133579e23cb4f5b0d0f67d4c7c
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020452"
---
<a name="programming-aspnet-web-pages-razor-using-visual-studio"></a><span data-ttu-id="274df-103">Programování webových stránek ASP.NET (Razor) pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="274df-103">Programming ASP.NET Web Pages (Razor) Using Visual Studio</span></span>
====================
<span data-ttu-id="274df-104">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="274df-104">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="274df-105">Tento článek vysvětluje, jak můžete pomocí sady Visual Studio nebo Visual Web Developer Express do programu websites webových stránek ASP.NET (Razor).</span><span class="sxs-lookup"><span data-stu-id="274df-105">This article explains how you can use Visual Studio or Visual Web Developer Express to program ASP.NET Web Pages (Razor) websites.</span></span>
>
> <span data-ttu-id="274df-106">Co se dozvíte</span><span class="sxs-lookup"><span data-stu-id="274df-106">What you'll learn</span></span>
>
> - <span data-ttu-id="274df-107">Co je potřeba nainstalovat (Pokud se něco) pro práci s webovými stránkami ASP.NET ve vaší verzi sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="274df-107">What you need to install (if anything) to work with ASP.NET Web Pages in your version of Visual Studio.</span></span>
> - <span data-ttu-id="274df-108">Jak přidat podporu pro webové stránky ASP.NET pro aplikaci Visual Web Developer 2010 Express.</span><span class="sxs-lookup"><span data-stu-id="274df-108">How to add support for ASP.NET Web Pages to Visual Web Developer 2010 Express.</span></span>
> - <span data-ttu-id="274df-109">Jak používat funkce v sadě Visual Studio pro práci s ASP.NET Razor pages, včetně technologie IntelliSense a ladicí program.</span><span class="sxs-lookup"><span data-stu-id="274df-109">How to use features in Visual Studio to work with ASP.NET Razor pages, including IntelliSense and the debugger.</span></span>
>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="274df-110">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="274df-110">Software versions used in the tutorial</span></span>
>
>
> - <span data-ttu-id="274df-111">Webové stránky ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="274df-111">ASP.NET Web Pages (Razor) 3</span></span>
> - <span data-ttu-id="274df-112">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="274df-112">Visual Studio 2013</span></span>
> - <span data-ttu-id="274df-113">Služba WebMatrix 3</span><span class="sxs-lookup"><span data-stu-id="274df-113">WebMatrix 3</span></span>
>
>
> <span data-ttu-id="274df-114">V tomto kurzu se také pracuje s ASP.NET Web Pages 2, Visual Studio 2012, Visual Studio 2010 a službě WebMatrix 2.</span><span class="sxs-lookup"><span data-stu-id="274df-114">This tutorial also works with ASP.NET Web Pages 2, Visual Studio 2012, Visual Studio 2010, and WebMatrix 2.</span></span>


<span data-ttu-id="274df-115">Můžete naprogramovat ASP.NET Web pages se syntaxí Razor pomocí služby WebMatrix nebo mnoha jiných editorů kódu.</span><span class="sxs-lookup"><span data-stu-id="274df-115">You can program ASP.NET Web pages with Razor syntax using WebMatrix or many other code editors.</span></span> <span data-ttu-id="274df-116">Můžete také použít Microsoft Visual Studio, což je plně vybavené integrované vývojové prostředí (IDE), která poskytuje výkonnou sadu nástrojů pro vytváření mnoho typů aplikací (ne jenom weby).</span><span class="sxs-lookup"><span data-stu-id="274df-116">You can also use Microsoft Visual Studio which is a full-featured integrated development environment (IDE) that provides a powerful set of tools for creating many types of applications (not just websites).</span></span> <span data-ttu-id="274df-117">Chcete-li pracovat se stránkami ASP.NET Razor, můžete použít [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017).</span><span class="sxs-lookup"><span data-stu-id="274df-117">To work with ASP.NET Razor pages, you can use [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017).</span></span>

<span data-ttu-id="274df-118">Jsou dvě užitečné funkce, které poskytuje Visual Studio pro programování s webovými stránkami ASP.NET Razor:</span><span class="sxs-lookup"><span data-stu-id="274df-118">Two particularly useful features that Visual Studio provides for programming with ASP.NET Razor web pages are:</span></span>

- <span data-ttu-id="274df-119">*Technologie IntelliSense*.</span><span class="sxs-lookup"><span data-stu-id="274df-119">*IntelliSense*.</span></span> <span data-ttu-id="274df-120">Funkce technologie IntelliSense, integrované do sady Visual Studio je komplexnější než IntelliSense ve Webmatrixu.</span><span class="sxs-lookup"><span data-stu-id="274df-120">The IntelliSense feature built into Visual Studio is more comprehensive than IntelliSense in WebMatrix.</span></span>
- <span data-ttu-id="274df-121">*Ladicí program*.</span><span class="sxs-lookup"><span data-stu-id="274df-121">*Debugger*.</span></span> <span data-ttu-id="274df-122">Ladicí program umožňuje řešit kódu zastavte program je spuštěn, zkoumání proměnné a krokování kódu řádek po řádku.</span><span class="sxs-lookup"><span data-stu-id="274df-122">The debugger lets you troubleshoot your code by stopping a program while it's running, examining variables, and stepping through the code line by line.</span></span>

## <a name="using-visual-studio-with-different-versions-of-aspnet-web-pages"></a><span data-ttu-id="274df-123">Pomocí sady Visual Studio s různými verzemi nástroje ASP.NET Web Pages</span><span class="sxs-lookup"><span data-stu-id="274df-123">Using Visual Studio with Different Versions of ASP.NET Web Pages</span></span>

<span data-ttu-id="274df-124">Pro vývoj webových aplikací ASP.NET v sadě Visual Studio 2017, nainstalujte **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="274df-124">To develop ASP.NET web apps in Visual Studio 2017, install the **ASP.NET and web development** workload.</span></span>

<span data-ttu-id="274df-125">Visual Studio 2012 a Visual Studio 2013 zahrnují podporu pro rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="274df-125">Visual Studio 2012 and Visual Studio 2013 include support for ASP.NET Web Pages.</span></span> <span data-ttu-id="274df-126">(Balíčky, které jsou nutné pro podporu rozhraní ASP.NET Web Pages nainstalují při instalaci sady Visual Studio.)</span><span class="sxs-lookup"><span data-stu-id="274df-126">(The packages that are required in order to support ASP.NET Web Pages are installed when you install Visual Studio.)</span></span>

<span data-ttu-id="274df-127">Visual Studio 2010 nezahrnuje podporu ve výchozím nastavení pro rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="274df-127">Visual Studio 2010 does not include support by default for ASP.NET Web Pages.</span></span> <span data-ttu-id="274df-128">Rozhraní ASP.NET Web Pages pomocí sady Visual Studio 2010, musíte nainstalovat balíček ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="274df-128">To use ASP.NET Web Pages with Visual Studio 2010, you must install the ASP.NET MVC package.</span></span> <span data-ttu-id="274df-129">ASP.NET Web Pages 2 získáte instalace technologie ASP.NET MVC 4.</span><span class="sxs-lookup"><span data-stu-id="274df-129">To get ASP.NET Web Pages 2, you install ASP.NET MVC 4.</span></span>

<span data-ttu-id="274df-130">Následující tabulka shrnuje podporu pro rozhraní ASP.NET Web Pages v různých verzích systému Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="274df-130">The following table summarizes the support for ASP.NET Web Pages in different versions of Visual Studio.</span></span>

|  | <span data-ttu-id="274df-131">Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="274df-131">Visual Studio 2010</span></span> | <span data-ttu-id="274df-132">Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="274df-132">Visual Studio 2012</span></span> | <span data-ttu-id="274df-133">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="274df-133">Visual Studio 2013</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="274df-134">**Rozhraní ASP.NET Web Pages 2**</span><span class="sxs-lookup"><span data-stu-id="274df-134">**ASP.NET Web Pages 2**</span></span> | <span data-ttu-id="274df-135">Instalace technologie ASP.NET MVC 4</span><span class="sxs-lookup"><span data-stu-id="274df-135">Install ASP.NET MVC 4</span></span> | <span data-ttu-id="274df-136">(Zahrnuto)</span><span class="sxs-lookup"><span data-stu-id="274df-136">(Included)</span></span> | <span data-ttu-id="274df-137">(Zahrnuto)</span><span class="sxs-lookup"><span data-stu-id="274df-137">(Included)</span></span> |
| <span data-ttu-id="274df-138">**ASP.NET – webové stránky 3**</span><span class="sxs-lookup"><span data-stu-id="274df-138">**ASP.NET Web Pages 3**</span></span> |  | <span data-ttu-id="274df-139">Aktualizace technologie ASP.NET webové stránky 3 přes NuGet</span><span class="sxs-lookup"><span data-stu-id="274df-139">Update to ASP.NET Web Pages 3 through NuGet</span></span> | <span data-ttu-id="274df-140">(Zahrnuto)</span><span class="sxs-lookup"><span data-stu-id="274df-140">(Included)</span></span> |

<span data-ttu-id="274df-141">Pokud chcete pracovat se službou Visual Studio 2010, přečtěte si téma [instalace podpory pro rozhraní ASP.NET Web Pages v sadě Visual Studio 2010](#vs2010support).</span><span class="sxs-lookup"><span data-stu-id="274df-141">To work with Visual Studio 2010, see [Installing Support for ASP.NET Web Pages in Visual Studio 2010](#vs2010support).</span></span>

## <a name="launching-visual-studio-from-webmatrix"></a><span data-ttu-id="274df-142">Spuštění sady Visual Studio ze služby WebMatrix</span><span class="sxs-lookup"><span data-stu-id="274df-142">Launching Visual Studio from WebMatrix</span></span>

<span data-ttu-id="274df-143">Pokud jste vytvořili projekt v nástroji WebMatrix a chcete ho přepněte do aplikace Visual Studio, služba WebMatrix poskytuje možnost jednoduše otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="274df-143">If you have started a project in WebMatrix and want to switch to Visual Studio, WebMatrix provides a button to easily open the project in Visual Studio.</span></span> <span data-ttu-id="274df-144">Musíte mít nainstalovanou sadu Visual Studio v počítači pro toto tlačítko Povolit.</span><span class="sxs-lookup"><span data-stu-id="274df-144">You must have Visual Studio installed on your computer for this button to be enabled.</span></span> <span data-ttu-id="274df-145">Následující obrázek ukazuje tlačítko ve Webmatrixu.</span><span class="sxs-lookup"><span data-stu-id="274df-145">The following image shows the button in WebMatrix.</span></span>

![Spusťte sadu Visual Studio](program-asp-net-web-pages-in-visual-studio/_static/image1.png)

<span data-ttu-id="274df-147">Když kliknete na tlačítko, projekt otevřen v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="274df-147">When you click the button, the project is opened in Visual Studio.</span></span> <span data-ttu-id="274df-148">Můžete přepínat vpřed a zpět mezi službu WebMatrix a sady Visual Studio bez problémů.</span><span class="sxs-lookup"><span data-stu-id="274df-148">You can switch back and forth between WebMatrix and Visual Studio without any problems.</span></span> <span data-ttu-id="274df-149">Pokud se všechny soubory byly změněny v jiném prostředí a být potřeba znovu načíst k získání nejnovějších změn, budete upozorněni.</span><span class="sxs-lookup"><span data-stu-id="274df-149">You will be notified if any files have changed in the other environment and need to be reloaded to get the latest changes.</span></span>

## <a name="creating-aspnet-razor-site-in-visual-studio"></a><span data-ttu-id="274df-150">Vytváří se syntaxe Razor rozhraní ASP.NET Web v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="274df-150">Creating ASP.NET Razor Site in Visual Studio</span></span>

<span data-ttu-id="274df-151">Chcete-li v sadě Visual Studio můžete vytvořit web ASP.NET Razor:</span><span class="sxs-lookup"><span data-stu-id="274df-151">To create an ASP.NET Razor website in Visual Studio:</span></span>

1. <span data-ttu-id="274df-152">Otevřít Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="274df-152">Open Visual Studio.</span></span>
2. <span data-ttu-id="274df-153">V **souboru** nabídky, klikněte na tlačítko **nový web**.</span><span class="sxs-lookup"><span data-stu-id="274df-153">In the **File** menu, click **New Web Site**.</span></span>

    ![Vytvořit nový web](program-asp-net-web-pages-in-visual-studio/_static/image2.png)
3. <span data-ttu-id="274df-155">V **nový web** dialogového okna, vyberte jazyk, který chcete použít (Visual C# nebo Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="274df-155">In the **New Web Site** dialog box, select the language to use (Visual C# or Visual Basic).</span></span>
4. <span data-ttu-id="274df-156">Vyberte **webové stránky ASP.NET (Razor)** šablony.</span><span class="sxs-lookup"><span data-stu-id="274df-156">Select the **ASP.NET Web Site (Razor)** template.</span></span>

    ![webu Razor](program-asp-net-web-pages-in-visual-studio/_static/image3.png)
5. <span data-ttu-id="274df-158">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="274df-158">Click **OK**.</span></span>

<span data-ttu-id="274df-159">Nový projekt existuje a naplní se některé výchozí webové stránky, abychom vám mohli začít.</span><span class="sxs-lookup"><span data-stu-id="274df-159">Your new project exists and is populated with some default web pages to help you get started.</span></span>

### <a name="using-intellisense"></a><span data-ttu-id="274df-160">Používání atributu IntelliSense</span><span class="sxs-lookup"><span data-stu-id="274df-160">Using IntelliSense</span></span>

<span data-ttu-id="274df-161">Teď, když jste vytvořili webu, najdete v článku Jak funguje technologie IntelliSense v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="274df-161">Now that you've created a site, you can see how IntelliSense works in Visual Studio.</span></span>

1. <span data-ttu-id="274df-162">Na webu jste právě vytvořili, otevřete *stránku Default.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="274df-162">In the website you just created, open the *Default.cshtml* page.</span></span>
2. <span data-ttu-id="274df-163">Po `<h3>` značky na stránce zadejte `@ServerInfo.` (včetně tečky).</span><span class="sxs-lookup"><span data-stu-id="274df-163">After the `<h3>` tags in the page, type `@ServerInfo.` (including the dot).</span></span> <span data-ttu-id="274df-164">Všimněte si, jak technologie IntelliSense zobrazuje dostupné metody pro `ServerInfo` pomocné rutiny v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="274df-164">Notice how IntelliSense displays the available methods for the `ServerInfo` helper in a drop-down list.</span></span>

    ![technologie IntelliSense](program-asp-net-web-pages-in-visual-studio/_static/image4.png)
3. <span data-ttu-id="274df-166">Vyberte `GetHtml` metody ze seznamu a potom stiskněte klávesu Enter.</span><span class="sxs-lookup"><span data-stu-id="274df-166">Select the `GetHtml` method from the list and then press Enter.</span></span> <span data-ttu-id="274df-167">Technologie IntelliSense automaticky vyplní metodu.</span><span class="sxs-lookup"><span data-stu-id="274df-167">IntelliSense automatically fills in the method.</span></span> <span data-ttu-id="274df-168">(Jak s jakoukoli metodou v jazyce C#, je nutné přidat `()` znaky za metodu.) Dokončený kód `GetHtml` metoda vypadá jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="274df-168">(As with any method in C#, you must add `()` characters after the method.) The completed code for the `GetHtml` method looks like the following example:</span></span>

    [!code-cshtml[Main](program-asp-net-web-pages-in-visual-studio/samples/sample1.cshtml)]
4. <span data-ttu-id="274df-169">Stiskněte kombinaci kláves Ctrl + F5 ke spuštění stránky.</span><span class="sxs-lookup"><span data-stu-id="274df-169">Press Ctrl+F5 to run the page.</span></span> <span data-ttu-id="274df-170">Je to, co bude stránka vypadat, když se zobrazí v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="274df-170">This is what the page looks like when displayed in a browser:</span></span>

    ![výchozí stránku v prohlížeči](program-asp-net-web-pages-in-visual-studio/_static/image5.png)
5. <span data-ttu-id="274df-172">Zavřete prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="274df-172">Close the browser.</span></span>

### <a name="using-the-debugger"></a><span data-ttu-id="274df-173">Pomocí ladicího programu</span><span class="sxs-lookup"><span data-stu-id="274df-173">Using the Debugger</span></span>

1. <span data-ttu-id="274df-174">V horní části *stránku Default.cshtml* stránku po řádek, který začíná `Page.Title`, přidejte následující řádek kódu:</span><span class="sxs-lookup"><span data-stu-id="274df-174">At the top of the *Default.cshtml* page, after the line that begins with `Page.Title`, add the following line of code:</span></span>

    [!code-csharp[Main](program-asp-net-web-pages-in-visual-studio/samples/sample2.cs)]
2. <span data-ttu-id="274df-175">Šedé okraj editoru nalevo od kódu, klikněte vedle tohoto nového řádku, chcete-li přidat *zarážku*.</span><span class="sxs-lookup"><span data-stu-id="274df-175">In the gray margin of the editor to the left of the code, click next to this new line in order to add a *breakpoint*.</span></span> <span data-ttu-id="274df-176">Zarážka je, že se ladicí program zastaví, abyste si mohli zobrazit, co se děje v daném okamžiku spuštění programu značku.</span><span class="sxs-lookup"><span data-stu-id="274df-176">A breakpoint is a marker that tells the debugger to stop running the program at that point so you can see what's happening.</span></span>

    ![nastavit zarážku](program-asp-net-web-pages-in-visual-studio/_static/image6.png)
3. <span data-ttu-id="274df-178">Odeberte volání `ServerInfo.GetHtml` metoda a přidejte volání `@myTime` proměnné na příslušné místo.</span><span class="sxs-lookup"><span data-stu-id="274df-178">Remove the call to the `ServerInfo.GetHtml` method, and add a call to the `@myTime` variable in its place.</span></span> <span data-ttu-id="274df-179">Toto volání zobrazí aktuální hodnotu čas, který je vrácen nový řádek kódu.</span><span class="sxs-lookup"><span data-stu-id="274df-179">This call displays the current time value that's returned by the new line of code.</span></span>
4. <span data-ttu-id="274df-180">Stiskněte klávesu F5 ke spuštění stránky v ladicím programu.</span><span class="sxs-lookup"><span data-stu-id="274df-180">Press F5 to run the page in the debugger.</span></span> <span data-ttu-id="274df-181">Na stránce se zastaví na zarážce, kterou jste nastavili.</span><span class="sxs-lookup"><span data-stu-id="274df-181">The page stops on the breakpoint that you set.</span></span> <span data-ttu-id="274df-182">Následující obrázek ukazuje, co bude stránka vypadat jako v editoru se zarážkou (žlutě).</span><span class="sxs-lookup"><span data-stu-id="274df-182">The following image shows what the page looks like in the editor with the breakpoint (in yellow).</span></span>

    ![ladění zarážku](program-asp-net-web-pages-in-visual-studio/_static/image7.png)
5. <span data-ttu-id="274df-184">Na panelu nástrojů ladění, klikněte na tlačítko **Krokovat s vnořením** tlačítko (nebo stisknutím klávesy F11) spusťte další řádek kódu.</span><span class="sxs-lookup"><span data-stu-id="274df-184">In the Debug toolbar, click the **Step Into** button (or press F11) to run the next line of code.</span></span> <span data-ttu-id="274df-185">Pokaždé, když kliknete na toto tlačítko přechodu spuštění na další řádek kódu.</span><span class="sxs-lookup"><span data-stu-id="274df-185">Each time you click this button, you advance the execution to the next line of code.</span></span>

    ![Krokovat s vnořením tlačítko](program-asp-net-web-pages-in-visual-studio/_static/image8.png)
6. <span data-ttu-id="274df-187">Zkoumat hodnoty `myTime` proměnné tak, že ukazatel myši nad ním nebo zkontrolováním hodnoty zobrazené v **lokální** a **zásobník volání** systému windows.</span><span class="sxs-lookup"><span data-stu-id="274df-187">Examine the value of the `myTime` variable by holding your mouse pointer over it or by inspecting the values displayed in the **Locals** and **Call Stack** windows.</span></span> <span data-ttu-id="274df-188">Visual Studio umožňuje zobrazit hodnotu proměnné.</span><span class="sxs-lookup"><span data-stu-id="274df-188">Visual Studio display the value of the variable.</span></span>

    ![hodnoty času zobrazit](program-asp-net-web-pages-in-visual-studio/_static/image9.png)
7. <span data-ttu-id="274df-190">Až skončíte zkoumání proměnné a krokování kódem, stiskněte klávesu F5, aby kontinuálně běžely na stránku bez zastavení na každém řádku.</span><span class="sxs-lookup"><span data-stu-id="274df-190">When you're done examining the variable and stepping through code, press F5 to continue running the page without stopping at each line.</span></span> <span data-ttu-id="274df-191">Jakmile budete hotovi, krokování kódu, prohlížeč zobrazí na stránce.</span><span class="sxs-lookup"><span data-stu-id="274df-191">When you've finished stepping through all the code, the browser displays the page.</span></span>

<span data-ttu-id="274df-192">Další informace o ladicím programu a o tom, jak ladit kód v sadě Visual Studio najdete v tématu [návod: ladění webové stránky v aplikaci Visual Web Developer](https://msdn.microsoft.com/library/z9e7w6cs.aspx).</span><span class="sxs-lookup"><span data-stu-id="274df-192">To learn more about the debugger and about how to debug code in Visual Studio, see [Walkthrough: Debugging Web Pages in Visual Web Developer](https://msdn.microsoft.com/library/z9e7w6cs.aspx).</span></span>

## <a name="using-razor-in-aspnet-mvc-projects-with-visual-studio"></a><span data-ttu-id="274df-193">V projektech ASP.NET MVC pomocí sady Visual Studio pomocí syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="274df-193">Using Razor in ASP.NET MVC projects with Visual Studio</span></span>

<span data-ttu-id="274df-194">Syntaxe Razor se také často používá v projektech ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="274df-194">The Razor syntax is also used extensively in ASP.NET MVC projects.</span></span> <span data-ttu-id="274df-195">MVC je výkonný, na základě vzorů způsob, jak vytvářet dynamické weby.</span><span class="sxs-lookup"><span data-stu-id="274df-195">MVC is a powerful, patterns-based way to build dynamic websites.</span></span> <span data-ttu-id="274df-196">Pokud webové stránky ASP.NET Web přestane být obtížné zachovat, můžete chtít zvažte jeho převod na aplikace ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="274df-196">If your ASP.NET Web Pages site becomes difficult to maintain, you might want to consider converting it to an ASP.NET MVC application.</span></span> <span data-ttu-id="274df-197">Příklad vytvoření aplikace MVC, naleznete v tématu [Začínáme s ASP.NET MVC 5](../../../mvc/overview/getting-started/introduction/getting-started.md).</span><span class="sxs-lookup"><span data-stu-id="274df-197">For an example of creating an MVC application, see [Getting Started with ASP.NET MVC 5](../../../mvc/overview/getting-started/introduction/getting-started.md).</span></span>

<a id="vs2010support"></a>
## <a name="installing-support-for-aspnet-web-pages-in-visual-studio-2010"></a><span data-ttu-id="274df-198">Instalace podpory pro webové stránky ASP.NET v sadě Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="274df-198">Installing Support for ASP.NET Web Pages in Visual Studio 2010</span></span>

<span data-ttu-id="274df-199">V této části najdete postup instalace nástroje webových stránek ASP.NET a Visual Web Developer Express 2010 pro sadu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="274df-199">This section shows how to install Visual Web Developer Express 2010 and the ASP.NET Web Pages Tools for Visual Studio.</span></span>

1. <span data-ttu-id="274df-200">Pokud ještě nemáte instalačního programu webové platformy, můžete ji stáhněte z následující adresy URL:</span><span class="sxs-lookup"><span data-stu-id="274df-200">If you don't already have the Web Platform Installer, download it from the following URL:</span></span>

    [https://www.microsoft.com/web/downloads/platform.aspx](https://www.microsoft.com/web/downloads/platform.aspx)
2. <span data-ttu-id="274df-201">Spuštění instalačního programu webové platformy.</span><span class="sxs-lookup"><span data-stu-id="274df-201">Run the Web Platform Installer.</span></span>
3. <span data-ttu-id="274df-202">Klikněte na tlačítko **produkty** kartu.</span><span class="sxs-lookup"><span data-stu-id="274df-202">Click the **Products** tab.</span></span>

    ![Karta produktů instalace webové platformy](program-asp-net-web-pages-in-visual-studio/_static/image10.png)
4. <span data-ttu-id="274df-204">Vyhledejte **ASP.NET MVC 4** (pro ASP.NET Web Pages 2) a potom klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="274df-204">Search for **ASP.NET MVC 4** (for ASP.NET Web Pages 2) and then click **Add**.</span></span> <span data-ttu-id="274df-205">Tyto produkty zahrnují Visual Studio tools pro vytváření webů ASP.NET Razor.</span><span class="sxs-lookup"><span data-stu-id="274df-205">These products include Visual Studio tools for building ASP.NET Razor websites.</span></span>

    ![Možnosti instalace instalace webové platformy](program-asp-net-web-pages-in-visual-studio/_static/image11.png)
5. <span data-ttu-id="274df-207">Klikněte na tlačítko **nainstalovat** k dokončení instalace.</span><span class="sxs-lookup"><span data-stu-id="274df-207">Click **Install** to complete the installation.</span></span>

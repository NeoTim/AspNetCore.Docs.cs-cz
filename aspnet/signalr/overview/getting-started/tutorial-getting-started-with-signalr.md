---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Kurz: Začínáme s knihovnou SignalR 2 | Dokumentace Microsoftu'
author: pfletcher
description: V tomto kurzu se naučíte používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase. Přidáte SignalR prázdná webová aplikace ASP.NET a vytvoření pa HTML...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 3b06e7d0a602e061112adbceba92276f836f6311
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287335"
---
<a name="tutorial-getting-started-with-signalr-2"></a><span data-ttu-id="b7266-104">Kurz: Začínáme s funkcí SignalR 2</span><span class="sxs-lookup"><span data-stu-id="b7266-104">Tutorial: Getting Started with SignalR 2</span></span>
====================
<span data-ttu-id="b7266-105">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="b7266-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="b7266-106">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="b7266-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

> <span data-ttu-id="b7266-107">V tomto kurzu se naučíte používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="b7266-107">This tutorial shows how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="b7266-108">Přidáte SignalR prázdná webová aplikace ASP.NET a vytvořte stránku HTML k odeslání a zobrazení zprávy.</span><span class="sxs-lookup"><span data-stu-id="b7266-108">You will add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span> 
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="b7266-109">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="b7266-109">Software versions used in the tutorial</span></span>
> 
> 
> - [<span data-ttu-id="b7266-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="b7266-110">Visual Studio 2013</span></span>](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - <span data-ttu-id="b7266-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="b7266-111">.NET 4.5</span></span>
> - <span data-ttu-id="b7266-112">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="b7266-112">SignalR version 2</span></span>
>   
> 
> 
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="b7266-113">V tomto kurzu pomocí sady Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="b7266-113">Using Visual Studio 2012 with this tutorial</span></span>
> 
> 
> <span data-ttu-id="b7266-114">Pokud chcete použít Visual Studio 2012 s tímto kurzem, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="b7266-114">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
> 
> - <span data-ttu-id="b7266-115">Aktualizace vašeho [Správce balíčků](http://docs.nuget.org/docs/start-here/installing-nuget) na nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="b7266-115">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="b7266-116">Nainstalujte [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="b7266-116">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="b7266-117">Instalace webové platformy, vyhledejte a nainstalujte **technologie ASP.NET a webové nástroje 2013.1 pro Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="b7266-117">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="b7266-118">Tím se nainstaluje šablony sady Visual Studio pro funkci SignalR třídy jako **centra**.</span><span class="sxs-lookup"><span data-stu-id="b7266-118">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="b7266-119">Některé šablony (jako například **třídy pro spuštění OWIN**) nebudou k dispozici; pro ty, použijte místo toho soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="b7266-119">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
> 
> 
> ## <a name="tutorial-versions"></a><span data-ttu-id="b7266-120">Kurz verze</span><span class="sxs-lookup"><span data-stu-id="b7266-120">Tutorial versions</span></span>
> 
> <span data-ttu-id="b7266-121">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="b7266-121">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
> 
> ## <a name="questions-and-comments"></a><span data-ttu-id="b7266-122">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="b7266-122">Questions and comments</span></span>
> 
> <span data-ttu-id="b7266-123">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="b7266-123">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="b7266-124">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="b7266-124">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="b7266-125">Přehled</span><span class="sxs-lookup"><span data-stu-id="b7266-125">Overview</span></span>

<span data-ttu-id="b7266-126">V tomto kurzu vám ukazuje, jak vytvořit jednoduchý založené na prohlížeči chatovací aplikaci představí vývoj pro funkci SignalR.</span><span class="sxs-lookup"><span data-stu-id="b7266-126">This tutorial introduces SignalR development by showing how to build a simple browser-based chat application.</span></span> <span data-ttu-id="b7266-127">Přidání knihovny SignalR na prázdnou webovou aplikaci ASP.NET, vytvoříte třídu hub pro odesílání zpráv do klientů a vytvořte stránku HTML, který umožňuje uživatelům odesílat a přijímat zprávy chatu.</span><span class="sxs-lookup"><span data-stu-id="b7266-127">You will add the SignalR library to an empty ASP.NET web application, create a hub class for sending messages to clients, and create an HTML page that lets users send and receive chat messages.</span></span> <span data-ttu-id="b7266-128">Podobný kurz, který ukazuje, jak vytvořit chatovací aplikaci MVC 5 pomocí zobrazení MVC, naleznete v tématu [Začínáme s knihovnou SignalR 2 a MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).</span><span class="sxs-lookup"><span data-stu-id="b7266-128">For a similar tutorial that shows how to create a chat application in MVC 5 using an MVC view, see [Getting Started with SignalR 2 and MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

> [!NOTE]
> <span data-ttu-id="b7266-129">Tento kurz ukazuje, jak vytvářet aplikace SignalR ve verzi 2.</span><span class="sxs-lookup"><span data-stu-id="b7266-129">This tutorial demonstrates how to create SignalR applications in version 2.</span></span> <span data-ttu-id="b7266-130">Podrobnosti o změny mezi knihovnou SignalR 1.x a 2, najdete v části [projektů upgrade SignalR 1.x](../releases/upgrading-signalr-1x-projects-to-20.md) a [Visual Studio 2013 – poznámky k](../../../visual-studio/overview/2013/release-notes.md#TOC13).</span><span class="sxs-lookup"><span data-stu-id="b7266-130">For details on changes between SignalR 1.x and 2, see [Upgrading SignalR 1.x Projects](../releases/upgrading-signalr-1x-projects-to-20.md) and [Visual Studio 2013 Release Notes](../../../visual-studio/overview/2013/release-notes.md#TOC13).</span></span>

<span data-ttu-id="b7266-131">SignalR je open source knihovna .NET pro vytváření webových aplikací, které vyžadují aktualizace dat v reálném čase nebo interakci uživatelů za provozu.</span><span class="sxs-lookup"><span data-stu-id="b7266-131">SignalR is an open-source .NET library for building web applications that require live user interaction or real-time data updates.</span></span> <span data-ttu-id="b7266-132">Mezi příklady patří sociální aplikace, hry pro více uživatelů, obchodní spolupráci a novinky, počasí nebo finanční aktualizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7266-132">Examples include social applications, multiuser games, business collaboration, and news, weather, or financial update applications.</span></span> <span data-ttu-id="b7266-133">Nazývají se často aplikací v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="b7266-133">These are often called real-time applications.</span></span>

<span data-ttu-id="b7266-134">Funkce SignalR zjednodušuje proces vytváření aplikace v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="b7266-134">SignalR simplifies the process of building real-time applications.</span></span> <span data-ttu-id="b7266-135">Zahrnuje server knihovny ASP.NET a Javascriptovou klientskou knihovnu, aby bylo snazší spravovat připojení klient server a push aktualizace obsahu pro klienty.</span><span class="sxs-lookup"><span data-stu-id="b7266-135">It includes an ASP.NET server library and a JavaScript client library to make it easier to manage client-server connections and push content updates to clients.</span></span> <span data-ttu-id="b7266-136">Knihovny SignalR můžete přidat do stávající aplikace ASP.NET k získání funkcí v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="b7266-136">You can add the SignalR library to an existing ASP.NET application to gain real-time functionality.</span></span>

<span data-ttu-id="b7266-137">Tento kurz demonstruje následující úkoly vývoje SignalR:</span><span class="sxs-lookup"><span data-stu-id="b7266-137">The tutorial demonstrates the following SignalR development tasks:</span></span>

- <span data-ttu-id="b7266-138">Přidání knihovny SignalR k webové aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="b7266-138">Adding the SignalR library to an ASP.NET web application.</span></span>
- <span data-ttu-id="b7266-139">Vytvoření třídy centra tak, aby nabízel obsah pro klienty.</span><span class="sxs-lookup"><span data-stu-id="b7266-139">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="b7266-140">Vytvoření třídy pro spuštění OWIN konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7266-140">Creating an OWIN startup class to configure the application.</span></span>
- <span data-ttu-id="b7266-141">K odesílání zpráv a zobrazení aktualizací z centra pomocí knihovny jQuery SignalR na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="b7266-141">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="b7266-142">Na následujícím snímku obrazovky je vidět chatovací aplikace spuštěné v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="b7266-142">The following screen shot shows the chat application running in a browser.</span></span> <span data-ttu-id="b7266-143">Všichni noví uživatelé mohou připomínky a naleznete v tématu komentáře přidané po uživatel připojí chat.</span><span class="sxs-lookup"><span data-stu-id="b7266-143">Each new user can post comments and see comments added after the user joins the chat.</span></span>

![Instance chatu](tutorial-getting-started-with-signalr/_static/image1.png)

<span data-ttu-id="b7266-145">Oddíly:</span><span class="sxs-lookup"><span data-stu-id="b7266-145">Sections:</span></span>

- [<span data-ttu-id="b7266-146">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="b7266-146">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="b7266-147">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="b7266-147">Run the Sample</span></span>](#run)
- [<span data-ttu-id="b7266-148">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="b7266-148">Examine the Code</span></span>](#code)
- [<span data-ttu-id="b7266-149">Další kroky</span><span class="sxs-lookup"><span data-stu-id="b7266-149">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="b7266-150">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="b7266-150">Set up the Project</span></span>

<span data-ttu-id="b7266-151">Tato část ukazuje, jak pomocí sady Visual Studio 2013 a technologie SignalR verze 2 můžete vytvořit prázdnou webovou aplikaci ASP.NET, přidejte SignalR a vytvořit chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7266-151">This section shows how to use Visual Studio 2013 and SignalR version 2 to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

<span data-ttu-id="b7266-152">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="b7266-152">Prerequisites:</span></span>

- <span data-ttu-id="b7266-153">Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="b7266-153">Visual Studio 2013.</span></span> <span data-ttu-id="b7266-154">Pokud nemáte Visual Studio, přečtěte si téma [ASP.NET stáhne](https://www.asp.net/downloads) získat bezplatné Visual Studio 2013 Express vývojový nástroj.</span><span class="sxs-lookup"><span data-stu-id="b7266-154">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2013 Express Development Tool.</span></span>

<span data-ttu-id="b7266-155">Následující kroky slouží k vytvoření prázdná webová aplikace ASP.NET a přidání knihovny SignalR Visual Studio 2013:</span><span class="sxs-lookup"><span data-stu-id="b7266-155">The following steps use Visual Studio 2013 to create an ASP.NET Empty Web Application and add the SignalR library:</span></span>

1. <span data-ttu-id="b7266-156">V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="b7266-156">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Vytvořte web](tutorial-getting-started-with-signalr/_static/image2.png)
2. <span data-ttu-id="b7266-158">V **nový projekt ASP.NET** okně, ponechejte tuto položku **prázdný** vybraný a klikněte na tlačítko **vytvořit projekt**.</span><span class="sxs-lookup"><span data-stu-id="b7266-158">In the **New ASP.NET Project** window, leave **Empty** selected and click **Create Project**.</span></span>

    ![Vytvoření prázdného webu](tutorial-getting-started-with-signalr/_static/image3.png)
3. <span data-ttu-id="b7266-160">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Třída rozbočovače SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="b7266-160">In **Solution Explorer**, right-click the project, select **Add | SignalR Hub Class (v2)**.</span></span> <span data-ttu-id="b7266-161">Název třídy **ChatHub.cs** a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="b7266-161">Name the class **ChatHub.cs** and add it to the project.</span></span> <span data-ttu-id="b7266-162">Tento krok vytvoří **ChatHub** třídy a přidá do projektu sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR.</span><span class="sxs-lookup"><span data-stu-id="b7266-162">This step creates the **ChatHub** class and adds to the project a set of script files and assembly references that support SignalR.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b7266-163">Funkce SignalR můžete také přidat do projektu tak, že otevřete **nástroje > Správce balíčků NuGet > Konzola správce balíčků** a spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="b7266-163">You can also add SignalR to a project by opening the **Tools > NuGet Package Manager > Package Manager Console** and running a command:</span></span>

    `install-package Microsoft.AspNet.SignalR`

    <span data-ttu-id="b7266-164">Pokud používáte konzolu pro přidání SignalR, vytvořte třída rozbočovače SignalR jako samostatný krok po přidání SignalR.</span><span class="sxs-lookup"><span data-stu-id="b7266-164">If you use the console to add SignalR, create the SignalR hub class as a separate step after you add SignalR.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b7266-165">Pokud používáte sadu Visual Studio 2012, **třída rozbočovače SignalR (v2)** šablonu nebude k dispozici.</span><span class="sxs-lookup"><span data-stu-id="b7266-165">If you are using Visual Studio 2012, the **SignalR Hub Class (v2)** template will not be available.</span></span> <span data-ttu-id="b7266-166">Můžete přidat prostého **třídy** volá `ChatHub` místo.</span><span class="sxs-lookup"><span data-stu-id="b7266-166">You can add a plain **Class** called `ChatHub` instead.</span></span>
4. <span data-ttu-id="b7266-167">V **Průzkumníka řešení**, rozbalte uzel skripty.</span><span class="sxs-lookup"><span data-stu-id="b7266-167">In **Solution Explorer**, expand the Scripts node.</span></span> <span data-ttu-id="b7266-168">Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.</span><span class="sxs-lookup"><span data-stu-id="b7266-168">Script libraries for jQuery and SignalR are visible in the project.</span></span>
5. <span data-ttu-id="b7266-169">Nahraďte kód v novém **ChatHub** třídy následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="b7266-169">Replace the code in the new **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. <span data-ttu-id="b7266-170">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Třídy pro spuštění OWIN**.</span><span class="sxs-lookup"><span data-stu-id="b7266-170">In **Solution Explorer**, right-click the project, then click **Add | OWIN Startup Class**.</span></span> <span data-ttu-id="b7266-171">Pojmenujte novou třídu `Startup` a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="b7266-171">Name the new class `Startup` and click OK.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b7266-172">Pokud používáte sadu Visual Studio 2012, **třídy pro spuštění OWIN** šablonu nebude k dispozici.</span><span class="sxs-lookup"><span data-stu-id="b7266-172">If you are using Visual Studio 2012, the **OWIN Startup Class** template will not be available.</span></span> <span data-ttu-id="b7266-173">Můžete přidat prostého **třídy** volá `Startup` místo.</span><span class="sxs-lookup"><span data-stu-id="b7266-173">You can add a plain **Class** called `Startup` instead.</span></span>
7. <span data-ttu-id="b7266-174">Změňte obsah nová třída při spuštění následující.</span><span class="sxs-lookup"><span data-stu-id="b7266-174">Change the contents of the new Startup class to the following.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. <span data-ttu-id="b7266-175">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Stránka HTML**.</span><span class="sxs-lookup"><span data-stu-id="b7266-175">In **Solution Explorer**, right-click the project, then click **Add | HTML Page**.</span></span> <span data-ttu-id="b7266-176">Zadejte název nové stránky `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b7266-176">Name the new page `index.html`.</span></span>
    >[!NOTE]
    ><span data-ttu-id="b7266-177">Možná budete muset změnit čísla verzí pro odkazy na knihovny JQuery a technologie SignalR</span><span class="sxs-lookup"><span data-stu-id="b7266-177">You might need to change the version numbers for the references to JQuery and SignalR libraries</span></span>
9. <span data-ttu-id="b7266-178">V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku HTML, který jste právě vytvořili a klikněte na tlačítko **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="b7266-178">In **Solution Explorer**, right-click the HTML page you just created and click **Set as Start Page**.</span></span>
10. <span data-ttu-id="b7266-179">Nahraďte kód výchozí stránku HTML s následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="b7266-179">Replace the default code in the HTML page with the following code.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b7266-180">Novější verzi SignalR skriptů, které mohou být nainstalovány službou Správce balíčků.</span><span class="sxs-lookup"><span data-stu-id="b7266-180">A later version of the SignalR scripts may be installed by the package manager.</span></span> <span data-ttu-id="b7266-181">Ověřte, že odkazy na skript níže odpovídají verzi souborů skript v projektu (budou lišit, když jste přidali pomocí nástroje NuGet a místo přidávání rozbočovači SignalR.)</span><span class="sxs-lookup"><span data-stu-id="b7266-181">Verify that the script references below correspond to the versions of the script files in the project (they will be different if you added SignalR using NuGet rather than adding a hub.)</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]
11. <span data-ttu-id="b7266-182">**Uložit vše** pro projekt.</span><span class="sxs-lookup"><span data-stu-id="b7266-182">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="b7266-183">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="b7266-183">Run the Sample</span></span>

1. <span data-ttu-id="b7266-184">Stisknutím klávesy F5 spusťte projekt v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="b7266-184">Press F5 to run the project in debug mode.</span></span> <span data-ttu-id="b7266-185">HTML stránka načte v instanci prohlížeče a vyzve k zadání uživatelského jména.</span><span class="sxs-lookup"><span data-stu-id="b7266-185">The HTML page loads in a browser instance and prompts for a user name.</span></span>

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr/_static/image4.png)
2. <span data-ttu-id="b7266-187">Zadejte uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="b7266-187">Enter a user name.</span></span>
3. <span data-ttu-id="b7266-188">Zkopírujte adresu URL v adresním řádku prohlížeče a můžete otevřít dvě další instance prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b7266-188">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="b7266-189">V každé instanci prohlížeče zadejte jedinečné uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="b7266-189">In each browser instance, enter a unique user name.</span></span>
4. <span data-ttu-id="b7266-190">V každé instanci prohlížeče, přidejte komentář a klikněte na tlačítko **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="b7266-190">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="b7266-191">Zobrazit komentáře ve všech instancích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b7266-191">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b7266-192">Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru.</span><span class="sxs-lookup"><span data-stu-id="b7266-192">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="b7266-193">Centrum vysílá poznámky pro všechny aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="b7266-193">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="b7266-194">Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.</span><span class="sxs-lookup"><span data-stu-id="b7266-194">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="b7266-195">Následující snímek obrazovky ukazuje chatovací aplikaci spuštěnou v tři instance prohlížeče, které aktualizují po jedné instance odešle zprávu:</span><span class="sxs-lookup"><span data-stu-id="b7266-195">The following screen shot shows the chat application running in three browser instances, all of which are updated when one instance sends a message:</span></span>

    ![Chat prohlížeče](tutorial-getting-started-with-signalr/_static/image5.png)
5. <span data-ttu-id="b7266-197">V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7266-197">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="b7266-198">Existuje soubor skriptu s názvem **rozbočovače** generující knihovně SignalR dynamicky za běhu.</span><span class="sxs-lookup"><span data-stu-id="b7266-198">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="b7266-199">Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="b7266-199">This file manages the communication between jQuery script and server-side code.</span></span>

    ![](tutorial-getting-started-with-signalr/_static/image6.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="b7266-200">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="b7266-200">Examine the Code</span></span>

<span data-ttu-id="b7266-201">Chatovací aplikace SignalR ukazuje dvě základní úkoly vývoje SignalR: vytváří se centrum jako objekt hlavního koordinace na serveru a pomocí knihovny jQuery SignalR k odesílání a příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="b7266-201">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="b7266-202">Rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="b7266-202">SignalR Hubs</span></span>

<span data-ttu-id="b7266-203">V ukázce kódu **ChatHub** třída odvozena z **Microsoft.AspNet.SignalR.Hub** třídy.</span><span class="sxs-lookup"><span data-stu-id="b7266-203">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="b7266-204">Odvozování z **centra** třída je užitečný způsob, jak vytvořit aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="b7266-204">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="b7266-205">Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z skripty na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="b7266-205">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="b7266-206">V kódu, konverzace, klienti volání **ChatHub.Send** metoda odesílá nová zpráva.</span><span class="sxs-lookup"><span data-stu-id="b7266-206">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="b7266-207">Centrum pak odešle zprávu pro všechny klienty voláním **Clients.All.broadcastMessage**.</span><span class="sxs-lookup"><span data-stu-id="b7266-207">The hub in turn sends the message to all clients by calling **Clients.All.broadcastMessage**.</span></span>

<span data-ttu-id="b7266-208">**Odeslat** metoda ukazuje několik konceptů hub:</span><span class="sxs-lookup"><span data-stu-id="b7266-208">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="b7266-209">Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.</span><span class="sxs-lookup"><span data-stu-id="b7266-209">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="b7266-210">Použití **Microsoft.AspNet.SignalR.Hub.Clients** dynamických vlastností pro přístup k všechny klienty připojené pro toto centrum.</span><span class="sxs-lookup"><span data-stu-id="b7266-210">Use the **Microsoft.AspNet.SignalR.Hub.Clients** dynamic property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="b7266-211">Volání funkce na straně klienta (například `broadcastMessage` funkce) aktualizovat klienty.</span><span class="sxs-lookup"><span data-stu-id="b7266-211">Call a function on the client (such as the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="b7266-212">SignalR a jQuery</span><span class="sxs-lookup"><span data-stu-id="b7266-212">SignalR and jQuery</span></span>

<span data-ttu-id="b7266-213">Na stránce HTML ve vzorovém kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="b7266-213">The HTML page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="b7266-214">Proxy server tak, aby odkazovaly rozbočovače, deklarace funkce, která můžete volat na serveru, abyste předávaný obsah pro klienty a spouští se připojení k odeslání zprávy do centra jsou deklarace základních úloh v kódu.</span><span class="sxs-lookup"><span data-stu-id="b7266-214">The essential tasks in the code are declaring a proxy to reference the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="b7266-215">Následující kód deklaruje odkaz na proxy server rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="b7266-215">The following code declares a reference to a hub proxy.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> <span data-ttu-id="b7266-216">V jazyce JavaScript je odkaz na třídu serveru a jeho členy v stylem camel case.</span><span class="sxs-lookup"><span data-stu-id="b7266-216">In JavaScript the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="b7266-217">Odkazuje na vzorový kód jazyka C# **ChatHub** třídy v jazyce JavaScript jako **chatHub**.</span><span class="sxs-lookup"><span data-stu-id="b7266-217">The code sample references the C# **ChatHub** class in JavaScript as **chatHub**.</span></span>


<span data-ttu-id="b7266-218">Následující kód je, jak vytvořit funkci zpětného volání ve skriptu.</span><span class="sxs-lookup"><span data-stu-id="b7266-218">The following code is how you create a callback function in the script.</span></span> <span data-ttu-id="b7266-219">Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty.</span><span class="sxs-lookup"><span data-stu-id="b7266-219">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="b7266-220">Následující dva řádky, že s kódováním HTML obsah před jeho zobrazení jsou volitelné a zobrazit jednoduchý způsob, jak brání injektáži skriptu.</span><span class="sxs-lookup"><span data-stu-id="b7266-220">The two lines that HTML encode the content before displaying it are optional and show a simple way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

<span data-ttu-id="b7266-221">Následující kód ukazuje, jak otevřít připojení v centru.</span><span class="sxs-lookup"><span data-stu-id="b7266-221">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="b7266-222">Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce HTML.</span><span class="sxs-lookup"><span data-stu-id="b7266-222">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

> [!NOTE]
> <span data-ttu-id="b7266-223">Tento přístup zajišťuje, že připojení před provedením obslužná rutina události.</span><span class="sxs-lookup"><span data-stu-id="b7266-223">This approach ensures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="b7266-224">Další kroky</span><span class="sxs-lookup"><span data-stu-id="b7266-224">Next Steps</span></span>

<span data-ttu-id="b7266-225">Jste zjistili, že SignalR je architektura určená k vytváření aplikací webu v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="b7266-225">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="b7266-226">Také jste se naučili několik úloh vývoje SignalR: jak přidat do aplikace ASP.NET SignalR, jak vytvořit třídu centra a jak odesílat a přijímat zprávy z centra.</span><span class="sxs-lookup"><span data-stu-id="b7266-226">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="b7266-227">Návod k nasazení ukázkové aplikace SignalR pro Azure najdete v tématu [pomocí SignalR s webovými aplikacemi ve službě Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span><span class="sxs-lookup"><span data-stu-id="b7266-227">For a walkthrough on how to deploy the sample SignalR application to Azure, see [Using SignalR with Web Apps in Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span></span> <span data-ttu-id="b7266-228">Podrobné informace o tom, jak nasadit webový projekt sady Visual Studio na webu Windows Azure naleznete v tématu [vytvoření webové aplikace ASP.NET ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span><span class="sxs-lookup"><span data-stu-id="b7266-228">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Create an ASP.NET web app in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span></span>

<span data-ttu-id="b7266-229">Informace o pokročilejších pojmech vývoj SignalR, naleznete na následujících stránkách pro funkci SignalR zdrojový kód a prostředky:</span><span class="sxs-lookup"><span data-stu-id="b7266-229">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="b7266-230">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="b7266-230">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="b7266-231">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="b7266-231">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="b7266-232">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="b7266-232">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

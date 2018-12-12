---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
title: 'Kurz: Začínáme s knihovnou SignalR 1.x a MVC 4 | Dokumentace Microsoftu'
author: pfletcher
description: Pomocí funkce SignalR technologie ASP.NET a ASP.NET MVC 4 k sestavení aplikace pro chatování v reálném čase.
ms.author: riande
ms.date: 03/29/2013
ms.assetid: eeef9f73-6de3-49f9-b50b-9af22108f2ce
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
msc.type: authoredcontent
ms.openlocfilehash: a67d05288252c17d84b1d7df5f7bcddde3c887f5
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287707"
---
<a name="tutorial-getting-started-with-signalr-1x-and-mvc-4"></a><span data-ttu-id="2d805-103">Kurz: Začínáme s knihovnou SignalR 1.x a MVC 4</span><span class="sxs-lookup"><span data-stu-id="2d805-103">Tutorial: Getting Started with SignalR 1.x and MVC 4</span></span>
====================
<span data-ttu-id="2d805-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="2d805-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="2d805-105">Tento kurz ukazuje, jak používat knihovnu ASP.NET SignalR k vytvoření aplikace pro chatování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="2d805-105">This tutorial shows how to use ASP.NET SignalR to create a real-time chat application.</span></span> <span data-ttu-id="2d805-106">Přidáte funkci SignalR k aplikaci MVC 4 a vytvořit zobrazení chatu k odesílání a zobrazení zprávy.</span><span class="sxs-lookup"><span data-stu-id="2d805-106">You will add SignalR to an MVC 4 application and create a chat view to send and display messages.</span></span>


## <a name="overview"></a><span data-ttu-id="2d805-107">Přehled</span><span class="sxs-lookup"><span data-stu-id="2d805-107">Overview</span></span>

<span data-ttu-id="2d805-108">Tento kurz vás seznámí s vývoj webu v reálném čase aplikace s použitím funkce SignalR technologie ASP.NET a ASP.NET MVC 4.</span><span class="sxs-lookup"><span data-stu-id="2d805-108">This tutorial introduces you to real-time web application development with ASP.NET SignalR and ASP.NET MVC 4.</span></span> <span data-ttu-id="2d805-109">V tomto kurzu použijete stejný kód aplikace chat jako [kurzu Začínáme se SignalR](tutorial-getting-started-with-signalr.md), ale ukazuje, jak přidat do aplikace MVC 4 na základě šablony Internet.</span><span class="sxs-lookup"><span data-stu-id="2d805-109">The tutorial uses the same chat application code as the [SignalR Getting Started tutorial](tutorial-getting-started-with-signalr.md), but shows how to add it to an MVC 4 application based on the Internet template.</span></span>

<span data-ttu-id="2d805-110">V tomto tématu se dozvíte následujících úkolů SignalR:</span><span class="sxs-lookup"><span data-stu-id="2d805-110">In this topic you will learn the following SignalR development tasks:</span></span>

- <span data-ttu-id="2d805-111">Přidání knihovny SignalR pro aplikaci MVC 4.</span><span class="sxs-lookup"><span data-stu-id="2d805-111">Adding the SignalR library to an MVC 4 application.</span></span>
- <span data-ttu-id="2d805-112">Vytvoření třídy centra tak, aby nabízel obsah pro klienty.</span><span class="sxs-lookup"><span data-stu-id="2d805-112">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="2d805-113">K odesílání zpráv a zobrazení aktualizací z centra pomocí knihovny jQuery SignalR na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="2d805-113">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="2d805-114">Následující snímek obrazovky ukazuje dokončené chatovací aplikaci spuštěnou v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="2d805-114">The following screen shot shows the completed chat application running in a browser.</span></span>

![Instance chatu](tutorial-getting-started-with-signalr-and-mvc-4/_static/image2.png)

<span data-ttu-id="2d805-116">Oddíly:</span><span class="sxs-lookup"><span data-stu-id="2d805-116">Sections:</span></span>

- [<span data-ttu-id="2d805-117">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="2d805-117">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="2d805-118">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="2d805-118">Run the Sample</span></span>](#run)
- [<span data-ttu-id="2d805-119">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="2d805-119">Examine the Code</span></span>](#code)
- [<span data-ttu-id="2d805-120">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2d805-120">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="2d805-121">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="2d805-121">Set up the Project</span></span>

<span data-ttu-id="2d805-122">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="2d805-122">Prerequisites:</span></span>

- <span data-ttu-id="2d805-123">Visual Studio 2010 SP1, Visual Studio 2012 nebo Visual Studio 2012 Express.</span><span class="sxs-lookup"><span data-stu-id="2d805-123">Visual Studio 2010 SP1, Visual Studio 2012, or Visual Studio 2012 Express.</span></span> <span data-ttu-id="2d805-124">Pokud nemáte Visual Studio, přečtěte si téma [ASP.NET stáhne](https://www.asp.net/downloads) získat bezplatné Visual Studio 2012 Express vývojový nástroj.</span><span class="sxs-lookup"><span data-stu-id="2d805-124">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2012 Express Development Tool.</span></span>
- <span data-ttu-id="2d805-125">Pro sadu Visual Studio 2010, nainstalujte [ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683).</span><span class="sxs-lookup"><span data-stu-id="2d805-125">For Visual Studio 2010, install [ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683).</span></span>

<span data-ttu-id="2d805-126">Tato část ukazuje, jak vytvořit aplikaci ASP.NET MVC 4, přidejte knihovny SignalR a vytvořit chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d805-126">This section shows how to create an ASP.NET MVC 4 application, add the SignalR library, and create the chat application.</span></span>

1. 1. <span data-ttu-id="2d805-127">V sadě Visual Studio vytvořit aplikaci ASP.NET MVC 4, pojmenujte ho SignalRChat a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="2d805-127">In Visual Studio create an ASP.NET MVC 4 application, name it SignalRChat, and click OK.</span></span>

        > [!NOTE]
        > <span data-ttu-id="2d805-128">Ve VS 2010, vyberte **rozhraní .NET Framework 4** v ovládacím prvku rozevíracího seznamu verzi rozhraní Framework.</span><span class="sxs-lookup"><span data-stu-id="2d805-128">In VS 2010, select **.NET Framework 4** in the Framework version dropdown control.</span></span> <span data-ttu-id="2d805-129">SignalR kód běží na rozhraní .NET Framework verze 4 a 4.5.</span><span class="sxs-lookup"><span data-stu-id="2d805-129">SignalR code runs on .NET Framework versions 4 and 4.5.</span></span>

        ![Vytvořte mvc web](tutorial-getting-started-with-signalr-and-mvc-4/_static/image3.png)
      2. <span data-ttu-id="2d805-131">Vyberte šablonu aplikace Internet, zrušte zaškrtnutí políčka pro **vytvořit projekt testování částí**a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="2d805-131">Select the Internet Application template, clear the option to **Create a unit test project**, and click OK.</span></span>

         ![Vytvořte web na Internetu mvc](tutorial-getting-started-with-signalr-and-mvc-4/_static/image4.png)
      3. <span data-ttu-id="2d805-133">Otevřít **nástroje > Správce balíčků NuGet > Konzola správce balíčků** a spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="2d805-133">Open the **Tools > NuGet Package Manager > Package Manager Console** and run the following command.</span></span> <span data-ttu-id="2d805-134">Tento krok přidává do projektu sadu souborů skriptů a odkazy na sestavení, které umožňují funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="2d805-134">This step adds to the project a set of script files and assembly references that enable SignalR functionality.</span></span>

         `install-package Microsoft.AspNet.SignalR -Version 1.1.3`
      4. <span data-ttu-id="2d805-135">V **Průzkumníka řešení** rozbalte složku skripty.</span><span class="sxs-lookup"><span data-stu-id="2d805-135">In **Solution Explorer** expand the Scripts folder.</span></span> <span data-ttu-id="2d805-136">Všimněte si, že byly přidány do projektu knihovny skriptů pro funkci SignalR.</span><span class="sxs-lookup"><span data-stu-id="2d805-136">Note that script libraries for SignalR have been added to the project.</span></span>

         ![Odkazy na knihovny](tutorial-getting-started-with-signalr-and-mvc-4/_static/image6.png)
      5. <span data-ttu-id="2d805-138">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Nová složka**, a přidejte novou složku s názvem **rozbočovače**.</span><span class="sxs-lookup"><span data-stu-id="2d805-138">In **Solution Explorer**, right-click the project, select **Add | New Folder**, and add a new folder named **Hubs**.</span></span>
      6. <span data-ttu-id="2d805-139">Klikněte pravým tlačítkem myši **rozbočovače** složky, klikněte na tlačítko **přidat | Třída**a vytvořte novou třídu C# s názvem **ChatHub.cs**.</span><span class="sxs-lookup"><span data-stu-id="2d805-139">Right-click the **Hubs** folder, click **Add | Class**, and create a new C# class named **ChatHub.cs**.</span></span> <span data-ttu-id="2d805-140">Tato třída bude používat jako server rozbočovače SignalR, která odesílá zprávy na všechny klienty.</span><span class="sxs-lookup"><span data-stu-id="2d805-140">You will use this class as a SignalR server hub that sends messages to all clients.</span></span>

> [!NOTE]
> <span data-ttu-id="2d805-141">Pokud používáte sadu Visual Studio 2012 a nainstalovali [ASP.NET and Web Tools 2012.2 aktualizace](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation), můžete použít novou šablonu položky SignalR k vytvoření třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="2d805-141">If you use Visual Studio 2012 and have installed the [ASP.NET and Web Tools 2012.2 update](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation), you can use the new SignalR item template to create the hub class.</span></span> <span data-ttu-id="2d805-142">To mohli udělat, klikněte pravým tlačítkem myši **rozbočovače** složky, klikněte na tlačítko **přidat | Nová položka**vyberte **třída rozbočovače SignalR (v1)** a název třídy **ChatHub.cs**.</span><span class="sxs-lookup"><span data-stu-id="2d805-142">To do that, right-click the **Hubs** folder, click **Add | New Item**, select **SignalR Hub Class (v1)**, and name the class **ChatHub.cs**.</span></span>


1. <span data-ttu-id="2d805-143">Nahraďte kód v **ChatHub** třídy následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="2d805-143">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample1.cs)]
2. <span data-ttu-id="2d805-144">Otevřít **Global.asax** souboru pro projekt a přidejte volání do metody `RouteTable.Routes.MapHubs();` jako první řádek kódu v `Application_Start` metody.</span><span class="sxs-lookup"><span data-stu-id="2d805-144">Open the **Global.asax** file for the project, and add a call to the method `RouteTable.Routes.MapHubs();` as the first line of code in the `Application_Start` method.</span></span> <span data-ttu-id="2d805-145">Tento kód zaregistruje výchozí trasu rozbočovače SignalR a musí být volán předtím, než zaregistrujete jakýchkoli jiných tras.</span><span class="sxs-lookup"><span data-stu-id="2d805-145">This code registers the default route for SignalR hubs and must be called before you register any other routes.</span></span> <span data-ttu-id="2d805-146">Dokončené `Application_Start` metoda vypadá jako v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="2d805-146">The completed `Application_Start` method looks like the following example.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample2.cs)]
3. <span data-ttu-id="2d805-147">Upravit `HomeController` třída součástí **Controllers/HomeController.cs** a přidejte následující metodu do třídy.</span><span class="sxs-lookup"><span data-stu-id="2d805-147">Edit the `HomeController` class found in **Controllers/HomeController.cs** and add the following method to the class.</span></span> <span data-ttu-id="2d805-148">Tato metoda vrátí **Chat** zobrazení, které vytvoříte v pozdějším kroku.</span><span class="sxs-lookup"><span data-stu-id="2d805-148">This method returns the **Chat** view that you will create in a later step.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample3.cs)]
4. <span data-ttu-id="2d805-149">Klikněte pravým tlačítkem `Chat` metoda jste právě vytvořili a klikněte na tlačítko **přidat zobrazení** k vytvoření nového zobrazení souboru.</span><span class="sxs-lookup"><span data-stu-id="2d805-149">Right-click within the `Chat` method you just created, and click **Add View** to create a new view file.</span></span>
5. <span data-ttu-id="2d805-150">V **přidat zobrazení** dialogového okna, ujistěte se, že políčko k **použít rozložení stránky předlohy** (zrušte zaškrtnutí ostatních políček) a potom klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="2d805-150">In the **Add View** dialog, make sure the check box is selected to **Use a layout or master page** (clear the other check boxes), and then click **Add**.</span></span>

    ![Přidání zobrazení](tutorial-getting-started-with-signalr-and-mvc-4/_static/image8.png)
6. <span data-ttu-id="2d805-152">Upravte nový soubor zobrazení s názvem **Chat.cshtml**.</span><span class="sxs-lookup"><span data-stu-id="2d805-152">Edit the new view file named **Chat.cshtml**.</span></span> <span data-ttu-id="2d805-153">Po &lt;h2&gt; značku, vložte následující &lt;div&gt; oddílu a `@section scripts` blok kódu do stránky.</span><span class="sxs-lookup"><span data-stu-id="2d805-153">After the &lt;h2&gt; tag, paste the following &lt;div&gt; section and `@section scripts` code block into the page.</span></span> <span data-ttu-id="2d805-154">Tímto skriptem povolíte stránku k odeslání zprávy chatu a zobrazení zprávy ze serveru.</span><span class="sxs-lookup"><span data-stu-id="2d805-154">This script enables the page to send chat messages and display messages from the server.</span></span> <span data-ttu-id="2d805-155">Kompletní kód pro chat zobrazení se zobrazí v následující blok kódu.</span><span class="sxs-lookup"><span data-stu-id="2d805-155">The complete code for the chat view appears in the following code block.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="2d805-156">Když přidáte SignalR a další skript knihovny do projektu sady Visual Studio, může nainstalovat Správce balíčků verze skripty, které jsou novější než verze uvedené v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="2d805-156">When you add SignalR and other script libraries to your Visual Studio project, the Package Manager might install versions of the scripts that are more recent than the versions shown in this topic.</span></span> <span data-ttu-id="2d805-157">Ujistěte se, že skript odkazy ve vašem kódu shodovat s verzemi knihovny skriptů, které jsou nainstalovány ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="2d805-157">Make sure that the script references in your code match the versions of the script libraries installed in your project.</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample4.cshtml)]
7. <span data-ttu-id="2d805-158">**Uložit vše** pro projekt.</span><span class="sxs-lookup"><span data-stu-id="2d805-158">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="2d805-159">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="2d805-159">Run the Sample</span></span>

1. <span data-ttu-id="2d805-160">Stisknutím klávesy F5 spusťte projekt v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="2d805-160">Press F5 to run the project in debug mode.</span></span>
2. <span data-ttu-id="2d805-161">V adresním řádku prohlížeče připojit **/home/chat** odeslané na adresu výchozí stránky pro projekt.</span><span class="sxs-lookup"><span data-stu-id="2d805-161">In the browser address line, append **/home/chat** to the URL of the default page for the project.</span></span> <span data-ttu-id="2d805-162">Chat stránka načte v instanci prohlížeče a vyzve k zadání uživatelského jména.</span><span class="sxs-lookup"><span data-stu-id="2d805-162">The Chat page loads in a browser instance and prompts for a user name.</span></span>

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr-and-mvc-4/_static/image9.png)
3. <span data-ttu-id="2d805-164">Zadejte uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="2d805-164">Enter a user name.</span></span>
4. <span data-ttu-id="2d805-165">Zkopírujte adresu URL v adresním řádku prohlížeče a můžete otevřít dvě další instance prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2d805-165">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="2d805-166">V každé instanci prohlížeče zadejte jedinečné uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="2d805-166">In each browser instance, enter a unique user name.</span></span>
5. <span data-ttu-id="2d805-167">V každé instanci prohlížeče, přidejte komentář a klikněte na tlačítko **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="2d805-167">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="2d805-168">Zobrazit komentáře ve všech instancích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2d805-168">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="2d805-169">Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru.</span><span class="sxs-lookup"><span data-stu-id="2d805-169">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="2d805-170">Centrum vysílá poznámky pro všechny aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="2d805-170">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="2d805-171">Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.</span><span class="sxs-lookup"><span data-stu-id="2d805-171">Users who join the chat later will see messages added from the time they join.</span></span>
6. <span data-ttu-id="2d805-172">Na následujícím snímku obrazovky je vidět chatovací aplikace spuštěné v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="2d805-172">The following screen shot shows the chat application running in a browser.</span></span>

    ![Chat prohlížeče](tutorial-getting-started-with-signalr-and-mvc-4/_static/image11.png)
7. <span data-ttu-id="2d805-174">V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d805-174">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="2d805-175">Tento uzel je viditelný v režimu ladění, pokud používáte Internet Explorer jako svůj prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="2d805-175">This node is visible in debug mode if you are using Internet Explorer as your browser.</span></span> <span data-ttu-id="2d805-176">Existuje soubor skriptu s názvem **rozbočovače** generující knihovně SignalR dynamicky za běhu.</span><span class="sxs-lookup"><span data-stu-id="2d805-176">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="2d805-177">Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="2d805-177">This file manages the communication between jQuery script and server-side code.</span></span> <span data-ttu-id="2d805-178">Pokud používáte prohlížeč než Internet Explorer, se dá dostat taky dynamické **rozbočovače** souboru tak, že přejdete na ni přímo, například http://mywebsite/signalr/hubs.</span><span class="sxs-lookup"><span data-stu-id="2d805-178">If you use a browser other than Internet Explorer, you can also access the dynamic **hubs** file by browsing to it directly, for example http://mywebsite/signalr/hubs.</span></span>

    ![Vygenerovaný centra skriptů](tutorial-getting-started-with-signalr-and-mvc-4/_static/image13.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="2d805-180">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="2d805-180">Examine the Code</span></span>

<span data-ttu-id="2d805-181">Chatovací aplikace SignalR ukazuje dvě základní úkoly vývoje SignalR: vytváří se centrum jako objekt hlavního koordinace na serveru a pomocí knihovny jQuery SignalR k odesílání a příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="2d805-181">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="2d805-182">Rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="2d805-182">SignalR Hubs</span></span>

<span data-ttu-id="2d805-183">V ukázce kódu **ChatHub** třída odvozena z **Microsoft.AspNet.SignalR.Hub** třídy.</span><span class="sxs-lookup"><span data-stu-id="2d805-183">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="2d805-184">Odvozování z **centra** třída je užitečný způsob, jak vytvořit aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="2d805-184">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="2d805-185">Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z jQuery skripty na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="2d805-185">You can create public methods on your hub class and then access those methods by calling them from jQuery scripts in a web page.</span></span>

<span data-ttu-id="2d805-186">V kódu, konverzace, klienti volání **ChatHub.Send** metoda odesílá nová zpráva.</span><span class="sxs-lookup"><span data-stu-id="2d805-186">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="2d805-187">Centrum pak odešle zprávu pro všechny klienty voláním **Clients.All.addNewMessageToPage**.</span><span class="sxs-lookup"><span data-stu-id="2d805-187">The hub in turn sends the message to all clients by calling **Clients.All.addNewMessageToPage**.</span></span>

<span data-ttu-id="2d805-188">**Odeslat** metoda ukazuje několik konceptů hub:</span><span class="sxs-lookup"><span data-stu-id="2d805-188">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="2d805-189">Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.</span><span class="sxs-lookup"><span data-stu-id="2d805-189">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="2d805-190">Použití **Microsoft.AspNet.SignalR.Hub.Clients** vlastnosti pro přístup k všechny klienty připojené pro toto centrum.</span><span class="sxs-lookup"><span data-stu-id="2d805-190">Use the **Microsoft.AspNet.SignalR.Hub.Clients** property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="2d805-191">Volání funkce jQuery na straně klienta (například `addNewMessageToPage` funkce) aktualizovat klienty.</span><span class="sxs-lookup"><span data-stu-id="2d805-191">Call a jQuery function on the client (such as the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="2d805-192">SignalR a jQuery</span><span class="sxs-lookup"><span data-stu-id="2d805-192">SignalR and jQuery</span></span>

<span data-ttu-id="2d805-193">**Chat.cshtml** zobrazení souboru v příkladu kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="2d805-193">The **Chat.cshtml** view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="2d805-194">Odkaz na automaticky generovaný proxy serveru pro rozbočovač, deklarace funkce, která můžete volat na serveru, abyste předávaný obsah pro klienty a spuštění připojení pro odesílání zpráv do centra vytváření základních úloh v kódu.</span><span class="sxs-lookup"><span data-stu-id="2d805-194">The essential tasks in the code are creating a reference to the auto-generated proxy for the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="2d805-195">Následující kód deklaruje proxy server rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="2d805-195">The following code declares a proxy for a hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="2d805-196">V jQuery je odkaz na třídu serveru a jeho členy v stylem camel case.</span><span class="sxs-lookup"><span data-stu-id="2d805-196">In jQuery the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="2d805-197">Odkazuje na vzorový kód jazyka C# **ChatHub** třídy v jQuery jako **chatHub**.</span><span class="sxs-lookup"><span data-stu-id="2d805-197">The code sample references the C# **ChatHub** class in jQuery as **chatHub**.</span></span> <span data-ttu-id="2d805-198">Pokud chcete odkazovat `ChatHub` třídy v jQuery s konvenčním Pascal malá a velká písmena stejně jako v jazyce C#, upravte soubor třídy ChatHub.cs.</span><span class="sxs-lookup"><span data-stu-id="2d805-198">If you want to reference the `ChatHub` class in jQuery with conventional Pascal casing as you would in C#, edit the ChatHub.cs class file.</span></span> <span data-ttu-id="2d805-199">Přidat `using` příkaz tak, aby odkazovaly `Microsoft.AspNet.SignalR.Hubs` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="2d805-199">Add a `using` statement to reference the `Microsoft.AspNet.SignalR.Hubs` namespace.</span></span> <span data-ttu-id="2d805-200">Pak přidejte `HubName` atribut `ChatHub` třídy, například `[HubName("ChatHub")]`.</span><span class="sxs-lookup"><span data-stu-id="2d805-200">Then add the `HubName` attribute to the `ChatHub` class, for example `[HubName("ChatHub")]`.</span></span> <span data-ttu-id="2d805-201">Nakonec aktualizujte referenci jQuery pro `ChatHub` třídy.</span><span class="sxs-lookup"><span data-stu-id="2d805-201">Finally, update your jQuery reference to the `ChatHub` class.</span></span>


<span data-ttu-id="2d805-202">Následující kód ukazuje, jak vytvořit funkci zpětného volání ve skriptu.</span><span class="sxs-lookup"><span data-stu-id="2d805-202">The following code shows how to create a callback function in the script.</span></span> <span data-ttu-id="2d805-203">Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty.</span><span class="sxs-lookup"><span data-stu-id="2d805-203">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="2d805-204">Volitelné volání `htmlEncode` funkce ukazuje způsob, jak HTML kódování obsahu zprávy před jejich zobrazením na stránce jako způsob, jak brání injektáži skriptu.</span><span class="sxs-lookup"><span data-stu-id="2d805-204">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page, as a way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample7.html)]

<span data-ttu-id="2d805-205">Následující kód ukazuje, jak otevřít připojení v centru.</span><span class="sxs-lookup"><span data-stu-id="2d805-205">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="2d805-206">Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce konverzace.</span><span class="sxs-lookup"><span data-stu-id="2d805-206">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

> [!NOTE]
> <span data-ttu-id="2d805-207">Tento přístup zajišťuje, že připojení před provedením obslužná rutina události.</span><span class="sxs-lookup"><span data-stu-id="2d805-207">This approach ensures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="2d805-208">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2d805-208">Next Steps</span></span>

<span data-ttu-id="2d805-209">Jste zjistili, že SignalR je architektura určená k vytváření aplikací webu v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="2d805-209">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="2d805-210">Také jste se naučili několik úloh vývoje SignalR: jak přidat do aplikace ASP.NET SignalR, jak vytvořit třídu centra a jak odesílat a přijímat zprávy z centra.</span><span class="sxs-lookup"><span data-stu-id="2d805-210">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="2d805-211">Informace o pokročilejších pojmech vývoj SignalR, naleznete na následujících stránkách pro funkci SignalR zdrojový kód a prostředky:</span><span class="sxs-lookup"><span data-stu-id="2d805-211">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources :</span></span>

- [<span data-ttu-id="2d805-212">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="2d805-212">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="2d805-213">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="2d805-213">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="2d805-214">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="2d805-214">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Kurz: Začínáme s SignalR 2 a MVC 5 | Microsoft Docs'
author: pfletcher
description: Tento kurz ukazuje, jak vytvořit v reálném čase chatovací aplikace pomocí ASP.NET SignalR 2. SignalR přidáte do aplikace MVC 5 a vytvořit zobrazení chat...
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.openlocfilehash: ca0471114da7363c5df9d459308708e7ab4f8b84
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
---
<a name="tutorial-getting-started-with-signalr-2-and-mvc-5"></a><span data-ttu-id="55a1a-104">Kurz: Začínáme s SignalR 2 a MVC 5</span><span class="sxs-lookup"><span data-stu-id="55a1a-104">Tutorial: Getting Started with SignalR 2 and MVC 5</span></span>
====================
<span data-ttu-id="55a1a-105">podle [Patrik Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="55a1a-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[<span data-ttu-id="55a1a-106">Stáhněte si dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="55a1a-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

> <span data-ttu-id="55a1a-107">Tento kurz ukazuje, jak vytvořit v reálném čase chatovací aplikace pomocí ASP.NET SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="55a1a-107">This tutorial shows how to use ASP.NET SignalR 2 to create a real-time chat application.</span></span> <span data-ttu-id="55a1a-108">SignalR přidáte do aplikace MVC 5 a vytvořit zobrazení chatu a odesílat zprávy o zobrazení.</span><span class="sxs-lookup"><span data-stu-id="55a1a-108">You will add SignalR to an MVC 5 application and create a chat view to send and display messages.</span></span> 
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="55a1a-109">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="55a1a-109">Software versions used in the tutorial</span></span>
> 
> 
> - [<span data-ttu-id="55a1a-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="55a1a-110">Visual Studio 2013</span></span>](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - <span data-ttu-id="55a1a-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="55a1a-111">.NET 4.5</span></span>
> - <span data-ttu-id="55a1a-112">MVC 5</span><span class="sxs-lookup"><span data-stu-id="55a1a-112">MVC 5</span></span>
> - <span data-ttu-id="55a1a-113">SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="55a1a-113">SignalR version 2</span></span>
>   
> 
> 
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="55a1a-114">Tento kurz pomocí sady Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="55a1a-114">Using Visual Studio 2012 with this tutorial</span></span>
> 
> 
> <span data-ttu-id="55a1a-115">Pokud chcete používat Visual Studio 2012 v tomto kurzu, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="55a1a-115">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
> 
> - <span data-ttu-id="55a1a-116">Aktualizace vašeho [Správce balíčků](http://docs.nuget.org/docs/start-here/installing-nuget) na nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="55a1a-116">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="55a1a-117">Nainstalujte [webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="55a1a-117">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="55a1a-118">Ve webové platformy, vyhledání a instalace **ASP.NET a webové nástroje 2013.1 pro sadu Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-118">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="55a1a-119">Šablony sady Visual Studio pro třídy SignalR dojde k instalaci, jako **rozbočovače**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-119">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="55a1a-120">Některé šablony (jako například **třídy pro spuštění OWIN**) nebudou k dispozici; pro tyto třídy soubor použít místo.</span><span class="sxs-lookup"><span data-stu-id="55a1a-120">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
> 
> 
> ## <a name="tutorial-versions"></a><span data-ttu-id="55a1a-121">Kurz verze</span><span class="sxs-lookup"><span data-stu-id="55a1a-121">Tutorial Versions</span></span>
> 
> <span data-ttu-id="55a1a-122">Informace o předchozích verzích SignalR najdete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="55a1a-122">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
> 
> ## <a name="questions-and-comments"></a><span data-ttu-id="55a1a-123">Dotazy a připomínky</span><span class="sxs-lookup"><span data-stu-id="55a1a-123">Questions and comments</span></span>
> 
> <span data-ttu-id="55a1a-124">Prosím sdělit svůj názor na tom, jak líbilo tohoto kurzu a co jsme může zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="55a1a-124">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="55a1a-125">Pokud máte otázky, které přímo nesouvisejí s kurz, můžete je do příspěvku [fórum pro ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="55a1a-125">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="55a1a-126">Přehled</span><span class="sxs-lookup"><span data-stu-id="55a1a-126">Overview</span></span>

<span data-ttu-id="55a1a-127">Tento kurz vás seznámí s vývoj v reálném čase webové aplikace s ASP.NET SignalR 2 a ASP.NET MVC 5.</span><span class="sxs-lookup"><span data-stu-id="55a1a-127">This tutorial introduces you to real-time web application development with ASP.NET SignalR 2 and ASP.NET MVC 5.</span></span> <span data-ttu-id="55a1a-128">Tento kurz používá stejný kód aplikace chat jako [kurzu Začínáme SignalR](tutorial-getting-started-with-signalr.md), ale ukazuje, jak přidat do aplikace MVC 5.</span><span class="sxs-lookup"><span data-stu-id="55a1a-128">The tutorial uses the same chat application code as the [SignalR Getting Started tutorial](tutorial-getting-started-with-signalr.md), but shows how to add it to an MVC 5 application.</span></span>

<span data-ttu-id="55a1a-129">V tomto tématu se dozvíte následující úlohy vývoj SignalR:</span><span class="sxs-lookup"><span data-stu-id="55a1a-129">In this topic you will learn the following SignalR development tasks:</span></span>

- <span data-ttu-id="55a1a-130">Přidání do aplikace MVC 5 knihovně SignalR.</span><span class="sxs-lookup"><span data-stu-id="55a1a-130">Adding the SignalR library to an MVC 5 application.</span></span>
- <span data-ttu-id="55a1a-131">Vytvoření třídy spuštění OWIN tak, aby nabízel obsah pro klienty a rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="55a1a-131">Creating hub and OWIN startup classes to push content to clients.</span></span>
- <span data-ttu-id="55a1a-132">K odesílání zpráv a zobrazení aktualizací z centra pomocí knihovny jQuery SignalR na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="55a1a-132">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="55a1a-133">Následující snímek obrazovky ukazuje dokončené chatovací aplikace spuštěné v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="55a1a-133">The following screen shot shows the completed chat application running in a browser.</span></span>

![Instance chatu](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

<span data-ttu-id="55a1a-135">Části:</span><span class="sxs-lookup"><span data-stu-id="55a1a-135">Sections:</span></span>

- [<span data-ttu-id="55a1a-136">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="55a1a-136">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="55a1a-137">Spustit ukázku</span><span class="sxs-lookup"><span data-stu-id="55a1a-137">Run the Sample</span></span>](#run)
- [<span data-ttu-id="55a1a-138">Zkontrolujte kód</span><span class="sxs-lookup"><span data-stu-id="55a1a-138">Examine the Code</span></span>](#code)
- [<span data-ttu-id="55a1a-139">Další kroky</span><span class="sxs-lookup"><span data-stu-id="55a1a-139">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="55a1a-140">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="55a1a-140">Set up the Project</span></span>

<span data-ttu-id="55a1a-141">Předpoklady:</span><span class="sxs-lookup"><span data-stu-id="55a1a-141">Prerequisites:</span></span>

- <span data-ttu-id="55a1a-142">Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="55a1a-142">Visual Studio 2013.</span></span> <span data-ttu-id="55a1a-143">Pokud nemáte Visual Studio, najdete v části [ASP.NET stáhne](https://www.asp.net/downloads) získat volné Visual Studio 2013 Express vývoj nástroj.</span><span class="sxs-lookup"><span data-stu-id="55a1a-143">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2013 Express Development Tool.</span></span>

<span data-ttu-id="55a1a-144">V této části ukazuje, jak vytvořit aplikaci ASP.NET MVC 5, přidejte knihovně SignalR a vytvoření chatovací aplikace.</span><span class="sxs-lookup"><span data-stu-id="55a1a-144">This section shows how to create an ASP.NET MVC 5 application, add the SignalR library, and create the chat application.</span></span>

1. <span data-ttu-id="55a1a-145">V sadě Visual Studio vytvořte aplikaci C# ASP.NET s cílem rozhraní .NET Framework 4.5, pojmenujte ji SignalRChat a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="55a1a-145">In Visual Studio, create a C# ASP.NET application that targets .NET Framework 4.5, name it SignalRChat, and click OK.</span></span>

    ![Vytvořit web](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)
2. <span data-ttu-id="55a1a-147">V `New ASP.NET Project` dialogové okno a vyberte **MVC**a klikněte na tlačítko **změna ověřování**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-147">In the `New ASP.NET Project` dialog, and select **MVC**, and click **Change Authentication**.</span></span>

    ![Vytvořit web](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)
3. <span data-ttu-id="55a1a-149">Vyberte **bez ověřování** v **změna ověřování** dialogové okno a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-149">Select **No Authentication** in the **Change Authentication** dialog, and click **OK**.</span></span>

    ![Vyberte bez ověřování](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

    > [!NOTE]
    > <span data-ttu-id="55a1a-151">Pokud vyberete zprostředkovatele různých ověřování pro aplikaci, `Startup.cs` pro vás bude možné vytvořit třídu; nebudete muset vytvořit vlastní `Startup.cs` – třída v kroku 10 níže.</span><span class="sxs-lookup"><span data-stu-id="55a1a-151">If you select a different authentication provider for your application, a `Startup.cs` class will be created for you; you will not need to create your own `Startup.cs` class in step 10 below.</span></span>
4. <span data-ttu-id="55a1a-152">Klikněte na tlačítko **OK** v **nový projekt ASP.NET** dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="55a1a-152">Click **OK** in the **New ASP.NET Project** dialog.</span></span>
5. <span data-ttu-id="55a1a-153">Otevřete **nástroje | Správce balíčků knihoven | Konzola správce balíčků** a spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="55a1a-153">Open the **Tools | Library Package Manager | Package Manager Console** and run the following command.</span></span> <span data-ttu-id="55a1a-154">Tento krok přidává do projektu sadu souborů skriptů a odkazy na sestavení, které umožňují funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="55a1a-154">This step adds to the project a set of script files and assembly references that enable SignalR functionality.</span></span>

    `install-package Microsoft.AspNet.SignalR`
6. <span data-ttu-id="55a1a-155">V **Průzkumníku**, rozbalte složku skripty.</span><span class="sxs-lookup"><span data-stu-id="55a1a-155">In **Solution Explorer**, expand the Scripts folder.</span></span> <span data-ttu-id="55a1a-156">Všimněte si, že byly přidány do projektu knihovny skriptů pro SignalR.</span><span class="sxs-lookup"><span data-stu-id="55a1a-156">Note that script libraries for SignalR have been added to the project.</span></span>

    ![Složka skripty](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)
7. <span data-ttu-id="55a1a-158">V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Nová složka**, a přidejte novou složku s názvem **Hubs**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-158">In **Solution Explorer**, right-click the project, select **Add | New Folder**, and add a new folder named **Hubs**.</span></span>
8. <span data-ttu-id="55a1a-159">Klikněte pravým tlačítkem myši **Hubs** složku, klikněte na tlačítko **přidat | Nová položka**, vyberte **Visual C# | Web | SignalR** uzlu **nainstalovaná** podokně, vyberte **třídy rozbočovače SignalR (v2)** z podokna center a vytvoření nového centra s názvem **ChatHub.cs**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-159">Right-click the **Hubs** folder, click **Add | New Item**, select the **Visual C# | Web | SignalR** node in the **Installed** pane, select **SignalR Hub Class (v2)** from the center pane, and create a new hub named **ChatHub.cs**.</span></span> <span data-ttu-id="55a1a-160">Tato třída použije jako server rozbočovače SignalR zasílání zpráv na všechny klienty.</span><span class="sxs-lookup"><span data-stu-id="55a1a-160">You will use this class as a SignalR server hub that sends messages to all clients.</span></span>

    ![Vytvoření nového centra](tutorial-getting-started-with-signalr-and-mvc/_static/image6.png)
9. <span data-ttu-id="55a1a-162">Nahraďte kód v **ChatHub** třídy následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="55a1a-162">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]
10. <span data-ttu-id="55a1a-163">Vytvořte novou třídu s názvem souboru Startup.cs.</span><span class="sxs-lookup"><span data-stu-id="55a1a-163">Create a new class called Startup.cs.</span></span> <span data-ttu-id="55a1a-164">Změňte obsah souboru následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="55a1a-164">Change the contents of the file to the following.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]
11. <span data-ttu-id="55a1a-165">Upravit `HomeController` nalezena třída v **Controllers/HomeController.cs** a přidejte následující metodu do třídy.</span><span class="sxs-lookup"><span data-stu-id="55a1a-165">Edit the `HomeController` class found in **Controllers/HomeController.cs** and add the following method to the class.</span></span> <span data-ttu-id="55a1a-166">Tato metoda vrátí hodnotu **Chat** zobrazení, který chcete vytvořit později.</span><span class="sxs-lookup"><span data-stu-id="55a1a-166">This method returns the **Chat** view that you will create in a later step.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]
12. <span data-ttu-id="55a1a-167">Klikněte pravým tlačítkem myši **zobrazení Domů** složky a vyberte **přidat... | Zobrazení**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-167">Right-click the **Views/Home** folder, and select **Add... | View**.</span></span>
13. <span data-ttu-id="55a1a-168">V **přidat zobrazení** dialogové okno, název nového zobrazení **Chat**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-168">In the **Add View** dialog, name the new view **Chat**.</span></span>

    ![Přidání zobrazení](tutorial-getting-started-with-signalr-and-mvc/_static/image7.png)
14. <span data-ttu-id="55a1a-170">Nahraďte obsah **Chat.cshtml** následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="55a1a-170">Replace the contents of **Chat.cshtml** with the following code.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="55a1a-171">Když přidáte do projektu sady Visual Studio SignalR a další skript knihovny, může správce balíčků nainstalujte verzi souboru skriptu SignalR, která je novější než verze uvedené v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="55a1a-171">When you add SignalR and other script libraries to your Visual Studio project, the Package Manager might install a version of the SignalR script file that is more recent than the version shown in this topic.</span></span> <span data-ttu-id="55a1a-172">Ujistěte se, že odkaz na skript v kódu odpovídá verzi knihovny skriptu nainstalovaná ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="55a1a-172">Make sure that the script reference in your code matches the version of the script library installed in your project.</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]
15. <span data-ttu-id="55a1a-173">**Uložte všechny** pro projekt.</span><span class="sxs-lookup"><span data-stu-id="55a1a-173">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="55a1a-174">Spustit ukázku</span><span class="sxs-lookup"><span data-stu-id="55a1a-174">Run the Sample</span></span>

1. <span data-ttu-id="55a1a-175">Stisknutím klávesy F5 spusťte projekt v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="55a1a-175">Press F5 to run the project in debug mode.</span></span>
2. <span data-ttu-id="55a1a-176">V adresním řádku prohlížeče, připojit **/home/chat** na adresu URL výchozí stránky pro projekt.</span><span class="sxs-lookup"><span data-stu-id="55a1a-176">In the browser address line, append **/home/chat** to the URL of the default page for the project.</span></span> <span data-ttu-id="55a1a-177">Načtení stránky Chat v instanci prohlížeče a vyzve k uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="55a1a-177">The Chat page loads in a browser instance and prompts for a user name.</span></span>

    ![Zadejte uživatelské jméno](tutorial-getting-started-with-signalr-and-mvc/_static/image8.png)
3. <span data-ttu-id="55a1a-179">Zadejte uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="55a1a-179">Enter a user name.</span></span>
4. <span data-ttu-id="55a1a-180">Zkopírujte adresu URL v adresním řádku prohlížeče a použít ho k otevřít dva další instance prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="55a1a-180">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="55a1a-181">V každé instanci prohlížeče zadejte jedinečné uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="55a1a-181">In each browser instance, enter a unique user name.</span></span>
5. <span data-ttu-id="55a1a-182">V každé instanci prohlížeče přidat komentář a klikněte na tlačítko **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-182">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="55a1a-183">Komentáře by měl zobrazit ve všech instancích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="55a1a-183">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="55a1a-184">Tento jednoduchý chatovací aplikace neudržuje kontext diskuzi na serveru.</span><span class="sxs-lookup"><span data-stu-id="55a1a-184">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="55a1a-185">Rozbočovač, všesměrově komentáře pro všechny aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="55a1a-185">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="55a1a-186">Uživatelé, kteří připojení chat později zobrazí zprávy přidat od okamžiku že připojí.</span><span class="sxs-lookup"><span data-stu-id="55a1a-186">Users who join the chat later will see messages added from the time they join.</span></span>
6. <span data-ttu-id="55a1a-187">Následující snímek obrazovky ukazuje chatovací aplikace spuštěné v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="55a1a-187">The following screen shot shows the chat application running in a browser.</span></span>

    ![Konverzace prohlížečů](tutorial-getting-started-with-signalr-and-mvc/_static/image9.png)
7. <span data-ttu-id="55a1a-189">V **Průzkumníku řešení**, zkontrolujte **dokumentů skriptu** uzel pro běžící aplikaci.</span><span class="sxs-lookup"><span data-stu-id="55a1a-189">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="55a1a-190">Tento uzel je viditelný v režimu ladění, pokud používáte Internet Explorer jako prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="55a1a-190">This node is visible in debug mode if you are using Internet Explorer as your browser.</span></span> <span data-ttu-id="55a1a-191">Existuje soubor skriptu s názvem **centra** vygenerovanou knihovně SignalR dynamicky za běhu.</span><span class="sxs-lookup"><span data-stu-id="55a1a-191">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="55a1a-192">Tento soubor spravuje komunikace mezi skriptu jQuery a kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="55a1a-192">This file manages the communication between jQuery script and server-side code.</span></span> <span data-ttu-id="55a1a-193">Pokud používáte jiný prohlížeč než Internet Explorer, můžete taky přejít dynamická **centra** soubor procházením ji přímo, například http://mywebsite/signalr/hubs.</span><span class="sxs-lookup"><span data-stu-id="55a1a-193">If you use a browser other than Internet Explorer, you can also access the dynamic **hubs** file by browsing to it directly, for example http://mywebsite/signalr/hubs.</span></span>

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="55a1a-194">Zkontrolujte kód</span><span class="sxs-lookup"><span data-stu-id="55a1a-194">Examine the Code</span></span>

<span data-ttu-id="55a1a-195">Chatovací aplikace SignalR ukazuje dva základní úlohy vývoj SignalR: vytváření rozbočovač jako hlavní koordinaci objekt na serveru a odesílat a přijímat zprávy pomocí knihovny jQuery SignalR.</span><span class="sxs-lookup"><span data-stu-id="55a1a-195">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="55a1a-196">Rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="55a1a-196">SignalR Hubs</span></span>

<span data-ttu-id="55a1a-197">V ukázce kódu **ChatHub** třída odvozená z **Microsoft.AspNet.SignalR.Hub** třídy.</span><span class="sxs-lookup"><span data-stu-id="55a1a-197">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="55a1a-198">Odvozování z **rozbočovače** třída je užitečný způsob, jak sestavit aplikaci pro SignalR.</span><span class="sxs-lookup"><span data-stu-id="55a1a-198">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="55a1a-199">Můžete vytvořit veřejné metody na vaší třídě rozbočovače a pak tyto metody přístup voláním z skriptů na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="55a1a-199">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="55a1a-200">V kódu chat klienti volání **ChatHub.Send** metodu pro odeslání nové zprávy.</span><span class="sxs-lookup"><span data-stu-id="55a1a-200">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="55a1a-201">Rozbočovače pak odešle zprávu do všech klientů voláním **Clients.All.addNewMessageToPage**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-201">The hub in turn sends the message to all clients by calling **Clients.All.addNewMessageToPage**.</span></span>

<span data-ttu-id="55a1a-202">**Odeslat** metoda ukazuje několik konceptů rozbočovače:</span><span class="sxs-lookup"><span data-stu-id="55a1a-202">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="55a1a-203">Deklarujte veřejné metody v rozbočovači, aby klienti mohou volat je.</span><span class="sxs-lookup"><span data-stu-id="55a1a-203">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="55a1a-204">Použití **Microsoft.AspNet.SignalR.Hub.Clients** vlastnost, která má přístup všichni klienti připojení k této rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="55a1a-204">Use the **Microsoft.AspNet.SignalR.Hub.Clients** property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="55a1a-205">Volání funkce na straně klienta (například `addNewMessageToPage` funkce) k aktualizaci klientů.</span><span class="sxs-lookup"><span data-stu-id="55a1a-205">Call a function on the client (such as the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="55a1a-206">SignalR a jQuery</span><span class="sxs-lookup"><span data-stu-id="55a1a-206">SignalR and jQuery</span></span>

<span data-ttu-id="55a1a-207">**Chat.cshtml** zobrazení souboru v ukázce kódu ukazuje, jak používat knihovny jQuery SignalR ke komunikaci s rozbočovači SignalR.</span><span class="sxs-lookup"><span data-stu-id="55a1a-207">The **Chat.cshtml** view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="55a1a-208">Základních úloh v kódu vytváříte odkaz na automaticky generovaný proxy serveru pro rozbočovač, deklarace funkci, která serveru můžete volat k předávaný obsah pro klienty a spuštění připojení k odesílání zpráv do rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="55a1a-208">The essential tasks in the code are creating a reference to the auto-generated proxy for the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="55a1a-209">Následující kód deklaruje odkaz na proxy server rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="55a1a-209">The following code declares a reference to a hub proxy.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="55a1a-210">V jazyce JavaScript je odkaz na třídě serveru a její členy v camelCase.</span><span class="sxs-lookup"><span data-stu-id="55a1a-210">In JavaScript the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="55a1a-211">Odkazuje na ukázka kódu jazyka C# **ChatHub** třídy v jazyce JavaScript jako **chatHub**.</span><span class="sxs-lookup"><span data-stu-id="55a1a-211">The code sample references the C# **ChatHub** class in JavaScript as **chatHub**.</span></span> <span data-ttu-id="55a1a-212">Pokud chcete odkazovat `ChatHub` – třída v jQuery s konvenční Pascal velká a malá písmena stejně jako v jazyce C#, upravte soubor ChatHub.cs třídy.</span><span class="sxs-lookup"><span data-stu-id="55a1a-212">If you want to reference the `ChatHub` class in jQuery with conventional Pascal casing as you would in C#, edit the ChatHub.cs class file.</span></span> <span data-ttu-id="55a1a-213">Přidat `using` příkaz tak, aby odkazovaly `Microsoft.AspNet.SignalR.Hubs` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="55a1a-213">Add a `using` statement to reference the `Microsoft.AspNet.SignalR.Hubs` namespace.</span></span> <span data-ttu-id="55a1a-214">Pak přidejte `HubName` atribut `ChatHub` třídy, například `[HubName("ChatHub")]`.</span><span class="sxs-lookup"><span data-stu-id="55a1a-214">Then add the `HubName` attribute to the `ChatHub` class, for example `[HubName("ChatHub")]`.</span></span> <span data-ttu-id="55a1a-215">Nakonec aktualizujte vaši jQuery informaci, abyste `ChatHub` třídy.</span><span class="sxs-lookup"><span data-stu-id="55a1a-215">Finally, update your jQuery reference to the `ChatHub` class.</span></span>


<span data-ttu-id="55a1a-216">Následující kód ukazuje, jak vytvořit funkci zpětného volání ve skriptu.</span><span class="sxs-lookup"><span data-stu-id="55a1a-216">The following code shows how to create a callback function in the script.</span></span> <span data-ttu-id="55a1a-217">Třídy rozbočovače na serveru volá tuto funkci tak, aby nabízel aktualizace obsahu do jednotlivých klientů.</span><span class="sxs-lookup"><span data-stu-id="55a1a-217">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="55a1a-218">Volitelné volání `htmlEncode` funkce zobrazí způsob, jak HTML kódování obsahu zprávy, než se zobrazí na stránce jako způsob, jak zabránit vložení skriptu.</span><span class="sxs-lookup"><span data-stu-id="55a1a-218">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page, as a way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

<span data-ttu-id="55a1a-219">Následující kód ukazuje, jak k otevření připojení do centra.</span><span class="sxs-lookup"><span data-stu-id="55a1a-219">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="55a1a-220">Kód spustí připojení a předává je funkce, která se zpracovat událost kliknutím na **odeslat** tlačítka na stránce konverzace.</span><span class="sxs-lookup"><span data-stu-id="55a1a-220">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

> [!NOTE]
> <span data-ttu-id="55a1a-221">Tento přístup zajišťuje, že připojení před provedením obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="55a1a-221">This approach ensures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="55a1a-222">Další kroky</span><span class="sxs-lookup"><span data-stu-id="55a1a-222">Next Steps</span></span>

<span data-ttu-id="55a1a-223">Jste zjistili, že SignalR je architektura pro vytváření aplikací webu v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="55a1a-223">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="55a1a-224">Také jste zjistili několik úloh vývoj SignalR: jak přidat do aplikace ASP.NET SignalR, jak vytvořit třídy rozbočovače a jak odesílat a přijímat zprávy z rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="55a1a-224">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="55a1a-225">Podrobný postup nasazení ukázkové aplikace SignalR do Azure, najdete v části [pomocí SignalR s webovými aplikacemi ve službě Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span><span class="sxs-lookup"><span data-stu-id="55a1a-225">For a walkthrough on how to deploy the sample SignalR application to Azure, see [Using SignalR with Web Apps in Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span></span> <span data-ttu-id="55a1a-226">Podrobné informace o tom, jak nasadit webový projekt sady Visual Studio na webu systému Windows Azure najdete v tématu [vytvoření webové aplikace ASP.NET ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span><span class="sxs-lookup"><span data-stu-id="55a1a-226">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Create an ASP.NET web app in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span></span>

<span data-ttu-id="55a1a-227">Informace o pokročilejší SignalR vývoji koncepty, najdete na následujících stránkách pro SignalR zdrojového kódu a prostředky:</span><span class="sxs-lookup"><span data-stu-id="55a1a-227">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources :</span></span>

- [<span data-ttu-id="55a1a-228">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="55a1a-228">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="55a1a-229">SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="55a1a-229">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="55a1a-230">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="55a1a-230">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Kurz: Chatování v reálném čase s knihovnou SignalR 2 a MVC 5 | Dokumentace Microsoftu'
author: bradygaster
description: Tento kurz ukazuje použití funkcí SignalR 2 technologie ASP.NET k vytvoření aplikace pro chatování v reálném čase. Přidáte do aplikace MVC 5 SignalR.
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 1b02aecc68a93dbd6373ca5304530e76c9d0b6b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836998"
---
# <a name="tutorial-real-time-chat-with-signalr-2-and-mvc-5"></a><span data-ttu-id="56d99-104">Kurz: Chatování v reálném čase s knihovnou SignalR 2 a MVC 5</span><span class="sxs-lookup"><span data-stu-id="56d99-104">Tutorial: Real-time chat with SignalR 2 and MVC 5</span></span>

<span data-ttu-id="56d99-105">Tento kurz ukazuje použití funkcí SignalR 2 technologie ASP.NET k vytvoření aplikace pro chatování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="56d99-105">This tutorial shows how to use ASP.NET SignalR 2 to create a real-time chat application.</span></span> <span data-ttu-id="56d99-106">Přidat funkci SignalR k aplikaci MVC 5 a vytvořte zobrazení chatu k odesílání a zobrazení zprávy.</span><span class="sxs-lookup"><span data-stu-id="56d99-106">You add SignalR to an MVC 5 application and create a chat view to send and display messages.</span></span>

<span data-ttu-id="56d99-107">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="56d99-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="56d99-108">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="56d99-108">Set up the project</span></span>
> * <span data-ttu-id="56d99-109">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="56d99-109">Run the sample</span></span>
> * <span data-ttu-id="56d99-110">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="56d99-110">Examine the code</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="56d99-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="56d99-111">Prerequisites</span></span>

* <span data-ttu-id="56d99-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="56d99-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="56d99-113">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="56d99-113">Set up the Project</span></span>

<span data-ttu-id="56d99-114">Tato část ukazuje, jak pomocí sady Visual Studio 2017 a knihovnou SignalR 2 vytvořit prázdnou aplikaci ASP.NET MVC 5 a přidejte knihovny SignalR, vytvořit chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="56d99-114">This section shows how to use Visual Studio 2017 and SignalR 2 to create an empty ASP.NET MVC 5 application, add the SignalR library, and create the chat application.</span></span>

1. <span data-ttu-id="56d99-115">V sadě Visual Studio vytvořit aplikaci C# ASP.NET, který cílí na rozhraní .NET Framework 4.5, pojmenujte ho SignalRChat a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="56d99-115">In Visual Studio, create a C# ASP.NET application that targets .NET Framework 4.5, name it SignalRChat, and click OK.</span></span>

    ![Vytvořte web](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

1. <span data-ttu-id="56d99-117">V **nová webová aplikace ASP.NET - SignalRMvcChat**vyberte **MVC** a pak vyberte **změna ověřování**.</span><span class="sxs-lookup"><span data-stu-id="56d99-117">In **New ASP.NET Web Application - SignalRMvcChat**, select **MVC** and then select **Change Authentication**.</span></span>

1. <span data-ttu-id="56d99-118">V **změna ověřování**vyberte **bez ověřování** a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="56d99-118">In **Change Authentication**, select **No Authentication** and click **OK**.</span></span>

    ![Vyberte bez ověřování](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)

1. <span data-ttu-id="56d99-120">V **nová webová aplikace ASP.NET - SignalRMvcChat**vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="56d99-120">In **New ASP.NET Web Application - SignalRMvcChat**, select **OK**.</span></span>

1. <span data-ttu-id="56d99-121">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="56d99-121">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="56d99-122">V **přidat novou položku - SignalRChat**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="56d99-122">In **Add New Item - SignalRChat**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="56d99-123">Název třídy *ChatHub* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="56d99-123">Name the class *ChatHub* and add it to the project.</span></span>

    <span data-ttu-id="56d99-124">Tento krok vytvoří *ChatHub.cs* třídy soubor a přidá sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR k projektu.</span><span class="sxs-lookup"><span data-stu-id="56d99-124">This step creates the *ChatHub.cs* class file and adds a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="56d99-125">Nahraďte kód v novém *ChatHub.cs* soubor třídy s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="56d99-125">Replace the code in the new *ChatHub.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]

1. <span data-ttu-id="56d99-126">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="56d99-126">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="56d99-127">Pojmenujte novou třídu *spuštění* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="56d99-127">Name the new class *Startup* and add it to the project.</span></span>

1. <span data-ttu-id="56d99-128">Nahraďte kód v *Startup.cs* soubor třídy s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="56d99-128">Replace the code in the *Startup.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]

1. <span data-ttu-id="56d99-129">V **Průzkumníka řešení**vyberte **řadiče** > **HomeController.cs**.</span><span class="sxs-lookup"><span data-stu-id="56d99-129">In **Solution Explorer**, select **Controllers** > **HomeController.cs**.</span></span>

1. <span data-ttu-id="56d99-130">Přidejte tuto metodu za účelem *HomeController.cs*.</span><span class="sxs-lookup"><span data-stu-id="56d99-130">Add this method to the *HomeController.cs*.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]

    <span data-ttu-id="56d99-131">Tato metoda vrátí **Chat** zobrazení, které vytvoříte v pozdějším kroku.</span><span class="sxs-lookup"><span data-stu-id="56d99-131">This method returns the **Chat** view that you create in a later step.</span></span>

1. <span data-ttu-id="56d99-132">V **Průzkumníka řešení**, klikněte pravým tlačítkem na **zobrazení** > **Domů**a vyberte **přidat**  >    **Zobrazení**.</span><span class="sxs-lookup"><span data-stu-id="56d99-132">In **Solution Explorer**, right-click **Views** > **Home**, and select **Add** >  **View**.</span></span>

1. <span data-ttu-id="56d99-133">V **přidat zobrazení**, pojmenujte nové zobrazení **Chat** a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="56d99-133">In **Add View**, name the new view **Chat** and select **Add**.</span></span>

1. <span data-ttu-id="56d99-134">Nahraďte obsah **Chat.cshtml** s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="56d99-134">Replace the contents of **Chat.cshtml** with this code:</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]

1. <span data-ttu-id="56d99-135">V **Průzkumníka řešení**, rozbalte **skripty**.</span><span class="sxs-lookup"><span data-stu-id="56d99-135">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="56d99-136">Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.</span><span class="sxs-lookup"><span data-stu-id="56d99-136">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="56d99-137">Správce balíčků nainstalovanou novější verzi skriptů SignalR.</span><span class="sxs-lookup"><span data-stu-id="56d99-137">The package manager may have installed a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="56d99-138">Zkontrolujte, jestli odkazy na skript v bloku kódu, které odpovídají verze souborů skript v projektu.</span><span class="sxs-lookup"><span data-stu-id="56d99-138">Check that the script references in the code block correspond to the versions of the script files in the project.</span></span>

    <span data-ttu-id="56d99-139">Odkazy na skript z původního bloku kódu:</span><span class="sxs-lookup"><span data-stu-id="56d99-139">Script references from the original code block:</span></span>

    ```cshtml
    <!--Script references. -->
    <!--The jQuery library is required and is referenced by default in _Layout.cshtml. -->
    <!--Reference the SignalR library. -->
    <script src="~/Scripts/jquery.signalR-2.1.0.min.js"></script>
    ```

1. <span data-ttu-id="56d99-140">Pokud se neshodují, aktualizujte *.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="56d99-140">If they don't match, update the *.cshtml* file.</span></span>

1. <span data-ttu-id="56d99-141">V panelu nabídky vyberte **souboru** > **Uložit vše**.</span><span class="sxs-lookup"><span data-stu-id="56d99-141">From the menu bar, select **File** > **Save All**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="56d99-142">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="56d99-142">Run the Sample</span></span>

1. <span data-ttu-id="56d99-143">Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte ukázku v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="56d99-143">In the toolbar, turn on **Script Debugging** and then select the play button to run the sample in Debug mode.</span></span>

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)

1. <span data-ttu-id="56d99-145">Pokud v prohlížeči se otevře, zadejte název pro svou identitu konverzace.</span><span class="sxs-lookup"><span data-stu-id="56d99-145">When the browser opens, enter a name for your chat identity.</span></span>

1. <span data-ttu-id="56d99-146">Zkopírujte adresu URL z prohlížeče, otevřete dvou dalších prohlížečích a adresy URL vložte do řádku s adresou.</span><span class="sxs-lookup"><span data-stu-id="56d99-146">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="56d99-147">V každé prohlížeče zadejte jedinečný název.</span><span class="sxs-lookup"><span data-stu-id="56d99-147">In each browser, enter a unique name.</span></span>

1. <span data-ttu-id="56d99-148">Teď přidejte komentář a vyberte **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="56d99-148">Now, add a comment and select **Send**.</span></span> <span data-ttu-id="56d99-149">Opakování, který v dalších prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="56d99-149">Repeat that in the other browsers.</span></span> <span data-ttu-id="56d99-150">Komentáře se zobrazí v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="56d99-150">The comments appear in real time.</span></span>

    > [!NOTE]
    > <span data-ttu-id="56d99-151">Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru.</span><span class="sxs-lookup"><span data-stu-id="56d99-151">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="56d99-152">Centrum vysílá poznámky pro všechny aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="56d99-152">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="56d99-153">Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.</span><span class="sxs-lookup"><span data-stu-id="56d99-153">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="56d99-154">Podívejte se jak chatovací aplikaci spustí ve třech různých prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="56d99-154">See how the chat application runs in three different browsers.</span></span> <span data-ttu-id="56d99-155">Při Tom Anand a Susan odesílat zprávy, všechny prohlížeče aktualizace v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="56d99-155">When Tom, Anand, and Susan send messages, all browsers update in real time:</span></span>

    ![Všechny tři prohlížeče zobrazit historii stejné konverzace](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

1. <span data-ttu-id="56d99-157">V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci.</span><span class="sxs-lookup"><span data-stu-id="56d99-157">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="56d99-158">Existuje soubor skriptu s názvem *rozbočovače* generující knihovně SignalR v době běhu.</span><span class="sxs-lookup"><span data-stu-id="56d99-158">There's a script file named *hubs* that the SignalR library generates at runtime.</span></span> <span data-ttu-id="56d99-159">Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="56d99-159">This file manages the communication between jQuery script and server-side code.</span></span>

    ![automaticky generované hubs skript v uzlu dokumenty skriptu](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)

## <a name="examine-the-code"></a><span data-ttu-id="56d99-161">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="56d99-161">Examine the Code</span></span>

<span data-ttu-id="56d99-162">Chatovací aplikace SignalR ukazuje dvě základní úlohy vývoj pro funkci SignalR.</span><span class="sxs-lookup"><span data-stu-id="56d99-162">The SignalR chat application demonstrates two basic SignalR development tasks.</span></span> <span data-ttu-id="56d99-163">To ukazuje, jak vytvořit centrum.</span><span class="sxs-lookup"><span data-stu-id="56d99-163">It shows you how to create a hub.</span></span> <span data-ttu-id="56d99-164">Server používá jako objekt hlavního koordinace daném rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="56d99-164">The server uses that hub as the main coordination object.</span></span> <span data-ttu-id="56d99-165">Centrum používá knihovny jQuery SignalR k odesílání a příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="56d99-165">The hub uses the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs-in-the-chathubcs"></a><span data-ttu-id="56d99-166">Rozbočovače SignalR v ChatHub.cs</span><span class="sxs-lookup"><span data-stu-id="56d99-166">SignalR Hubs in the ChatHub.cs</span></span>

<span data-ttu-id="56d99-167">V ukázce kódu `ChatHub` třída odvozena z `Microsoft.AspNet.SignalR.Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="56d99-167">In the code sample, the `ChatHub` class derives from the `Microsoft.AspNet.SignalR.Hub` class.</span></span> <span data-ttu-id="56d99-168">Odvozování z `Hub` třída je užitečný způsob, jak vytvořit aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="56d99-168">Deriving from the `Hub` class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="56d99-169">Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z skripty na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="56d99-169">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="56d99-170">V kódu, konverzace, klienti volání `ChatHub.Send` metoda odesílá nová zpráva.</span><span class="sxs-lookup"><span data-stu-id="56d99-170">In the chat code, clients call the `ChatHub.Send` method to send a new message.</span></span> <span data-ttu-id="56d99-171">Centrum pak odešle zprávu pro všechny klienty voláním `Clients.All.addNewMessageToPage`.</span><span class="sxs-lookup"><span data-stu-id="56d99-171">The hub in turn sends the message to all clients by calling `Clients.All.addNewMessageToPage`.</span></span>

<span data-ttu-id="56d99-172">`Send` Metoda ukazuje několik konceptů hub:</span><span class="sxs-lookup"><span data-stu-id="56d99-172">The `Send` method demonstrates several hub concepts:</span></span>

* <span data-ttu-id="56d99-173">Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.</span><span class="sxs-lookup"><span data-stu-id="56d99-173">Declare public methods on a hub so that clients can call them.</span></span>

* <span data-ttu-id="56d99-174">Použití `Microsoft.AspNet.SignalR.Hub.Clients` dynamických vlastností pro komunikaci se všemi klienty připojené pro toto centrum.</span><span class="sxs-lookup"><span data-stu-id="56d99-174">Use the `Microsoft.AspNet.SignalR.Hub.Clients` dynamic property to communicate with all clients connected to this hub.</span></span>

* <span data-ttu-id="56d99-175">Volání funkce na straně klienta (například `addNewMessageToPage` funkce) aktualizovat klienty.</span><span class="sxs-lookup"><span data-stu-id="56d99-175">Call a function on the client (like the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery-chatcshtml"></a><span data-ttu-id="56d99-176">SignalR a jQuery Chat.cshtml</span><span class="sxs-lookup"><span data-stu-id="56d99-176">SignalR and jQuery Chat.cshtml</span></span>

<span data-ttu-id="56d99-177">*Chat.cshtml* zobrazení souboru v příkladu kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="56d99-177">The *Chat.cshtml* view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span>  <span data-ttu-id="56d99-178">Kód provede celou řadu důležitých úloh.</span><span class="sxs-lookup"><span data-stu-id="56d99-178">The code carries out many important tasks.</span></span> <span data-ttu-id="56d99-179">Vytvoří odkaz na automaticky vygenerovaný proxy server rozbočovače, deklaruje funkci, že server může volat tak, aby nabízel obsah pro klienty a spustí připojení k odeslání zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="56d99-179">It creates a reference to the autogenerated proxy for the hub, declares a function that the server can call to push content to clients, and it starts a connection to send messages to the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="56d99-180">V jazyce JavaScript je odkaz na třídu serveru a jeho členy ve formátu camelCase.</span><span class="sxs-lookup"><span data-stu-id="56d99-180">In JavaScript, the reference to the server class and its members is in camelCase.</span></span> <span data-ttu-id="56d99-181">Odkazy na ukázkový kód C# `ChatHub` třídy v jazyce JavaScript jako `chatHub`.</span><span class="sxs-lookup"><span data-stu-id="56d99-181">The code sample references the C# `ChatHub` class in JavaScript as `chatHub`.</span></span>

<span data-ttu-id="56d99-182">V tomto bloku kódu můžete vytvořit funkce zpětného volání ve skriptu.</span><span class="sxs-lookup"><span data-stu-id="56d99-182">In this code block, you create a callback function in the script.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

<span data-ttu-id="56d99-183">Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty.</span><span class="sxs-lookup"><span data-stu-id="56d99-183">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="56d99-184">Volitelné volání `htmlEncode` funkce ukazuje způsob, jak HTML kódování obsahu zprávy před jejich zobrazením na stránce.</span><span class="sxs-lookup"><span data-stu-id="56d99-184">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page.</span></span> <span data-ttu-id="56d99-185">Je to způsob, aby se zabránilo injektáži skriptu.</span><span class="sxs-lookup"><span data-stu-id="56d99-185">It's a way to prevent script injection.</span></span>

<span data-ttu-id="56d99-186">Tento kód otevře připojení v centru.</span><span class="sxs-lookup"><span data-stu-id="56d99-186">This code opens a connection with the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

> [!NOTE]
> <span data-ttu-id="56d99-187">Tento přístup zajišťuje, před provedením obslužná rutina události navázat připojení.</span><span class="sxs-lookup"><span data-stu-id="56d99-187">This approach ensures that you establish a connection before the event handler executes.</span></span>

<span data-ttu-id="56d99-188">Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce konverzace.</span><span class="sxs-lookup"><span data-stu-id="56d99-188">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="56d99-189">Získat kód</span><span class="sxs-lookup"><span data-stu-id="56d99-189">Get the code</span></span>

[<span data-ttu-id="56d99-190">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="56d99-190">Download Completed Project</span></span>](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

## <a name="additional-resources"></a><span data-ttu-id="56d99-191">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="56d99-191">Additional resources</span></span>

<span data-ttu-id="56d99-192">Další informace o funkci SignalR naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="56d99-192">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="56d99-193">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="56d99-193">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="56d99-194">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="56d99-194">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="56d99-195">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="56d99-195">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="56d99-196">Další kroky</span><span class="sxs-lookup"><span data-stu-id="56d99-196">Next steps</span></span>

<span data-ttu-id="56d99-197">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="56d99-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="56d99-198">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="56d99-198">Set up the project</span></span>
> * <span data-ttu-id="56d99-199">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="56d99-199">Ran the sample</span></span>
> * <span data-ttu-id="56d99-200">Prozkoumat kód</span><span class="sxs-lookup"><span data-stu-id="56d99-200">Examined the code</span></span>

<span data-ttu-id="56d99-201">Přejděte k dalším článku se naučíte, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 zasílání zpráv nakonfigurovánu vysokou frekvencí.</span><span class="sxs-lookup"><span data-stu-id="56d99-201">Advance to the next article to learn how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="56d99-202">Webová aplikace s vysokou frekvencí zasílání zpráv</span><span class="sxs-lookup"><span data-stu-id="56d99-202">Web app with high-frequency messaging</span></span>](tutorial-high-frequency-realtime-with-signalr.md)
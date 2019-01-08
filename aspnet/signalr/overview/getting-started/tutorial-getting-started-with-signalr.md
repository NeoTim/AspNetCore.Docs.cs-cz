---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Kurz: Chatování v reálném čase s knihovnou SignalR 2 | Dokumentace Microsoftu'
author: pfletcher
description: V tomto kurzu se naučíte používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase. Přidáte do prázdná webová aplikace ASP.NET SignalR.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: aa015abc47bb2450e04e167c0404aaa1d119ba2c
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098621"
---
# <a name="tutorial-real-time-chat-with-signalr-2"></a><span data-ttu-id="32a70-104">Kurz: Chatování v reálném čase s knihovnou SignalR 2</span><span class="sxs-lookup"><span data-stu-id="32a70-104">Tutorial: Real-time chat with SignalR 2</span></span>

<span data-ttu-id="32a70-105">V tomto kurzu se dozvíte, jak používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="32a70-105">This tutorial shows you how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="32a70-106">Přidat prázdnou webovou aplikaci ASP.NET SignalR a vytvořte stránku HTML k odeslání a zobrazení zprávy.</span><span class="sxs-lookup"><span data-stu-id="32a70-106">You add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span>

<span data-ttu-id="32a70-107">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="32a70-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="32a70-108">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="32a70-108">Set up the project</span></span>
> * <span data-ttu-id="32a70-109">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="32a70-109">Run the sample</span></span>
> * <span data-ttu-id="32a70-110">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="32a70-110">Examine the code</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="32a70-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="32a70-111">Prerequisites</span></span>

* <span data-ttu-id="32a70-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="32a70-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="32a70-113">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="32a70-113">Set up the Project</span></span>

<span data-ttu-id="32a70-114">Tato část ukazuje, jak pomocí sady Visual Studio 2017 a knihovnou SignalR 2 můžete vytvořit prázdnou webovou aplikaci ASP.NET, přidejte SignalR a vytvořit chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="32a70-114">This section shows how to use Visual Studio 2017 and SignalR 2 to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

1. <span data-ttu-id="32a70-115">V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="32a70-115">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Vytvořte web](tutorial-getting-started-with-signalr/_static/image2.png)

1. <span data-ttu-id="32a70-117">V **nový projekt ASP.NET – SignalRChat** okně, ponechejte tuto položku **prázdný** vybraný a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="32a70-117">In the **New ASP.NET Project - SignalRChat** window, leave **Empty** selected and select **OK**.</span></span>

1. <span data-ttu-id="32a70-118">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="32a70-118">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="32a70-119">V **přidat novou položku - SignalRChat**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="32a70-119">In **Add New Item - SignalRChat**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="32a70-120">Název třídy *ChatHub* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="32a70-120">Name the class *ChatHub* and add it to the project.</span></span>

    <span data-ttu-id="32a70-121">Tento krok vytvoří *ChatHub.cs* třídy soubor a přidá sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR k projektu.</span><span class="sxs-lookup"><span data-stu-id="32a70-121">This step creates the *ChatHub.cs* class file and adds a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="32a70-122">Nahraďte kód v novém *ChatHub.cs* soubor třídy s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="32a70-122">Replace the code in the new *ChatHub.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]

1. <span data-ttu-id="32a70-123">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="32a70-123">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="32a70-124">V **přidat novou položku - SignalRChat** vyberte **nainstalováno** > **Visual C#**   >  **webové** a pak Vyberte **třídy pro spuštění OWIN**.</span><span class="sxs-lookup"><span data-stu-id="32a70-124">In **Add New Item - SignalRChat** select **Installed** > **Visual C#** > **Web**  and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="32a70-125">Název třídy *spuštění* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="32a70-125">Name the class *Startup* and add it to the project.</span></span>

1. <span data-ttu-id="32a70-126">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **stránku HTML**.</span><span class="sxs-lookup"><span data-stu-id="32a70-126">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="32a70-127">Zadejte název nové stránky *index* a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="32a70-127">Name the new page *index* and select **OK**.</span></span>

1. <span data-ttu-id="32a70-128">V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku HTML, který jste vytvořili a vyberte **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="32a70-128">In **Solution Explorer**, right-click the HTML page you created and select **Set as Start Page**.</span></span>

1. <span data-ttu-id="32a70-129">Nahraďte kód výchozí stránku HTML s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="32a70-129">Replace the default code in the HTML page with this code:</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]

1. <span data-ttu-id="32a70-130">V **Průzkumníka řešení**, rozbalte **skripty**.</span><span class="sxs-lookup"><span data-stu-id="32a70-130">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="32a70-131">Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.</span><span class="sxs-lookup"><span data-stu-id="32a70-131">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="32a70-132">Správce balíčků nainstalovanou novější verzi skriptů SignalR.</span><span class="sxs-lookup"><span data-stu-id="32a70-132">The package manager may have installed a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="32a70-133">Zkontrolujte, jestli odkazy na skript v bloku kódu, které odpovídají verze souborů skript v projektu.</span><span class="sxs-lookup"><span data-stu-id="32a70-133">Check that the script references in the code block correspond to the versions of the script files in the project.</span></span>

    <span data-ttu-id="32a70-134">Odkazy na skript z původního bloku kódu:</span><span class="sxs-lookup"><span data-stu-id="32a70-134">Script references from the original code block:</span></span>
    ```html
    <!--Script references. -->
    <!--Reference the jQuery library. -->
    <script src="Scripts/jquery-3.1.1.min.js" ></script>
    <!--Reference the SignalR library. -->
    <script src="Scripts/jquery.signalR-2.2.1.min.js"></script>
    ```

1. <span data-ttu-id="32a70-135">Pokud se neshodují, aktualizujte *.html* souboru.</span><span class="sxs-lookup"><span data-stu-id="32a70-135">If they don't match, update the *.html* file.</span></span>

1. <span data-ttu-id="32a70-136">V panelu nabídky vyberte **souboru** > **Uložit vše**.</span><span class="sxs-lookup"><span data-stu-id="32a70-136">From the menu bar, select **File** > **Save All**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="32a70-137">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="32a70-137">Run the Sample</span></span>

1. <span data-ttu-id="32a70-138">Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte ukázku v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="32a70-138">In the toolbar, turn on **Script Debugging** and then select the play button to run the sample in Debug mode.</span></span>

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr/_static/image3.png)

1. <span data-ttu-id="32a70-140">Pokud v prohlížeči se otevře, zadejte název pro svou identitu konverzace.</span><span class="sxs-lookup"><span data-stu-id="32a70-140">When the browser opens, enter a name for your chat identity.</span></span>

1. <span data-ttu-id="32a70-141">Zkopírujte adresu URL z prohlížeče, otevřete dvou dalších prohlížečích a adresy URL vložte do řádku s adresou.</span><span class="sxs-lookup"><span data-stu-id="32a70-141">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="32a70-142">V každé prohlížeče zadejte jedinečný název.</span><span class="sxs-lookup"><span data-stu-id="32a70-142">In each browser, enter a unique name.</span></span>

1. <span data-ttu-id="32a70-143">Teď přidejte komentář a vyberte **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="32a70-143">Now, add a comment and select **Send**.</span></span> <span data-ttu-id="32a70-144">Opakování, který v dalších prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="32a70-144">Repeat that in the other browsers.</span></span> <span data-ttu-id="32a70-145">Komentáře se zobrazí v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="32a70-145">The comments appear in real-time.</span></span>

    > [!NOTE]
    > <span data-ttu-id="32a70-146">Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru.</span><span class="sxs-lookup"><span data-stu-id="32a70-146">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="32a70-147">Centrum vysílá poznámky pro všechny aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="32a70-147">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="32a70-148">Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.</span><span class="sxs-lookup"><span data-stu-id="32a70-148">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="32a70-149">Podívejte se jak chatovací aplikaci spustí ve třech různých prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="32a70-149">See how the chat application runs in three different browsers.</span></span> <span data-ttu-id="32a70-150">Při Tom Anand a Susan odesílat zprávy, všechny prohlížeče aktualizace v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="32a70-150">When Tom, Anand, and Susan send messages, all browsers update in real time:</span></span>

    ![Všechny tři prohlížeče zobrazit historii stejné konverzace](tutorial-getting-started-with-signalr/_static/image4.png)

1. <span data-ttu-id="32a70-152">V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci.</span><span class="sxs-lookup"><span data-stu-id="32a70-152">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="32a70-153">Existuje soubor skriptu s názvem *rozbočovače* generující knihovně SignalR v době běhu.</span><span class="sxs-lookup"><span data-stu-id="32a70-153">There's a script file named *hubs* that the SignalR library generates at runtime.</span></span> <span data-ttu-id="32a70-154">Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="32a70-154">This file manages the communication between jQuery script and server-side code.</span></span>

    ![automaticky generované hubs skript v uzlu dokumenty skriptu](tutorial-getting-started-with-signalr/_static/image5.png)

## <a name="examine-the-code"></a><span data-ttu-id="32a70-156">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="32a70-156">Examine the Code</span></span>

<span data-ttu-id="32a70-157">Aplikace SignalRChat ukazuje dvě základní úlohy vývoj pro funkci SignalR.</span><span class="sxs-lookup"><span data-stu-id="32a70-157">The SignalRChat application demonstrates two basic SignalR development tasks.</span></span> <span data-ttu-id="32a70-158">To ukazuje, jak vytvořit centrum.</span><span class="sxs-lookup"><span data-stu-id="32a70-158">It shows you how to create a hub.</span></span> <span data-ttu-id="32a70-159">Server používá jako objekt hlavního koordinace daném rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="32a70-159">The server uses that hub as the main coordination object.</span></span> <span data-ttu-id="32a70-160">Centrum používá knihovny jQuery SignalR k odesílání a příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="32a70-160">The hub uses the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs-in-the-chathubcs"></a><span data-ttu-id="32a70-161">Rozbočovače SignalR v ChatHub.cs</span><span class="sxs-lookup"><span data-stu-id="32a70-161">SignalR Hubs in the ChatHub.cs</span></span>

<span data-ttu-id="32a70-162">V ukázkovém kódu výše `ChatHub` třída odvozena z `Microsoft.AspNet.SignalR.Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="32a70-162">In the code sample above, the `ChatHub` class derives from the `Microsoft.AspNet.SignalR.Hub` class.</span></span> <span data-ttu-id="32a70-163">Odvozování z `Hub` třída je užitečný způsob, jak vytvořit aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="32a70-163">Deriving from the `Hub` class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="32a70-164">Můžete vytvořit veřejné metody na třídě centra a potom použít tyto metody jejich voláním z skripty na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="32a70-164">You can create public methods on your hub class and then use those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="32a70-165">V kódu, konverzace, klienti volání `ChatHub.Send` metoda odesílá nová zpráva.</span><span class="sxs-lookup"><span data-stu-id="32a70-165">In the chat code, clients call the `ChatHub.Send` method to send a new message.</span></span> <span data-ttu-id="32a70-166">Centrum pak odešle zprávu pro všechny klienty voláním `Clients.All.broadcastMessage`.</span><span class="sxs-lookup"><span data-stu-id="32a70-166">The hub then sends the message to all clients by calling `Clients.All.broadcastMessage`.</span></span>

<span data-ttu-id="32a70-167">`Send` Metoda ukazuje několik konceptů hub:</span><span class="sxs-lookup"><span data-stu-id="32a70-167">The `Send` method demonstrates several hub concepts:</span></span>

* <span data-ttu-id="32a70-168">Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.</span><span class="sxs-lookup"><span data-stu-id="32a70-168">Declare public methods on a hub so that clients can call them.</span></span>

* <span data-ttu-id="32a70-169">Použití `Microsoft.AspNet.SignalR.Hub.Clients` dynamických vlastností pro komunikaci se všemi klienty připojené pro toto centrum.</span><span class="sxs-lookup"><span data-stu-id="32a70-169">Use the `Microsoft.AspNet.SignalR.Hub.Clients` dynamic property to communicate with all clients connected to this hub.</span></span>

* <span data-ttu-id="32a70-170">Volání funkce na straně klienta (například `broadcastMessage` funkce) aktualizovat klienty.</span><span class="sxs-lookup"><span data-stu-id="32a70-170">Call a function on the client (like the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery-in-the-indexhtml"></a><span data-ttu-id="32a70-171">SignalR a jQuery v index.html</span><span class="sxs-lookup"><span data-stu-id="32a70-171">SignalR and jQuery in the index.html</span></span>

<span data-ttu-id="32a70-172">*Index.html* stránky ve vzorovém kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="32a70-172">The *index.html* page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="32a70-173">Kód provede celou řadu důležitých úloh.</span><span class="sxs-lookup"><span data-stu-id="32a70-173">The code carries out many important tasks.</span></span> <span data-ttu-id="32a70-174">Deklaruje proxy server tak, aby odkazovaly rozbočovači, deklaruje funkci, že server může volat do předávaný obsah pro klienty a spustí připojení k odeslání zprávy do centra.</span><span class="sxs-lookup"><span data-stu-id="32a70-174">It declares a proxy to reference the hub, declares a function that the server can call to push content to clients, and it starts a connection to send messages to the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> <span data-ttu-id="32a70-175">V jazyce JavaScript odkaz na třídu serveru a jeho členy musí být camelCase.</span><span class="sxs-lookup"><span data-stu-id="32a70-175">In JavaScript the reference to the server class and its members has to be camelCase.</span></span> <span data-ttu-id="32a70-176">Odkazy na ukázkový kód C# *ChatHub* třídy v jazyce JavaScript jako `chatHub`.</span><span class="sxs-lookup"><span data-stu-id="32a70-176">The code sample references the C# *ChatHub* class in JavaScript as `chatHub`.</span></span>

<span data-ttu-id="32a70-177">V tomto bloku kódu můžete vytvořit funkce zpětného volání ve skriptu.</span><span class="sxs-lookup"><span data-stu-id="32a70-177">In this code block, you create a callback function in the script.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

<span data-ttu-id="32a70-178">Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty.</span><span class="sxs-lookup"><span data-stu-id="32a70-178">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="32a70-179">Dva řádky, že kódování HTML obsah před jeho zobrazení jsou volitelné a zobrazit vhodný způsob, jak brání injektáži skriptu.</span><span class="sxs-lookup"><span data-stu-id="32a70-179">The two lines that HTML-encode the content before displaying it are optional and show a good way to prevent script injection.</span></span>

<span data-ttu-id="32a70-180">Tento kód otevře připojení v centru.</span><span class="sxs-lookup"><span data-stu-id="32a70-180">This code opens a connection with the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

> [!NOTE]
> <span data-ttu-id="32a70-181">Tento přístup zajišťuje, že kód naváže připojení před provedením obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="32a70-181">This approach ensures that the code establishes a connection before the event handler executes.</span></span>

<span data-ttu-id="32a70-182">Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce HTML.</span><span class="sxs-lookup"><span data-stu-id="32a70-182">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32a70-183">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="32a70-183">Additional resources</span></span>

<span data-ttu-id="32a70-184">Další informace o funkci SignalR naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="32a70-184">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="32a70-185">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="32a70-185">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="32a70-186">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="32a70-186">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="32a70-187">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="32a70-187">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="32a70-188">Další kroky</span><span class="sxs-lookup"><span data-stu-id="32a70-188">Next steps</span></span>

<span data-ttu-id="32a70-189">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="32a70-189">In this tutorial you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="32a70-190">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="32a70-190">Set up the project</span></span>
> * <span data-ttu-id="32a70-191">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="32a70-191">Ran the sample</span></span>
> * <span data-ttu-id="32a70-192">Prozkoumat kód</span><span class="sxs-lookup"><span data-stu-id="32a70-192">Examined the code</span></span>

<span data-ttu-id="32a70-193">Přejděte k dalším článku se dozvíte, jak používat funkci SignalR a MVC 5.</span><span class="sxs-lookup"><span data-stu-id="32a70-193">Advance to the next article to learn how to use SignalR and MVC 5.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="32a70-194">Knihovnou SignalR 2 a MVC 5</span><span class="sxs-lookup"><span data-stu-id="32a70-194">SignalR 2 and MVC 5</span></span>](tutorial-getting-started-with-signalr-and-mvc.md)
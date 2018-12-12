---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr
title: 'Kurz: Začínáme s knihovnou SignalR 1.x | Dokumentace Microsoftu'
author: pfletcher
description: Použijte knihovnu ASP.NET SignalR k sestavení aplikace chat v reálném čase na stránce HTML.
ms.author: riande
ms.date: 02/18/2013
ms.assetid: fdc3599a-5217-44c1-951f-0eec9812dce7
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 720a4879f5fbe3c0c2b4c7809cb94c22547329c3
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287354"
---
<a name="tutorial-getting-started-with-signalr-1x"></a><span data-ttu-id="0691f-103">Kurz: Začínáme s knihovnou SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="0691f-103">Tutorial: Getting Started with SignalR 1.x</span></span>
====================
<span data-ttu-id="0691f-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="0691f-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="0691f-105">V tomto kurzu se naučíte používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="0691f-105">This tutorial shows how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="0691f-106">Přidáte SignalR prázdná webová aplikace ASP.NET a vytvořte stránku HTML k odeslání a zobrazení zprávy.</span><span class="sxs-lookup"><span data-stu-id="0691f-106">You will add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span>


## <a name="overview"></a><span data-ttu-id="0691f-107">Přehled</span><span class="sxs-lookup"><span data-stu-id="0691f-107">Overview</span></span>

<span data-ttu-id="0691f-108">V tomto kurzu vám ukazuje, jak vytvořit jednoduchý založené na prohlížeči chatovací aplikaci představí vývoj pro funkci SignalR.</span><span class="sxs-lookup"><span data-stu-id="0691f-108">This tutorial introduces SignalR development by showing how to build a simple browser-based chat application.</span></span> <span data-ttu-id="0691f-109">Přidání knihovny SignalR na prázdnou webovou aplikaci ASP.NET, vytvoříte třídu hub pro odesílání zpráv do klientů a vytvořte stránku HTML, který umožňuje uživatelům odesílat a přijímat zprávy chatu.</span><span class="sxs-lookup"><span data-stu-id="0691f-109">You will add the SignalR library to an empty ASP.NET web application, create a hub class for sending messages to clients, and create an HTML page that lets users send and receive chat messages.</span></span> <span data-ttu-id="0691f-110">Podobný kurz, který ukazuje, jak vytvoření chatovací aplikace v MVC 4 pomocí zobrazení MVC, naleznete v tématu [Začínáme s knihovnou SignalR a MVC 4](index.md).</span><span class="sxs-lookup"><span data-stu-id="0691f-110">For a similar tutorial that shows how to create a chat application in MVC 4 using an MVC view, see [Getting Started with SignalR and MVC 4](index.md).</span></span>

> [!NOTE]
> <span data-ttu-id="0691f-111">Tento kurz používá verzi vydání (1.x) ze systému SignalR.</span><span class="sxs-lookup"><span data-stu-id="0691f-111">This tutorial uses the release (1.x) version of SignalR.</span></span> <span data-ttu-id="0691f-112">Podrobnosti o změny mezi knihovnou SignalR 1.x a 2.0, najdete v části [projektů upgrade SignalR 1.x](../releases/upgrading-signalr-1x-projects-to-20.md).</span><span class="sxs-lookup"><span data-stu-id="0691f-112">For details on changes between SignalR 1.x and 2.0, see [Upgrading SignalR 1.x Projects](../releases/upgrading-signalr-1x-projects-to-20.md).</span></span>

<span data-ttu-id="0691f-113">SignalR je open source knihovna .NET pro vytváření webových aplikací, které vyžadují aktualizace dat v reálném čase nebo interakci uživatelů za provozu.</span><span class="sxs-lookup"><span data-stu-id="0691f-113">SignalR is an open-source .NET library for building web applications that require live user interaction or real-time data updates.</span></span> <span data-ttu-id="0691f-114">Mezi příklady patří sociální aplikace, hry pro více uživatelů, obchodní spolupráci a novinky, počasí nebo finanční aktualizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="0691f-114">Examples include social applications, multiuser games, business collaboration, and news, weather, or financial update applications.</span></span> <span data-ttu-id="0691f-115">Nazývají se často aplikací v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="0691f-115">These are often called real-time applications.</span></span>

<span data-ttu-id="0691f-116">Funkce SignalR zjednodušuje proces vytváření aplikace v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="0691f-116">SignalR simplifies the process of building real-time applications.</span></span> <span data-ttu-id="0691f-117">Zahrnuje server knihovny ASP.NET a Javascriptovou klientskou knihovnu, aby bylo snazší spravovat připojení klient server a push aktualizace obsahu pro klienty.</span><span class="sxs-lookup"><span data-stu-id="0691f-117">It includes an ASP.NET server library and a JavaScript client library to make it easier to manage client-server connections and push content updates to clients.</span></span> <span data-ttu-id="0691f-118">Knihovny SignalR můžete přidat do stávající aplikace ASP.NET k získání funkcí v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="0691f-118">You can add the SignalR library to an existing ASP.NET application to gain real-time functionality.</span></span>

<span data-ttu-id="0691f-119">Tento kurz demonstruje následující úkoly vývoje SignalR:</span><span class="sxs-lookup"><span data-stu-id="0691f-119">The tutorial demonstrates the following SignalR development tasks:</span></span>

- <span data-ttu-id="0691f-120">Přidání knihovny SignalR k webové aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0691f-120">Adding the SignalR library to an ASP.NET web application.</span></span>
- <span data-ttu-id="0691f-121">Vytvoření třídy centra tak, aby nabízel obsah pro klienty.</span><span class="sxs-lookup"><span data-stu-id="0691f-121">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="0691f-122">K odesílání zpráv a zobrazení aktualizací z centra pomocí knihovny jQuery SignalR na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="0691f-122">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="0691f-123">Na následujícím snímku obrazovky je vidět chatovací aplikace spuštěné v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="0691f-123">The following screen shot shows the chat application running in a browser.</span></span> <span data-ttu-id="0691f-124">Všichni noví uživatelé mohou připomínky a naleznete v tématu komentáře přidané po uživatel připojí chat.</span><span class="sxs-lookup"><span data-stu-id="0691f-124">Each new user can post comments and see comments added after the user joins the chat.</span></span>

![Instance chatu](tutorial-getting-started-with-signalr/_static/image1.png)

<span data-ttu-id="0691f-126">Oddíly:</span><span class="sxs-lookup"><span data-stu-id="0691f-126">Sections:</span></span>

- [<span data-ttu-id="0691f-127">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="0691f-127">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="0691f-128">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="0691f-128">Run the Sample</span></span>](#run)
- [<span data-ttu-id="0691f-129">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="0691f-129">Examine the Code</span></span>](#code)
- [<span data-ttu-id="0691f-130">Další kroky</span><span class="sxs-lookup"><span data-stu-id="0691f-130">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="0691f-131">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="0691f-131">Set up the Project</span></span>

<span data-ttu-id="0691f-132">Tato část ukazuje, jak vytvořit prázdnou webovou aplikaci ASP.NET, přidejte SignalR a vytvořit chatovací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0691f-132">This section shows how to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

<span data-ttu-id="0691f-133">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="0691f-133">Prerequisites:</span></span>

- <span data-ttu-id="0691f-134">Visual Studio 2010 SP1 nebo 2012.</span><span class="sxs-lookup"><span data-stu-id="0691f-134">Visual Studio 2010 SP1 or 2012.</span></span> <span data-ttu-id="0691f-135">Pokud nemáte Visual Studio, přečtěte si téma [ASP.NET stáhne](https://www.asp.net/downloads) získat bezplatné Visual Studio 2012 Express vývojový nástroj.</span><span class="sxs-lookup"><span data-stu-id="0691f-135">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2012 Express Development Tool.</span></span>
- <span data-ttu-id="0691f-136">[Microsoft ASP.NET a Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941).</span><span class="sxs-lookup"><span data-stu-id="0691f-136">[Microsoft ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941).</span></span> <span data-ttu-id="0691f-137">Tento instalační program sady Visual Studio 2012, přidá nové funkce technologie ASP.NET, včetně šablon SignalR k sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0691f-137">For Visual Studio 2012, this installer adds new ASP.NET features including SignalR templates to Visual Studio.</span></span> <span data-ttu-id="0691f-138">Pro Visual Studio 2010 SP1 instalační program není k dispozici, ale tento kurz můžete dokončit tak jak je popsáno v krocích instalační program instaluje se balíček SignalR NuGet.</span><span class="sxs-lookup"><span data-stu-id="0691f-138">For Visual Studio 2010 SP1, an installer is not available but you can complete the tutorial by installing the SignalR NuGet package as described in the setup steps.</span></span>

<span data-ttu-id="0691f-139">Následující kroky slouží k vytvoření prázdná webová aplikace ASP.NET a přidání knihovny SignalR Visual Studio 2012:</span><span class="sxs-lookup"><span data-stu-id="0691f-139">The following steps use Visual Studio 2012 to create an ASP.NET Empty Web Application and add the SignalR library:</span></span>

1. <span data-ttu-id="0691f-140">V sadě Visual Studio vytvořte prázdnou webovou aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0691f-140">In Visual Studio create an ASP.NET Empty Web Application.</span></span>

    ![Vytvoření prázdného webu](tutorial-getting-started-with-signalr/_static/image2.png)
2. <span data-ttu-id="0691f-142">Otevřít **Konzola správce balíčků** tak, že vyberete **nástroje | Správce balíčků NuGet | Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="0691f-142">Open the **Package Manager Console** by selecting **Tools | NuGet Package Manager | Package Manager Console**.</span></span> <span data-ttu-id="0691f-143">Do okna konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0691f-143">Enter the following command into the console window:</span></span>

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    <span data-ttu-id="0691f-144">Tento příkaz nainstaluje nejnovější verzi SignalR 1.x.</span><span class="sxs-lookup"><span data-stu-id="0691f-144">This command installs the latest version of SignalR 1.x.</span></span>
3. <span data-ttu-id="0691f-145">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Třída**.</span><span class="sxs-lookup"><span data-stu-id="0691f-145">In **Solution Explorer**, right-click the project, select **Add | Class**.</span></span> <span data-ttu-id="0691f-146">Pojmenujte novou třídu **ChatHub**.</span><span class="sxs-lookup"><span data-stu-id="0691f-146">Name the new class **ChatHub**.</span></span>
4. <span data-ttu-id="0691f-147">V **Průzkumníka řešení** rozbalte uzel skripty.</span><span class="sxs-lookup"><span data-stu-id="0691f-147">In **Solution Explorer** expand the Scripts node.</span></span> <span data-ttu-id="0691f-148">Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.</span><span class="sxs-lookup"><span data-stu-id="0691f-148">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    ![Odkazy na knihovny](tutorial-getting-started-with-signalr/_static/image3.png)
5. <span data-ttu-id="0691f-150">Nahraďte kód v **ChatHub** třídy následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="0691f-150">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. <span data-ttu-id="0691f-151">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Nová položka**.</span><span class="sxs-lookup"><span data-stu-id="0691f-151">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="0691f-152">V **přidat novou položku** dialogového okna, vyberte **Global Application Class** a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="0691f-152">In the **Add New Item** dialog, select **Global Application Class** and click **Add**.</span></span>

    ![Přidání globální](tutorial-getting-started-with-signalr/_static/image4.png)
7. <span data-ttu-id="0691f-154">Přidejte následující `using` příkazy po zadaných `using` příkazy ve třídě Global.asax.cs.</span><span class="sxs-lookup"><span data-stu-id="0691f-154">Add the following `using` statements after the provided `using` statements in the Global.asax.cs class.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. <span data-ttu-id="0691f-155">Přidejte následující řádek kódu `Application_Start` metoda globální třídy k registraci výchozí trasu rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="0691f-155">Add the following line of code in the `Application_Start` method of the Global class to register the default route for SignalR hubs.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. <span data-ttu-id="0691f-156">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Nová položka**.</span><span class="sxs-lookup"><span data-stu-id="0691f-156">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="0691f-157">V **přidat novou položku** dialogového okna, vyberte stránka Html a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="0691f-157">In the **Add New Item** dialog, select Html Page and click **Add**.</span></span>
10. <span data-ttu-id="0691f-158">V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku HTML, který jste právě vytvořili a klikněte na tlačítko **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="0691f-158">In **Solution Explorer**, right-click the HTML page you just created and click **Set as Start Page**.</span></span>
11. <span data-ttu-id="0691f-159">Nahraďte kód výchozí stránku HTML s následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="0691f-159">Replace the default code in the HTML page with the following code.</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. <span data-ttu-id="0691f-160">**Uložit vše** pro projekt.</span><span class="sxs-lookup"><span data-stu-id="0691f-160">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="0691f-161">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="0691f-161">Run the Sample</span></span>

1. <span data-ttu-id="0691f-162">Stisknutím klávesy F5 spusťte projekt v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="0691f-162">Press F5 to run the project in debug mode.</span></span> <span data-ttu-id="0691f-163">HTML stránka načte v instanci prohlížeče a vyzve k zadání uživatelského jména.</span><span class="sxs-lookup"><span data-stu-id="0691f-163">The HTML page loads in a browser instance and prompts for a user name.</span></span>

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr/_static/image5.png)
2. <span data-ttu-id="0691f-165">Zadejte uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="0691f-165">Enter a user name.</span></span>
3. <span data-ttu-id="0691f-166">Zkopírujte adresu URL v adresním řádku prohlížeče a můžete otevřít dvě další instance prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="0691f-166">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="0691f-167">V každé instanci prohlížeče zadejte jedinečné uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="0691f-167">In each browser instance, enter a unique user name.</span></span>
4. <span data-ttu-id="0691f-168">V každé instanci prohlížeče, přidejte komentář a klikněte na tlačítko **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="0691f-168">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="0691f-169">Zobrazit komentáře ve všech instancích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="0691f-169">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="0691f-170">Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru.</span><span class="sxs-lookup"><span data-stu-id="0691f-170">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="0691f-171">Centrum vysílá poznámky pro všechny aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="0691f-171">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="0691f-172">Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.</span><span class="sxs-lookup"><span data-stu-id="0691f-172">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="0691f-173">Následující snímek obrazovky ukazuje chatovací aplikaci spuštěnou v tři instance prohlížeče, které aktualizují po jedné instance odešle zprávu:</span><span class="sxs-lookup"><span data-stu-id="0691f-173">The following screen shot shows the chat application running in three browser instances, all of which are updated when one instance sends a message:</span></span>

    ![Chat prohlížeče](tutorial-getting-started-with-signalr/_static/image6.png)
5. <span data-ttu-id="0691f-175">V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0691f-175">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="0691f-176">Existuje soubor skriptu s názvem **rozbočovače** generující knihovně SignalR dynamicky za běhu.</span><span class="sxs-lookup"><span data-stu-id="0691f-176">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="0691f-177">Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="0691f-177">This file manages the communication between jQuery script and server-side code.</span></span>

    ![Vygenerovaný centra skriptů](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="0691f-179">Prozkoumejte kód</span><span class="sxs-lookup"><span data-stu-id="0691f-179">Examine the Code</span></span>

<span data-ttu-id="0691f-180">Chatovací aplikace SignalR ukazuje dvě základní úkoly vývoje SignalR: vytváří se centrum jako objekt hlavního koordinace na serveru a pomocí knihovny jQuery SignalR k odesílání a příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="0691f-180">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="0691f-181">Rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="0691f-181">SignalR Hubs</span></span>

<span data-ttu-id="0691f-182">V ukázce kódu **ChatHub** třída odvozena z **Microsoft.AspNet.SignalR.Hub** třídy.</span><span class="sxs-lookup"><span data-stu-id="0691f-182">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="0691f-183">Odvozování z **centra** třída je užitečný způsob, jak vytvořit aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="0691f-183">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="0691f-184">Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z jQuery skripty na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="0691f-184">You can create public methods on your hub class and then access those methods by calling them from jQuery scripts in a web page.</span></span>

<span data-ttu-id="0691f-185">V kódu, konverzace, klienti volání **ChatHub.Send** metoda odesílá nová zpráva.</span><span class="sxs-lookup"><span data-stu-id="0691f-185">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="0691f-186">Centrum pak odešle zprávu pro všechny klienty voláním **Clients.All.broadcastMessage**.</span><span class="sxs-lookup"><span data-stu-id="0691f-186">The hub in turn sends the message to all clients by calling **Clients.All.broadcastMessage**.</span></span>

<span data-ttu-id="0691f-187">**Odeslat** metoda ukazuje několik konceptů hub:</span><span class="sxs-lookup"><span data-stu-id="0691f-187">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="0691f-188">Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.</span><span class="sxs-lookup"><span data-stu-id="0691f-188">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="0691f-189">Použití **Microsoft.AspNet.SignalR.Hub.Clients** dynamických vlastností pro přístup k všechny klienty připojené pro toto centrum.</span><span class="sxs-lookup"><span data-stu-id="0691f-189">Use the **Microsoft.AspNet.SignalR.Hub.Clients** dynamic property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="0691f-190">Volání funkce jQuery na straně klienta (například `broadcastMessage` funkce) aktualizovat klienty.</span><span class="sxs-lookup"><span data-stu-id="0691f-190">Call a jQuery function on the client (such as the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="0691f-191">SignalR a jQuery</span><span class="sxs-lookup"><span data-stu-id="0691f-191">SignalR and jQuery</span></span>

<span data-ttu-id="0691f-192">Na stránce HTML ve vzorovém kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="0691f-192">The HTML page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="0691f-193">Proxy server tak, aby odkazovaly rozbočovače, deklarace funkce, která můžete volat na serveru, abyste předávaný obsah pro klienty a spouští se připojení k odeslání zprávy do centra jsou deklarace základních úloh v kódu.</span><span class="sxs-lookup"><span data-stu-id="0691f-193">The essential tasks in the code are declaring a proxy to reference the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="0691f-194">Následující kód deklaruje proxy server rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="0691f-194">The following code declares a proxy for a hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="0691f-195">V jQuery je odkaz na třídu serveru a jeho členy v stylem camel case.</span><span class="sxs-lookup"><span data-stu-id="0691f-195">In jQuery the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="0691f-196">Odkazuje na vzorový kód jazyka C# **ChatHub** třídy v jQuery jako **chatHub**.</span><span class="sxs-lookup"><span data-stu-id="0691f-196">The code sample references the C# **ChatHub** class in jQuery as **chatHub**.</span></span>


<span data-ttu-id="0691f-197">Následující kód je, jak vytvořit funkci zpětného volání ve skriptu.</span><span class="sxs-lookup"><span data-stu-id="0691f-197">The following code is how you create a callback function in the script.</span></span> <span data-ttu-id="0691f-198">Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty.</span><span class="sxs-lookup"><span data-stu-id="0691f-198">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="0691f-199">Následující dva řádky, že s kódováním HTML obsah před jeho zobrazení jsou volitelné a zobrazit jednoduchý způsob, jak brání injektáži skriptu.</span><span class="sxs-lookup"><span data-stu-id="0691f-199">The two lines that HTML encode the content before displaying it are optional and show a simple way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

<span data-ttu-id="0691f-200">Následující kód ukazuje, jak otevřít připojení v centru.</span><span class="sxs-lookup"><span data-stu-id="0691f-200">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="0691f-201">Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce HTML.</span><span class="sxs-lookup"><span data-stu-id="0691f-201">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

> [!NOTE]
> <span data-ttu-id="0691f-202">Tento přístup zajistí, že připojení před provedením obslužná rutina události.</span><span class="sxs-lookup"><span data-stu-id="0691f-202">This approach insures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="0691f-203">Další kroky</span><span class="sxs-lookup"><span data-stu-id="0691f-203">Next Steps</span></span>

<span data-ttu-id="0691f-204">Jste zjistili, že SignalR je architektura určená k vytváření aplikací webu v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="0691f-204">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="0691f-205">Také jste se naučili několik úloh vývoje SignalR: jak přidat do aplikace ASP.NET SignalR, jak vytvořit třídu centra a jak odesílat a přijímat zprávy z centra.</span><span class="sxs-lookup"><span data-stu-id="0691f-205">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="0691f-206">Můžete zpřístupnit ukázkovou aplikaci v tomto kurzu nebo jiné aplikace SignalR přes Internet podle jejich nasazení do poskytovatele hostitelských služeb.</span><span class="sxs-lookup"><span data-stu-id="0691f-206">You can make the sample application in this tutorial or other SignalR applications available over the Internet by deploying them to a hosting provider.</span></span> <span data-ttu-id="0691f-207">Společnost Microsoft nabízí bezplatné webových hostitelských služeb pro až 10 webových serverů v bezplatné [zkušebního účtu Windows Azure](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604).</span><span class="sxs-lookup"><span data-stu-id="0691f-207">Microsoft offers free web hosting for up to 10 web sites in a free [Windows Azure trial account](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604).</span></span> <span data-ttu-id="0691f-208">Podrobný postup nasazení ukázkové aplikace SignalR, naleznete v tématu [publikovat SignalR získávání spuštění ukázky jako webu Windows Azure](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx).</span><span class="sxs-lookup"><span data-stu-id="0691f-208">For a walkthrough on how to deploy the sample SignalR application, see [Publish the SignalR Getting Started Sample as a Windows Azure Web Site](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx).</span></span> <span data-ttu-id="0691f-209">Podrobné informace o tom, jak nasadit webový projekt sady Visual Studio na webu Windows Azure naleznete v tématu [nasazení aplikace ASP.NET na web Windows Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="0691f-209">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Deploying an ASP.NET Application to a Windows Azure Web Site](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).</span></span> <span data-ttu-id="0691f-210">(Poznámka: Přenos pomocí protokolu WebSocket není aktuálně podporován pro Windows Azure websites.</span><span class="sxs-lookup"><span data-stu-id="0691f-210">(Note: The WebSocket transport is not currently supported for Windows Azure Web Sites.</span></span> <span data-ttu-id="0691f-211">Přenos při protokolu WebSocket nejsou k dispozici, SignalR používá k dispozici přenosy, jak je popsáno v části přenosy [Úvod do tématu SignalR](index.md).)</span><span class="sxs-lookup"><span data-stu-id="0691f-211">When WebSocket transport is not available, SignalR uses the other available transports as described in the Transports section of the [Introduction to SignalR topic](index.md).)</span></span>

<span data-ttu-id="0691f-212">Informace o pokročilejších pojmech vývoj SignalR, naleznete na následujících stránkách pro funkci SignalR zdrojový kód a prostředky:</span><span class="sxs-lookup"><span data-stu-id="0691f-212">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="0691f-213">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="0691f-213">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="0691f-214">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="0691f-214">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="0691f-215">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="0691f-215">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Kurz: Vytvoření aplikace v reálném čase vysokou frekvencí s funkcí SignalR 2 | Dokumentace Microsoftu'
author: bradygaster
description: Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá knihovnu ASP.NET SignalR k zajištění funkce zasílání zpráv vysokou frekvencí.
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 44aaa2b0c059de310e963f642fa56c2f00a7e443
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836724"
---
# <a name="tutorial-create-high-frequency-real-time-app-with-signalr-2"></a><span data-ttu-id="98424-103">Kurz: Vytvoření aplikace v reálném čase vysokou frekvencí s funkcí SignalR 2</span><span class="sxs-lookup"><span data-stu-id="98424-103">Tutorial: Create high-frequency real-time app with SignalR 2</span></span>

<span data-ttu-id="98424-104">Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 zasílání zpráv nakonfigurovánu vysokou frekvencí.</span><span class="sxs-lookup"><span data-stu-id="98424-104">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span> <span data-ttu-id="98424-105">V tomto případě "vysokou frekvencí zasílání zpráv" znamená, že server odešle aktualizace s pevnou sazbou.</span><span class="sxs-lookup"><span data-stu-id="98424-105">In this case, "high-frequency messaging" means the server sends updates at a fixed rate.</span></span> <span data-ttu-id="98424-106">Můžete odeslat až 10 zpráv za sekundu.</span><span class="sxs-lookup"><span data-stu-id="98424-106">You send up to 10 messages a second.</span></span>

<span data-ttu-id="98424-107">Vytvářené aplikace zobrazí obrazec, který můžete přetáhnout uživatelů.</span><span class="sxs-lookup"><span data-stu-id="98424-107">The application you create displays a shape that users can drag.</span></span> <span data-ttu-id="98424-108">Na serveru aktualizuje umístění obrazce ve všech propojených prohlížečů tak, aby odpovídala pozici Přetahované tvaru použitím vypršel časový limit aktualizace.</span><span class="sxs-lookup"><span data-stu-id="98424-108">The server updates the position of the shape in all connected browsers to match the position of the dragged shape using timed updates.</span></span>

<span data-ttu-id="98424-109">Koncepty představenými v tomto kurzu jste aplikací v reálném čase hry a další aplikace simulace.</span><span class="sxs-lookup"><span data-stu-id="98424-109">Concepts introduced in this tutorial have applications in real-time gaming and other simulation applications.</span></span>

<span data-ttu-id="98424-110">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="98424-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="98424-111">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="98424-111">Set up the project</span></span>
> * <span data-ttu-id="98424-112">Vytvoření základní aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-112">Create the base application</span></span>
> * <span data-ttu-id="98424-113">Mapovat k centru při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-113">Map to the hub when app starts</span></span>
> * <span data-ttu-id="98424-114">Přidat klienta</span><span class="sxs-lookup"><span data-stu-id="98424-114">Add the client</span></span>
> * <span data-ttu-id="98424-115">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-115">Run the app</span></span>
> * <span data-ttu-id="98424-116">Přidat cyklus klienta</span><span class="sxs-lookup"><span data-stu-id="98424-116">Add the client loop</span></span>
> * <span data-ttu-id="98424-117">Přidat cyklus serveru</span><span class="sxs-lookup"><span data-stu-id="98424-117">Add the server loop</span></span>
> * <span data-ttu-id="98424-118">Přidat plynulou animaci</span><span class="sxs-lookup"><span data-stu-id="98424-118">Add smooth animation</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="98424-119">Požadavky</span><span class="sxs-lookup"><span data-stu-id="98424-119">Prerequisites</span></span>

* <span data-ttu-id="98424-120">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="98424-120">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="98424-121">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="98424-121">Set up the project</span></span>

<span data-ttu-id="98424-122">V této části vytvoříte projekt v sadě Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="98424-122">In this section, you create the project in Visual Studio 2017.</span></span>

<span data-ttu-id="98424-123">Tato část ukazuje, jak pomocí sady Visual Studio 2017 vytvoří prázdná webová aplikace ASP.NET a přidejte knihovny SignalR a jQuery.UI.</span><span class="sxs-lookup"><span data-stu-id="98424-123">This section shows how to use Visual Studio 2017 to create an empty ASP.NET Web Application and add the SignalR and jQuery.UI libraries.</span></span>

1. <span data-ttu-id="98424-124">V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="98424-124">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Vytvořte web](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

1. <span data-ttu-id="98424-126">V **nová webová aplikace ASP.NET - MoveShapeDemo** okně, ponechejte tuto položku **prázdný** vybraný a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="98424-126">In the **New ASP.NET Web Application - MoveShapeDemo** window, leave **Empty** selected and select **OK**.</span></span>

1. <span data-ttu-id="98424-127">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="98424-127">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="98424-128">V **přidat novou položku - MoveShapeDemo**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="98424-128">In **Add New Item - MoveShapeDemo**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="98424-129">Název třídy *MoveShapeHub* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="98424-129">Name the class *MoveShapeHub* and add it to the project.</span></span>

    <span data-ttu-id="98424-130">Tento krok vytvoří *MoveShapeHub.cs* souboru třídy.</span><span class="sxs-lookup"><span data-stu-id="98424-130">This step creates the *MoveShapeHub.cs* class file.</span></span> <span data-ttu-id="98424-131">Současně přidá sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR k projektu.</span><span class="sxs-lookup"><span data-stu-id="98424-131">Simultaneously, it adds  a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="98424-132">Vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="98424-132">Select **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

1. <span data-ttu-id="98424-133">V **Konzola správce balíčků**, spusťte tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="98424-133">In **Package Manager Console**, run this command:</span></span>

    ```console
    Install-Package jQuery.UI.Combined
    ```

    <span data-ttu-id="98424-134">Tento příkaz nainstaluje knihovny uživatelského rozhraní jQuery.</span><span class="sxs-lookup"><span data-stu-id="98424-134">The command installs the jQuery UI library.</span></span> <span data-ttu-id="98424-135">Použít pro animaci tvaru.</span><span class="sxs-lookup"><span data-stu-id="98424-135">You use it to animate the shape.</span></span>

1. <span data-ttu-id="98424-136">V **Průzkumníka řešení**, rozbalte uzel skripty.</span><span class="sxs-lookup"><span data-stu-id="98424-136">In **Solution Explorer**, expand the Scripts node.</span></span>

    ![Odkazy na knihovnu skriptu](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

    <span data-ttu-id="98424-138">Knihovny skriptů pro funkci SignalR, jQuery a jQueryUI jsou viditelné v projektu.</span><span class="sxs-lookup"><span data-stu-id="98424-138">Script libraries for jQuery, jQueryUI, and SignalR are visible in the project.</span></span>

## <a name="create-the-base-application"></a><span data-ttu-id="98424-139">Vytvoření základní aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-139">Create the base application</span></span>

<span data-ttu-id="98424-140">V této části vytvoříte aplikace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="98424-140">In this section, you create a browser application.</span></span> <span data-ttu-id="98424-141">Aplikace odešle umístění obrazce na server během každou událost pohybu myší.</span><span class="sxs-lookup"><span data-stu-id="98424-141">The app sends the location of the shape to the server during each mouse move event.</span></span> <span data-ttu-id="98424-142">Server vysílá tyto informace do dalších připojených klientů v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="98424-142">The server broadcasts this information to all other connected clients in real time.</span></span> <span data-ttu-id="98424-143">Další informace o této aplikaci v předchozích částech.</span><span class="sxs-lookup"><span data-stu-id="98424-143">You learn more about this application in later sections.</span></span>

1. <span data-ttu-id="98424-144">Otevřít *MoveShapeHub.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="98424-144">Open the *MoveShapeHub.cs* file.</span></span>

1. <span data-ttu-id="98424-145">Nahraďte kód v *MoveShapeHub.cs* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="98424-145">Replace the code in the *MoveShapeHub.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

1. <span data-ttu-id="98424-146">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="98424-146">Save the file.</span></span>

<span data-ttu-id="98424-147">`MoveShapeHub` Třída je implementace rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="98424-147">The `MoveShapeHub` class is an implementation of a SignalR hub.</span></span> <span data-ttu-id="98424-148">Stejně jako [Začínáme s knihovnou SignalR](tutorial-getting-started-with-signalr.md) kurzu centra má metodu, která klientům volat přímo.</span><span class="sxs-lookup"><span data-stu-id="98424-148">As in the [Getting Started with SignalR](tutorial-getting-started-with-signalr.md) tutorial, the hub has a method that the clients call directly.</span></span> <span data-ttu-id="98424-149">V tomto případě klient zasílá objektu s novou X a Y souřadnic tvar, který má na serveru.</span><span class="sxs-lookup"><span data-stu-id="98424-149">In this case, the client sends an object with the new X and Y coordinates of the shape to the server.</span></span> <span data-ttu-id="98424-150">Všechny ostatní připojeným klientům získat vysílali Tyhle souřadnice.</span><span class="sxs-lookup"><span data-stu-id="98424-150">Those coordinates get broadcasted to all other connected clients.</span></span> <span data-ttu-id="98424-151">Funkce SignalR automaticky serializuje tento objekt pomocí formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="98424-151">SignalR automatically serializes this object using JSON.</span></span>

<span data-ttu-id="98424-152">Aplikace odešle `ShapeModel` objektu do klienta.</span><span class="sxs-lookup"><span data-stu-id="98424-152">The app sends the `ShapeModel` object to the client.</span></span> <span data-ttu-id="98424-153">Obsahuje členy k uložení umístění obrazce.</span><span class="sxs-lookup"><span data-stu-id="98424-153">It has members to store the position of the shape.</span></span> <span data-ttu-id="98424-154">Verze objektu na serveru má také členy, aby mohli sledovat kterého klienta data ukládají.</span><span class="sxs-lookup"><span data-stu-id="98424-154">The version of the object on the server also has a member to track which client's data is being stored.</span></span> <span data-ttu-id="98424-155">Tento objekt brání serveru z odesílání dat klientovi sám na sebe.</span><span class="sxs-lookup"><span data-stu-id="98424-155">This object prevents the server from sending a client's data back to itself.</span></span> <span data-ttu-id="98424-156">Používá tento člen `JsonIgnore` atribut, aby byla aplikace z serializace dat a jejich odesláním zpět do klienta.</span><span class="sxs-lookup"><span data-stu-id="98424-156">This member uses the `JsonIgnore` attribute to keep the application from serializing the data and sending it back to the client.</span></span>

## <a name="map-to-the-hub-when-app-starts"></a><span data-ttu-id="98424-157">Mapovat k centru při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-157">Map to the hub when app starts</span></span>

<span data-ttu-id="98424-158">V dalším kroku můžete nastavit mapování k centru při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="98424-158">Next, you set up mapping to the hub when the application starts.</span></span> <span data-ttu-id="98424-159">Přidání třídy pro spuštění OWIN SignalR 2, vytvoří mapování.</span><span class="sxs-lookup"><span data-stu-id="98424-159">In SignalR 2, adding an OWIN startup class creates the mapping.</span></span>

1. <span data-ttu-id="98424-160">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="98424-160">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="98424-161">V **přidat novou položku - MoveShapeDemo** vyberte **nainstalováno** > **Visual C#**   >  **webové** a pak Vyberte **třídy pro spuštění OWIN**.</span><span class="sxs-lookup"><span data-stu-id="98424-161">In **Add New Item - MoveShapeDemo** select **Installed** > **Visual C#** > **Web** and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="98424-162">Název třídy *spuštění* a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="98424-162">Name the class *Startup* and select **OK**.</span></span>

1. <span data-ttu-id="98424-163">Nahraďte kód v *Startup.cs* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="98424-163">Replace the default code in the *Startup.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

<span data-ttu-id="98424-164">Volání OWIN při spuštění třídy `MapSignalR` kdy aplikace spouští `Configuration` metody.</span><span class="sxs-lookup"><span data-stu-id="98424-164">The OWIN startup class calls `MapSignalR` when the app executes the `Configuration` method.</span></span> <span data-ttu-id="98424-165">Aplikace přidá třídu pro spuštění OWIN pro zpracování pomocí `OwinStartup` atribut sestavení.</span><span class="sxs-lookup"><span data-stu-id="98424-165">The app adds the class to OWIN's startup process using the `OwinStartup` assembly attribute.</span></span>

## <a name="add-the-client"></a><span data-ttu-id="98424-166">Přidat klienta</span><span class="sxs-lookup"><span data-stu-id="98424-166">Add the client</span></span>

<span data-ttu-id="98424-167">Přidáte stránku HTML pro klienta.</span><span class="sxs-lookup"><span data-stu-id="98424-167">Add the HTML page for the client.</span></span>

1. <span data-ttu-id="98424-168">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **stránku HTML**.</span><span class="sxs-lookup"><span data-stu-id="98424-168">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="98424-169">Pojmenujte stránku **výchozí** a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="98424-169">Name the page **Default** and select **OK**.</span></span>

1. <span data-ttu-id="98424-170">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *Default.html* a vyberte **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="98424-170">In **Solution Explorer**, right-click *Default.html* and select **Set as Start Page**.</span></span>

1. <span data-ttu-id="98424-171">Nahraďte kód v *Default.html* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="98424-171">Replace the default code in the *Default.html* file with this code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html?highlight=14-16)]

1. <span data-ttu-id="98424-172">V **Průzkumníka řešení**, rozbalte **skripty**.</span><span class="sxs-lookup"><span data-stu-id="98424-172">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="98424-173">Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.</span><span class="sxs-lookup"><span data-stu-id="98424-173">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="98424-174">Správce balíčků nainstaluje novější verzi skriptů SignalR.</span><span class="sxs-lookup"><span data-stu-id="98424-174">The package manager installs a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="98424-175">Aktualizujte odkazy na skript v bloku kódu tak, aby odpovídala verzi skriptů soubory v projektu.</span><span class="sxs-lookup"><span data-stu-id="98424-175">Update the script references in the code block to correspond to the versions of the script files in the project.</span></span>

<span data-ttu-id="98424-176">Tento kód HTML a JavaScript vytvoří červený `div` volá `shape`.</span><span class="sxs-lookup"><span data-stu-id="98424-176">This HTML and JavaScript code creates a red `div` called `shape`.</span></span> <span data-ttu-id="98424-177">Přetahování chování tvaru pomocí knihovny jQuery a používá `drag` události a umístění obrazce odeslat na server.</span><span class="sxs-lookup"><span data-stu-id="98424-177">It enables the shape's dragging behavior using the jQuery library and uses the `drag` event to send the shape's position to the server.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="98424-178">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-178">Run the app</span></span>

<span data-ttu-id="98424-179">Aplikaci můžete spustit na se'e fungovat.</span><span class="sxs-lookup"><span data-stu-id="98424-179">You can run the app to se\`e it work.</span></span> <span data-ttu-id="98424-180">Při přetažení tvar kolem okno prohlížeče, přesune se v jiných prohlížečích příliš.</span><span class="sxs-lookup"><span data-stu-id="98424-180">When you drag the shape around a browser window, the shape moves in the other browsers too.</span></span>

1. <span data-ttu-id="98424-181">Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte aplikaci v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="98424-181">In the toolbar, turn on **Script Debugging** and then select the play button to run the application in Debug mode.</span></span>

    ![Snímek obrazovky uživatele, zapnutí režimu ladění a vyberete play.](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)

    <span data-ttu-id="98424-183">Otevře se okno prohlížeče s červenou tvar v pravém horním rohu.</span><span class="sxs-lookup"><span data-stu-id="98424-183">A browser window opens with the red shape in the upper-right corner.</span></span>

1. <span data-ttu-id="98424-184">Zkopírujte adresu URL stránky.</span><span class="sxs-lookup"><span data-stu-id="98424-184">Copy the page's URL.</span></span>

1. <span data-ttu-id="98424-185">Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="98424-185">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="98424-186">Přetáhněte obrazec v jednom z okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="98424-186">Drag the shape in one of the browser windows.</span></span> <span data-ttu-id="98424-187">Následuje tvar v druhém okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="98424-187">The shape in the other browser window follows.</span></span>

<span data-ttu-id="98424-188">Při použití funkce touto metodou, není doporučenou programovací model.</span><span class="sxs-lookup"><span data-stu-id="98424-188">While the application functions using this method, it's not a recommended programming model.</span></span> <span data-ttu-id="98424-189">Neexistuje žádná horní limit počtu získání odeslaných zpráv.</span><span class="sxs-lookup"><span data-stu-id="98424-189">There's no upper limit to the number of messages getting sent.</span></span> <span data-ttu-id="98424-190">V důsledku toho klienty a serverem získat zahlcen zprávy a snižuje výkon.</span><span class="sxs-lookup"><span data-stu-id="98424-190">As a result, the clients and server get overwhelmed with messages and performance degrades.</span></span> <span data-ttu-id="98424-191">Také aplikace zobrazí odděleném animací na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="98424-191">Also, the app displays a disjointed animation on the client.</span></span> <span data-ttu-id="98424-192">Tato přehrávat nepravidelně animace se stane, protože tvar přesune okamžitě každé metody.</span><span class="sxs-lookup"><span data-stu-id="98424-192">This jerky animation happens because the shape moves instantly by each method.</span></span> <span data-ttu-id="98424-193">Je lepší, pokud tvar proběhnout do každého nového umístění.</span><span class="sxs-lookup"><span data-stu-id="98424-193">It's better if the shape moves smoothly to each new location.</span></span> <span data-ttu-id="98424-194">V dalším kroku se dozvíte, jak tyto problémy napravit.</span><span class="sxs-lookup"><span data-stu-id="98424-194">Next, you learn how to fix those issues.</span></span>

## <a name="add-the-client-loop"></a><span data-ttu-id="98424-195">Přidat cyklus klienta</span><span class="sxs-lookup"><span data-stu-id="98424-195">Add the client loop</span></span>

<span data-ttu-id="98424-196">Odesílání umístění tvar na každou událost pohybu myší vytvoří zbytečné objemu síťových přenosů.</span><span class="sxs-lookup"><span data-stu-id="98424-196">Sending the location of the shape on every mouse move event creates an unnecessary amount of network traffic.</span></span> <span data-ttu-id="98424-197">Aplikace je potřeba omezit zprávy z klienta.</span><span class="sxs-lookup"><span data-stu-id="98424-197">The app needs to throttle the messages from the client.</span></span>

<span data-ttu-id="98424-198">Použití jazyka javascript `setInterval` funkce nastavit smyčku, která odešle nové informace o umístění na server s pevnou sazbou.</span><span class="sxs-lookup"><span data-stu-id="98424-198">Use the javascript `setInterval` function to set up a loop that sends new position information to the server at a fixed rate.</span></span> <span data-ttu-id="98424-199">Smyčka je základní reprezentace "herní cyklus."</span><span class="sxs-lookup"><span data-stu-id="98424-199">This loop is a basic representation of a "game loop."</span></span> <span data-ttu-id="98424-200">Je opakovaně volaná funkce, která řídí všechny funkce, které jsou součástí hru.</span><span class="sxs-lookup"><span data-stu-id="98424-200">It's a repeatedly called function that drives all the functionality of a game.</span></span>

1. <span data-ttu-id="98424-201">Nahraďte kód klienta v *Default.html* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="98424-201">Replace the client code in the *Default.html* file with this code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html?highlight=14-16)]

    > [!IMPORTANT]
    > <span data-ttu-id="98424-202">Je nutné nahradit odkazy na skript znovu.</span><span class="sxs-lookup"><span data-stu-id="98424-202">You have to replace the script references again.</span></span> <span data-ttu-id="98424-203">Musí se shodovat verze skriptů v projektu.</span><span class="sxs-lookup"><span data-stu-id="98424-203">They must match the versions of the scripts in the project.</span></span>

    <span data-ttu-id="98424-204">Tento nový kód přidá `updateServerModel` funkce.</span><span class="sxs-lookup"><span data-stu-id="98424-204">This new code adds the `updateServerModel` function.</span></span> <span data-ttu-id="98424-205">Je volána na frekvenci dlouhodobého.</span><span class="sxs-lookup"><span data-stu-id="98424-205">It gets called on a fixed frequency.</span></span> <span data-ttu-id="98424-206">Funkce odešle data umístění serveru vždy, když `moved` příznak určuje, že je nová umístění dat k odeslání.</span><span class="sxs-lookup"><span data-stu-id="98424-206">The function sends the position data to the server whenever the `moved` flag indicates that there's new position data to send.</span></span>

1. <span data-ttu-id="98424-207">Vyberte tlačítko Přehrát a spusťte tak aplikaci</span><span class="sxs-lookup"><span data-stu-id="98424-207">Select the play button to start the application</span></span>

1. <span data-ttu-id="98424-208">Zkopírujte adresu URL stránky.</span><span class="sxs-lookup"><span data-stu-id="98424-208">Copy the page's URL.</span></span>

1. <span data-ttu-id="98424-209">Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="98424-209">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="98424-210">Přetáhněte obrazec v jednom z okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="98424-210">Drag the shape in one of the browser windows.</span></span> <span data-ttu-id="98424-211">Následuje tvar v druhém okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="98424-211">The shape in the other browser window follows.</span></span>

<span data-ttu-id="98424-212">Protože aplikace omezuje počet zpráv, které odesílání na server, se nezobrazí jako plynulé animace se nespustil na první.</span><span class="sxs-lookup"><span data-stu-id="98424-212">Since the app throttles the number of messages that get sent to the server, the animation won't appear as smooth did at first.</span></span>

## <a name="add-the-server-loop"></a><span data-ttu-id="98424-213">Přidat cyklus serveru</span><span class="sxs-lookup"><span data-stu-id="98424-213">Add the server loop</span></span>

<span data-ttu-id="98424-214">V aktuální aplikaci zprávy odeslané ze serveru klientovi chodit přímo tak často, jak jste dostali.</span><span class="sxs-lookup"><span data-stu-id="98424-214">In the current application, messages sent from the server to the client go out as often as they're received.</span></span> <span data-ttu-id="98424-215">Tento provoz sítě představuje problém podobné, jak vidíme na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="98424-215">This network traffic presents a similar problem as we see on the client.</span></span>

<span data-ttu-id="98424-216">Aplikace může odesílat zprávy častěji, než je budete potřebovat.</span><span class="sxs-lookup"><span data-stu-id="98424-216">The app can send messages more often than they're needed.</span></span> <span data-ttu-id="98424-217">Díky tomu můžou stát zahlcenou připojení.</span><span class="sxs-lookup"><span data-stu-id="98424-217">The connection can become flooded as a result.</span></span> <span data-ttu-id="98424-218">Tato část popisuje postup aktualizace serveru přidat časovač, který omezuje počet odchozích zpráv.</span><span class="sxs-lookup"><span data-stu-id="98424-218">This section describes how to update the server to add a timer that throttles the rate of the outgoing messages.</span></span>

1. <span data-ttu-id="98424-219">Nahraďte obsah `MoveShapeHub.cs` s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="98424-219">Replace the contents of `MoveShapeHub.cs` with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

1. <span data-ttu-id="98424-220">Vyberte tlačítko Přehrát a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="98424-220">Select the play button to start the application.</span></span>

1. <span data-ttu-id="98424-221">Zkopírujte adresu URL stránky.</span><span class="sxs-lookup"><span data-stu-id="98424-221">Copy the page's URL.</span></span>

1. <span data-ttu-id="98424-222">Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="98424-222">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="98424-223">Přetáhněte obrazec v jednom z okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="98424-223">Drag the shape in one of the browser windows.</span></span>

<span data-ttu-id="98424-224">Tento kód rozšiřuje klienta k přidání `Broadcaster` třídy.</span><span class="sxs-lookup"><span data-stu-id="98424-224">This code expands the client to add the `Broadcaster` class.</span></span> <span data-ttu-id="98424-225">Nová třída omezuje odchozí zprávy pomocí `Timer` třídy z rozhraní .NET framework.</span><span class="sxs-lookup"><span data-stu-id="98424-225">The new class throttles the outgoing messages using the `Timer` class from the .NET framework.</span></span>

<span data-ttu-id="98424-226">Je dobré se dozvíte, že Centrum samotného je přechodné.</span><span class="sxs-lookup"><span data-stu-id="98424-226">It's good to learn that the hub itself is transitory.</span></span> <span data-ttu-id="98424-227">Vytvoří se pokaždé, když je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="98424-227">It's created every time it's needed.</span></span> <span data-ttu-id="98424-228">Proto vytvoří aplikaci `Broadcaster` jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="98424-228">So the app creates the `Broadcaster` as a singleton.</span></span> <span data-ttu-id="98424-229">Opožděná inicializace využívá k odložení `Broadcaster`pro vytváření, dokud nebude potřeba.</span><span class="sxs-lookup"><span data-stu-id="98424-229">It uses lazy initialization to defer the `Broadcaster`'s creation until it's needed.</span></span> <span data-ttu-id="98424-230">To zaručuje, že aplikace vytvoří první instanci rozbočovače zcela před zahájením časovač.</span><span class="sxs-lookup"><span data-stu-id="98424-230">That guarantees that the app creates the first hub instance completely before starting the timer.</span></span>

<span data-ttu-id="98424-231">Volání klientů `UpdateShape` funkce je poté přesunut mimo centra `UpdateModel` metody.</span><span class="sxs-lookup"><span data-stu-id="98424-231">The call to the clients' `UpdateShape` function is then moved out of the hub's `UpdateModel` method.</span></span> <span data-ttu-id="98424-232">Už je volána ihned pokaždé, když aplikace přijme příchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="98424-232">It's no longer called immediately whenever the app receives incoming messages.</span></span> <span data-ttu-id="98424-233">Místo toho aplikace odesílá zprávy klientům ve výši 25 volání za sekundu.</span><span class="sxs-lookup"><span data-stu-id="98424-233">Instead, the app sends the messages to the clients at a rate of 25 calls per second.</span></span> <span data-ttu-id="98424-234">Spravuje proces `_broadcastLoop` časovač v rámci `Broadcaster` třídy.</span><span class="sxs-lookup"><span data-stu-id="98424-234">The process is  managed by the `_broadcastLoop` timer from within the `Broadcaster` class.</span></span>

<span data-ttu-id="98424-235">A konečně, namísto volání metody klienta z centra přímo, `Broadcaster` třídy je potřeba získat odkaz na aktuálně spuštěné `_hubContext` rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="98424-235">Lastly, instead of calling the client method from the hub directly, the `Broadcaster` class needs to get a reference to the currently operating `_hubContext` hub.</span></span> <span data-ttu-id="98424-236">Získá odkaz se `GlobalHost`.</span><span class="sxs-lookup"><span data-stu-id="98424-236">It gets the reference with the `GlobalHost`.</span></span>

## <a name="add-smooth-animation"></a><span data-ttu-id="98424-237">Přidat plynulou animaci</span><span class="sxs-lookup"><span data-stu-id="98424-237">Add smooth animation</span></span>

<span data-ttu-id="98424-238">Aplikace je téměř dokončena, ale jsme dokonce vytvářet jeden další vylepšení.</span><span class="sxs-lookup"><span data-stu-id="98424-238">The application is almost finished, but we could make one more improvement.</span></span> <span data-ttu-id="98424-239">Aplikace přesune tvar na straně klienta v reakci na zprávu serveru.</span><span class="sxs-lookup"><span data-stu-id="98424-239">The app moves the shape on the client in response to server messages.</span></span> <span data-ttu-id="98424-240">Namísto nastavení pozice tvar do nového umístění uvedena v každém serveru, použijte uživatelské rozhraní JQuery knihovnu `animate` funkce.</span><span class="sxs-lookup"><span data-stu-id="98424-240">Instead of setting the position of the shape to the new location given by the server, use the JQuery UI library's `animate` function.</span></span> <span data-ttu-id="98424-241">To můžete tvar přesunout plynule mezi jeho aktuální a nové pozice.</span><span class="sxs-lookup"><span data-stu-id="98424-241">It can move the shape smoothly between its current and new position.</span></span>

1. <span data-ttu-id="98424-242">Také aktualizovat klienta sady `updateShape` metodu *Default.html* souboru, aby vypadala jako zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="98424-242">Update the client's `updateShape` method in the *Default.html* file to look like the highlighted code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

1. <span data-ttu-id="98424-243">Vyberte tlačítko Přehrát a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="98424-243">Select the play button to start the application.</span></span>

1. <span data-ttu-id="98424-244">Zkopírujte adresu URL stránky.</span><span class="sxs-lookup"><span data-stu-id="98424-244">Copy the page's URL.</span></span>

1. <span data-ttu-id="98424-245">Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="98424-245">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="98424-246">Přetáhněte obrazec v jednom z okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="98424-246">Drag the shape in one of the browser windows.</span></span>

<span data-ttu-id="98424-247">Pohyb tvaru v druhém okně zobrazí méně přehrávat nepravidelně.</span><span class="sxs-lookup"><span data-stu-id="98424-247">The movement of the shape in the other window appears less jerky.</span></span> <span data-ttu-id="98424-248">Aplikace argument pohyb interpolaci přes čas Místo nastavování jednou za příchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="98424-248">The app interpolates its movement over time rather than being set once per incoming message.</span></span>

<span data-ttu-id="98424-249">Tento kód přesune tvaru z původního umístění do nové.</span><span class="sxs-lookup"><span data-stu-id="98424-249">This code moves the shape from the old location to the new one.</span></span> <span data-ttu-id="98424-250">Server poskytuje pozici obrazce v průběhu intervalu animace.</span><span class="sxs-lookup"><span data-stu-id="98424-250">The server gives the position of the shape over the course of the animation interval.</span></span> <span data-ttu-id="98424-251">V tomto případě to je 100 ms.</span><span class="sxs-lookup"><span data-stu-id="98424-251">In this case, that's 100 milliseconds.</span></span> <span data-ttu-id="98424-252">Aplikace vymaže všechny předchozí animace spuštěna s tvarem před zahájením nové animace.</span><span class="sxs-lookup"><span data-stu-id="98424-252">The app clears any previous animation running on the shape before the new animation starts.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="98424-253">Získat kód</span><span class="sxs-lookup"><span data-stu-id="98424-253">Get the code</span></span>

[<span data-ttu-id="98424-254">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="98424-254">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

## <a name="additional-resources"></a><span data-ttu-id="98424-255">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="98424-255">Additional resources</span></span>

<span data-ttu-id="98424-256">Právě jste se dozvěděli o paradigma komunikace je užitečné pro vývoj online hry a další simulace, jako je třeba [ShootR hru vytvořili s knihovnou SignalR](https://shootr.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="98424-256">The communication paradigm you just learned about is useful for developing online games and other simulations, like [the ShootR game created with SignalR](https://shootr.azurewebsites.net/).</span></span>

<span data-ttu-id="98424-257">Další informace o funkci SignalR naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="98424-257">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="98424-258">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="98424-258">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="98424-259">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="98424-259">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="98424-260">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="98424-260">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="98424-261">Další kroky</span><span class="sxs-lookup"><span data-stu-id="98424-261">Next steps</span></span>

<span data-ttu-id="98424-262">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="98424-262">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="98424-263">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="98424-263">Set up the project</span></span>
> * <span data-ttu-id="98424-264">Vytvoření základní aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-264">Created the base application</span></span>
> * <span data-ttu-id="98424-265">Mapovat na rozbočovači při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-265">Mapped to the hub when app starts</span></span>
> * <span data-ttu-id="98424-266">Přidání klienta</span><span class="sxs-lookup"><span data-stu-id="98424-266">Added the client</span></span>
> * <span data-ttu-id="98424-267">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="98424-267">Ran the app</span></span>
> * <span data-ttu-id="98424-268">Přidání smyčky klienta</span><span class="sxs-lookup"><span data-stu-id="98424-268">Added the client loop</span></span>
> * <span data-ttu-id="98424-269">Přidat server smyčky</span><span class="sxs-lookup"><span data-stu-id="98424-269">Added the server loop</span></span>
> * <span data-ttu-id="98424-270">Přidání plynulou animaci</span><span class="sxs-lookup"><span data-stu-id="98424-270">Added smooth animation</span></span>

<span data-ttu-id="98424-271">Přejděte k dalším článku se dozvíte, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 pro zajištění všesměrového vysílání funkce serveru.</span><span class="sxs-lookup"><span data-stu-id="98424-271">Advance to the next article to learn how to create a web application that uses ASP.NET SignalR 2 to provide server broadcast functionality.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="98424-272">Funkce SignalR 2 a vysílání serveru</span><span class="sxs-lookup"><span data-stu-id="98424-272">SignalR 2 and server broadcasting</span></span>](tutorial-server-broadcast-with-signalr.md)
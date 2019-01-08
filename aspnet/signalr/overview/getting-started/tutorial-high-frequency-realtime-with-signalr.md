---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Kurz: Vytvoření aplikace v reálném čase vysokou frekvencí s funkcí SignalR 2 | Dokumentace Microsoftu'
author: pfletcher
description: Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá knihovnu ASP.NET SignalR k zajištění funkce zasílání zpráv vysokou frekvencí.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 85503db0b41be6f87136627667d6dd71f0d4f609
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098587"
---
# <a name="tutorial-create-high-frequency-real-time-app-with-signalr-2"></a><span data-ttu-id="23b72-103">Kurz: Vytvoření aplikace v reálném čase vysokou frekvencí s funkcí SignalR 2</span><span class="sxs-lookup"><span data-stu-id="23b72-103">Tutorial: Create high-frequency real-time app with SignalR 2</span></span>

<span data-ttu-id="23b72-104">Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 zasílání zpráv nakonfigurovánu vysokou frekvencí.</span><span class="sxs-lookup"><span data-stu-id="23b72-104">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span> <span data-ttu-id="23b72-105">V tomto případě "vysokou frekvencí zasílání zpráv" znamená, že server odešle aktualizace s pevnou sazbou.</span><span class="sxs-lookup"><span data-stu-id="23b72-105">In this case, "high-frequency messaging" means the server sends updates at a fixed rate.</span></span> <span data-ttu-id="23b72-106">Můžete odeslat až 10 zpráv za sekundu.</span><span class="sxs-lookup"><span data-stu-id="23b72-106">You send up to 10 messages a second.</span></span>

<span data-ttu-id="23b72-107">Vytvářené aplikace zobrazí obrazec, který můžete přetáhnout uživatelů.</span><span class="sxs-lookup"><span data-stu-id="23b72-107">The application you create displays a shape that users can drag.</span></span> <span data-ttu-id="23b72-108">Na serveru aktualizuje umístění obrazce ve všech propojených prohlížečů tak, aby odpovídala pozici Přetahované tvaru použitím vypršel časový limit aktualizace.</span><span class="sxs-lookup"><span data-stu-id="23b72-108">The server updates the position of the shape in all connected browsers to match the position of the dragged shape using timed updates.</span></span>

<span data-ttu-id="23b72-109">Koncepty představenými v tomto kurzu jste aplikací v reálném čase hry a další aplikace simulace.</span><span class="sxs-lookup"><span data-stu-id="23b72-109">Concepts introduced in this tutorial have applications in real-time gaming and other simulation applications.</span></span>

<span data-ttu-id="23b72-110">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="23b72-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23b72-111">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="23b72-111">Set up the project</span></span>
> * <span data-ttu-id="23b72-112">Vytvoření základní aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-112">Create the base application</span></span>
> * <span data-ttu-id="23b72-113">Mapovat k centru při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-113">Map to the hub when app starts</span></span>
> * <span data-ttu-id="23b72-114">Přidat klienta</span><span class="sxs-lookup"><span data-stu-id="23b72-114">Add the client</span></span>
> * <span data-ttu-id="23b72-115">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-115">Run the app</span></span>
> * <span data-ttu-id="23b72-116">Přidat cyklus klienta</span><span class="sxs-lookup"><span data-stu-id="23b72-116">Add the client loop</span></span>
> * <span data-ttu-id="23b72-117">Přidat cyklus serveru</span><span class="sxs-lookup"><span data-stu-id="23b72-117">Add the server loop</span></span>
> * <span data-ttu-id="23b72-118">Přidat plynulou animaci</span><span class="sxs-lookup"><span data-stu-id="23b72-118">Add smooth animation</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="23b72-119">Požadavky</span><span class="sxs-lookup"><span data-stu-id="23b72-119">Prerequisites</span></span>

* <span data-ttu-id="23b72-120">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="23b72-120">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="23b72-121">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="23b72-121">Set up the project</span></span>

<span data-ttu-id="23b72-122">V této části vytvoříte projekt v sadě Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="23b72-122">In this section, you create the project in Visual Studio 2017.</span></span>

<span data-ttu-id="23b72-123">Tato část ukazuje, jak pomocí sady Visual Studio 2017 vytvoří prázdná webová aplikace ASP.NET a přidejte knihovny SignalR a jQuery.UI.</span><span class="sxs-lookup"><span data-stu-id="23b72-123">This section shows how to use Visual Studio 2017 to create an empty ASP.NET Web Application and add the SignalR and jQuery.UI libraries.</span></span>

1. <span data-ttu-id="23b72-124">V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="23b72-124">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Vytvořte web](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

1. <span data-ttu-id="23b72-126">V **nová webová aplikace ASP.NET - MoveShapeDemo** okně, ponechejte tuto položku **prázdný** vybraný a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="23b72-126">In the **New ASP.NET Web Application - MoveShapeDemo** window, leave **Empty** selected and select **OK**.</span></span>

1. <span data-ttu-id="23b72-127">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="23b72-127">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="23b72-128">V **přidat novou položku - MoveShapeDemo**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="23b72-128">In **Add New Item - MoveShapeDemo**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="23b72-129">Název třídy *MoveShapeHub* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="23b72-129">Name the class *MoveShapeHub* and add it to the project.</span></span>

    <span data-ttu-id="23b72-130">Tento krok vytvoří *MoveShapeHub.cs* souboru třídy.</span><span class="sxs-lookup"><span data-stu-id="23b72-130">This step creates the *MoveShapeHub.cs* class file.</span></span> <span data-ttu-id="23b72-131">Současně přidá sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR k projektu.</span><span class="sxs-lookup"><span data-stu-id="23b72-131">Simultaneously, it adds  a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="23b72-132">Vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="23b72-132">Select **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

1. <span data-ttu-id="23b72-133">V **Konzola správce balíčků**, spusťte tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="23b72-133">In **Package Manager Console**, run this command:</span></span>

    ```console
    Install-Package jQuery.UI.Combined
    ```

    <span data-ttu-id="23b72-134">Tento příkaz nainstaluje knihovny uživatelského rozhraní jQuery.</span><span class="sxs-lookup"><span data-stu-id="23b72-134">The command installs the jQuery UI library.</span></span> <span data-ttu-id="23b72-135">Použít pro animaci tvaru.</span><span class="sxs-lookup"><span data-stu-id="23b72-135">You use it to animate the shape.</span></span>

1. <span data-ttu-id="23b72-136">V **Průzkumníka řešení**, rozbalte uzel skripty.</span><span class="sxs-lookup"><span data-stu-id="23b72-136">In **Solution Explorer**, expand the Scripts node.</span></span>

    ![Odkazy na knihovnu skriptu](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

    <span data-ttu-id="23b72-138">Knihovny skriptů pro funkci SignalR, jQuery a jQueryUI jsou viditelné v projektu.</span><span class="sxs-lookup"><span data-stu-id="23b72-138">Script libraries for jQuery, jQueryUI, and SignalR are visible in the project.</span></span>

## <a name="create-the-base-application"></a><span data-ttu-id="23b72-139">Vytvoření základní aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-139">Create the base application</span></span>

<span data-ttu-id="23b72-140">V této části vytvoříte aplikace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23b72-140">In this section, you create a browser application.</span></span> <span data-ttu-id="23b72-141">Aplikace odešle umístění obrazce na server během každou událost pohybu myší.</span><span class="sxs-lookup"><span data-stu-id="23b72-141">The app sends the location of the shape to the server during each mouse move event.</span></span> <span data-ttu-id="23b72-142">Server vysílá tyto informace do dalších připojených klientů v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="23b72-142">The server broadcasts this information to all other connected clients in real time.</span></span> <span data-ttu-id="23b72-143">Další informace o této aplikaci v předchozích částech.</span><span class="sxs-lookup"><span data-stu-id="23b72-143">You learn more about this application in later sections.</span></span>

1. <span data-ttu-id="23b72-144">Otevřít *MoveShapeHub.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="23b72-144">Open the *MoveShapeHub.cs* file.</span></span>

1. <span data-ttu-id="23b72-145">Nahraďte kód v *MoveShapeHub.cs* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="23b72-145">Replace the code in the *MoveShapeHub.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

1. <span data-ttu-id="23b72-146">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="23b72-146">Save the file.</span></span>

<span data-ttu-id="23b72-147">`MoveShapeHub` Třída je implementace rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="23b72-147">The `MoveShapeHub` class is an implementation of a SignalR hub.</span></span> <span data-ttu-id="23b72-148">Stejně jako [Začínáme s knihovnou SignalR](tutorial-getting-started-with-signalr.md) kurzu centra má metodu, která klientům volat přímo.</span><span class="sxs-lookup"><span data-stu-id="23b72-148">As in the [Getting Started with SignalR](tutorial-getting-started-with-signalr.md) tutorial, the hub has a method that the clients call directly.</span></span> <span data-ttu-id="23b72-149">V tomto případě klient zasílá objektu s novou X a Y souřadnic tvar, který má na serveru.</span><span class="sxs-lookup"><span data-stu-id="23b72-149">In this case, the client sends an object with the new X and Y coordinates of the shape to the server.</span></span> <span data-ttu-id="23b72-150">Všechny ostatní připojeným klientům získat vysílali Tyhle souřadnice.</span><span class="sxs-lookup"><span data-stu-id="23b72-150">Those coordinates get broadcasted to all other connected clients.</span></span> <span data-ttu-id="23b72-151">Funkce SignalR automaticky serializuje tento objekt pomocí formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="23b72-151">SignalR automatically serializes this object using JSON.</span></span>

<span data-ttu-id="23b72-152">Aplikace odešle `ShapeModel` objektu do klienta.</span><span class="sxs-lookup"><span data-stu-id="23b72-152">The app sends the `ShapeModel` object to the client.</span></span> <span data-ttu-id="23b72-153">Obsahuje členy k uložení umístění obrazce.</span><span class="sxs-lookup"><span data-stu-id="23b72-153">It has members to store the position of the shape.</span></span> <span data-ttu-id="23b72-154">Verze objektu na serveru má také členy, aby mohli sledovat kterého klienta data ukládají.</span><span class="sxs-lookup"><span data-stu-id="23b72-154">The version of the object on the server also has a member to track which client's data is being stored.</span></span> <span data-ttu-id="23b72-155">Tento objekt brání serveru z odesílání dat klientovi sám na sebe.</span><span class="sxs-lookup"><span data-stu-id="23b72-155">This object prevents the server from sending a client's data back to itself.</span></span> <span data-ttu-id="23b72-156">Používá tento člen `JsonIgnore` atribut, aby byla aplikace z serializace dat a jejich odesláním zpět do klienta.</span><span class="sxs-lookup"><span data-stu-id="23b72-156">This member uses the `JsonIgnore` attribute to keep the application from serializing the data and sending it back to the client.</span></span>

## <a name="map-to-the-hub-when-app-starts"></a><span data-ttu-id="23b72-157">Mapovat k centru při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-157">Map to the hub when app starts</span></span>

<span data-ttu-id="23b72-158">V dalším kroku můžete nastavit mapování k centru při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="23b72-158">Next, you set up mapping to the hub when the application starts.</span></span> <span data-ttu-id="23b72-159">Přidání třídy pro spuštění OWIN SignalR 2, vytvoří mapování.</span><span class="sxs-lookup"><span data-stu-id="23b72-159">In SignalR 2, adding an OWIN startup class creates the mapping.</span></span>

1. <span data-ttu-id="23b72-160">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="23b72-160">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="23b72-161">V **přidat novou položku - MoveShapeDemo** vyberte **nainstalováno** > **Visual C#**   >  **webové** a pak Vyberte **třídy pro spuštění OWIN**.</span><span class="sxs-lookup"><span data-stu-id="23b72-161">In **Add New Item - MoveShapeDemo** select **Installed** > **Visual C#** > **Web** and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="23b72-162">Název třídy *spuštění* a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="23b72-162">Name the class *Startup* and select **OK**.</span></span>

1. <span data-ttu-id="23b72-163">Nahraďte kód v *Startup.cs* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="23b72-163">Replace the default code in the *Startup.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

<span data-ttu-id="23b72-164">Volání OWIN při spuštění třídy `MapSignalR` kdy aplikace spouští `Configuration` metody.</span><span class="sxs-lookup"><span data-stu-id="23b72-164">The OWIN startup class calls `MapSignalR` when the app executes the `Configuration` method.</span></span> <span data-ttu-id="23b72-165">Aplikace přidá třídu pro spuštění OWIN pro zpracování pomocí `OwinStartup` atribut sestavení.</span><span class="sxs-lookup"><span data-stu-id="23b72-165">The app adds the class to OWIN's startup process using the `OwinStartup` assembly attribute.</span></span>

## <a name="add-the-client"></a><span data-ttu-id="23b72-166">Přidat klienta</span><span class="sxs-lookup"><span data-stu-id="23b72-166">Add the client</span></span>

<span data-ttu-id="23b72-167">Přidáte stránku HTML pro klienta.</span><span class="sxs-lookup"><span data-stu-id="23b72-167">Add the HTML page for the client.</span></span>

1. <span data-ttu-id="23b72-168">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **stránku HTML**.</span><span class="sxs-lookup"><span data-stu-id="23b72-168">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="23b72-169">Pojmenujte stránku **výchozí** a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="23b72-169">Name the page **Default** and select **OK**.</span></span>

1. <span data-ttu-id="23b72-170">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *Default.html* a vyberte **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="23b72-170">In **Solution Explorer**, right-click *Default.html* and select **Set as Start Page**.</span></span>

1. <span data-ttu-id="23b72-171">Nahraďte kód v *Default.html* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="23b72-171">Replace the default code in the *Default.html* file with this code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html?highlight=14-16)]

1. <span data-ttu-id="23b72-172">V **Průzkumníka řešení**, rozbalte **skripty**.</span><span class="sxs-lookup"><span data-stu-id="23b72-172">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="23b72-173">Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.</span><span class="sxs-lookup"><span data-stu-id="23b72-173">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="23b72-174">Správce balíčků nainstaluje novější verzi skriptů SignalR.</span><span class="sxs-lookup"><span data-stu-id="23b72-174">The package manager installs a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="23b72-175">Aktualizujte odkazy na skript v bloku kódu tak, aby odpovídala verzi skriptů soubory v projektu.</span><span class="sxs-lookup"><span data-stu-id="23b72-175">Update the script references in the code block to correspond to the versions of the script files in the project.</span></span>

<span data-ttu-id="23b72-176">Tento kód HTML a JavaScript vytvoří červený `div` volá `shape`.</span><span class="sxs-lookup"><span data-stu-id="23b72-176">This HTML and JavaScript code creates a red `div` called `shape`.</span></span> <span data-ttu-id="23b72-177">Přetahování chování tvaru pomocí knihovny jQuery a používá `drag` události a umístění obrazce odeslat na server.</span><span class="sxs-lookup"><span data-stu-id="23b72-177">It enables the shape's dragging behavior using the jQuery library and uses the `drag` event to send the shape's position to the server.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="23b72-178">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-178">Run the app</span></span>

<span data-ttu-id="23b72-179">Aplikaci můžete spustit na se'e fungovat.</span><span class="sxs-lookup"><span data-stu-id="23b72-179">You can run the app to se\`e it work.</span></span> <span data-ttu-id="23b72-180">Při přetažení tvar kolem okno prohlížeče, přesune se v jiných prohlížečích příliš.</span><span class="sxs-lookup"><span data-stu-id="23b72-180">When you drag the shape around a browser window, the shape moves in the other browsers too.</span></span>

1. <span data-ttu-id="23b72-181">Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte aplikaci v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="23b72-181">In the toolbar, turn on **Script Debugging** and then select the play button to run the application in Debug mode.</span></span>

    ![Snímek obrazovky uživatele, zapnutí režimu ladění a vyberete play.](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)

    <span data-ttu-id="23b72-183">Otevře se okno prohlížeče s červenou tvar v pravém horním rohu.</span><span class="sxs-lookup"><span data-stu-id="23b72-183">A browser window opens with the red shape in the upper-right corner.</span></span>

1. <span data-ttu-id="23b72-184">Zkopírujte adresu URL stránky.</span><span class="sxs-lookup"><span data-stu-id="23b72-184">Copy the page's URL.</span></span>

1. <span data-ttu-id="23b72-185">Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="23b72-185">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="23b72-186">Přetáhněte obrazec v jednom z okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23b72-186">Drag the shape in one of the browser windows.</span></span> <span data-ttu-id="23b72-187">Následuje tvar v druhém okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23b72-187">The shape in the other browser window follows.</span></span>

<span data-ttu-id="23b72-188">Při použití funkce touto metodou, není doporučenou programovací model.</span><span class="sxs-lookup"><span data-stu-id="23b72-188">While the application functions using this method, it's not a recommended programming model.</span></span> <span data-ttu-id="23b72-189">Neexistuje žádná horní limit počtu získání odeslaných zpráv.</span><span class="sxs-lookup"><span data-stu-id="23b72-189">There's no upper limit to the number of messages getting sent.</span></span> <span data-ttu-id="23b72-190">V důsledku toho klienty a serverem získat zahlcen zprávy a snižuje výkon.</span><span class="sxs-lookup"><span data-stu-id="23b72-190">As a result, the clients and server get overwhelmed with messages and performance degrades.</span></span> <span data-ttu-id="23b72-191">Také aplikace zobrazí odděleném animací na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="23b72-191">Also, the app displays a disjointed animation on the client.</span></span> <span data-ttu-id="23b72-192">Tato přehrávat nepravidelně animace se stane, protože tvar přesune okamžitě každé metody.</span><span class="sxs-lookup"><span data-stu-id="23b72-192">This jerky animation happens because the shape moves instantly by each method.</span></span> <span data-ttu-id="23b72-193">Je lepší, pokud tvar proběhnout do každého nového umístění.</span><span class="sxs-lookup"><span data-stu-id="23b72-193">It's better if the shape moves smoothly to each new location.</span></span> <span data-ttu-id="23b72-194">V dalším kroku se dozvíte, jak tyto problémy napravit.</span><span class="sxs-lookup"><span data-stu-id="23b72-194">Next, you learn how to fix those issues.</span></span>

## <a name="add-the-client-loop"></a><span data-ttu-id="23b72-195">Přidat cyklus klienta</span><span class="sxs-lookup"><span data-stu-id="23b72-195">Add the client loop</span></span>

<span data-ttu-id="23b72-196">Odesílání umístění tvar na každou událost pohybu myší vytvoří zbytečné objemu síťových přenosů.</span><span class="sxs-lookup"><span data-stu-id="23b72-196">Sending the location of the shape on every mouse move event creates an unnecessary amount of network traffic.</span></span> <span data-ttu-id="23b72-197">Aplikace je potřeba omezit zprávy z klienta.</span><span class="sxs-lookup"><span data-stu-id="23b72-197">The app needs to throttle the messages from the client.</span></span>

<span data-ttu-id="23b72-198">Použití jazyka javascript `setInterval` funkce nastavit smyčku, která odešle nové informace o umístění na server s pevnou sazbou.</span><span class="sxs-lookup"><span data-stu-id="23b72-198">Use the javascript `setInterval` function to set up a loop that sends new position information to the server at a fixed rate.</span></span> <span data-ttu-id="23b72-199">Smyčka je základní reprezentace "herní cyklus."</span><span class="sxs-lookup"><span data-stu-id="23b72-199">This loop is a basic representation of a "game loop."</span></span> <span data-ttu-id="23b72-200">Je opakovaně volaná funkce, která řídí všechny funkce, které jsou součástí hru.</span><span class="sxs-lookup"><span data-stu-id="23b72-200">It's a repeatedly called function that drives all the functionality of a game.</span></span>

1. <span data-ttu-id="23b72-201">Nahraďte kód klienta v *Default.html* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="23b72-201">Replace the client code in the *Default.html* file with this code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html?highlight=14-16)]

    > [!IMPORTANT]
    > <span data-ttu-id="23b72-202">Je nutné nahradit odkazy na skript znovu.</span><span class="sxs-lookup"><span data-stu-id="23b72-202">You have to replace the script references again.</span></span> <span data-ttu-id="23b72-203">Musí se shodovat verze skriptů v projektu.</span><span class="sxs-lookup"><span data-stu-id="23b72-203">They must match the versions of the scripts in the project.</span></span>

    <span data-ttu-id="23b72-204">Tento nový kód přidá `updateServerModel` funkce.</span><span class="sxs-lookup"><span data-stu-id="23b72-204">This new code adds the `updateServerModel` function.</span></span> <span data-ttu-id="23b72-205">Je volána na frekvenci dlouhodobého.</span><span class="sxs-lookup"><span data-stu-id="23b72-205">It gets called on a fixed frequency.</span></span> <span data-ttu-id="23b72-206">Funkce odešle data umístění serveru vždy, když `moved` příznak určuje, že je nová umístění dat k odeslání.</span><span class="sxs-lookup"><span data-stu-id="23b72-206">The function sends the position data to the server whenever the `moved` flag indicates that there's new position data to send.</span></span>

1. <span data-ttu-id="23b72-207">Vyberte tlačítko Přehrát a spusťte tak aplikaci</span><span class="sxs-lookup"><span data-stu-id="23b72-207">Select the play button to start the application</span></span>

1. <span data-ttu-id="23b72-208">Zkopírujte adresu URL stránky.</span><span class="sxs-lookup"><span data-stu-id="23b72-208">Copy the page's URL.</span></span>

1. <span data-ttu-id="23b72-209">Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="23b72-209">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="23b72-210">Přetáhněte obrazec v jednom z okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23b72-210">Drag the shape in one of the browser windows.</span></span> <span data-ttu-id="23b72-211">Následuje tvar v druhém okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23b72-211">The shape in the other browser window follows.</span></span>

<span data-ttu-id="23b72-212">Protože aplikace omezuje počet zpráv, které odesílání na server, se nezobrazí jako plynulé animace se nespustil na první.</span><span class="sxs-lookup"><span data-stu-id="23b72-212">Since the app throttles the number of messages that get sent to the server, the animation won't appear as smooth did at first.</span></span>

## <a name="add-the-server-loop"></a><span data-ttu-id="23b72-213">Přidat cyklus serveru</span><span class="sxs-lookup"><span data-stu-id="23b72-213">Add the server loop</span></span>

<span data-ttu-id="23b72-214">V aktuální aplikaci zprávy odeslané ze serveru klientovi chodit přímo tak často, jak jste dostali.</span><span class="sxs-lookup"><span data-stu-id="23b72-214">In the current application, messages sent from the server to the client go out as often as they're received.</span></span> <span data-ttu-id="23b72-215">Tento provoz sítě představuje problém podobné, jak vidíme na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="23b72-215">This network traffic presents a similar problem as we see on the client.</span></span>

<span data-ttu-id="23b72-216">Aplikace může odesílat zprávy častěji, než je budete potřebovat.</span><span class="sxs-lookup"><span data-stu-id="23b72-216">The app can send messages more often than they're needed.</span></span> <span data-ttu-id="23b72-217">Díky tomu můžou stát zahlcenou připojení.</span><span class="sxs-lookup"><span data-stu-id="23b72-217">The connection can become flooded as a result.</span></span> <span data-ttu-id="23b72-218">Tato část popisuje postup aktualizace serveru přidat časovač, který omezuje počet odchozích zpráv.</span><span class="sxs-lookup"><span data-stu-id="23b72-218">This section describes how to update the server to add a timer that throttles the rate of the outgoing messages.</span></span>

1. <span data-ttu-id="23b72-219">Nahraďte obsah `MoveShapeHub.cs` s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="23b72-219">Replace the contents of `MoveShapeHub.cs` with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

1. <span data-ttu-id="23b72-220">Vyberte tlačítko Přehrát a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="23b72-220">Select the play button to start the application.</span></span>

1. <span data-ttu-id="23b72-221">Zkopírujte adresu URL stránky.</span><span class="sxs-lookup"><span data-stu-id="23b72-221">Copy the page's URL.</span></span>

1. <span data-ttu-id="23b72-222">Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="23b72-222">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="23b72-223">Přetáhněte obrazec v jednom z okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23b72-223">Drag the shape in one of the browser windows.</span></span>

<span data-ttu-id="23b72-224">Tento kód rozšiřuje klienta k přidání `Broadcaster` třídy.</span><span class="sxs-lookup"><span data-stu-id="23b72-224">This code expands the client to add the `Broadcaster` class.</span></span> <span data-ttu-id="23b72-225">Nová třída omezuje odchozí zprávy pomocí `Timer` třídy z rozhraní .NET framework.</span><span class="sxs-lookup"><span data-stu-id="23b72-225">The new class throttles the outgoing messages using the `Timer` class from the .NET framework.</span></span>

<span data-ttu-id="23b72-226">Je dobré se dozvíte, že Centrum samotného je přechodné.</span><span class="sxs-lookup"><span data-stu-id="23b72-226">It's good to learn that the hub itself is transitory.</span></span> <span data-ttu-id="23b72-227">Vytvoří se pokaždé, když je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="23b72-227">It's created every time it's needed.</span></span> <span data-ttu-id="23b72-228">Proto vytvoří aplikaci `Broadcaster` jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="23b72-228">So the app creates the `Broadcaster` as a singleton.</span></span> <span data-ttu-id="23b72-229">Opožděná inicializace využívá k odložení `Broadcaster`pro vytváření, dokud nebude potřeba.</span><span class="sxs-lookup"><span data-stu-id="23b72-229">It uses lazy initialization to defer the `Broadcaster`'s creation until it's needed.</span></span> <span data-ttu-id="23b72-230">To zaručuje, že aplikace vytvoří první instanci rozbočovače zcela před zahájením časovač.</span><span class="sxs-lookup"><span data-stu-id="23b72-230">That guarantees that the app creates the first hub instance completely before starting the timer.</span></span>

<span data-ttu-id="23b72-231">Volání klientů `UpdateShape` funkce je poté přesunut mimo centra `UpdateModel` metody.</span><span class="sxs-lookup"><span data-stu-id="23b72-231">The call to the clients' `UpdateShape` function is then moved out of the hub's `UpdateModel` method.</span></span> <span data-ttu-id="23b72-232">Už je volána ihned pokaždé, když aplikace přijme příchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="23b72-232">It's no longer called immediately whenever the app receives incoming messages.</span></span> <span data-ttu-id="23b72-233">Místo toho aplikace odesílá zprávy klientům ve výši 25 volání za sekundu.</span><span class="sxs-lookup"><span data-stu-id="23b72-233">Instead, the app sends the messages to the clients at a rate of 25 calls per second.</span></span> <span data-ttu-id="23b72-234">Spravuje proces `_broadcastLoop` časovač v rámci `Broadcaster` třídy.</span><span class="sxs-lookup"><span data-stu-id="23b72-234">The process is  managed by the `_broadcastLoop` timer from within the `Broadcaster` class.</span></span>

<span data-ttu-id="23b72-235">A konečně, namísto volání metody klienta z centra přímo, `Broadcaster` třídy je potřeba získat odkaz na aktuálně spuštěné `_hubContext` rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="23b72-235">Lastly, instead of calling the client method from the hub directly, the `Broadcaster` class needs to get a reference to the currently operating `_hubContext` hub.</span></span> <span data-ttu-id="23b72-236">Získá odkaz se `GlobalHost`.</span><span class="sxs-lookup"><span data-stu-id="23b72-236">It gets the reference with the `GlobalHost`.</span></span>

## <a name="add-smooth-animation"></a><span data-ttu-id="23b72-237">Přidat plynulou animaci</span><span class="sxs-lookup"><span data-stu-id="23b72-237">Add smooth animation</span></span>

<span data-ttu-id="23b72-238">Aplikace je téměř dokončena, ale jsme dokonce vytvářet jeden další vylepšení.</span><span class="sxs-lookup"><span data-stu-id="23b72-238">The application is almost finished, but we could make one more improvement.</span></span> <span data-ttu-id="23b72-239">Aplikace přesune tvar na straně klienta v reakci na zprávu serveru.</span><span class="sxs-lookup"><span data-stu-id="23b72-239">The app moves the shape on the client in response to server messages.</span></span> <span data-ttu-id="23b72-240">Namísto nastavení pozice tvar do nového umístění uvedena v každém serveru, použijte uživatelské rozhraní JQuery knihovnu `animate` funkce.</span><span class="sxs-lookup"><span data-stu-id="23b72-240">Instead of setting the position of the shape to the new location given by the server, use the JQuery UI library's `animate` function.</span></span> <span data-ttu-id="23b72-241">To můžete tvar přesunout plynule mezi jeho aktuální a nové pozice.</span><span class="sxs-lookup"><span data-stu-id="23b72-241">It can move the shape smoothly between its current and new position.</span></span>

1. <span data-ttu-id="23b72-242">Také aktualizovat klienta sady `updateShape` metodu *Default.html* souboru, aby vypadala jako zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="23b72-242">Update the client's `updateShape` method in the *Default.html* file to look like the highlighted code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

1. <span data-ttu-id="23b72-243">Vyberte tlačítko Přehrát a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="23b72-243">Select the play button to start the application.</span></span>

1. <span data-ttu-id="23b72-244">Zkopírujte adresu URL stránky.</span><span class="sxs-lookup"><span data-stu-id="23b72-244">Copy the page's URL.</span></span>

1. <span data-ttu-id="23b72-245">Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="23b72-245">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="23b72-246">Přetáhněte obrazec v jednom z okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="23b72-246">Drag the shape in one of the browser windows.</span></span>

<span data-ttu-id="23b72-247">Pohyb tvaru v druhém okně zobrazí méně přehrávat nepravidelně.</span><span class="sxs-lookup"><span data-stu-id="23b72-247">The movement of the shape in the other window appears less jerky.</span></span> <span data-ttu-id="23b72-248">Aplikace argument pohyb interpolaci přes čas Místo nastavování jednou za příchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="23b72-248">The app interpolates its movement over time rather than being set once per incoming message.</span></span>

<span data-ttu-id="23b72-249">Tento kód přesune tvaru z původního umístění do nové.</span><span class="sxs-lookup"><span data-stu-id="23b72-249">This code moves the shape from the old location to the new one.</span></span> <span data-ttu-id="23b72-250">Server poskytuje pozici obrazce v průběhu intervalu animace.</span><span class="sxs-lookup"><span data-stu-id="23b72-250">The server gives the position of the shape over the course of the animation interval.</span></span> <span data-ttu-id="23b72-251">V tomto případě to je 100 ms.</span><span class="sxs-lookup"><span data-stu-id="23b72-251">In this case, that's 100 milliseconds.</span></span> <span data-ttu-id="23b72-252">Aplikace vymaže všechny předchozí animace spuštěna s tvarem před zahájením nové animace.</span><span class="sxs-lookup"><span data-stu-id="23b72-252">The app clears any previous animation running on the shape before the new animation starts.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="23b72-253">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="23b72-253">Additional resources</span></span>

<span data-ttu-id="23b72-254">Právě jste se dozvěděli o paradigma komunikace je užitečné pro vývoj online hry a další simulace, jako je třeba [ShootR hru vytvořili s knihovnou SignalR](https://shootr.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="23b72-254">The communication paradigm you just learned about is useful for developing online games and other simulations, like [the ShootR game created with SignalR](https://shootr.azurewebsites.net/).</span></span>

<span data-ttu-id="23b72-255">Další informace o funkci SignalR naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="23b72-255">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="23b72-256">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="23b72-256">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="23b72-257">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="23b72-257">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="23b72-258">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="23b72-258">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="23b72-259">Další kroky</span><span class="sxs-lookup"><span data-stu-id="23b72-259">Next steps</span></span>

<span data-ttu-id="23b72-260">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="23b72-260">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23b72-261">Nastavení projektu</span><span class="sxs-lookup"><span data-stu-id="23b72-261">Set up the project</span></span>
> * <span data-ttu-id="23b72-262">Vytvoření základní aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-262">Created the base application</span></span>
> * <span data-ttu-id="23b72-263">Mapovat na rozbočovači při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-263">Mapped to the hub when app starts</span></span>
> * <span data-ttu-id="23b72-264">Přidání klienta</span><span class="sxs-lookup"><span data-stu-id="23b72-264">Added the client</span></span>
> * <span data-ttu-id="23b72-265">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="23b72-265">Ran the app</span></span>
> * <span data-ttu-id="23b72-266">Přidání smyčky klienta</span><span class="sxs-lookup"><span data-stu-id="23b72-266">Added the client loop</span></span>
> * <span data-ttu-id="23b72-267">Přidat server smyčky</span><span class="sxs-lookup"><span data-stu-id="23b72-267">Added the server loop</span></span>
> * <span data-ttu-id="23b72-268">Přidání plynulou animaci</span><span class="sxs-lookup"><span data-stu-id="23b72-268">Added smooth animation</span></span>

<span data-ttu-id="23b72-269">Přejděte k dalším článku se dozvíte, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 pro zajištění všesměrového vysílání funkce serveru.</span><span class="sxs-lookup"><span data-stu-id="23b72-269">Advance to the next article to learn how to create a web application that uses ASP.NET SignalR 2 to provide server broadcast functionality.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="23b72-270">Funkce SignalR 2 a vysílání serveru</span><span class="sxs-lookup"><span data-stu-id="23b72-270">SignalR 2 and server broadcasting</span></span>](tutorial-server-broadcast-with-signalr.md)
---
uid: signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
title: 'Kurz: Serverové vysílání s knihovnou SignalR 2 | Dokumentace Microsoftu'
author: tdykstra
description: Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 pro zajištění všesměrového vysílání funkce serveru.
ms.author: bradyg
ms.date: 01/02/2019
ms.topic: tutorial
ms.assetid: 1568247f-60b5-4eca-96e0-e661fbb2b273
msc.legacyurl: /signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a243c78c7d552f1c82a88c6083871fcd16538618
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837426"
---
# <a name="tutorial-server-broadcast-with-signalr-2"></a><span data-ttu-id="4f9ea-103">Kurz: Server vysílání s knihovnou SignalR 2</span><span class="sxs-lookup"><span data-stu-id="4f9ea-103">Tutorial: Server broadcast with SignalR 2</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="4f9ea-104">Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 pro zajištění všesměrového vysílání funkce serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-104">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide server broadcast functionality.</span></span> <span data-ttu-id="4f9ea-105">Server vysílání znamená, že je server spuštěn komunikace klientů.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-105">Server broadcast means that the server starts the communications sent to clients.</span></span>

<span data-ttu-id="4f9ea-106">Aplikace, kterou vytvoříte v tomto kurzu simuluje akciích Typický scénář pro vysílání funkce serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-106">The application that you'll create in this tutorial simulates a stock ticker, a typical scenario for server broadcast functionality.</span></span> <span data-ttu-id="4f9ea-107">Server pravidelně, náhodně aktualizuje ceny akcií a vysílat aktualizace na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-107">Periodically, the server randomly updates stock prices and broadcast the updates to all connected clients.</span></span> <span data-ttu-id="4f9ea-108">V prohlížeči, čísla a symboly **změnit** a **%** dynamicky měnit sloupce v reakci na oznámení ze serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-108">In the browser, the numbers and symbols in the **Change** and **%** columns dynamically change in response to notifications from the server.</span></span> <span data-ttu-id="4f9ea-109">Pokud otevřete další prohlížeče pro stejnou adresu URL, všechny zobrazit stejná data a stejné změny dat současně.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-109">If you open additional browsers to the same URL, they all show the same data and the same changes to the data simultaneously.</span></span>

![Vytvořte web](tutorial-server-broadcast-with-signalr/_static/image1.png)

<span data-ttu-id="4f9ea-111">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-111">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4f9ea-112">Vytvoření projektu</span><span class="sxs-lookup"><span data-stu-id="4f9ea-112">Create the project</span></span>
> * <span data-ttu-id="4f9ea-113">Nastavte si do kódu serveru</span><span class="sxs-lookup"><span data-stu-id="4f9ea-113">Set up the server code</span></span>
> * <span data-ttu-id="4f9ea-114">Zkoumání kódu serveru</span><span class="sxs-lookup"><span data-stu-id="4f9ea-114">Examine the server code</span></span>
> * <span data-ttu-id="4f9ea-115">Nastavit kód klienta</span><span class="sxs-lookup"><span data-stu-id="4f9ea-115">Set up the client code</span></span>
> * <span data-ttu-id="4f9ea-116">Zkoumání kódu klienta</span><span class="sxs-lookup"><span data-stu-id="4f9ea-116">Examine the client code</span></span>
> * <span data-ttu-id="4f9ea-117">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="4f9ea-117">Test the application</span></span>
> * <span data-ttu-id="4f9ea-118">Povolit protokolování</span><span class="sxs-lookup"><span data-stu-id="4f9ea-118">Enable logging</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4f9ea-119">Pokud nechcete, aby pro seznámení se základními kroky při vytváření aplikace, můžete nainstalovat balíček SignalR.Sample v novém projektu prázdná webová aplikace ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-119">If you don't want to work through the steps of building the application, you can install the SignalR.Sample package in a new Empty ASP.NET Web Application project.</span></span> <span data-ttu-id="4f9ea-120">Pokud balíček NuGet nainstalovat bez provedení kroků v tomto kurzu, postupujte podle pokynů v *readme.txt* souboru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-120">If you install the NuGet package without performing the steps in this tutorial, you must follow the instructions in the *readme.txt* file.</span></span> <span data-ttu-id="4f9ea-121">Ke spuštění balíčku je třeba přidat OWIN startup třídy, který volá `ConfigureSignalR` metoda ve nainstalovaným balíčkem.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-121">To run the package you need to add an OWIN startup class which calls the `ConfigureSignalR` method in the installed package.</span></span> <span data-ttu-id="4f9ea-122">Pokud nemůžete přidat třídu pro spuštění OWIN, dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-122">You will receive an error if you do not add the OWIN startup class.</span></span> <span data-ttu-id="4f9ea-123">Zobrazit [nainstalovat vzorovou StockTicker](#install-the-stockticker-sample) části tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-123">See the [Install the StockTicker sample](#install-the-stockticker-sample) section of this article.</span></span>


## <a name="prerequisites"></a><span data-ttu-id="4f9ea-124">Požadavky</span><span class="sxs-lookup"><span data-stu-id="4f9ea-124">Prerequisites</span></span>

 * <span data-ttu-id="4f9ea-125">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-125">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="4f9ea-126">Vytvoření projektu</span><span class="sxs-lookup"><span data-stu-id="4f9ea-126">Create the project</span></span>

<span data-ttu-id="4f9ea-127">Tato část ukazuje, jak vytvořit prázdná webová aplikace ASP.NET pomocí sady Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-127">This section shows how to use Visual Studio 2017 to create an empty ASP.NET Web Application.</span></span>

1. <span data-ttu-id="4f9ea-128">V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-128">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Vytvořte web](tutorial-server-broadcast-with-signalr/_static/image2.png)

1. <span data-ttu-id="4f9ea-130">V **nová webová aplikace ASP.NET - SignalR.StockTicker** okně, ponechejte tuto položku **prázdný** vybraný a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-130">In the **New ASP.NET Web Application - SignalR.StockTicker** window, leave **Empty** selected and select **OK**.</span></span>

## <a name="set-up-the-server-code"></a><span data-ttu-id="4f9ea-131">Nastavte si do kódu serveru</span><span class="sxs-lookup"><span data-stu-id="4f9ea-131">Set up the server code</span></span>

<span data-ttu-id="4f9ea-132">V této části můžete nastavit kód, který běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-132">In this section, you set up the code that runs on the server.</span></span>

### <a name="create-the-stock-class"></a><span data-ttu-id="4f9ea-133">Vytvořte třídu akcií</span><span class="sxs-lookup"><span data-stu-id="4f9ea-133">Create the Stock class</span></span>

<span data-ttu-id="4f9ea-134">Začněte vytvořením *akcie* model třídy, které budete používat k ukládání a přenášení informací o stejných akcií.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-134">You begin by creating the *Stock* model class that you'll use to store and transmit information about a stock.</span></span>

1. <span data-ttu-id="4f9ea-135">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-135">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="4f9ea-136">Název třídy *akcie* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-136">Name the class *Stock* and add it to the project.</span></span>

1. <span data-ttu-id="4f9ea-137">Nahraďte kód v *Stock.cs* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-137">Replace the code in the *Stock.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    <span data-ttu-id="4f9ea-138">Jsou dvě vlastnosti, které nastavíte při vytváření akcie `Symbol` (například MSFT pro Microsoft) a `Price`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-138">The two properties that you'll set when you create stocks are `Symbol` (for example, MSFT for Microsoft) and `Price`.</span></span> <span data-ttu-id="4f9ea-139">Ostatní vlastnosti závisí na kdy a jak nastavit `Price`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-139">The other properties depend on how and when you set `Price`.</span></span> <span data-ttu-id="4f9ea-140">Při prvním nastavíte `Price`, získá hodnotu rozšíří na `DayOpen`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-140">The first time you set `Price`, the value gets propagated to `DayOpen`.</span></span> <span data-ttu-id="4f9ea-141">Poté, pokud nastavíte `Price`, vypočítá aplikace `Change` a `PercentChange` hodnoty vlastností podle rozdíl mezi `Price` a `DayOpen`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-141">After that, when you set `Price`, the app calculates the `Change` and `PercentChange` property values based on the difference between `Price` and `DayOpen`.</span></span>

### <a name="create-the-stocktickerhub-and-stockticker-classes"></a><span data-ttu-id="4f9ea-142">Vytvoření tříd StockTickerHub a StockTicker</span><span class="sxs-lookup"><span data-stu-id="4f9ea-142">Create the StockTickerHub and StockTicker classes</span></span>

<span data-ttu-id="4f9ea-143">Rozhraní API pro rozbočovače SignalR budete používat ke zpracování server klient interakce.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-143">You'll use the SignalR Hub API to handle server-to-client interaction.</span></span> <span data-ttu-id="4f9ea-144">A `StockTickerHub` třídu odvozenou od `SignalRHub` třídy bude zpracovávat připojení a volání metody přijímají od klientů.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-144">A `StockTickerHub` class that derives from the `SignalRHub` class will handle receiving connections and method calls from clients.</span></span> <span data-ttu-id="4f9ea-145">Také je potřeba udržovat uložených dat a spustit `Timer` objektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-145">You also need to maintain stock data and run a `Timer` object.</span></span> <span data-ttu-id="4f9ea-146">`Timer` Objektu bude pravidelně aktivovat aktualizaci cen nezávisle na připojení klientů.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-146">The `Timer` object will periodically trigger price updates independent of client connections.</span></span> <span data-ttu-id="4f9ea-147">Tyto funkce nejde umístit `Hub` třídy, protože jsou přechodné rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-147">You can't put these functions in a `Hub` class, because Hubs are transient.</span></span> <span data-ttu-id="4f9ea-148">Vytvoří aplikaci `Hub` instance třídy pro každý úkol v rozbočovači, jako je připojení a volání od klienta k serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-148">The app creates a `Hub` class instance for each task on the hub, like connections and calls from the client to the server.</span></span> <span data-ttu-id="4f9ea-149">Mechanismus, který zajišťuje uložených dat, aktualizuje ceny a vysílá aktualizaci cen má ke spuštění v samostatné třídě.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-149">So the mechanism that keeps stock data, updates prices, and broadcasts the price updates has to run in a separate class.</span></span> <span data-ttu-id="4f9ea-150">Zobrazí název třídy `StockTicker`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-150">You'll name the class `StockTicker`.</span></span>

![Všesměrové vysílání z StockTicker](tutorial-server-broadcast-with-signalr/_static/image3.png)

<span data-ttu-id="4f9ea-152">Chcete jenom jednu instanci `StockTicker` má třída spustit na serveru, takže budete muset nastavit odkaz z každého `StockTickerHub` instanci typu singleton `StockTicker` instance.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-152">You only want one instance of the `StockTicker` class to run on the server, so you'll need to set up a reference from each `StockTickerHub` instance to the singleton `StockTicker` instance.</span></span> <span data-ttu-id="4f9ea-153">`StockTicker` Třída má vysílat klientům, protože má uložených dat a aktivuje aktualizace, ale `StockTicker` není `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-153">The `StockTicker` class has to broadcast to clients because it has the stock data and triggers updates, but `StockTicker` isn't a `Hub` class.</span></span> <span data-ttu-id="4f9ea-154">`StockTicker` Třída má k získání odkazu na objekt kontextu připojení rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-154">The `StockTicker` class has to get a reference to the SignalR Hub connection context object.</span></span> <span data-ttu-id="4f9ea-155">Pak můžete objekt context připojení SignalR na klienty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-155">It can then use the SignalR connection context object to broadcast to clients.</span></span>

#### <a name="create-stocktickerhubcs"></a><span data-ttu-id="4f9ea-156">Create StockTickerHub.cs</span><span class="sxs-lookup"><span data-stu-id="4f9ea-156">Create StockTickerHub.cs</span></span>

1. <span data-ttu-id="4f9ea-157">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-157">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="4f9ea-158">V **přidat novou položku - SignalR.StockTicker**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-158">In **Add New Item - SignalR.StockTicker**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="4f9ea-159">Název třídy *StockTickerHub* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-159">Name the class *StockTickerHub* and add it to the project.</span></span>

    <span data-ttu-id="4f9ea-160">Tento krok vytvoří *StockTickerHub.cs* souboru třídy.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-160">This step creates the *StockTickerHub.cs* class file.</span></span> <span data-ttu-id="4f9ea-161">Současně přidá sadu souborů skriptů a odkazy na sestavení podporující funkci SignalR k projektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-161">Simultaneously, it adds a set of script files and assembly references that supports SignalR to the project.</span></span>

1. <span data-ttu-id="4f9ea-162">Nahraďte kód v *StockTickerHub.cs* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-162">Replace the code in the *StockTickerHub.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

1. <span data-ttu-id="4f9ea-163">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-163">Save the file.</span></span>

<span data-ttu-id="4f9ea-164">Tato aplikace používá [centra](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) třída definuje metody, které klienty můžete volat na serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-164">The app uses the [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) class to define methods the clients can call on the server.</span></span> <span data-ttu-id="4f9ea-165">Definujete jednu metodu: `GetAllStocks()`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-165">You're defining one method: `GetAllStocks()`.</span></span> <span data-ttu-id="4f9ea-166">Když klient se nejprve připojí k serveru, bude volat tuto metodu za účelem získání seznamu všech zásob s jejich aktuální ceny.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-166">When a client initially connects to the server, it will call this method to get a list of all of the stocks with their current prices.</span></span> <span data-ttu-id="4f9ea-167">Můžete spustit synchronně a vrátí metodu `IEnumerable<Stock>` vzhledem k tomu, že ji vrací data z paměti.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-167">The method can run synchronously and return `IEnumerable<Stock>` because it's returning data from memory.</span></span>

<span data-ttu-id="4f9ea-168">Pokud má metodu k získání dat tímto způsobem něco, co by vyžadovalo čekání, jako je vyhledávání v databázi nebo volání webové služby, zadali byste `Task<IEnumerable<Stock>>` jako návratovou hodnotu k povolení asynchronního zpracování.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-168">If the method had to get the data by doing something that would involve waiting, like a database lookup or a web service call, you would specify `Task<IEnumerable<Stock>>` as the return value to enable asynchronous processing.</span></span> <span data-ttu-id="4f9ea-169">Další informace najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - kdy spustit asynchronně](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).</span><span class="sxs-lookup"><span data-stu-id="4f9ea-169">For more information, see [ASP.NET SignalR Hubs API Guide - Server - When to execute asynchronously](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).</span></span>

<span data-ttu-id="4f9ea-170">`HubName` Atribut určuje, jak aplikace bude odkazovat centra v kódu jazyka JavaScript na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-170">The `HubName` attribute specifies how the app will reference the Hub in JavaScript code on the client.</span></span> <span data-ttu-id="4f9ea-171">Výchozí jméno v klientovi, pokud nepoužijete tento atribut je verze camelCase název třídy, kterou v tomto případě by `stockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-171">The default name on the client if you don't use this attribute, is a camelCase version of the class name, which in this case would be `stockTickerHub`.</span></span>

<span data-ttu-id="4f9ea-172">Jak uvidíte později při vytváření `StockTicker` třídy, aplikace vytvoří instanci typu singleton této třídy v jeho statické `Instance` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-172">As you'll see later when you create the `StockTicker` class, the app creates a singleton instance of that class in its static `Instance` property.</span></span> <span data-ttu-id="4f9ea-173">Tuto instanci typu singleton `StockTicker` se v paměti bez ohledu na to, kolik klientů připojit nebo odpojit.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-173">That singleton instance of `StockTicker` is in memory no matter how many clients connect or disconnect.</span></span> <span data-ttu-id="4f9ea-174">Co je instance `GetAllStocks()` metoda používá k vrácení aktuální uložených informací.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-174">That instance is what the `GetAllStocks()` method uses to return current stock information.</span></span>

#### <a name="create-stocktickercs"></a><span data-ttu-id="4f9ea-175">Create StockTicker.cs</span><span class="sxs-lookup"><span data-stu-id="4f9ea-175">Create StockTicker.cs</span></span>

1. <span data-ttu-id="4f9ea-176">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-176">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="4f9ea-177">Název třídy *StockTicker* a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-177">Name the class *StockTicker* and add it to the project.</span></span>

1. <span data-ttu-id="4f9ea-178">Nahraďte kód v *StockTicker.cs* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-178">Replace the code in the *StockTicker.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

<span data-ttu-id="4f9ea-179">Vzhledem k tomu, že všechna vlákna budou používat stejnou instanci StockTicker kódu, StockTicker třídy musí být bezpečné pro vlákna.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-179">Since all threads will be running the same instance of StockTicker code, the StockTicker class has to be thread-safe.</span></span>

### <a name="examine-the-server-code"></a><span data-ttu-id="4f9ea-180">Zkoumání kódu serveru</span><span class="sxs-lookup"><span data-stu-id="4f9ea-180">Examine the server code</span></span>

<span data-ttu-id="4f9ea-181">Když si zblízka do kódu serveru, pomůže vám pochopit, jak aplikace funguje.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-181">If you examine the server code, it will help you understand how the app works.</span></span>

#### <a name="storing-the-singleton-instance-in-a-static-field"></a><span data-ttu-id="4f9ea-182">Instanci typu singleton ukládání do statických polí</span><span class="sxs-lookup"><span data-stu-id="4f9ea-182">Storing the singleton instance in a static field</span></span>

<span data-ttu-id="4f9ea-183">Kód inicializuje statické `_instance` pole, která zálohuje `Instance` vlastnost s instancí třídy.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-183">The code initializes the static `_instance` field that backs the `Instance` property with an instance of the class.</span></span> <span data-ttu-id="4f9ea-184">Protože konstruktor není soukromý, je pouze instance třídy, která může vytvářet aplikace.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-184">Because the constructor is private, it's the only instance of the class that the app can create.</span></span> <span data-ttu-id="4f9ea-185">Tato aplikace používá [opožděné inicializace](/dotnet/framework/performance/lazy-initialization) pro `_instance` pole.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-185">The app uses [Lazy initialization](/dotnet/framework/performance/lazy-initialization) for the `_instance` field.</span></span> <span data-ttu-id="4f9ea-186">Není z důvodů výkonu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-186">It's not for performance reasons.</span></span> <span data-ttu-id="4f9ea-187">Je zajistit, aby že vytvoření instance je bezpečná pro vlákno.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-187">It's to make sure the instance creation is thread-safe.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

<span data-ttu-id="4f9ea-188">Pokaždé, když klient připojí k serveru, novou instanci třídy StockTickerHub spuštěna v samostatném vlákně získá instanci typu singleton StockTicker z `StockTicker.Instance` statické vlastnosti, jako jste viděli v `StockTickerHub` třídy.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-188">Each time a client connects to the server, a new instance of the StockTickerHub class running in a separate thread gets the StockTicker singleton instance from the `StockTicker.Instance` static property, as you saw earlier in the `StockTickerHub` class.</span></span>

#### <a name="storing-stock-data-in-a-concurrentdictionary"></a><span data-ttu-id="4f9ea-189">Ukládání dat uložených v ConcurrentDictionary</span><span class="sxs-lookup"><span data-stu-id="4f9ea-189">Storing stock data in a ConcurrentDictionary</span></span>

<span data-ttu-id="4f9ea-190">Konstruktor inicializuje `_stocks` kolekce s ukázkovými daty zásob, a `GetAllStocks` vrátí zásob.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-190">The constructor initializes the `_stocks` collection with some sample stock data, and `GetAllStocks` returns the stocks.</span></span> <span data-ttu-id="4f9ea-191">Jak jste viděli již dříve, je vrácena této kolekce akcie `StockTickerHub.GetAllStocks`, což je metoda serveru v `Hub` třídu, která může volat klientů.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-191">As you saw earlier, this collection of stocks is returned by `StockTickerHub.GetAllStocks`, which is a server method in the `Hub` class that clients can call.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

<span data-ttu-id="4f9ea-192">Kolekce akcie je definován jako [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) typ pro bezpečný přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-192">The stocks collection is defined as a [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) type for thread safety.</span></span> <span data-ttu-id="4f9ea-193">Jako alternativu můžete použít [slovníku](https://msdn.microsoft.com/library/xfhwa508.aspx) objektu a explicitně zamknout slovníku, když provedete změny.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-193">As an alternative, you could use a [Dictionary](https://msdn.microsoft.com/library/xfhwa508.aspx) object and explicitly lock the dictionary when you make changes to it.</span></span>

<span data-ttu-id="4f9ea-194">V této ukázkové aplikaci je OK k ukládání dat aplikací v paměti a přijít o data, když aplikace uvolní `StockTicker` instance.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-194">For this sample application, it's OK to store application data in memory and to lose the data when the app disposes of the  `StockTicker` instance.</span></span> <span data-ttu-id="4f9ea-195">V reálné aplikaci když pracujete s back endovým datům úložiště, jako jsou databáze.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-195">In a real application, you would work with a back-end data store like a database.</span></span>

#### <a name="periodically-updating-stock-prices"></a><span data-ttu-id="4f9ea-196">Pravidelně aktualizuje cenami akcií</span><span class="sxs-lookup"><span data-stu-id="4f9ea-196">Periodically updating stock prices</span></span>

<span data-ttu-id="4f9ea-197">Konstruktor spuštění `Timer` objektu, která pravidelně volá metody, které aktualizují cenami akcií v náhodných intervalech.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-197">The constructor starts up a `Timer` object that periodically calls methods that update stock prices on a random basis.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

 <span data-ttu-id="4f9ea-198">`Timer` volání `UpdateStockPrices`, která předá vynulujte v parametru state.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-198">`Timer` calls `UpdateStockPrices`, which passes in null in the state parameter.</span></span> <span data-ttu-id="4f9ea-199">Před aktualizací ceny, aplikace převezme Zámek `_updateStockPricesLock` objektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-199">Before updating prices, the app takes a lock on the `_updateStockPricesLock` object.</span></span> <span data-ttu-id="4f9ea-200">Kód zkontroluje, jestli jiné vlákno se už aktualizuje ceny, a pak zavolá `TryUpdateStockPrice` na každé populace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-200">The code checks if another thread is already updating prices, and then it calls `TryUpdateStockPrice` on each stock in the list.</span></span> <span data-ttu-id="4f9ea-201">`TryUpdateStockPrice` Metoda rozhodne, zda se má změnit akcií a kolik ho změnit.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-201">The `TryUpdateStockPrice` method decides whether to change the stock price, and how much to change it.</span></span> <span data-ttu-id="4f9ea-202">Pokud se změní ceny akcie v aplikaci označuje jako `BroadcastStockPrice` vysílat minimální cenu akcie změny na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-202">If the stock price changes, the app calls `BroadcastStockPrice` to broadcast the stock price change to all connected clients.</span></span>

<span data-ttu-id="4f9ea-203">`_updatingStockPrices` Příznak určené [volatile](https://msdn.microsoft.com/library/x13ttww7.aspx) k zkontrolujte, zda je bezpečná pro vlákno.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-203">The `_updatingStockPrices` flag designated [volatile](https://msdn.microsoft.com/library/x13ttww7.aspx) to make sure it is thread-safe.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

<span data-ttu-id="4f9ea-204">V reálné aplikaci `TryUpdateStockPrice` by volání metody webové služby k vyhledání cena.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-204">In a real application, the `TryUpdateStockPrice` method would call a web service to look up the price.</span></span> <span data-ttu-id="4f9ea-205">V tomto kódu aplikace používá generátor náhodných čísel náhodně provádět změny.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-205">In this code, the app uses a random number generator to make changes randomly.</span></span>

#### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a><span data-ttu-id="4f9ea-206">Získávání kontextu SignalR tak, aby třída StockTicker můžete vysílat pro klienty</span><span class="sxs-lookup"><span data-stu-id="4f9ea-206">Getting the SignalR context so that the StockTicker class can broadcast to clients</span></span>

<span data-ttu-id="4f9ea-207">Protože změny cen pocházet tady `StockTicker` objekt, je objekt, který potřebuje volat `updateStockPrice` metodu na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-207">Because the price changes originate here in the `StockTicker` object, it's the object that needs to call an `updateStockPrice` method on all connected clients.</span></span> <span data-ttu-id="4f9ea-208">V `Hub` třídy, máte rozhraní API pro volání metody klienta, ale `StockTicker` není odvozen od `Hub` třídy a nemá odkaz na jakýkoli `Hub` objektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-208">In a `Hub` class, you have an API for calling client methods, but `StockTicker` doesn't derive from the `Hub` class and doesn't have a reference to any `Hub` object.</span></span> <span data-ttu-id="4f9ea-209">Na připojení klienti, `StockTicker` třída má pro získání instance kontextu SignalR pro `StockTickerHub` třídy, který budete používat pro volání metod na klientských počítačích.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-209">To broadcast to connected clients, the `StockTicker` class has to get the SignalR context instance for the `StockTickerHub` class and use that to call methods on clients.</span></span>

<span data-ttu-id="4f9ea-210">Kód získá odkaz na kontext SignalR při vytváření instance třídy singleton, předá, které odkazují konstruktor a konstruktor umístí ho `Clients` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-210">The code gets a reference to the SignalR context when it creates the singleton class instance, passes that reference to the constructor, and the constructor puts it in the `Clients` property.</span></span>

<span data-ttu-id="4f9ea-211">Existují dva důvody, proč chcete přijímat pouze jednou kontextu: získání kontextu je nákladné úlohy a jak ji získat, jakmile je, že aplikace zachová zamýšleném pořadí zpráv odeslaných do klientů.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-211">There are two reasons why you want to get the context only once: getting the context is an expensive task, and getting it once makes sure the app preserves the intended order of messages sent to the clients.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

<span data-ttu-id="4f9ea-212">Začínáme `Clients` vlastnost kontextu a umístit ho do `StockTickerClient` vlastnost umožňuje napsat kód pro volání metody klienta, která vypadá stejně, jako by tomu bylo v `Hub` třídy.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-212">Getting the `Clients` property of the context and putting it in the `StockTickerClient` property lets you write code to call client methods that looks the same as it would in a `Hub` class.</span></span> <span data-ttu-id="4f9ea-213">Například vysílat pro všechny klienty můžete napsat `Clients.All.updateStockPrice(stock)`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-213">For instance, to broadcast to all clients you can write `Clients.All.updateStockPrice(stock)`.</span></span>

<span data-ttu-id="4f9ea-214">`updateStockPrice` Metodu, která voláte v `BroadcastStockPrice` ještě neexistuje.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-214">The `updateStockPrice` method that you're calling in `BroadcastStockPrice` doesn't exist yet.</span></span> <span data-ttu-id="4f9ea-215">Přidáte je později při psaní kódu, který běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-215">You'll add it later when you write code that runs on the client.</span></span> <span data-ttu-id="4f9ea-216">Můžete se podívat do `updateStockPrice` zde protože `Clients.All` je dynamická, což znamená, že aplikace vyhodnotí výraz v době běhu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-216">You can refer to `updateStockPrice` here because `Clients.All` is dynamic, which means the app will evaluate the expression at runtime.</span></span> <span data-ttu-id="4f9ea-217">Při volání této metody se spustí, SignalR pošle názvu metody a hodnota parametru klienta, a pokud má klient metodu s názvem `updateStockPrice`, aplikace bude volat tuto metodu a předat hodnotu parametru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-217">When this method call executes, SignalR will send the method name and the parameter value to the client, and if the client has a method named `updateStockPrice`, the app will call that method and pass the parameter value to it.</span></span>

<span data-ttu-id="4f9ea-218">`Clients.All` znamená, že odesílání pro všechny klienty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-218">`Clients.All` means send to all clients.</span></span> <span data-ttu-id="4f9ea-219">Funkce SignalR poskytuje další možnosti k určení, které klienti nebo skupiny klientů k odeslání.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-219">SignalR gives you other options to specify which clients or groups of clients to send to.</span></span> <span data-ttu-id="4f9ea-220">Další informace najdete v tématu [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="4f9ea-220">For more information, see [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).</span></span>

### <a name="register-the-signalr-route"></a><span data-ttu-id="4f9ea-221">Zaregistrujte směrování funkce SignalR</span><span class="sxs-lookup"><span data-stu-id="4f9ea-221">Register the SignalR route</span></span>

<span data-ttu-id="4f9ea-222">Server musí znát adresu URL, která je pro zachycení a přístupem k systému SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-222">The server needs to know which URL to intercept and direct to SignalR.</span></span> <span data-ttu-id="4f9ea-223">K tomu, přidejte třídu OWIN při spuštění:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-223">To do that, add an OWIN startup class:</span></span>

1. <span data-ttu-id="4f9ea-224">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-224">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="4f9ea-225">V **přidat novou položku - SignalR.StockTicker** vyberte **nainstalováno** > **Visual C#**   >  **webové** a potom vyberte **třídy pro spuštění OWIN**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-225">In **Add New Item - SignalR.StockTicker** select **Installed** > **Visual C#** > **Web** and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="4f9ea-226">Název třídy *spuštění* a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-226">Name the class *Startup* and select **OK**.</span></span>

1. <span data-ttu-id="4f9ea-227">Nahraďte kód v *Startup.cs* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-227">Replace the default code in the *Startup.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

<span data-ttu-id="4f9ea-228">Nyní dokončili jste nastavování do kódu serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-228">You have now finished setting up the server code.</span></span> <span data-ttu-id="4f9ea-229">V další části které nastavíte klienta.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-229">In the next section, you'll set up the client.</span></span>

## <a name="set-up-the-client-code"></a><span data-ttu-id="4f9ea-230">Nastavit kód klienta</span><span class="sxs-lookup"><span data-stu-id="4f9ea-230">Set up the client code</span></span>

<span data-ttu-id="4f9ea-231">V této části můžete nastavit kód, který běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-231">In this section, you set up the code that runs on the client.</span></span>

### <a name="create-the-html-page-and-javascript-file"></a><span data-ttu-id="4f9ea-232">Vytvoření stránky HTML a JavaScript souboru</span><span class="sxs-lookup"><span data-stu-id="4f9ea-232">Create the HTML page and JavaScript file</span></span>

<span data-ttu-id="4f9ea-233">Na stránce HTML se zobrazí data a soubor jazyka JavaScript uspořádá data.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-233">The HTML page will display the data and the JavaScript file will organize the data.</span></span>

#### <a name="create-stocktickerhtml"></a><span data-ttu-id="4f9ea-234">Create StockTicker.html</span><span class="sxs-lookup"><span data-stu-id="4f9ea-234">Create StockTicker.html</span></span>

<span data-ttu-id="4f9ea-235">Nejprve přidejte klienta HTML.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-235">First, you'll add the HTML client.</span></span>

1. <span data-ttu-id="4f9ea-236">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **stránku HTML**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-236">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="4f9ea-237">Název souboru *StockTicker* a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-237">Name the file *StockTicker* and select **OK**.</span></span>

1. <span data-ttu-id="4f9ea-238">Nahraďte kód v *StockTicker.html* soubor s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-238">Replace the default code in the *StockTicker.html* file with this code:</span></span>

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html?highlight=40-43)]

    <span data-ttu-id="4f9ea-239">Kód HTML vytvoří tabulku se pět sloupců, řádek záhlaví a řádek dat s jedinou buňku, která překlenuje všechny pět sloupců.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-239">The HTML creates a table with five columns, a header row, and a data row with a single cell that spans all five columns.</span></span> <span data-ttu-id="4f9ea-240">Řádek dat se zobrazí "načítání..." okamžité při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-240">The data row shows "loading..." momentarily when the app starts.</span></span> <span data-ttu-id="4f9ea-241">Kód jazyka JavaScript se odebrat tento řádek a přidat v místě řádky s uložených dat načtených ze serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-241">JavaScript code will remove that row and add in its place rows with stock data retrieved from the server.</span></span>

    <span data-ttu-id="4f9ea-242">Zadejte značky skriptu:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-242">The script tags specify:</span></span>

    * <span data-ttu-id="4f9ea-243">Soubor skriptu jQuery.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-243">The jQuery script file.</span></span>

    * <span data-ttu-id="4f9ea-244">Soubor skriptu základní funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-244">The SignalR core script file.</span></span>

    * <span data-ttu-id="4f9ea-245">Soubor skriptu proxy služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-245">The SignalR proxies script file.</span></span>

    * <span data-ttu-id="4f9ea-246">StockTicker soubor skriptu, který později vytvoříte.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-246">A StockTicker script file that you'll create later.</span></span>

    <span data-ttu-id="4f9ea-247">Aplikace dynamicky generuje soubor skriptu proxy služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-247">The app dynamically generates the SignalR proxies script file.</span></span> <span data-ttu-id="4f9ea-248">Určuje adresu URL "/ signalr/centra" a definuje metody proxy pro metody u třídy rozbočovače, v takovém případě pro `StockTickerHub.GetAllStocks`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-248">It specifies the "/signalr/hubs" URL and defines proxy methods for the methods on the Hub class, in this case, for `StockTickerHub.GetAllStocks`.</span></span> <span data-ttu-id="4f9ea-249">Pokud dáváte přednost, tento soubor JavaScript vygenerovat ručně pomocí [nástroje SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/).</span><span class="sxs-lookup"><span data-stu-id="4f9ea-249">If you prefer, you can generate this JavaScript file manually by using [SignalR Utilities](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/).</span></span> <span data-ttu-id="4f9ea-250">Nezapomeňte zakázat vytváření dynamického souboru `MapHubs` volání metody.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-250">Don't forget to disable dynamic file creation in the `MapHubs` method call.</span></span>

1. <span data-ttu-id="4f9ea-251">V **Průzkumníka řešení**, rozbalte **skripty**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-251">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="4f9ea-252">Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-252">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="4f9ea-253">Správce balíčků bude nainstalujte novější verzi skriptů SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-253">The package manager will install a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="4f9ea-254">Aktualizujte odkazy na skript v bloku kódu tak, aby odpovídala verzi skriptů soubory v projektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-254">Update the script references in the code block to correspond to the versions of the script files in the project.</span></span>

1. <span data-ttu-id="4f9ea-255">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *StockTicker.html*a pak vyberte **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-255">In **Solution Explorer**, right-click *StockTicker.html*, and then select **Set as Start Page**.</span></span>

#### <a name="create-stocktickerjs"></a><span data-ttu-id="4f9ea-256">Create StockTicker.js</span><span class="sxs-lookup"><span data-stu-id="4f9ea-256">Create StockTicker.js</span></span>

<span data-ttu-id="4f9ea-257">Teď vytvořte soubor jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-257">Now create the JavaScript file.</span></span>

1. <span data-ttu-id="4f9ea-258">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **soubor JavaScript**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-258">In **Solution Explorer**, right-click the project and select **Add** > **JavaScript File**.</span></span>

1. <span data-ttu-id="4f9ea-259">Název souboru *StockTicker* a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-259">Name the file *StockTicker* and select **OK**.</span></span>

1. <span data-ttu-id="4f9ea-260">Přidejte tento kód *StockTicker.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-260">Add this code to the *StockTicker.js* file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

### <a name="examine-the-client-code"></a><span data-ttu-id="4f9ea-261">Zkoumání kódu klienta</span><span class="sxs-lookup"><span data-stu-id="4f9ea-261">Examine the client code</span></span>

<span data-ttu-id="4f9ea-262">Když si zblízka klientský kód, pomůže vám zjistěte, jak kód klienta komunikuje s kód serveru tak, aby aplikace funguje.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-262">If you examine the client code, it will help you learn how the client code interacts with the server code to make the app work.</span></span>

#### <a name="starting-the-connection"></a><span data-ttu-id="4f9ea-263">Spouští se připojení</span><span class="sxs-lookup"><span data-stu-id="4f9ea-263">Starting the connection</span></span>

<span data-ttu-id="4f9ea-264">`$.connection` odkazuje na proxy služby SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-264">`$.connection` refers to the SignalR proxies.</span></span> <span data-ttu-id="4f9ea-265">Kód získá odkaz na proxy serveru pro `StockTickerHub` třídy a umístí jej `ticker` proměnné.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-265">The code gets a reference to the proxy for the `StockTickerHub` class and puts it in the `ticker` variable.</span></span> <span data-ttu-id="4f9ea-266">Název proxy serveru je název, který nastavil `HubName` atribut:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-266">The proxy name is the name that was set by the `HubName` attribute:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

<span data-ttu-id="4f9ea-267">Po definování proměnné a funkce, poslední řádek kódu v souboru inicializuje připojení SignalR voláním funkce SignalR `start` funkce.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-267">After you define all the variables and functions, the last line of code in the file initializes the SignalR connection by calling the SignalR `start` function.</span></span> <span data-ttu-id="4f9ea-268">`start` Funkce provádí asynchronně a vrací [jQuery odloženo objekt](http://api.jquery.com/category/deferred-object/).</span><span class="sxs-lookup"><span data-stu-id="4f9ea-268">The `start` function executes asynchronously and returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/).</span></span> <span data-ttu-id="4f9ea-269">Můžete volat funkci Hotovo k určení funkce, která má být volána po dokončení asynchronní akce aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-269">You can call the done function to specify the function to call when the app finishes the asynchronous action.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

#### <a name="getting-all-the-stocks"></a><span data-ttu-id="4f9ea-270">Získání všech akcie.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-270">Getting all the stocks</span></span>

<span data-ttu-id="4f9ea-271">`init` Volání funkce `getAllStocks` funkce na serveru a používá informace, který server vrátí aktualizace základní tabulky.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-271">The `init` function calls the `getAllStocks` function on the server and uses the information that the server returns to update the stock table.</span></span> <span data-ttu-id="4f9ea-272">Všimněte si, že ve výchozím nastavení, budete muset použít camelCasing na straně klienta i v případě, že název metody rozlišuje jazyka Pascal – na serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-272">Notice that, by default, you have to use camelCasing on the client even though the method name is pascal-cased on the server.</span></span> <span data-ttu-id="4f9ea-273">Pravidlo camelCasing platí pouze pro metody, ne objekty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-273">The camelCasing rule only applies to methods, not objects.</span></span> <span data-ttu-id="4f9ea-274">Například můžete odkazovat na `stock.Symbol` a `stock.Price`, nikoli `stock.symbol` nebo `stock.price`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-274">For example, you refer to `stock.Symbol` and `stock.Price`, not `stock.symbol` or `stock.price`.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

<span data-ttu-id="4f9ea-275">V `init` metodu, aplikace vytvoří HTML pro řádek tabulky pro každý skladový objekt přijatou ze serveru voláním `formatStock` formát vlastnosti `stock` objekt a potom volala `supplant` nahradit zástupné symboly v `rowTemplate` proměnnou `stock` hodnot vlastností objektu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-275">In the `init` method, the app creates HTML for a table row for each stock object received from the server by calling `formatStock` to format properties of the `stock` object, and then by calling `supplant` to replace placeholders in the `rowTemplate` variable with the `stock` object property values.</span></span> <span data-ttu-id="4f9ea-276">Výsledného souboru HTML se pak připojí k základní tabulky.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-276">The resulting HTML is then appended to the stock table.</span></span>

> [!NOTE]
> <span data-ttu-id="4f9ea-277">Volání `init` ji jako `callback` funkci, která spustí po asynchronní `start` funkce dokončí.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-277">You call `init` by passing it in as a `callback` function that executes after the asynchronous `start` function finishes.</span></span> <span data-ttu-id="4f9ea-278">Pokud jste volali `init` jako samostatný příkaz jazyka JavaScript po volání `start`, funkce selže, protože by spustit okamžitě bez čekání na spuštění funkce dokončete navazování připojení.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-278">If you called `init` as a separate JavaScript statement after calling `start`, the function would fail because it would run immediately without waiting for the start function to finish establishing the connection.</span></span> <span data-ttu-id="4f9ea-279">V takovém případě `init` funkce by se pokusil zavolat `getAllStocks` funkce předtím, než aplikace naváže připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-279">In that case, the `init` function would try to call the `getAllStocks` function before the app establishes a server connection.</span></span>

#### <a name="getting-updated-stock-prices"></a><span data-ttu-id="4f9ea-280">Získání aktualizované ceny akcií</span><span class="sxs-lookup"><span data-stu-id="4f9ea-280">Getting updated stock prices</span></span>

<span data-ttu-id="4f9ea-281">Na serveru změní cena akcie společnosti, zavolá `updateStockPrice` na připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-281">When the server changes a stock's price, it calls the `updateStockPrice` on connected clients.</span></span> <span data-ttu-id="4f9ea-282">Aplikace přidá funkce do vlastnosti klienta `stockTicker` proxy, aby byla k dispozici k volání ze serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-282">The app adds the function to the client property of the `stockTicker` proxy to make it available to calls from the server.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

<span data-ttu-id="4f9ea-283">`updateStockPrice` Formáty funkce stejně jako v řádku skladový objekt přijatou ze serveru do tabulky `init` funkce.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-283">The `updateStockPrice` function formats a stock object received from the server into a table row the same way as in the `init` function.</span></span> <span data-ttu-id="4f9ea-284">Místo přidávání řádku do tabulky, najde skladě aktuální řádek v tabulce a tento řádek nahradí novým.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-284">Instead of appending the row to the table, it finds the stock's current row in the table and replaces that row with the new one.</span></span>

## <a name="test-the-application"></a><span data-ttu-id="4f9ea-285">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="4f9ea-285">Test the application</span></span>

<span data-ttu-id="4f9ea-286">Můžete testovat aplikaci, aby zajistili, že funguje.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-286">You can test the app to make sure it's working.</span></span> <span data-ttu-id="4f9ea-287">Zobrazí se vám zobrazit živé základní tabulku s cenami akcií kolísá všechna okna prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-287">You'll see all browser windows display the live stock table with stock prices fluctuating.</span></span>

1. <span data-ttu-id="4f9ea-288">Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte aplikaci v režimu ladění.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-288">In the toolbar, turn on **Script Debugging** and then select the play button to run the app in Debug mode.</span></span>

    ![Snímek obrazovky uživatele, zapnutí režimu ladění a vyberete play.](tutorial-server-broadcast-with-signalr/_static/image4.png)

    <span data-ttu-id="4f9ea-290">Otevře se okno prohlížeče, zobrazí **Live tabulky akcie**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-290">A browser window will open displaying the **Live Stock Table**.</span></span> <span data-ttu-id="4f9ea-291">Základní tabulky zpočátku ukazuje řádku "..." načítání"poté, po krátkou dobu, aplikace bude zobrazovat úvodní uložených dat a spusťte ceny akcií, chcete-li změnit.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-291">The stock table initially shows the "loading..." line, then, after a short time, the app shows the initial stock data, and then the stock prices start to change.</span></span>

1. <span data-ttu-id="4f9ea-292">Zkopírujte adresu URL z prohlížeče, otevřete dvou dalších prohlížečích a adresy URL vložte do řádku s adresou.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-292">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

    <span data-ttu-id="4f9ea-293">Počáteční uložených zobrazení je stejný jako první prohlížeče a změny probíhají souběžně.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-293">The initial stock display is the same as the first browser and changes happen simultaneously.</span></span>

1. <span data-ttu-id="4f9ea-294">Zavřete všechny prohlížeče, otevřete nový prohlížeč a přejděte na stejnou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-294">Close all browsers, open a new browser, and go to the same URL.</span></span>

    <span data-ttu-id="4f9ea-295">Objekt typu singleton StockTicker pokračování ke spuštění na serveru.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-295">The StockTicker singleton object continued to run in the server.</span></span> <span data-ttu-id="4f9ea-296">**Live tabulky akcie** odhalí populací zlepšovalo, chcete-li změnit.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-296">The **Live Stock Table** shows that the stocks have continued to change.</span></span> <span data-ttu-id="4f9ea-297">Nevidíte počáteční tabulka s nulou změnit hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-297">You don't see the initial table with zero change figures.</span></span>

1. <span data-ttu-id="4f9ea-298">Zavřete prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-298">Close the browser.</span></span>

## <a name="enable-logging"></a><span data-ttu-id="4f9ea-299">Povolit protokolování</span><span class="sxs-lookup"><span data-stu-id="4f9ea-299">Enable logging</span></span>

<span data-ttu-id="4f9ea-300">SignalR má vestavěné protokolování funkci, kterou můžete povolit na straně klienta na podporu při řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-300">SignalR has a built-in logging function that you can enable on the client to aid in troubleshooting.</span></span> <span data-ttu-id="4f9ea-301">V této části povolte protokolování a podívejte se na příklady, které ukazují, jak protokoly, že jste které z následujících metod přenosu pomocí funkce SignalR:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-301">In this section, you enable logging and see examples that show how logs tell you which of the following transport methods SignalR is using:</span></span>

* <span data-ttu-id="4f9ea-302">[Protokoly Websocket](http://en.wikipedia.org/wiki/WebSocket)podporovaný IIS 8 a aktuální prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-302">[WebSockets](http://en.wikipedia.org/wiki/WebSocket), supported by IIS 8 and current browsers.</span></span>

* <span data-ttu-id="4f9ea-303">[Události odeslané serverem](http://en.wikipedia.org/wiki/Server-sent_events)podporovaný prohlížečích než Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-303">[Server-sent events](http://en.wikipedia.org/wiki/Server-sent_events), supported by browsers other than Internet Explorer.</span></span>

* <span data-ttu-id="4f9ea-304">[Navždy rámec](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe)podporovaný aplikace Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-304">[Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), supported by Internet Explorer.</span></span>

* <span data-ttu-id="4f9ea-305">[Dlouhý interval dotazování AJAX](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling)podporovaná ve všech prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-305">[Ajax long polling](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), supported by all browsers.</span></span>

<span data-ttu-id="4f9ea-306">Pro jakékoli dané připojení SignalR vybere nejlepší metody přenosu, které podporují server i klient.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-306">For any given connection, SignalR chooses the best transport method that both the server and the client support.</span></span>

1. <span data-ttu-id="4f9ea-307">Open *StockTicker.js*.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-307">Open *StockTicker.js*.</span></span>

1. <span data-ttu-id="4f9ea-308">Přidejte následující zvýrazněný řádek kód pro povolení protokolování bezprostředně před kódem, který inicializuje připojení na konci souboru:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-308">Add this highlighted line of code to enable logging immediately before the code that initializes the connection at the end of the file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js?highlight=2)]

1. <span data-ttu-id="4f9ea-309">Stisknutím klávesy **F5** spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-309">Press **F5** to run the project.</span></span>

1. <span data-ttu-id="4f9ea-310">Otevřete okno vývojářských nástrojů v prohlížeči a vyberte v konzole naleznete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-310">Open your browser's developer tools window, and select the Console to see the logs.</span></span> <span data-ttu-id="4f9ea-311">Budete muset aktualizovat stránku, aby zobrazil protokoly vyjednávání přepravy pro nové připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-311">You might have to refresh the page to see the logs of SignalR negotiating the transport method for a new connection.</span></span>

    * <span data-ttu-id="4f9ea-312">Pokud používáte Internet Explorer 10 v systému Windows 8 (IIS 8), je způsob přepravy **objekty Websocket**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-312">If you're running Internet Explorer 10 on Windows 8 (IIS 8), the transport method is **WebSockets**.</span></span>

    * <span data-ttu-id="4f9ea-313">Pokud používáte Internet Explorer 10 na Windows 7 (službu IIS 7.5), je způsob přepravy **iframe**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-313">If you're running Internet Explorer 10 on Windows 7 (IIS 7.5), the transport method is **iframe**.</span></span>

    * <span data-ttu-id="4f9ea-314">Pokud používáte Firefox 19 ve Windows 8 (IIS 8), je způsob přepravy **objekty Websocket**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-314">If you're running Firefox 19 on Windows 8 (IIS 8), the transport method is **WebSockets**.</span></span>

        > [!TIP]
        > <span data-ttu-id="4f9ea-315">V aplikaci Firefox nainstalujte doplněk Firebug okno konzoly.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-315">In Firefox, install the Firebug add-in to get a Console window.</span></span>

    * <span data-ttu-id="4f9ea-316">Pokud používáte Firefox 19 ve Windows 7 (službu IIS 7.5), je způsob přepravy **server odeslal** události.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-316">If you're running Firefox 19 on Windows 7 (IIS 7.5), the transport method is **server-sent** events.</span></span>

## <a name="install-the-stockticker-sample"></a><span data-ttu-id="4f9ea-317">Nainstalovat vzorovou StockTicker</span><span class="sxs-lookup"><span data-stu-id="4f9ea-317">Install the StockTicker sample</span></span>

<span data-ttu-id="4f9ea-318">[Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) nainstaluje aplikaci StockTicker.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-318">The [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) installs the StockTicker application.</span></span> <span data-ttu-id="4f9ea-319">Balíček NuGet obsahuje víc funkcí než zjednodušenou verzi, kterou jste vytvořili úplně od začátku.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-319">The NuGet package includes more features than the simplified version that you created from scratch.</span></span> <span data-ttu-id="4f9ea-320">V této části kurzu nainstalujte balíček NuGet a projděte si nové funkce a kód, který je implementuje.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-320">In this section of the tutorial, you install the NuGet package and review the new features and the code that implements them.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4f9ea-321">Pokud instalace balíčku bez provedení předchozích kroků v tomto kurzu je nutné přidat do vašeho projektu třídy pro spuštění OWIN.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-321">If you install the package without performing the earlier steps of this tutorial, you must add an OWIN startup class to your project.</span></span> <span data-ttu-id="4f9ea-322">Tento soubor readme.txt pro balíček NuGet popisuje tento krok.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-322">This readme.txt file for the NuGet package explains this step.</span></span>

### <a name="install-the-signalrsample-nuget-package"></a><span data-ttu-id="4f9ea-323">Nainstalujte balíček SignalR.Sample NuGet</span><span class="sxs-lookup"><span data-stu-id="4f9ea-323">Install the SignalR.Sample NuGet package</span></span>

1. <span data-ttu-id="4f9ea-324">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-324">In **Solution Explorer**, right-click the project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4f9ea-325">V **Správce balíčků NuGet: SignalR.StockTicker**vyberte **Procházet**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-325">In **NuGet Package manager: SignalR.StockTicker**, select **Browse**.</span></span>

1. <span data-ttu-id="4f9ea-326">Z **zdroj balíčku**vyberte **nuget.org**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-326">From **Package source**, select **nuget.org**.</span></span>

1. <span data-ttu-id="4f9ea-327">Zadejte *SignalR.Sample* vyhledávacího pole a vyberte **Microsoft.AspNet.SignalR.Sample** > **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-327">Enter *SignalR.Sample* in the search box and select **Microsoft.AspNet.SignalR.Sample** > **Install**.</span></span>

1. <span data-ttu-id="4f9ea-328">V **Průzkumníka řešení**, rozbalte *SignalR.Sample* složky.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-328">In **Solution Explorer**, expand the *SignalR.Sample* folder.</span></span>

    <span data-ttu-id="4f9ea-329">Instaluje se balíček SignalR.Sample vytvořena složka a její obsah.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-329">Installing the SignalR.Sample package created the folder and its contents.</span></span>

1. <span data-ttu-id="4f9ea-330">V *SignalR.Sample* složky, klikněte pravým tlačítkem na *StockTicker.html*a pak vyberte **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-330">In the *SignalR.Sample* folder, right-click *StockTicker.html*, and then select **Set As Start Page**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="4f9ea-331">Instalace SignalR.Sample NuGet balíčku může změnit verzi jQuery, který máte v vaše *skripty* složky.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-331">Installing The SignalR.Sample NuGet package might change the version of jQuery that you have in your *Scripts* folder.</span></span> <span data-ttu-id="4f9ea-332">Nové *StockTicker.html* soubor, který se nainstaluje balíček *SignalR.Sample* složkou je synchronizovaný s verzí jQuery, který nainstaluje balíček, ale pokud budete chtít spustit váš původním *StockTicker.html* soubor znovu, možná budete muset nejprve aktualizovat odkaz jQuery ve značce skriptu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-332">The new *StockTicker.html* file that the package installs in the *SignalR.Sample* folder will be in sync with the jQuery version that the package installs, but if you want to run your original *StockTicker.html* file again, you might have to update the jQuery reference in the script tag first.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="4f9ea-333">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="4f9ea-333">Run the application</span></span>

 <span data-ttu-id="4f9ea-334">V tabulce, které jste viděli v první aplikace měla užitečných funkcí.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-334">The table that you saw in the first app had useful features.</span></span> <span data-ttu-id="4f9ea-335">Úplné akciích aplikace zobrazuje nové funkce: vodorovně posuvné okno, které zobrazuje uložených dat a zásob, které Změna barvy tak, jak zvýšit a spadají.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-335">The full stock ticker application shows new features: a horizontally scrolling window that shows the stock data and stocks that change color as they rise and fall.</span></span>

1. <span data-ttu-id="4f9ea-336">Stisknutím klávesy **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-336">Press **F5** to run the app.</span></span>

     <span data-ttu-id="4f9ea-337">Při prvním spuštění aplikace, "trhu" je "uzavřený" a uvidíte statickou tabulku a akcie okno, které není posouvání.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-337">When you run the app for the first time, the "market" is "closed" and you see a static table and a ticker window that isn't scrolling.</span></span>

1. <span data-ttu-id="4f9ea-338">Vyberte **otevřeném trhu**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-338">Select **Open Market**.</span></span>

    ![Snímek obrazovky živé akcie.](tutorial-server-broadcast-with-signalr/_static/image5.png)

    * <span data-ttu-id="4f9ea-340">**Live akcie akcie** pole začne posouvat vodorovně a spustí se server pravidelně vysílat změny ceny akcie v náhodných intervalech.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-340">The **Live Stock Ticker** box starts to scroll horizontally, and the server starts to periodically broadcast stock price changes on a random basis.</span></span>

    * <span data-ttu-id="4f9ea-341">Pokaždé, když změny cen akcií, aktualizuje aplikace i **Live tabulky akcie** a **Live akcie akcie**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-341">Each time a stock price changes, the app updates both the **Live Stock Table** and the **Live Stock Ticker**.</span></span>

    * <span data-ttu-id="4f9ea-342">Změna ceny akcie společnosti po kladná, aplikace bude zobrazovat akcie zeleným pozadím.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-342">When a stock's price change is positive, the app shows the stock with a green background.</span></span>

    * <span data-ttu-id="4f9ea-343">Při změně je záporný, aplikace bude zobrazovat akcie s červeným pozadím.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-343">When the change is negative, the app shows the stock with a red background.</span></span>

1. <span data-ttu-id="4f9ea-344">Vyberte **zavřete trhu**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-344">Select **Close Market**.</span></span>

    * <span data-ttu-id="4f9ea-345">V tabulce aktualizuje stop.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-345">The table updates stop.</span></span>

    * <span data-ttu-id="4f9ea-346">Ukazateli zastaví posouvání.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-346">The ticker stops scrolling.</span></span>

1. <span data-ttu-id="4f9ea-347">Vyberte **resetování**.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-347">Select **Reset**.</span></span>

    * <span data-ttu-id="4f9ea-348">Všechny uložených dat se vynuluje.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-348">All stock data is reset.</span></span>

    * <span data-ttu-id="4f9ea-349">Aplikace obnovení původního stavu před změnami ceny začít.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-349">The app restores the initial state before price changes started.</span></span>

1. <span data-ttu-id="4f9ea-350">Zkopírujte adresu URL z prohlížeče, otevřete dvou dalších prohlížečích a adresy URL vložte do řádku s adresou.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-350">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="4f9ea-351">Můžete zobrazit stejná data dynamicky aktualizovat ve stejnou dobu v každým prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-351">You see the same data dynamically updated at the same time in each browser.</span></span>

1. <span data-ttu-id="4f9ea-352">Když vyberete některý z ovládacích prvků, všechny prohlížeče odpoví stejným způsobem jako ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-352">When you select any of the controls, all browsers respond the same way at the same time.</span></span>

### <a name="live-stock-ticker-display"></a><span data-ttu-id="4f9ea-353">Živé akcie časovače, může zobrazení</span><span class="sxs-lookup"><span data-stu-id="4f9ea-353">Live Stock Ticker display</span></span>

<span data-ttu-id="4f9ea-354">**Live akcie akcie** zobrazení je neuspořádaný seznam v `<div>` element formátované jako jeden řádek stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-354">The **Live Stock Ticker** display is an unordered list in a `<div>` element formatted into a single line by CSS styles.</span></span> <span data-ttu-id="4f9ea-355">Aplikace inicializuje a aktualizuje ukazateli stejným způsobem jako v tabulce: tak, že nahradíte zástupný text v `<li>` řetězec šablony a dynamicky přidat `<li>` prvků, které mají `<ul>` elementu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-355">The app initializes and updates the ticker the same way as the table: by replacing placeholders in an `<li>` template string and dynamically adding the `<li>` elements to the `<ul>` element.</span></span> <span data-ttu-id="4f9ea-356">Tato aplikace obsahuje posouvání pomocí jQuery `animate` funkce lišit v rozpětí vlevo neuspořádaný seznam v rámci `<div>`.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-356">The app includes  scrolling by using the jQuery `animate` function to vary the margin-left of the unordered list within the `<div>`.</span></span>

#### <a name="signalrsample-stocktickerhtml"></a><span data-ttu-id="4f9ea-357">SignalR.Sample StockTicker.html</span><span class="sxs-lookup"><span data-stu-id="4f9ea-357">SignalR.Sample StockTicker.html</span></span>

<span data-ttu-id="4f9ea-358">Akciích kód HTML:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-358">The stock ticker HTML code:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

#### <a name="signalrsample-stocktickercss"></a><span data-ttu-id="4f9ea-359">SignalR.Sample StockTicker.css</span><span class="sxs-lookup"><span data-stu-id="4f9ea-359">SignalR.Sample StockTicker.css</span></span>

<span data-ttu-id="4f9ea-360">Akciích kód šablony stylů CSS:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-360">The stock ticker CSS code:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

#### <a name="signalrsample-signalrstocktickerjs"></a><span data-ttu-id="4f9ea-361">SignalR.Sample SignalR.StockTicker.js</span><span class="sxs-lookup"><span data-stu-id="4f9ea-361">SignalR.Sample SignalR.StockTicker.js</span></span>

<span data-ttu-id="4f9ea-362">Posuňte se kód jazyka jQuery, která umožňuje:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-362">The jQuery code that makes it scroll:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a><span data-ttu-id="4f9ea-363">Další metody na serveru, na které můžete volat klienta</span><span class="sxs-lookup"><span data-stu-id="4f9ea-363">Additional methods on the server that the client can call</span></span>

<span data-ttu-id="4f9ea-364">Ke zvýšení flexibility aplikace, existují další metody, které může aplikace provést volání metody.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-364">To add flexibility to the app, there are additional methods the app can call.</span></span>

#### <a name="signalrsample-stocktickerhubcs"></a><span data-ttu-id="4f9ea-365">SignalR.Sample StockTickerHub.cs</span><span class="sxs-lookup"><span data-stu-id="4f9ea-365">SignalR.Sample StockTickerHub.cs</span></span>

<span data-ttu-id="4f9ea-366">`StockTickerHub` Třída definuje čtyři další metody, které můžete volat klienta:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-366">The `StockTickerHub` class defines four additional methods that the client can call:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

<span data-ttu-id="4f9ea-367">Volání aplikace `OpenMarket`, `CloseMarket`, a `Reset` v reakci na tlačítka v horní části stránky.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-367">The app calls `OpenMarket`, `CloseMarket`, and `Reset` in response to the buttons at the top of the page.</span></span> <span data-ttu-id="4f9ea-368">Vysvětlují vzor jednoho klienta, spouštění změnu stavu okamžitě rozšíří na všechny klienty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-368">They demonstrate the pattern of one client triggering a change in state immediately propagated to all clients.</span></span> <span data-ttu-id="4f9ea-369">Každá z těchto metod volá metodu v `StockTicker` třídu, která způsobí, že změnu stavu na trhu a potom vysílá nový stav.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-369">Each of these methods calls a method in the `StockTicker` class that causes the market state change and then broadcasts the new state.</span></span>

#### <a name="signalrsample-stocktickercs"></a><span data-ttu-id="4f9ea-370">SignalR.Sample StockTicker.cs</span><span class="sxs-lookup"><span data-stu-id="4f9ea-370">SignalR.Sample StockTicker.cs</span></span>

<span data-ttu-id="4f9ea-371">V `StockTicker` třídy aplikace udržuje stav na trh `MarketState` vlastnosti, která vrací `MarketState` hodnota výčtu:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-371">In the `StockTicker` class, the app maintains the state of the market with a `MarketState` property that returns a `MarketState` enum value:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

<span data-ttu-id="4f9ea-372">Každá z metod, které ke změně stavu na trhu provést uvnitř bloku zámek protože `StockTicker` třída musí být bezpečné pro vlákna:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-372">Each of the methods that change the market state do so inside a lock block because the `StockTicker` class has to be thread-safe:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

<span data-ttu-id="4f9ea-373">Abyste měli jistotu, tento kód je bezpečná pro vlákno, `_marketState` pole, která zálohuje `MarketState` vlastnosti určené `volatile`:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-373">To make sure this code is thread-safe, the `_marketState` field that backs the `MarketState` property designated `volatile`:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

<span data-ttu-id="4f9ea-374">`BroadcastMarketStateChange` a `BroadcastMarketReset` metody jsou podobné BroadcastStockPrice metody, které jste už viděli, s výjimkou volání různých metod definovaných v klientském počítači:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-374">The `BroadcastMarketStateChange` and `BroadcastMarketReset` methods are similar to the BroadcastStockPrice method that you already saw, except they call different methods defined at the client:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a><span data-ttu-id="4f9ea-375">Další funkce na straně klienta, která může volat na serveru</span><span class="sxs-lookup"><span data-stu-id="4f9ea-375">Additional functions on the client that the server can call</span></span>

<span data-ttu-id="4f9ea-376">`updateStockPrice` Funkce nyní zpracovává tabulky a zobrazení akcie a používá `jQuery.Color` na flash červenou a zelenou barvy.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-376">The `updateStockPrice` function now handles both the table and the ticker display, and it uses `jQuery.Color` to flash red and green colors.</span></span>

<span data-ttu-id="4f9ea-377">Nové funkce v *SignalR.StockTicker.js* povolení a zakázání tlačítka na základě stavu na trhu.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-377">New functions in *SignalR.StockTicker.js* enable and disable the buttons based on market state.</span></span> <span data-ttu-id="4f9ea-378">Jsou také zastavit nebo spustit **Live akcie akcie** vodorovného posouvání.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-378">They also stop or start the **Live Stock Ticker** horizontal scrolling.</span></span> <span data-ttu-id="4f9ea-379">Protože mnoho funkcí se přidávají do `ticker.client`, tato aplikace používá [jQuery rozšířit funkce](http://api.jquery.com/jQuery.extend/) je přidat.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-379">Since many functions are being added to `ticker.client`, the app uses the [jQuery extend function](http://api.jquery.com/jQuery.extend/) to add them.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a><span data-ttu-id="4f9ea-380">Instalace dalších klientských po navázání připojení</span><span class="sxs-lookup"><span data-stu-id="4f9ea-380">Additional client setup after establishing the connection</span></span>

<span data-ttu-id="4f9ea-381">Jakmile klient naváže připojení, má provést další úkony:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-381">After the client establishes the connection, it has some additional work to do:</span></span>

* <span data-ttu-id="4f9ea-382">Zjistěte, jestli je na trhu otevřeno nebo zavřeno volat odpovídající `marketOpened` nebo `marketClosed` funkce.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-382">Find out if the market is open or closed to call the appropriate `marketOpened` or `marketClosed` function.</span></span>

* <span data-ttu-id="4f9ea-383">Připojte server volání metod pro tlačítka.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-383">Attach the server method calls to the buttons.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

<span data-ttu-id="4f9ea-384">Metody serveru nejsou svázanou tlačítka až po aplikace vytváří připojení.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-384">The server methods aren't wired up to the buttons until after the app establishes the connection.</span></span> <span data-ttu-id="4f9ea-385">Jedná se proto kód nelze volat metody serveru, než jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-385">It's so the code can't call the server methods before they're available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f9ea-386">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4f9ea-386">Additional resources</span></span>

<span data-ttu-id="4f9ea-387">V tomto kurzu jste zjistili, jak programovat aplikace SignalR, který vysílá zprávy ze serveru na všechny připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-387">In this tutorial you've learned how to program a SignalR application that broadcasts messages from the server to all connected clients.</span></span> <span data-ttu-id="4f9ea-388">Nyní můžete vysílání zpráv v pravidelných intervalech a v reakci na oznámení z jakéhokoli klienta.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-388">Now you can broadcast messages on a periodic basis and in response to notifications from any client.</span></span> <span data-ttu-id="4f9ea-389">Koncept instanci typu singleton vícevláknové můžete použít k údržbě stavu serveru v režimu online her scénáře více hráčů.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-389">You can use the concept of multi-threaded singleton instance to maintain server state in multi-player online game scenarios.</span></span> <span data-ttu-id="4f9ea-390">Příklad najdete v tématu [ShootR hru založenou na oblíbeném SignalR](https://github.com/NTaylorMullen/ShootR).</span><span class="sxs-lookup"><span data-stu-id="4f9ea-390">For an example, see [the ShootR game based on SignalR](https://github.com/NTaylorMullen/ShootR).</span></span>

<span data-ttu-id="4f9ea-391">Kurzy, které ukazují scénáře komunikace peer-to-peer, naleznete v tématu [Začínáme s knihovnou SignalR](introduction-to-signalr.md) a [aktualizace v reálném čase s knihovnou SignalR](tutorial-high-frequency-realtime-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="4f9ea-391">For tutorials that show peer-to-peer communication scenarios, see [Getting Started with SignalR](introduction-to-signalr.md) and [Real-Time Updating with SignalR](tutorial-high-frequency-realtime-with-signalr.md).</span></span>

<span data-ttu-id="4f9ea-392">Další informace o funkci SignalR naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-392">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="4f9ea-393">Funkce SignalR technologie ASP.NET</span><span class="sxs-lookup"><span data-stu-id="4f9ea-393">ASP.NET SignalR</span></span>](../../index.md)
* [<span data-ttu-id="4f9ea-394">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="4f9ea-394">SignalR Project</span></span>](http://signalr.net/)
* [<span data-ttu-id="4f9ea-395">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="4f9ea-395">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)
* [<span data-ttu-id="4f9ea-396">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="4f9ea-396">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="4f9ea-397">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4f9ea-397">Next steps</span></span>

<span data-ttu-id="4f9ea-398">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="4f9ea-398">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4f9ea-399">Vytvoření projektu</span><span class="sxs-lookup"><span data-stu-id="4f9ea-399">Created the project</span></span>
> * <span data-ttu-id="4f9ea-400">Nastavte si do kódu serveru</span><span class="sxs-lookup"><span data-stu-id="4f9ea-400">Set up the server code</span></span>
> * <span data-ttu-id="4f9ea-401">Prozkoumat kód serveru</span><span class="sxs-lookup"><span data-stu-id="4f9ea-401">Examined the server code</span></span>
> * <span data-ttu-id="4f9ea-402">Nastavit kód klienta</span><span class="sxs-lookup"><span data-stu-id="4f9ea-402">Set up the client code</span></span>
> * <span data-ttu-id="4f9ea-403">Prozkoumat kód klienta</span><span class="sxs-lookup"><span data-stu-id="4f9ea-403">Examined the client code</span></span>
> * <span data-ttu-id="4f9ea-404">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="4f9ea-404">Tested the application</span></span>
> * <span data-ttu-id="4f9ea-405">Povolené protokolování</span><span class="sxs-lookup"><span data-stu-id="4f9ea-405">Enabled logging</span></span>

<span data-ttu-id="4f9ea-406">Přejděte k dalším článku se naučíte, jak vytvořit aplikaci webu v reálném čase, která používá ASP.NET SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="4f9ea-406">Advance to the next article to learn how to create a real-time web application that uses ASP.NET SignalR 2.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="4f9ea-407">Vytvořte aplikaci webu v reálném čase s knihovnou SignalR</span><span class="sxs-lookup"><span data-stu-id="4f9ea-407">Create real-time web app with SignalR</span></span>](real-time-web-applications-with-signalr.md)
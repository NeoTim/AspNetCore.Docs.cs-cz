---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC'
author: rick-anderson
description: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 02/4/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 686397cd25248ce7b37e505c7129a3b56d4ada1b
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833758"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core-mvc"></a><span data-ttu-id="5f597-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5f597-103">Tutorial: Create a web API with ASP.NET Core MVC</span></span>

<span data-ttu-id="5f597-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="5f597-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="5f597-105">V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f597-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="5f597-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="5f597-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5f597-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-107">Create a web API project.</span></span>
> * <span data-ttu-id="5f597-108">Přidejte třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="5f597-108">Add a model class.</span></span>
> * <span data-ttu-id="5f597-109">Vytvořte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5f597-109">Create the database context.</span></span>
> * <span data-ttu-id="5f597-110">Zaregistrujte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5f597-110">Register the database context.</span></span>
> * <span data-ttu-id="5f597-111">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="5f597-111">Add a controller.</span></span>
> * <span data-ttu-id="5f597-112">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="5f597-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="5f597-113">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="5f597-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="5f597-114">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5f597-114">Specify return values.</span></span>
> * <span data-ttu-id="5f597-115">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="5f597-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="5f597-116">Volání webového rozhraní API s jQuery.</span><span class="sxs-lookup"><span data-stu-id="5f597-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="5f597-117">Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="5f597-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="5f597-118">Přehled</span><span class="sxs-lookup"><span data-stu-id="5f597-118">Overview</span></span>

<span data-ttu-id="5f597-119">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="5f597-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="5f597-120">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="5f597-120">API</span></span> | <span data-ttu-id="5f597-121">Popis</span><span class="sxs-lookup"><span data-stu-id="5f597-121">Description</span></span> | <span data-ttu-id="5f597-122">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="5f597-122">Request body</span></span> | <span data-ttu-id="5f597-123">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="5f597-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="5f597-124">ZÍSKAT /api/todo</span><span class="sxs-lookup"><span data-stu-id="5f597-124">GET /api/todo</span></span> | <span data-ttu-id="5f597-125">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-125">Get all to-do items</span></span> | <span data-ttu-id="5f597-126">Žádné</span><span class="sxs-lookup"><span data-stu-id="5f597-126">None</span></span> | <span data-ttu-id="5f597-127">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-127">Array of to-do items</span></span>|
|<span data-ttu-id="5f597-128">ZÍSKAT/webové rozhraní API/todo / {id}</span><span class="sxs-lookup"><span data-stu-id="5f597-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="5f597-129">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="5f597-129">Get an item by ID</span></span> | <span data-ttu-id="5f597-130">Žádná</span><span class="sxs-lookup"><span data-stu-id="5f597-130">None</span></span> | <span data-ttu-id="5f597-131">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-131">To-do item</span></span>|
|<span data-ttu-id="5f597-132">Publikovat/api/todo</span><span class="sxs-lookup"><span data-stu-id="5f597-132">POST /api/todo</span></span> | <span data-ttu-id="5f597-133">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="5f597-133">Add a new item</span></span> | <span data-ttu-id="5f597-134">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-134">To-do item</span></span> | <span data-ttu-id="5f597-135">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-135">To-do item</span></span> |
|<span data-ttu-id="5f597-136">Vložení/webové rozhraní API/todo / {id}</span><span class="sxs-lookup"><span data-stu-id="5f597-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="5f597-137">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5f597-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="5f597-138">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-138">To-do item</span></span> | <span data-ttu-id="5f597-139">Žádná</span><span class="sxs-lookup"><span data-stu-id="5f597-139">None</span></span> |
|<span data-ttu-id="5f597-140">ODSTRANIT/webové rozhraní API/todo / {id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5f597-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="5f597-141">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5f597-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="5f597-142">Žádné</span><span class="sxs-lookup"><span data-stu-id="5f597-142">None</span></span> | <span data-ttu-id="5f597-143">Žádná</span><span class="sxs-lookup"><span data-stu-id="5f597-143">None</span></span>|

<span data-ttu-id="5f597-144">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="5f597-144">The following diagram shows the design of the app.</span></span>

![Klient je reprezentována pole na levé straně a odešle žádost a obdrží odpověď od aplikace vykreslen na pravé straně pole.](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="5f597-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="5f597-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f597-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f597-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5f597-151">Z **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="5f597-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5f597-152">Vyberte **webové aplikace ASP.NET Core** šablony.</span><span class="sxs-lookup"><span data-stu-id="5f597-152">Select the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="5f597-153">Pojmenujte projekt *TodoApi* a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="5f597-153">Name the project *TodoApi* and click **OK**.</span></span>
* <span data-ttu-id="5f597-154">V **nové webové aplikace ASP.NET Core – TodoApi** dialogového okna, vyberte verzi technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f597-154">In the **New ASP.NET Core Web Application - TodoApi** dialog, choose the ASP.NET Core version.</span></span> <span data-ttu-id="5f597-155">Vyberte **API** šablonu a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="5f597-155">Select the **API** template and click **OK**.</span></span> <span data-ttu-id="5f597-156">Proveďte **není** vyberte **povolit podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="5f597-156">Do **not** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5f597-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f597-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5f597-159">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5f597-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5f597-160">Změňte adresář (`cd`) do složky, která bude obsahovat složky projektu.</span><span class="sxs-lookup"><span data-stu-id="5f597-160">Change directories (`cd`) to the folder which will contain the project folder.</span></span>
* <span data-ttu-id="5f597-161">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5f597-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="5f597-162">Tyto příkazy vytvořte nový projekt webového rozhraní API a otevřete novou instanci sady Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="5f597-162">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="5f597-163">Když dialogové okno požádá, pokud chcete do projektu přidejte požadované prostředky, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="5f597-163">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5f597-164">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5f597-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5f597-165">Vyberte **souboru** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="5f597-165">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="5f597-167">Vyberte **aplikace .NET Core** > **webového rozhraní API ASP.NET Core** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="5f597-167">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="5f597-169">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="5f597-169">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="5f597-170">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5f597-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="5f597-172">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="5f597-172">Test the API</span></span>

<span data-ttu-id="5f597-173">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-173">The project template creates a `values` API.</span></span> <span data-ttu-id="5f597-174">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5f597-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f597-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f597-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f597-176">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5f597-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5f597-177">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="5f597-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="5f597-178">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="5f597-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="5f597-179">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="5f597-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5f597-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f597-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5f597-181">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5f597-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5f597-182">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="5f597-182">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5f597-183">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5f597-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5f597-184">Vyberte **spustit** > **spustit s ladění** aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="5f597-184">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="5f597-185">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="5f597-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="5f597-186">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="5f597-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="5f597-187">Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="5f597-187">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="5f597-188">Vrátí následující JSON:</span><span class="sxs-lookup"><span data-stu-id="5f597-188">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="5f597-189">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="5f597-189">Add a model class</span></span>

<span data-ttu-id="5f597-190">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="5f597-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="5f597-191">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="5f597-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f597-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f597-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5f597-193">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="5f597-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="5f597-194">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5f597-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5f597-195">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="5f597-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="5f597-196">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="5f597-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5f597-197">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="5f597-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="5f597-198">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5f597-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5f597-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f597-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5f597-200">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="5f597-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="5f597-201">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5f597-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5f597-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5f597-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5f597-203">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="5f597-203">Right-click the project.</span></span> <span data-ttu-id="5f597-204">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5f597-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5f597-205">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="5f597-205">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="5f597-207">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **nový soubor** > **Obecné**  >  **Prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="5f597-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="5f597-208">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="5f597-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="5f597-209">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5f597-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="5f597-210">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="5f597-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="5f597-211">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="5f597-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="5f597-212">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="5f597-212">Add a database context</span></span>

<span data-ttu-id="5f597-213">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="5f597-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="5f597-214">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="5f597-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f597-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f597-215">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5f597-216">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="5f597-216">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5f597-217">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="5f597-217">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5f597-218">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5f597-218">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5f597-219">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="5f597-219">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="5f597-220">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5f597-220">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="5f597-221">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="5f597-221">Register the database context</span></span>

<span data-ttu-id="5f597-222">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5f597-222">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5f597-223">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="5f597-223">The container provides the service to controllers.</span></span>

<span data-ttu-id="5f597-224">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="5f597-224">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="5f597-225">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="5f597-225">The preceding code:</span></span>

* <span data-ttu-id="5f597-226">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="5f597-226">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="5f597-227">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5f597-227">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="5f597-228">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="5f597-228">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="5f597-229">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="5f597-229">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5f597-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f597-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5f597-231">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="5f597-231">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="5f597-232">Vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="5f597-232">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="5f597-233">V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="5f597-233">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="5f597-234">Název třídy *TodoController*a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="5f597-234">Name the class *TodoController*, and select **Add**.</span></span>

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5f597-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5f597-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5f597-237">V *řadiče* složku, vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="5f597-237">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="5f597-238">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5f597-238">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="5f597-239">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="5f597-239">The preceding code:</span></span>

* <span data-ttu-id="5f597-240">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="5f597-240">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="5f597-241">Třída se upraví [ `[ApiController]` ](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="5f597-241">Decorates the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="5f597-242">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-242">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="5f597-243">Informace o konkrétní chování, které umožňuje atribut najdete v tématu [Poznámka atributem objektu ApiController](xref:web-api/index#annotation-with-apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="5f597-243">For information about specific behaviors that the attribute enables, see [Annotation with ApiController attribute](xref:web-api/index#annotation-with-apicontroller-attribute).</span></span>
* <span data-ttu-id="5f597-244">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="5f597-244">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="5f597-245">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="5f597-245">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="5f597-246">Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný.</span><span class="sxs-lookup"><span data-stu-id="5f597-246">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="5f597-247">Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="5f597-247">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="5f597-248">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-248">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="5f597-249">Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.</span><span class="sxs-lookup"><span data-stu-id="5f597-249">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="5f597-250">Přidejte metody Get</span><span class="sxs-lookup"><span data-stu-id="5f597-250">Add Get methods</span></span>

<span data-ttu-id="5f597-251">Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:</span><span class="sxs-lookup"><span data-stu-id="5f597-251">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="5f597-252">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="5f597-252">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="5f597-253">Testování aplikace pomocí volání dva koncové body v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5f597-253">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="5f597-254">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5f597-254">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="5f597-255">Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="5f597-255">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="5f597-256">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="5f597-256">Routing and URL paths</span></span>

<span data-ttu-id="5f597-257">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5f597-257">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="5f597-258">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="5f597-258">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="5f597-259">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="5f597-259">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="5f597-260">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="5f597-260">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="5f597-261">V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="5f597-261">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="5f597-262">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="5f597-262">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="5f597-263">Pokud `[HttpGet]` atribut má šablona trasy (například `[HttpGet("products")]`), připojení, která k cestě.</span><span class="sxs-lookup"><span data-stu-id="5f597-263">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="5f597-264">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="5f597-264">This sample doesn't use a template.</span></span> <span data-ttu-id="5f597-265">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="5f597-265">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="5f597-266">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="5f597-266">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="5f597-267">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="5f597-267">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="5f597-268">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="5f597-268">Return values</span></span>

<span data-ttu-id="5f597-269">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="5f597-269">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="5f597-270">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="5f597-270">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="5f597-271">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="5f597-271">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="5f597-272">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="5f597-272">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="5f597-273">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="5f597-273">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="5f597-274">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="5f597-274">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="5f597-275">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="5f597-275">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="5f597-276">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="5f597-276">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="5f597-277">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="5f597-277">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="5f597-278">Test GetTodoItems – metoda</span><span class="sxs-lookup"><span data-stu-id="5f597-278">Test the GetTodoItems method</span></span>

<span data-ttu-id="5f597-279">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-279">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="5f597-280">Nainstalujte [Postman](https://www.getpostman.com/apps)</span><span class="sxs-lookup"><span data-stu-id="5f597-280">Install [Postman](https://www.getpostman.com/apps)</span></span>
* <span data-ttu-id="5f597-281">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5f597-281">Start the web app.</span></span>
* <span data-ttu-id="5f597-282">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="5f597-282">Start Postman.</span></span>
* <span data-ttu-id="5f597-283">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="5f597-283">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="5f597-284">Z **soubor > Nastavení** (\**Obecné* kartu), zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="5f597-284">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="5f597-285">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="5f597-285">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="5f597-286">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="5f597-286">Create a new request.</span></span>
  * <span data-ttu-id="5f597-287">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="5f597-287">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="5f597-288">Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="5f597-288">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="5f597-289">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="5f597-289">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="5f597-290">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="5f597-290">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="5f597-291">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="5f597-291">Select **Send**.</span></span>

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="5f597-293">Přidání metody vytvoření</span><span class="sxs-lookup"><span data-stu-id="5f597-293">Add a Create method</span></span>

<span data-ttu-id="5f597-294">Přidejte následující `PostTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="5f597-294">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="5f597-295">Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="5f597-295">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="5f597-296">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="5f597-296">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="5f597-297">`CreatedAtAction` Metody:</span><span class="sxs-lookup"><span data-stu-id="5f597-297">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="5f597-298">Vrátí stavový kód HTTP 201, v případě úspěšného ověření.</span><span class="sxs-lookup"><span data-stu-id="5f597-298">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="5f597-299">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="5f597-299">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="5f597-300">Přidá `Location` hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="5f597-300">Adds a `Location` header to the response.</span></span> <span data-ttu-id="5f597-301">`Location` Hlavičky určuje identifikátor URI nově vytvořeného úkolu položky.</span><span class="sxs-lookup"><span data-stu-id="5f597-301">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="5f597-302">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="5f597-302">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="5f597-303">Odkazy `GetTodoItem` akce pro vytvoření `Location` hlavičky identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="5f597-303">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="5f597-304">C# `nameof` – Klíčové slovo se používá k vyhnuli pevnému zakódování název akce v `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="5f597-304">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="5f597-305">Test PostTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="5f597-305">Test the PostTodoItem method</span></span>

* <span data-ttu-id="5f597-306">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5f597-306">Build the project.</span></span>
* <span data-ttu-id="5f597-307">V nástroji Postman, nastavte jako metodu HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="5f597-307">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="5f597-308">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="5f597-308">Select the **Body** tab.</span></span>
* <span data-ttu-id="5f597-309">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="5f597-309">Select the **raw** radio button.</span></span>
* <span data-ttu-id="5f597-310">Nastavte typ **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="5f597-310">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="5f597-311">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="5f597-311">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="5f597-312">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="5f597-312">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  <span data-ttu-id="5f597-314">Pokud obdržíte chybu 405 Metoda není povolena, je pravděpodobně výsledkem není kompilaci projektu po přidání `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="5f597-314">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="5f597-315">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="5f597-315">Test the location header URI</span></span>

* <span data-ttu-id="5f597-316">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="5f597-316">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="5f597-317">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="5f597-317">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="5f597-319">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="5f597-319">Set the method to GET.</span></span>
* <span data-ttu-id="5f597-320">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="5f597-320">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="5f597-321">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="5f597-321">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="5f597-322">Přidejte metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5f597-322">Add a PutTodoItem method</span></span>

<span data-ttu-id="5f597-323">Přidejte následující `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="5f597-323">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="5f597-324">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="5f597-324">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="5f597-325">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5f597-325">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="5f597-326">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="5f597-326">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="5f597-327">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="5f597-327">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="5f597-328">Můžu získat o chybě volání `PutTodoItem`, volání `GET` aby se zajistilo položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="5f597-328">I you get an error calling `PutTodoItem`, call `GET` to ensure there is a an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="5f597-329">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="5f597-329">Test the PutTodoItem method</span></span>

<span data-ttu-id="5f597-330">Tato ukázka používá databázi v paměti, který musí být struktura inicializovaná při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5f597-330">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="5f597-331">V databázi se musí být položka před provedením volání PUT.</span><span class="sxs-lookup"><span data-stu-id="5f597-331">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="5f597-332">Volejte GET – pomáhat zajistit, že není položka v databázi před voláním PUT.</span><span class="sxs-lookup"><span data-stu-id="5f597-332">Call GET to insure there is an item in the database before making a PUT call.</span></span>

<span data-ttu-id="5f597-333">Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":</span><span class="sxs-lookup"><span data-stu-id="5f597-333">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="5f597-334">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="5f597-334">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="5f597-336">Přidejte metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5f597-336">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="5f597-337">Přidejte následující `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="5f597-337">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="5f597-338">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5f597-338">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="5f597-339">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="5f597-339">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="5f597-340">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="5f597-340">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="5f597-341">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="5f597-341">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="5f597-342">Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="5f597-342">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="5f597-343">Vyberte **odeslat**</span><span class="sxs-lookup"><span data-stu-id="5f597-343">Select **Send**</span></span>

<span data-ttu-id="5f597-344">Ukázkové aplikace můžete odstranit všechny položky, ale když se odstraní poslední položky, je vytvořen nový pomocí konstruktoru třídy modelu při příštím spuštění se volá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-344">The sample app allows you to delete all the items, but when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="5f597-345">Volání rozhraní API pomocí jQuery</span><span class="sxs-lookup"><span data-stu-id="5f597-345">Call the API with jQuery</span></span>

<span data-ttu-id="5f597-346">V této části se přidá stránku HTML, který používá jQuery volat webové rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="5f597-346">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="5f597-347">jQuery zahájí požadavek a aktualizuje stránku s podrobnostmi o z odpovědi rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-347">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="5f597-348">Nakonfiguruje aplikaci, aby [doručování statických souborů](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolit výchozí mapování souboru](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span><span class="sxs-lookup"><span data-stu-id="5f597-348">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="5f597-349">Vytvoření *wwwroot* složku v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="5f597-349">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="5f597-350">Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="5f597-350">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="5f597-351">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5f597-351">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="5f597-352">Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="5f597-352">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="5f597-353">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5f597-353">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="5f597-354">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="5f597-354">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="5f597-355">Otevřít *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5f597-355">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="5f597-356">Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="5f597-356">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="5f597-357">Existuje několik způsobů, jak získat jQuery.</span><span class="sxs-lookup"><span data-stu-id="5f597-357">There are several ways to get jQuery.</span></span> <span data-ttu-id="5f597-358">V předchozím fragmentu kódu je načíst knihovnu ze sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="5f597-358">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="5f597-359">Tato ukázka volá všechny metody CRUD rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-359">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="5f597-360">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f597-360">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="5f597-361">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-361">Get a list of to-do items</span></span>

<span data-ttu-id="5f597-362">JQuery [ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `GET` žádosti na rozhraní API, které vrací JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="5f597-362">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="5f597-363">`success` Funkce zpětného volání je vyvolána, pokud neproběhne.</span><span class="sxs-lookup"><span data-stu-id="5f597-363">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="5f597-364">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="5f597-364">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="5f597-365">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-365">Add a to-do item</span></span>

<span data-ttu-id="5f597-366">[Ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `POST` požadavek se úkol v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="5f597-366">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="5f597-367">`accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo.</span><span class="sxs-lookup"><span data-stu-id="5f597-367">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="5f597-368">Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="5f597-368">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="5f597-369">Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="5f597-369">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="5f597-370">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="5f597-370">Update a to-do item</span></span>

<span data-ttu-id="5f597-371">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="5f597-371">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="5f597-372">`url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="5f597-372">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="5f597-373">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="5f597-373">Delete a to-do item</span></span>

<span data-ttu-id="5f597-374">Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="5f597-374">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="5f597-375">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5f597-375">Additional resources</span></span>

<span data-ttu-id="5f597-376">[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="5f597-376">[View or download sample code for this tutorial](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="5f597-377">Zobrazit [stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5f597-377">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="5f597-378">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="5f597-378">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>

## <a name="next-steps"></a><span data-ttu-id="5f597-379">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5f597-379">Next steps</span></span>

<span data-ttu-id="5f597-380">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="5f597-380">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5f597-381">Vytvořte projekt webového rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="5f597-381">Create a web api project.</span></span>
> * <span data-ttu-id="5f597-382">Přidejte třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="5f597-382">Add a model class.</span></span>
> * <span data-ttu-id="5f597-383">Vytvořte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5f597-383">Create the database context.</span></span>
> * <span data-ttu-id="5f597-384">Zaregistrujte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5f597-384">Register the database context.</span></span>
> * <span data-ttu-id="5f597-385">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="5f597-385">Add a controller.</span></span>
> * <span data-ttu-id="5f597-386">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="5f597-386">Add CRUD methods.</span></span>
> * <span data-ttu-id="5f597-387">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="5f597-387">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="5f597-388">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5f597-388">Specify return values.</span></span>
> * <span data-ttu-id="5f597-389">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="5f597-389">Call the web API with Postman.</span></span>
> * <span data-ttu-id="5f597-390">Volání webového rozhraní api pomocí jQuery.</span><span class="sxs-lookup"><span data-stu-id="5f597-390">Call the web api with jQuery.</span></span>

<span data-ttu-id="5f597-391">Přejděte k dalšímu kurzu se naučíte generování stránek nápovědy rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="5f597-391">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

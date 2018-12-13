---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC'
author: rick-anderson
description: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2018
uid: tutorials/first-web-api
ms.openlocfilehash: a27031550dd6fe810a250b369f83b7fe322aad1f
ms.sourcegitcommit: 1ea1b4fc58055c62728143388562689f1ef96cb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329169"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core-mvc"></a><span data-ttu-id="44508-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="44508-103">Tutorial: Create a web API with ASP.NET Core MVC</span></span>

<span data-ttu-id="44508-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="44508-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="44508-105">V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44508-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="44508-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="44508-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="44508-107">Vytvořte projekt webového rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="44508-107">Create a web api project.</span></span>
> * <span data-ttu-id="44508-108">Přidejte třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="44508-108">Add a model class.</span></span>
> * <span data-ttu-id="44508-109">Vytvořte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="44508-109">Create the database context.</span></span>
> * <span data-ttu-id="44508-110">Zaregistrujte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="44508-110">Register the database context.</span></span>
> * <span data-ttu-id="44508-111">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="44508-111">Add a controller.</span></span>
> * <span data-ttu-id="44508-112">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="44508-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="44508-113">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="44508-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="44508-114">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="44508-114">Specify return values.</span></span>
> * <span data-ttu-id="44508-115">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="44508-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="44508-116">Volání webového rozhraní api pomocí jQuery.</span><span class="sxs-lookup"><span data-stu-id="44508-116">Call the web api with jQuery.</span></span>

<span data-ttu-id="44508-117">Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="44508-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="44508-118">Přehled</span><span class="sxs-lookup"><span data-stu-id="44508-118">Overview</span></span>

<span data-ttu-id="44508-119">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="44508-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="44508-120">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="44508-120">API</span></span> | <span data-ttu-id="44508-121">Popis</span><span class="sxs-lookup"><span data-stu-id="44508-121">Description</span></span> | <span data-ttu-id="44508-122">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="44508-122">Request body</span></span> | <span data-ttu-id="44508-123">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="44508-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="44508-124">ZÍSKAT /api/todo</span><span class="sxs-lookup"><span data-stu-id="44508-124">GET /api/todo</span></span> | <span data-ttu-id="44508-125">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-125">Get all to-do items</span></span> | <span data-ttu-id="44508-126">Žádné</span><span class="sxs-lookup"><span data-stu-id="44508-126">None</span></span> | <span data-ttu-id="44508-127">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-127">Array of to-do items</span></span>|
|<span data-ttu-id="44508-128">ZÍSKAT/webové rozhraní API/todo / {id}</span><span class="sxs-lookup"><span data-stu-id="44508-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="44508-129">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="44508-129">Get an item by ID</span></span> | <span data-ttu-id="44508-130">Žádná</span><span class="sxs-lookup"><span data-stu-id="44508-130">None</span></span> | <span data-ttu-id="44508-131">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-131">To-do item</span></span>|
|<span data-ttu-id="44508-132">Publikovat/api/todo</span><span class="sxs-lookup"><span data-stu-id="44508-132">POST /api/todo</span></span> | <span data-ttu-id="44508-133">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="44508-133">Add a new item</span></span> | <span data-ttu-id="44508-134">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-134">To-do item</span></span> | <span data-ttu-id="44508-135">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-135">To-do item</span></span> |
|<span data-ttu-id="44508-136">Vložení/webové rozhraní API/todo / {id}</span><span class="sxs-lookup"><span data-stu-id="44508-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="44508-137">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="44508-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="44508-138">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-138">To-do item</span></span> | <span data-ttu-id="44508-139">Žádná</span><span class="sxs-lookup"><span data-stu-id="44508-139">None</span></span> |
|<span data-ttu-id="44508-140">ODSTRANIT/webové rozhraní API/todo / {id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="44508-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="44508-141">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="44508-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="44508-142">Žádné</span><span class="sxs-lookup"><span data-stu-id="44508-142">None</span></span> | <span data-ttu-id="44508-143">Žádná</span><span class="sxs-lookup"><span data-stu-id="44508-143">None</span></span>|

<span data-ttu-id="44508-144">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="44508-144">The following diagram shows the design of the app.</span></span>

![Klient je reprezentována pole na levé straně a odešle žádost a obdrží odpověď od aplikace vykreslen na pravé straně pole.](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="44508-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="44508-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44508-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44508-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44508-151">Z **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="44508-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="44508-152">Vyberte **webové aplikace ASP.NET Core** šablony.</span><span class="sxs-lookup"><span data-stu-id="44508-152">Select the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="44508-153">Pojmenujte projekt *TodoApi* a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="44508-153">Name the project *TodoApi* and click **OK**.</span></span>
* <span data-ttu-id="44508-154">V **nové webové aplikace ASP.NET Core – TodoApi** dialogového okna, vyberte verzi technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44508-154">In the **New ASP.NET Core Web Application - TodoApi** dialog, choose the ASP.NET Core version.</span></span> <span data-ttu-id="44508-155">Vyberte **API** šablonu a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="44508-155">Select the **API** template and click **OK**.</span></span> <span data-ttu-id="44508-156">Proveďte **není** vyberte **povolit podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="44508-156">Do **not** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="44508-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44508-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="44508-159">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="44508-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="44508-160">Změňte adresář (`cd`) do složky, která bude obsahovat složky projektu.</span><span class="sxs-lookup"><span data-stu-id="44508-160">Change directories (`cd`) to the folder which will contain the project folder.</span></span>
* <span data-ttu-id="44508-161">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="44508-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="44508-162">Tyto příkazy vytvořte nový projekt webového rozhraní API a otevřete novou instanci sady Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="44508-162">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="44508-163">Když dialogové okno požádá, pokud chcete do projektu přidejte požadované prostředky, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="44508-163">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="44508-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44508-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="44508-165">Vyberte **souboru** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="44508-165">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="44508-167">Vyberte **aplikace .NET Core** > **webového rozhraní API ASP.NET Core** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="44508-167">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="44508-169">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="44508-169">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="44508-170">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="44508-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="44508-172">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="44508-172">Test the API</span></span>

<span data-ttu-id="44508-173">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44508-173">The project template creates a `values` API.</span></span> <span data-ttu-id="44508-174">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="44508-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44508-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44508-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44508-176">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="44508-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="44508-177">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="44508-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="44508-178">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="44508-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="44508-179">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="44508-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="44508-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44508-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="44508-181">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="44508-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="44508-182">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="44508-182">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="44508-183">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44508-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="44508-184">Vyberte **spustit** > **spustit s ladění** aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="44508-184">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="44508-185">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="44508-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="44508-186">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="44508-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="44508-187">Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="44508-187">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="44508-188">Vrátí následující JSON:</span><span class="sxs-lookup"><span data-stu-id="44508-188">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="44508-189">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="44508-189">Add a model class</span></span>

<span data-ttu-id="44508-190">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="44508-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="44508-191">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="44508-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44508-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44508-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44508-193">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="44508-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="44508-194">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="44508-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="44508-195">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="44508-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="44508-196">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="44508-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="44508-197">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="44508-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="44508-198">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44508-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="44508-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44508-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="44508-200">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="44508-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="44508-201">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44508-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="44508-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44508-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="44508-203">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="44508-203">Right-click the project.</span></span> <span data-ttu-id="44508-204">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="44508-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="44508-205">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="44508-205">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="44508-207">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **nový soubor** > **Obecné**  >  **Prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="44508-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="44508-208">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="44508-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="44508-209">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44508-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="44508-210">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="44508-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="44508-211">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="44508-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="44508-212">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="44508-212">Add a database context</span></span>

<span data-ttu-id="44508-213">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="44508-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="44508-214">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="44508-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44508-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44508-215">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44508-216">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="44508-216">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="44508-217">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="44508-217">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="44508-218">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44508-218">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="44508-219">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="44508-219">Add a `TodoContext` class to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="44508-220">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44508-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="44508-221">Přidat `TodoContext` třídy v *modely* složky:</span><span class="sxs-lookup"><span data-stu-id="44508-221">Add a `TodoContext` class in the *Models* folder:</span></span>

---

* <span data-ttu-id="44508-222">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44508-222">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="44508-223">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="44508-223">Register the database context</span></span>

<span data-ttu-id="44508-224">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="44508-224">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="44508-225">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="44508-225">The container provides the service to controllers.</span></span>

<span data-ttu-id="44508-226">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="44508-226">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="44508-227">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="44508-227">The preceding code:</span></span>

* <span data-ttu-id="44508-228">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="44508-228">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="44508-229">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="44508-229">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="44508-230">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="44508-230">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="44508-231">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="44508-231">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44508-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44508-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44508-233">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="44508-233">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="44508-234">Vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="44508-234">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="44508-235">V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="44508-235">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="44508-236">Název třídy *TodoController*a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="44508-236">Name the class *TodoController*, and select **Add**.</span></span>

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="44508-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44508-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="44508-239">V *řadiče* složku, vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="44508-239">In the *Controllers* folder, create a class named `TodoController`.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="44508-240">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44508-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="44508-241">V *řadiče* složky, přidejte třídu `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="44508-241">In the *Controllers* folder, add the class `TodoController`.</span></span>

---

* <span data-ttu-id="44508-242">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44508-242">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="44508-243">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="44508-243">The preceding code:</span></span>

* <span data-ttu-id="44508-244">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="44508-244">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="44508-245">Třída se upraví [ `[ApiController]` ](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="44508-245">Decorates the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="44508-246">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44508-246">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="44508-247">Informace o konkrétní chování, které umožňuje atribut najdete v tématu [Poznámka atributem objektu ApiController](xref:web-api/index#annotation-with-apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="44508-247">For information about specific behaviors that the attribute enables, see [Annotation with ApiController attribute](xref:web-api/index#annotation-with-apicontroller-attribute).</span></span>
* <span data-ttu-id="44508-248">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="44508-248">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="44508-249">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="44508-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="44508-250">Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný.</span><span class="sxs-lookup"><span data-stu-id="44508-250">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="44508-251">Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="44508-251">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="44508-252">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44508-252">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="44508-253">Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.</span><span class="sxs-lookup"><span data-stu-id="44508-253">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="44508-254">Přidejte metody Get</span><span class="sxs-lookup"><span data-stu-id="44508-254">Add Get methods</span></span>

<span data-ttu-id="44508-255">Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:</span><span class="sxs-lookup"><span data-stu-id="44508-255">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="44508-256">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="44508-256">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="44508-257">Testování aplikace pomocí volání dva koncové body v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="44508-257">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="44508-258">Příklad:</span><span class="sxs-lookup"><span data-stu-id="44508-258">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="44508-259">Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="44508-259">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="44508-260">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="44508-260">Routing and URL paths</span></span>

<span data-ttu-id="44508-261">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="44508-261">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="44508-262">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="44508-262">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="44508-263">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="44508-263">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="44508-264">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="44508-264">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="44508-265">V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="44508-265">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="44508-266">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="44508-266">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="44508-267">Pokud `[HttpGet]` atribut má šablona trasy (například `[HttpGet("/products")]`, připojení, která k cestě.</span><span class="sxs-lookup"><span data-stu-id="44508-267">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("/products")]`, append that to the path.</span></span> <span data-ttu-id="44508-268">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="44508-268">This sample doesn't use a template.</span></span> <span data-ttu-id="44508-269">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="44508-269">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="44508-270">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="44508-270">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="44508-271">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="44508-271">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

<span data-ttu-id="44508-272">`Name = "GetTodo"` Parametr vytvoří pojmenovanou trasu.</span><span class="sxs-lookup"><span data-stu-id="44508-272">The `Name = "GetTodo"` parameter creates a named route.</span></span> <span data-ttu-id="44508-273">Zobrazí se vám později jak může aplikace používat název vytvořit odkaz na protokol HTTP pomocí názvu trasy.</span><span class="sxs-lookup"><span data-stu-id="44508-273">You'll see later how the app can use the name to create an HTTP link using the route name.</span></span>

## <a name="return-values"></a><span data-ttu-id="44508-274">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="44508-274">Return values</span></span>

<span data-ttu-id="44508-275">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="44508-275">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="44508-276">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="44508-276">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="44508-277">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="44508-277">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="44508-278">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="44508-278">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="44508-279">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="44508-279">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="44508-280">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="44508-280">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="44508-281">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="44508-281">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="44508-282">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="44508-282">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="44508-283">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="44508-283">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="44508-284">Test GetTodoItems – metoda</span><span class="sxs-lookup"><span data-stu-id="44508-284">Test the GetTodoItems method</span></span>

<span data-ttu-id="44508-285">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44508-285">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="44508-286">Nainstalujte [Postman](https://www.getpostman.com/apps)</span><span class="sxs-lookup"><span data-stu-id="44508-286">Install [Postman](https://www.getpostman.com/apps)</span></span>
* <span data-ttu-id="44508-287">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="44508-287">Start the web app.</span></span>
* <span data-ttu-id="44508-288">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="44508-288">Start Postman.</span></span>
* <span data-ttu-id="44508-289">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="44508-289">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="44508-290">Z **soubor > Nastavení** (\**Obecné* kartu), zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="44508-290">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="44508-291">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="44508-291">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="44508-292">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="44508-292">Create a new request.</span></span>
  * <span data-ttu-id="44508-293">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="44508-293">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="44508-294">Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="44508-294">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="44508-295">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="44508-295">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="44508-296">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="44508-296">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="44508-297">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="44508-297">Select **Send**.</span></span>

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="44508-299">Přidání metody vytvoření</span><span class="sxs-lookup"><span data-stu-id="44508-299">Add a Create method</span></span>

<span data-ttu-id="44508-300">Přidejte následující `PostTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="44508-300">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="44508-301">Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="44508-301">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="44508-302">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="44508-302">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="44508-303">`CreatedAtRoute` Metody:</span><span class="sxs-lookup"><span data-stu-id="44508-303">The `CreatedAtRoute` method:</span></span>

* <span data-ttu-id="44508-304">Vrátí odezvě 201.</span><span class="sxs-lookup"><span data-stu-id="44508-304">Returns a 201 response.</span></span> <span data-ttu-id="44508-305">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="44508-305">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="44508-306">Přidá do odpovědi hlavičku umístění.</span><span class="sxs-lookup"><span data-stu-id="44508-306">Adds a Location header to the response.</span></span> <span data-ttu-id="44508-307">Hlavička umístění Určuje identifikátor URI nově vytvořeného úkolu položky.</span><span class="sxs-lookup"><span data-stu-id="44508-307">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="44508-308">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="44508-308">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="44508-309">K vytvoření adresy URL používá "GetTodo" s názvem trasy.</span><span class="sxs-lookup"><span data-stu-id="44508-309">Uses the "GetTodo" named route to create the URL.</span></span> <span data-ttu-id="44508-310">"GetTodo" s názvem trasy je definována v `GetTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="44508-310">The "GetTodo" named route is defined in `GetTodoItem`:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="44508-311">Test PostTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="44508-311">Test the PostTodoItem method</span></span>

* <span data-ttu-id="44508-312">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="44508-312">Build the project.</span></span>
* <span data-ttu-id="44508-313">V nástroji Postman, nastavte jako metodu HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="44508-313">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="44508-314">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="44508-314">Select the **Body** tab.</span></span>
* <span data-ttu-id="44508-315">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="44508-315">Select the **raw** radio button.</span></span>
* <span data-ttu-id="44508-316">Nastavte typ **JSON (application/json)**.</span><span class="sxs-lookup"><span data-stu-id="44508-316">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="44508-317">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="44508-317">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="44508-318">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="44508-318">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  <span data-ttu-id="44508-320">Pokud obdržíte chybu 405 Metoda není povolena, je pravděpodobně výsledkem není kompilaci projektu po přidání po přidání `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="44508-320">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="44508-321">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="44508-321">Test the location header URI</span></span>

* <span data-ttu-id="44508-322">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="44508-322">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="44508-323">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="44508-323">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="44508-325">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="44508-325">Set the method to GET.</span></span>
* <span data-ttu-id="44508-326">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="44508-326">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="44508-327">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="44508-327">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="44508-328">Přidejte metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="44508-328">Add a PutTodoItem method</span></span>

<span data-ttu-id="44508-329">Přidejte následující `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="44508-329">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="44508-330">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="44508-330">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="44508-331">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="44508-331">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="44508-332">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="44508-332">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="44508-333">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="44508-333">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="44508-334">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="44508-334">Test the PutTodoItem method</span></span>

<span data-ttu-id="44508-335">Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":</span><span class="sxs-lookup"><span data-stu-id="44508-335">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="44508-336">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="44508-336">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="44508-338">Přidejte metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="44508-338">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="44508-339">Přidejte následující `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="44508-339">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="44508-340">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="44508-340">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="44508-341">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="44508-341">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="44508-342">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="44508-342">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="44508-343">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="44508-343">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="44508-344">Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="44508-344">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="44508-345">Vyberte **odeslat**</span><span class="sxs-lookup"><span data-stu-id="44508-345">Select **Send**</span></span>

<span data-ttu-id="44508-346">Ukázkové aplikace můžete odstranit všechny položky, ale když se odstraní poslední položky, je vytvořen nový pomocí konstruktoru třídy modelu při příštím spuštění se volá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44508-346">The sample app allows you to delete all the items, but when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="44508-347">Volání rozhraní API pomocí jQuery</span><span class="sxs-lookup"><span data-stu-id="44508-347">Call the API with jQuery</span></span>

<span data-ttu-id="44508-348">V této části se přidá stránku HTML, který používá jQuery volat webové rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="44508-348">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="44508-349">jQuery zahájí požadavek a aktualizuje stránku s podrobnostmi o z odpovědi rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44508-349">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="44508-350">Nakonfiguruje aplikaci, aby [doručování statických souborů](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolit výchozí mapování souboru](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span><span class="sxs-lookup"><span data-stu-id="44508-350">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="44508-351">Vytvoření *wwwroot* složku v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="44508-351">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="44508-352">Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="44508-352">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="44508-353">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44508-353">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="44508-354">Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="44508-354">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="44508-355">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44508-355">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="44508-356">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="44508-356">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="44508-357">Otevřít *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="44508-357">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="44508-358">Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="44508-358">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="44508-359">Existuje několik způsobů, jak získat jQuery.</span><span class="sxs-lookup"><span data-stu-id="44508-359">There are several ways to get jQuery.</span></span> <span data-ttu-id="44508-360">V předchozím fragmentu kódu je načíst knihovnu ze sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="44508-360">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="44508-361">Tato ukázka volá všechny metody CRUD rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44508-361">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="44508-362">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44508-362">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="44508-363">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-363">Get a list of to-do items</span></span>

<span data-ttu-id="44508-364">JQuery [ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `GET` žádosti na rozhraní API, které vrací JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="44508-364">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="44508-365">`success` Funkce zpětného volání je vyvolána, pokud neproběhne.</span><span class="sxs-lookup"><span data-stu-id="44508-365">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="44508-366">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="44508-366">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="44508-367">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-367">Add a to-do item</span></span>

<span data-ttu-id="44508-368">[Ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `POST` požadavek se úkol v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="44508-368">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="44508-369">`accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo.</span><span class="sxs-lookup"><span data-stu-id="44508-369">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="44508-370">Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="44508-370">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="44508-371">Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="44508-371">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="44508-372">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="44508-372">Update a to-do item</span></span>

<span data-ttu-id="44508-373">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="44508-373">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="44508-374">`url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="44508-374">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="44508-375">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="44508-375">Delete a to-do item</span></span>

<span data-ttu-id="44508-376">Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="44508-376">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="44508-377">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="44508-377">Additional resources</span></span>

<span data-ttu-id="44508-378">[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="44508-378">[View or download sample code for this tutorial](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="44508-379">Zobrazit [stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="44508-379">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="44508-380">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="44508-380">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>

## <a name="next-steps"></a><span data-ttu-id="44508-381">Další kroky</span><span class="sxs-lookup"><span data-stu-id="44508-381">Next steps</span></span>

<span data-ttu-id="44508-382">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="44508-382">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="44508-383">Vytvořte projekt webového rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="44508-383">Create a web api project.</span></span>
> * <span data-ttu-id="44508-384">Přidejte třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="44508-384">Add a model class.</span></span>
> * <span data-ttu-id="44508-385">Vytvořte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="44508-385">Create the database context.</span></span>
> * <span data-ttu-id="44508-386">Zaregistrujte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="44508-386">Register the database context.</span></span>
> * <span data-ttu-id="44508-387">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="44508-387">Add a controller.</span></span>
> * <span data-ttu-id="44508-388">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="44508-388">Add CRUD methods.</span></span>
> * <span data-ttu-id="44508-389">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="44508-389">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="44508-390">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="44508-390">Specify return values.</span></span>
> * <span data-ttu-id="44508-391">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="44508-391">Call the web API with Postman.</span></span>
> * <span data-ttu-id="44508-392">Volání webového rozhraní api pomocí jQuery.</span><span class="sxs-lookup"><span data-stu-id="44508-392">Call the web api with jQuery.</span></span>

<span data-ttu-id="44508-393">Přejděte k dalšímu kurzu se naučíte generování stránek nápovědy rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="44508-393">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

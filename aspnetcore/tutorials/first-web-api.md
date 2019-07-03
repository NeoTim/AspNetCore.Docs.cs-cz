---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Zjistěte, jak vytvořit webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2019
uid: tutorials/first-web-api
ms.openlocfilehash: a53f7019c1079296f073e743ddbf9d90fc5abad3
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555872"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4b845-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b845-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4b845-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4b845-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4b845-105">V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b845-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="4b845-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="4b845-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4b845-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-107">Create a web API project.</span></span>
> * <span data-ttu-id="4b845-108">Přidejte třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="4b845-108">Add a model class.</span></span>
> * <span data-ttu-id="4b845-109">Vytvořte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="4b845-109">Create the database context.</span></span>
> * <span data-ttu-id="4b845-110">Zaregistrujte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="4b845-110">Register the database context.</span></span>
> * <span data-ttu-id="4b845-111">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4b845-111">Add a controller.</span></span>
> * <span data-ttu-id="4b845-112">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="4b845-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="4b845-113">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="4b845-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4b845-114">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4b845-114">Specify return values.</span></span>
> * <span data-ttu-id="4b845-115">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="4b845-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4b845-116">Volání webového rozhraní API s jQuery.</span><span class="sxs-lookup"><span data-stu-id="4b845-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="4b845-117">Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="4b845-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="4b845-118">Přehled</span><span class="sxs-lookup"><span data-stu-id="4b845-118">Overview</span></span>

<span data-ttu-id="4b845-119">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="4b845-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4b845-120">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4b845-120">API</span></span> | <span data-ttu-id="4b845-121">Popis</span><span class="sxs-lookup"><span data-stu-id="4b845-121">Description</span></span> | <span data-ttu-id="4b845-122">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="4b845-122">Request body</span></span> | <span data-ttu-id="4b845-123">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="4b845-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4b845-124">ZÍSKAT /api/todo</span><span class="sxs-lookup"><span data-stu-id="4b845-124">GET /api/todo</span></span> | <span data-ttu-id="4b845-125">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-125">Get all to-do items</span></span> | <span data-ttu-id="4b845-126">Žádné</span><span class="sxs-lookup"><span data-stu-id="4b845-126">None</span></span> | <span data-ttu-id="4b845-127">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-127">Array of to-do items</span></span>|
|<span data-ttu-id="4b845-128">ZÍSKAT/webové rozhraní API/todo / {id}</span><span class="sxs-lookup"><span data-stu-id="4b845-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="4b845-129">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="4b845-129">Get an item by ID</span></span> | <span data-ttu-id="4b845-130">Žádná</span><span class="sxs-lookup"><span data-stu-id="4b845-130">None</span></span> | <span data-ttu-id="4b845-131">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-131">To-do item</span></span>|
|<span data-ttu-id="4b845-132">Publikovat/api/todo</span><span class="sxs-lookup"><span data-stu-id="4b845-132">POST /api/todo</span></span> | <span data-ttu-id="4b845-133">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="4b845-133">Add a new item</span></span> | <span data-ttu-id="4b845-134">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-134">To-do item</span></span> | <span data-ttu-id="4b845-135">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-135">To-do item</span></span> |
|<span data-ttu-id="4b845-136">Vložení/webové rozhraní API/todo / {id}</span><span class="sxs-lookup"><span data-stu-id="4b845-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="4b845-137">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b845-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4b845-138">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-138">To-do item</span></span> | <span data-ttu-id="4b845-139">Žádná</span><span class="sxs-lookup"><span data-stu-id="4b845-139">None</span></span> |
|<span data-ttu-id="4b845-140">ODSTRANIT/webové rozhraní API/todo / {id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b845-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4b845-141">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b845-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4b845-142">Žádné</span><span class="sxs-lookup"><span data-stu-id="4b845-142">None</span></span> | <span data-ttu-id="4b845-143">Žádná</span><span class="sxs-lookup"><span data-stu-id="4b845-143">None</span></span>|

<span data-ttu-id="4b845-144">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b845-144">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován políčko vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4b845-150">Požadavky</span><span class="sxs-lookup"><span data-stu-id="4b845-150">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b845-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b845-151">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b845-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b845-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b845-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b845-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4b845-154">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="4b845-154">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b845-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b845-155">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b845-156">Z **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="4b845-156">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4b845-157">Vyberte **webové aplikace ASP.NET Core** šablonu a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="4b845-157">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4b845-158">Pojmenujte projekt *TodoApi* a klikněte na tlačítko **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4b845-158">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4b845-159">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **2.2 technologie ASP.NET Core** jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="4b845-159">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4b845-160">Vyberte **API** šablonu a klikněte na tlačítko **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4b845-160">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4b845-161">**Není** vyberte **povolit podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="4b845-161">**Don't** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b845-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b845-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b845-164">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4b845-164">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4b845-165">Změňte adresář (`cd`) do složky, která bude obsahovat složky projektu.</span><span class="sxs-lookup"><span data-stu-id="4b845-165">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4b845-166">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4b845-166">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4b845-167">Tyto příkazy vytvořte nový projekt webového rozhraní API a otevřete novou instanci sady Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="4b845-167">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4b845-168">Když dialogové okno požádá, pokud chcete do projektu přidejte požadované prostředky, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4b845-168">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b845-169">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b845-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b845-170">Vyberte **souboru** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="4b845-170">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4b845-172">Vyberte **aplikace .NET Core** > **webového rozhraní API ASP.NET Core** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="4b845-172">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4b845-174">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="4b845-174">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="4b845-175">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4b845-175">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4b845-177">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4b845-177">Test the API</span></span>

<span data-ttu-id="4b845-178">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-178">The project template creates a `values` API.</span></span> <span data-ttu-id="4b845-179">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b845-179">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b845-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b845-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b845-181">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4b845-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4b845-182">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="4b845-182">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4b845-183">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4b845-183">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4b845-184">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4b845-184">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b845-185">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b845-185">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4b845-186">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4b845-186">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4b845-187">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="4b845-187">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b845-188">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4b845-188">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b845-189">Vyberte **spustit** > **spustit s ladění** aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="4b845-189">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="4b845-190">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="4b845-190">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4b845-191">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="4b845-191">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4b845-192">Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="4b845-192">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4b845-193">Vrátí následující JSON:</span><span class="sxs-lookup"><span data-stu-id="4b845-193">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4b845-194">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="4b845-194">Add a model class</span></span>

<span data-ttu-id="4b845-195">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="4b845-195">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4b845-196">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="4b845-196">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b845-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b845-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b845-198">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="4b845-198">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4b845-199">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="4b845-199">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4b845-200">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="4b845-200">Name the folder *Models*.</span></span>

* <span data-ttu-id="4b845-201">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="4b845-201">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4b845-202">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="4b845-202">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4b845-203">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4b845-203">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b845-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b845-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b845-205">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="4b845-205">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4b845-206">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4b845-206">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b845-207">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4b845-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b845-208">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="4b845-208">Right-click the project.</span></span> <span data-ttu-id="4b845-209">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="4b845-209">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4b845-210">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="4b845-210">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4b845-212">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **nový soubor** > **Obecné**  >  **Prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="4b845-212">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4b845-213">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="4b845-213">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4b845-214">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4b845-214">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4b845-215">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="4b845-215">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4b845-216">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="4b845-216">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4b845-217">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="4b845-217">Add a database context</span></span>

<span data-ttu-id="4b845-218">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="4b845-218">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4b845-219">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="4b845-219">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b845-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b845-220">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b845-221">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="4b845-221">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4b845-222">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="4b845-222">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4b845-223">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4b845-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4b845-224">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="4b845-224">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4b845-225">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4b845-225">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4b845-226">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="4b845-226">Register the database context</span></span>

<span data-ttu-id="4b845-227">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="4b845-227">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4b845-228">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="4b845-228">The container provides the service to controllers.</span></span>

<span data-ttu-id="4b845-229">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="4b845-229">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4b845-230">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="4b845-230">The preceding code:</span></span>

* <span data-ttu-id="4b845-231">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="4b845-231">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4b845-232">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="4b845-232">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4b845-233">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="4b845-233">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4b845-234">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="4b845-234">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b845-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b845-235">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b845-236">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="4b845-236">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4b845-237">Vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="4b845-237">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4b845-238">V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="4b845-238">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4b845-239">Název třídy *TodoController*a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="4b845-239">Name the class *TodoController*, and select **Add**.</span></span>

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4b845-241">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4b845-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4b845-242">V *řadiče* složku, vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="4b845-242">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4b845-243">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4b845-243">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4b845-244">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="4b845-244">The preceding code:</span></span>

* <span data-ttu-id="4b845-245">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="4b845-245">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4b845-246">Třída se upraví [[objektu ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="4b845-246">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4b845-247">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-247">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4b845-248">Informace o konkrétní chování, které umožňuje atribut najdete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4b845-248">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4b845-249">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4b845-249">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4b845-250">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4b845-250">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4b845-251">Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný.</span><span class="sxs-lookup"><span data-stu-id="4b845-251">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4b845-252">Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b845-252">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4b845-253">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-253">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4b845-254">Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.</span><span class="sxs-lookup"><span data-stu-id="4b845-254">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4b845-255">Přidejte metody Get</span><span class="sxs-lookup"><span data-stu-id="4b845-255">Add Get methods</span></span>

<span data-ttu-id="4b845-256">Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:</span><span class="sxs-lookup"><span data-stu-id="4b845-256">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4b845-257">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="4b845-257">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4b845-258">Testování aplikace pomocí volání dva koncové body v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="4b845-258">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4b845-259">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4b845-259">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4b845-260">Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="4b845-260">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4b845-261">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="4b845-261">Routing and URL paths</span></span>

<span data-ttu-id="4b845-262">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="4b845-262">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4b845-263">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="4b845-263">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4b845-264">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="4b845-264">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4b845-265">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="4b845-265">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4b845-266">V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="4b845-266">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4b845-267">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4b845-267">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4b845-268">Pokud `[HttpGet]` atribut má šablona trasy (například `[HttpGet("products")]`), připojení, která k cestě.</span><span class="sxs-lookup"><span data-stu-id="4b845-268">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4b845-269">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="4b845-269">This sample doesn't use a template.</span></span> <span data-ttu-id="4b845-270">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4b845-270">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4b845-271">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="4b845-271">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4b845-272">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="4b845-272">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4b845-273">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="4b845-273">Return values</span></span>

<span data-ttu-id="4b845-274">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4b845-274">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4b845-275">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="4b845-275">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4b845-276">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="4b845-276">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4b845-277">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="4b845-277">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4b845-278">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="4b845-278">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4b845-279">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="4b845-279">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4b845-280">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="4b845-280">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4b845-281">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="4b845-281">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4b845-282">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="4b845-282">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4b845-283">Test GetTodoItems – metoda</span><span class="sxs-lookup"><span data-stu-id="4b845-283">Test the GetTodoItems method</span></span>

<span data-ttu-id="4b845-284">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-284">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4b845-285">Nainstalujte [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="4b845-285">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4b845-286">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4b845-286">Start the web app.</span></span>
* <span data-ttu-id="4b845-287">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="4b845-287">Start Postman.</span></span>
* <span data-ttu-id="4b845-288">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="4b845-288">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="4b845-289">Z **soubor > Nastavení** (\**Obecné* kartu), zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="4b845-289">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4b845-290">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4b845-290">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4b845-291">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="4b845-291">Create a new request.</span></span>
  * <span data-ttu-id="4b845-292">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="4b845-292">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4b845-293">Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4b845-293">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4b845-294">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4b845-294">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4b845-295">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="4b845-295">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4b845-296">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="4b845-296">Select **Send**.</span></span>

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4b845-298">Přidání metody vytvoření</span><span class="sxs-lookup"><span data-stu-id="4b845-298">Add a Create method</span></span>

<span data-ttu-id="4b845-299">Přidejte následující `PostTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="4b845-299">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4b845-300">Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="4b845-300">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4b845-301">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b845-301">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4b845-302">`CreatedAtAction` Metody:</span><span class="sxs-lookup"><span data-stu-id="4b845-302">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4b845-303">Vrátí stavový kód HTTP 201, v případě úspěšného ověření.</span><span class="sxs-lookup"><span data-stu-id="4b845-303">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4b845-304">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="4b845-304">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4b845-305">Přidá `Location` hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4b845-305">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4b845-306">`Location` Hlavičky určuje identifikátor URI nově vytvořeného úkolu položky.</span><span class="sxs-lookup"><span data-stu-id="4b845-306">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4b845-307">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4b845-307">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4b845-308">Odkazy `GetTodoItem` akce pro vytvoření `Location` hlavičky identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="4b845-308">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4b845-309">C# `nameof` – Klíčové slovo se používá k vyhnuli pevnému zakódování název akce v `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="4b845-309">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4b845-310">Test PostTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="4b845-310">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4b845-311">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="4b845-311">Build the project.</span></span>
* <span data-ttu-id="4b845-312">V nástroji Postman, nastavte jako metodu HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="4b845-312">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4b845-313">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="4b845-313">Select the **Body** tab.</span></span>
* <span data-ttu-id="4b845-314">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="4b845-314">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4b845-315">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="4b845-315">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4b845-316">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="4b845-316">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4b845-317">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="4b845-317">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  <span data-ttu-id="4b845-319">Pokud obdržíte chybu 405 Metoda není povolena, je pravděpodobně výsledkem není kompilaci projektu po přidání `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="4b845-319">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4b845-320">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="4b845-320">Test the location header URI</span></span>

* <span data-ttu-id="4b845-321">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="4b845-321">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4b845-322">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="4b845-322">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4b845-324">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="4b845-324">Set the method to GET.</span></span>
* <span data-ttu-id="4b845-325">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="4b845-325">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="4b845-326">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="4b845-326">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4b845-327">Přidejte metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4b845-327">Add a PutTodoItem method</span></span>

<span data-ttu-id="4b845-328">Přidejte následující `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="4b845-328">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4b845-329">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4b845-329">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4b845-330">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4b845-330">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4b845-331">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="4b845-331">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4b845-332">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="4b845-332">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4b845-333">Pokud dojde k chybě volání `PutTodoItem`, volání `GET` aby se zajistilo položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="4b845-333">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4b845-334">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="4b845-334">Test the PutTodoItem method</span></span>

<span data-ttu-id="4b845-335">Tato ukázka používá databázi v paměti, který musí být struktura inicializovaná při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b845-335">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="4b845-336">V databázi se musí být položka před provedením volání PUT.</span><span class="sxs-lookup"><span data-stu-id="4b845-336">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4b845-337">Volejte GET – pomáhat zajistit, že není položka v databázi před voláním PUT.</span><span class="sxs-lookup"><span data-stu-id="4b845-337">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4b845-338">Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":</span><span class="sxs-lookup"><span data-stu-id="4b845-338">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4b845-339">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="4b845-339">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4b845-341">Přidejte metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4b845-341">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4b845-342">Přidejte následující `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="4b845-342">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4b845-343">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4b845-343">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4b845-344">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="4b845-344">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4b845-345">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="4b845-345">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4b845-346">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="4b845-346">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4b845-347">Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="4b845-347">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="4b845-348">Vyberte **odeslat**</span><span class="sxs-lookup"><span data-stu-id="4b845-348">Select **Send**</span></span>

<span data-ttu-id="4b845-349">Ukázkové aplikace můžete odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="4b845-349">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4b845-350">Ale když se odstraní poslední položky, nový se vytvoří pomocí konstruktoru třídy modelu při příštím spuštění se volá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-350">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="4b845-351">Volání rozhraní API pomocí jQuery</span><span class="sxs-lookup"><span data-stu-id="4b845-351">Call the API with jQuery</span></span>

<span data-ttu-id="4b845-352">V této části se přidá stránku HTML, který používá jQuery volat webové rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="4b845-352">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="4b845-353">jQuery zahájí požadavek a aktualizuje stránku s podrobnostmi o z odpovědi rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-353">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="4b845-354">Nakonfiguruje aplikaci, aby [doručování statických souborů](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolit výchozí mapování souboru](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="4b845-354">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="4b845-355">Vytvoření *wwwroot* složku v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="4b845-355">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="4b845-356">Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="4b845-356">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4b845-357">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4b845-357">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4b845-358">Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="4b845-358">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4b845-359">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4b845-359">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4b845-360">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="4b845-360">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4b845-361">Otevřít *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4b845-361">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4b845-362">Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="4b845-362">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4b845-363">Existuje několik způsobů, jak získat jQuery.</span><span class="sxs-lookup"><span data-stu-id="4b845-363">There are several ways to get jQuery.</span></span> <span data-ttu-id="4b845-364">V předchozím fragmentu kódu je načíst knihovnu ze sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="4b845-364">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="4b845-365">Tato ukázka volá všechny metody CRUD rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-365">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="4b845-366">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4b845-366">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4b845-367">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-367">Get a list of to-do items</span></span>

<span data-ttu-id="4b845-368">JQuery [ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `GET` žádosti na rozhraní API, které vrací JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="4b845-368">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4b845-369">`success` Funkce zpětného volání je vyvolána, pokud neproběhne.</span><span class="sxs-lookup"><span data-stu-id="4b845-369">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4b845-370">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="4b845-370">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4b845-371">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-371">Add a to-do item</span></span>

<span data-ttu-id="4b845-372">[Ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `POST` požadavek se úkol v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="4b845-372">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="4b845-373">`accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo.</span><span class="sxs-lookup"><span data-stu-id="4b845-373">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4b845-374">Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="4b845-374">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4b845-375">Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="4b845-375">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4b845-376">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="4b845-376">Update a to-do item</span></span>

<span data-ttu-id="4b845-377">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="4b845-377">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4b845-378">`url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="4b845-378">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4b845-379">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="4b845-379">Delete a to-do item</span></span>

<span data-ttu-id="4b845-380">Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="4b845-380">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="4b845-381">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4b845-381">Additional resources</span></span>

<span data-ttu-id="4b845-382">[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="4b845-382">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4b845-383">Zobrazit [stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4b845-383">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4b845-384">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="4b845-384">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4b845-385">Verzi tohoto kurzu na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="4b845-385">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a><span data-ttu-id="4b845-386">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4b845-386">Next steps</span></span>

<span data-ttu-id="4b845-387">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="4b845-387">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4b845-388">Vytvořte projekt webového rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="4b845-388">Create a web api project.</span></span>
> * <span data-ttu-id="4b845-389">Přidejte třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="4b845-389">Add a model class.</span></span>
> * <span data-ttu-id="4b845-390">Vytvořte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="4b845-390">Create the database context.</span></span>
> * <span data-ttu-id="4b845-391">Zaregistrujte kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="4b845-391">Register the database context.</span></span>
> * <span data-ttu-id="4b845-392">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4b845-392">Add a controller.</span></span>
> * <span data-ttu-id="4b845-393">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="4b845-393">Add CRUD methods.</span></span>
> * <span data-ttu-id="4b845-394">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="4b845-394">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4b845-395">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4b845-395">Specify return values.</span></span>
> * <span data-ttu-id="4b845-396">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="4b845-396">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4b845-397">Volání webového rozhraní api pomocí jQuery.</span><span class="sxs-lookup"><span data-stu-id="4b845-397">Call the web api with jQuery.</span></span>

<span data-ttu-id="4b845-398">Přejděte k dalšímu kurzu se naučíte generování stránek nápovědy rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="4b845-398">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

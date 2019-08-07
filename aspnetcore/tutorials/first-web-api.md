---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 855d05fa2b9c1a7572212c40adbe61bb396f4bac
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819831"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="486df-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="486df-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="486df-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="486df-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="486df-105">V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="486df-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="486df-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="486df-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="486df-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-107">Create a web API project.</span></span>
> * <span data-ttu-id="486df-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="486df-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="486df-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="486df-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="486df-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="486df-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="486df-111">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="486df-111">Call the web API with Postman.</span></span>

<span data-ttu-id="486df-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="486df-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="486df-113">Overview</span></span>

<span data-ttu-id="486df-114">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="486df-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="486df-115">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="486df-115">API</span></span> | <span data-ttu-id="486df-116">Popis</span><span class="sxs-lookup"><span data-stu-id="486df-116">Description</span></span> | <span data-ttu-id="486df-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="486df-117">Request body</span></span> | <span data-ttu-id="486df-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="486df-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="486df-119">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="486df-119">GET /api/TodoItems</span></span> | <span data-ttu-id="486df-120">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-120">Get all to-do items</span></span> | <span data-ttu-id="486df-121">Žádné</span><span class="sxs-lookup"><span data-stu-id="486df-121">None</span></span> | <span data-ttu-id="486df-122">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-122">Array of to-do items</span></span>|
|<span data-ttu-id="486df-123">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="486df-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="486df-124">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="486df-124">Get an item by ID</span></span> | <span data-ttu-id="486df-125">Žádná</span><span class="sxs-lookup"><span data-stu-id="486df-125">None</span></span> | <span data-ttu-id="486df-126">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-126">To-do item</span></span>|
|<span data-ttu-id="486df-127">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="486df-127">POST /api/TodoItems</span></span> | <span data-ttu-id="486df-128">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="486df-128">Add a new item</span></span> | <span data-ttu-id="486df-129">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-129">To-do item</span></span> | <span data-ttu-id="486df-130">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-130">To-do item</span></span> |
|<span data-ttu-id="486df-131">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="486df-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="486df-132">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="486df-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="486df-133">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-133">To-do item</span></span> | <span data-ttu-id="486df-134">Žádný</span><span class="sxs-lookup"><span data-stu-id="486df-134">None</span></span> |
|<span data-ttu-id="486df-135">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="486df-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="486df-136">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="486df-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="486df-137">Žádné</span><span class="sxs-lookup"><span data-stu-id="486df-137">None</span></span> | <span data-ttu-id="486df-138">Žádná</span><span class="sxs-lookup"><span data-stu-id="486df-138">None</span></span>|

<span data-ttu-id="486df-139">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="486df-139">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="486df-145">Požadavky</span><span class="sxs-lookup"><span data-stu-id="486df-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="486df-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="486df-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="486df-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="486df-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="486df-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="486df-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="486df-151">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="486df-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="486df-152">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="486df-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="486df-153">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="486df-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="486df-154">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="486df-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="486df-155">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="486df-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="486df-156">Nevybírejte možnost **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="486df-156">**Don't** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="486df-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="486df-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="486df-159">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="486df-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="486df-160">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="486df-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="486df-161">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="486df-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="486df-162">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="486df-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="486df-163">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="486df-163">The preceding commands:</span></span>

  * <span data-ttu-id="486df-164">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="486df-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="486df-165">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="486df-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="486df-166">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="486df-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="486df-167">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="486df-167">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="486df-169">Vyberte rozhraní > **API** > aplikace> .NET Core další.</span><span class="sxs-lookup"><span data-stu-id="486df-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="486df-171">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \* *.NET Core 3,0*.</span><span class="sxs-lookup"><span data-stu-id="486df-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="486df-172">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="486df-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="486df-174">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="486df-174">Test the API</span></span>

<span data-ttu-id="486df-175">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="486df-176">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="486df-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="486df-178">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="486df-179">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="486df-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="486df-180">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="486df-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="486df-181">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="486df-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="486df-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="486df-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="486df-183">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="486df-184">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/WeatherForecast ](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="486df-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="486df-185">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="486df-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="486df-186">Vyberte **Spustit** > **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="486df-187">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="486df-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="486df-188">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="486df-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="486df-189">Připojit `/WeatherForecast` na adresu URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="486df-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="486df-190">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="486df-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="486df-191">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="486df-191">Add a model class</span></span>

<span data-ttu-id="486df-192">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="486df-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="486df-193">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="486df-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="486df-195">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="486df-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="486df-196">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="486df-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="486df-197">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="486df-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="486df-198">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="486df-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="486df-199">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="486df-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="486df-200">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="486df-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="486df-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="486df-202">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="486df-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="486df-203">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="486df-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="486df-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="486df-205">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="486df-205">Right-click the project.</span></span> <span data-ttu-id="486df-206">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="486df-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="486df-207">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="486df-207">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="486df-209">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="486df-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="486df-210">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="486df-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="486df-211">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="486df-212">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="486df-213">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="486df-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="486df-214">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="486df-214">Add a database context</span></span>

<span data-ttu-id="486df-215">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="486df-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="486df-216">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="486df-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="486df-218">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="486df-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="486df-219">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="486df-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="486df-220">Zaškrtněte políčko **zahrnout předběžné verze** .</span><span class="sxs-lookup"><span data-stu-id="486df-220">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="486df-221">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="486df-221">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="486df-222">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer v 3.0.0-Preview** .</span><span class="sxs-lookup"><span data-stu-id="486df-222">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="486df-223">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="486df-223">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="486df-224">Pomocí předchozích pokynů přidejte `Microsoft.EntityFrameworkCore.InMemory` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="486df-224">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="486df-226">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="486df-226">Add the TodoContext database context</span></span>

* <span data-ttu-id="486df-227">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="486df-227">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="486df-228">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="486df-228">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="486df-229">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="486df-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="486df-230">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="486df-230">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="486df-231">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="486df-231">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="486df-232">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="486df-232">Register the database context</span></span>

<span data-ttu-id="486df-233">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="486df-233">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="486df-234">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="486df-234">The container provides the service to controllers.</span></span>

<span data-ttu-id="486df-235">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="486df-235">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="486df-236">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="486df-236">The preceding code:</span></span>

* <span data-ttu-id="486df-237">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="486df-237">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="486df-238">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="486df-238">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="486df-239">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="486df-239">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="486df-240">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="486df-240">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-241">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="486df-242">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="486df-242">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="486df-243">Vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="486df-243">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="486df-244">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="486df-244">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="486df-245">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="486df-245">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="486df-246">V **třídě modelu**vyberte **TodoItem (TodoAPI. Models)** .</span><span class="sxs-lookup"><span data-stu-id="486df-246">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="486df-247">Ve **třídě Context data**vyberte **TodoContext (TodoAPI. Models)** .</span><span class="sxs-lookup"><span data-stu-id="486df-247">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="486df-248">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="486df-248">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="486df-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="486df-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="486df-250">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="486df-250">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="486df-251">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="486df-251">The preceding commands:</span></span>

* <span data-ttu-id="486df-252">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="486df-252">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="486df-253">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="486df-253">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="486df-254">Generování uživatelského rozhraní `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="486df-254">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="486df-255">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="486df-255">The generated code:</span></span>

* <span data-ttu-id="486df-256">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="486df-256">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="486df-257">Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="486df-257">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="486df-258">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-258">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="486df-259">Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="486df-259">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="486df-260">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="486df-260">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="486df-261">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="486df-261">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="486df-262">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="486df-262">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="486df-263">Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="486df-263">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="486df-264">Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="486df-264">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="486df-265">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="486df-265">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="486df-266"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Metody:</span><span class="sxs-lookup"><span data-stu-id="486df-266">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="486df-267">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="486df-267">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="486df-268">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="486df-268">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="486df-269">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="486df-269">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="486df-270">Záhlaví Určuje identifikátor URI nově vytvořené položky. [](https://developer.mozilla.org/docs/Glossary/URI) `Location`</span><span class="sxs-lookup"><span data-stu-id="486df-270">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="486df-271">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="486df-271">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="486df-272">Odkazuje na `GetTodoItem` akci `Location` vytvoření identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="486df-272">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="486df-273">Klíčové slovo se používá k zamezení hardwarového kódování názvu `CreatedAtAction` akce ve volání. C# `nameof`</span><span class="sxs-lookup"><span data-stu-id="486df-273">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="486df-274">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="486df-274">Install Postman</span></span>

<span data-ttu-id="486df-275">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-275">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="486df-276">Nainstalujte [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="486df-276">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="486df-277">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-277">Start the web app.</span></span>
* <span data-ttu-id="486df-278">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="486df-278">Start Postman.</span></span>
* <span data-ttu-id="486df-279">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="486df-279">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="486df-280">Z **soubor > Nastavení** (\**Obecné* kartu), zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="486df-280">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="486df-281">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="486df-281">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="486df-282">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="486df-282">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="486df-283">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="486df-283">Create a new request.</span></span>
* <span data-ttu-id="486df-284">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="486df-284">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="486df-285">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="486df-285">Select the **Body** tab.</span></span>
* <span data-ttu-id="486df-286">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="486df-286">Select the **raw** radio button.</span></span>
* <span data-ttu-id="486df-287">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="486df-287">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="486df-288">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="486df-288">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="486df-289">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="486df-289">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="486df-291">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="486df-291">Test the location header URI</span></span>

* <span data-ttu-id="486df-292">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="486df-292">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="486df-293">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="486df-293">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="486df-295">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="486df-295">Set the method to GET.</span></span>
* <span data-ttu-id="486df-296">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`)</span><span class="sxs-lookup"><span data-stu-id="486df-296">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="486df-297">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="486df-297">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="486df-298">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="486df-298">Examine the GET methods</span></span>

<span data-ttu-id="486df-299">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="486df-299">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="486df-300">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="486df-300">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="486df-301">Příklad:</span><span class="sxs-lookup"><span data-stu-id="486df-301">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="486df-302">Odpověď podobná následující je vytvořena voláním metody `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="486df-302">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="486df-303">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="486df-303">Test Get with Postman</span></span>

* <span data-ttu-id="486df-304">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="486df-304">Create a new request.</span></span>
* <span data-ttu-id="486df-305">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="486df-305">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="486df-306">Nastavení adresy URL požadavku `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="486df-306">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="486df-307">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="486df-307">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="486df-308">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="486df-308">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="486df-309">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="486df-309">Select **Send**.</span></span>

<span data-ttu-id="486df-310">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="486df-310">This app uses an in-memory database.</span></span> <span data-ttu-id="486df-311">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="486df-311">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="486df-312">Pokud se nevrátí žádná data [](#post) , odešlete data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="486df-312">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="486df-313">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="486df-313">Routing and URL paths</span></span>

<span data-ttu-id="486df-314">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="486df-314">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="486df-315">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="486df-315">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="486df-316">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="486df-316">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="486df-317">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="486df-317">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="486df-318">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="486df-318">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="486df-319">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="486df-319">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="486df-320">`[HttpGet("products")]`Pokud má `[HttpGet]` atribut směrovací šablonu (například), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="486df-320">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="486df-321">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="486df-321">This sample doesn't use a template.</span></span> <span data-ttu-id="486df-322">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="486df-322">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="486df-323">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="486df-323">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="486df-324">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="486df-324">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="486df-325">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="486df-325">Return values</span></span>

<span data-ttu-id="486df-326">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="486df-326">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="486df-327">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="486df-327">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="486df-328">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="486df-328">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="486df-329">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="486df-329">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="486df-330">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="486df-330">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="486df-331">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="486df-331">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="486df-332">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="486df-332">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="486df-333">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="486df-333">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="486df-334">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="486df-334">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="486df-335">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="486df-335">The PutTodoItem method</span></span>

<span data-ttu-id="486df-336">Projděte `PutTodoItem` si metodu:</span><span class="sxs-lookup"><span data-stu-id="486df-336">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="486df-337">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="486df-337">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="486df-338">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="486df-338">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="486df-339">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="486df-339">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="486df-340">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="486df-340">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="486df-341">Pokud se zobrazí chyba při volání `PutTodoItem` `GET` , zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-341">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="486df-342">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="486df-342">Test the PutTodoItem method</span></span>

<span data-ttu-id="486df-343">Tato ukázka používá databázi v paměti, která musí být při každém spuštění aplikace inicializovaná.</span><span class="sxs-lookup"><span data-stu-id="486df-343">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="486df-344">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-344">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="486df-345">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-345">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="486df-346">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="486df-346">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="486df-347">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="486df-347">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="486df-349">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="486df-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="486df-350">Projděte `DeleteTodoItem` si metodu:</span><span class="sxs-lookup"><span data-stu-id="486df-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="486df-351">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="486df-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="486df-352">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="486df-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="486df-353">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="486df-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="486df-354">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="486df-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="486df-355">Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="486df-355">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="486df-356">Vyberte **odeslat**</span><span class="sxs-lookup"><span data-stu-id="486df-356">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="486df-357">Volání rozhraní API ze jQuery</span><span class="sxs-lookup"><span data-stu-id="486df-357">Call the API from jQuery</span></span>

<span data-ttu-id="486df-358">Viz [kurz: Zavolejte ASP.NET Core webového rozhraní API pomocí jQuery](xref:tutorials/web-api-jquery).</span><span class="sxs-lookup"><span data-stu-id="486df-358">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="486df-359">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="486df-359">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="486df-360">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-360">Create a web API project.</span></span>
> * <span data-ttu-id="486df-361">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="486df-361">Add a model class and a database context.</span></span>
> * <span data-ttu-id="486df-362">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="486df-362">Add a controller.</span></span>
> * <span data-ttu-id="486df-363">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="486df-363">Add CRUD methods.</span></span>
> * <span data-ttu-id="486df-364">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="486df-364">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="486df-365">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="486df-365">Specify return values.</span></span>
> * <span data-ttu-id="486df-366">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="486df-366">Call the web API with Postman.</span></span>
> * <span data-ttu-id="486df-367">Zavolejte webové rozhraní API pomocí jQuery.</span><span class="sxs-lookup"><span data-stu-id="486df-367">Call the web API with jQuery.</span></span>

<span data-ttu-id="486df-368">Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-368">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="486df-369">Přehled</span><span class="sxs-lookup"><span data-stu-id="486df-369">Overview</span></span>

<span data-ttu-id="486df-370">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="486df-370">This tutorial creates the following API:</span></span>

|<span data-ttu-id="486df-371">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="486df-371">API</span></span> | <span data-ttu-id="486df-372">Popis</span><span class="sxs-lookup"><span data-stu-id="486df-372">Description</span></span> | <span data-ttu-id="486df-373">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="486df-373">Request body</span></span> | <span data-ttu-id="486df-374">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="486df-374">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="486df-375">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="486df-375">GET /api/TodoItems</span></span> | <span data-ttu-id="486df-376">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-376">Get all to-do items</span></span> | <span data-ttu-id="486df-377">Žádné</span><span class="sxs-lookup"><span data-stu-id="486df-377">None</span></span> | <span data-ttu-id="486df-378">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-378">Array of to-do items</span></span>|
|<span data-ttu-id="486df-379">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="486df-379">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="486df-380">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="486df-380">Get an item by ID</span></span> | <span data-ttu-id="486df-381">Žádná</span><span class="sxs-lookup"><span data-stu-id="486df-381">None</span></span> | <span data-ttu-id="486df-382">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-382">To-do item</span></span>|
|<span data-ttu-id="486df-383">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="486df-383">POST /api/TodoItems</span></span> | <span data-ttu-id="486df-384">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="486df-384">Add a new item</span></span> | <span data-ttu-id="486df-385">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-385">To-do item</span></span> | <span data-ttu-id="486df-386">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-386">To-do item</span></span> |
|<span data-ttu-id="486df-387">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="486df-387">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="486df-388">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="486df-388">Update an existing item &nbsp;</span></span> | <span data-ttu-id="486df-389">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-389">To-do item</span></span> | <span data-ttu-id="486df-390">Žádný</span><span class="sxs-lookup"><span data-stu-id="486df-390">None</span></span> |
|<span data-ttu-id="486df-391">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="486df-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="486df-392">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="486df-392">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="486df-393">Žádné</span><span class="sxs-lookup"><span data-stu-id="486df-393">None</span></span> | <span data-ttu-id="486df-394">Žádná</span><span class="sxs-lookup"><span data-stu-id="486df-394">None</span></span>|

<span data-ttu-id="486df-395">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="486df-395">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="486df-401">Požadavky</span><span class="sxs-lookup"><span data-stu-id="486df-401">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-402">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-402">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="486df-403">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="486df-403">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="486df-404">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="486df-404">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="486df-405">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="486df-405">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="486df-407">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="486df-407">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="486df-408">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="486df-408">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="486df-409">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="486df-409">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="486df-410">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="486df-410">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="486df-411">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="486df-411">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="486df-412">Nevybírejte možnost **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="486df-412">**Don't** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="486df-414">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="486df-414">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="486df-415">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="486df-415">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="486df-416">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="486df-416">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="486df-417">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="486df-417">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="486df-418">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="486df-418">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="486df-419">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="486df-419">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="486df-420">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="486df-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="486df-421">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="486df-421">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="486df-423">Vyberte rozhraní > **API** > aplikace> .NET Core další.</span><span class="sxs-lookup"><span data-stu-id="486df-423">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="486df-425">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="486df-425">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="486df-426">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="486df-426">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="486df-428">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="486df-428">Test the API</span></span>

<span data-ttu-id="486df-429">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-429">The project template creates a `values` API.</span></span> <span data-ttu-id="486df-430">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="486df-430">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-431">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-431">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="486df-432">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-432">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="486df-433">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="486df-433">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="486df-434">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="486df-434">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="486df-435">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="486df-435">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="486df-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="486df-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="486df-437">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-437">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="486df-438">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="486df-438">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="486df-439">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="486df-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="486df-440">Vyberte **Spustit** > **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-440">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="486df-441">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="486df-441">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="486df-442">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="486df-442">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="486df-443">Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="486df-443">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="486df-444">Vrátí následující JSON:</span><span class="sxs-lookup"><span data-stu-id="486df-444">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="486df-445">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="486df-445">Add a model class</span></span>

<span data-ttu-id="486df-446">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="486df-446">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="486df-447">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="486df-447">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-448">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-448">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="486df-449">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="486df-449">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="486df-450">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="486df-450">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="486df-451">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="486df-451">Name the folder *Models*.</span></span>

* <span data-ttu-id="486df-452">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="486df-452">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="486df-453">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="486df-453">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="486df-454">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-454">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="486df-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="486df-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="486df-456">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="486df-456">Add a folder named *Models*.</span></span>

* <span data-ttu-id="486df-457">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-457">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="486df-458">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="486df-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="486df-459">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="486df-459">Right-click the project.</span></span> <span data-ttu-id="486df-460">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="486df-460">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="486df-461">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="486df-461">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="486df-463">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="486df-463">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="486df-464">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="486df-464">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="486df-465">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-465">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="486df-466">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-466">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="486df-467">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="486df-467">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="486df-468">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="486df-468">Add a database context</span></span>

<span data-ttu-id="486df-469">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="486df-469">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="486df-470">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="486df-470">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="486df-472">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="486df-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="486df-473">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="486df-473">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="486df-474">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="486df-474">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="486df-475">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="486df-475">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="486df-476">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-476">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="486df-477">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="486df-477">Register the database context</span></span>

<span data-ttu-id="486df-478">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="486df-478">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="486df-479">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="486df-479">The container provides the service to controllers.</span></span>

<span data-ttu-id="486df-480">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="486df-480">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="486df-481">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="486df-481">The preceding code:</span></span>

* <span data-ttu-id="486df-482">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="486df-482">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="486df-483">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="486df-483">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="486df-484">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="486df-484">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="486df-485">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="486df-485">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="486df-487">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="486df-487">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="486df-488">Vyberte možnost **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="486df-488">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="486df-489">V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="486df-489">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="486df-490">Název třídy *TodoController*a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="486df-490">Name the class *TodoController*, and select **Add**.</span></span>

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="486df-492">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="486df-492">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="486df-493">V *řadiče* složku, vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="486df-493">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="486df-494">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-494">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="486df-495">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="486df-495">The preceding code:</span></span>

* <span data-ttu-id="486df-496">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="486df-496">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="486df-497">Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="486df-497">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="486df-498">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-498">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="486df-499">Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="486df-499">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="486df-500">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="486df-500">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="486df-501">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="486df-501">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="486df-502">Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný.</span><span class="sxs-lookup"><span data-stu-id="486df-502">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="486df-503">Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="486df-503">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="486df-504">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-504">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="486df-505">Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.</span><span class="sxs-lookup"><span data-stu-id="486df-505">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="486df-506">Přidejte metody Get</span><span class="sxs-lookup"><span data-stu-id="486df-506">Add Get methods</span></span>

<span data-ttu-id="486df-507">Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:</span><span class="sxs-lookup"><span data-stu-id="486df-507">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="486df-508">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="486df-508">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="486df-509">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-509">Stop the app if it's still running.</span></span> <span data-ttu-id="486df-510">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="486df-510">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="486df-511">Testování aplikace pomocí volání dva koncové body v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="486df-511">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="486df-512">Příklad:</span><span class="sxs-lookup"><span data-stu-id="486df-512">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="486df-513">Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="486df-513">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="486df-514">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="486df-514">Routing and URL paths</span></span>

<span data-ttu-id="486df-515">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="486df-515">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="486df-516">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="486df-516">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="486df-517">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="486df-517">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="486df-518">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="486df-518">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="486df-519">V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="486df-519">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="486df-520">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="486df-520">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="486df-521">`[HttpGet("products")]`Pokud má `[HttpGet]` atribut směrovací šablonu (například), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="486df-521">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="486df-522">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="486df-522">This sample doesn't use a template.</span></span> <span data-ttu-id="486df-523">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="486df-523">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="486df-524">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="486df-524">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="486df-525">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="486df-525">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="486df-526">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="486df-526">Return values</span></span>

<span data-ttu-id="486df-527">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="486df-527">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="486df-528">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="486df-528">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="486df-529">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="486df-529">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="486df-530">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="486df-530">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="486df-531">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="486df-531">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="486df-532">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="486df-532">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="486df-533">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="486df-533">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="486df-534">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="486df-534">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="486df-535">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="486df-535">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="486df-536">Test GetTodoItems – metoda</span><span class="sxs-lookup"><span data-stu-id="486df-536">Test the GetTodoItems method</span></span>

<span data-ttu-id="486df-537">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-537">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="486df-538">Nainstalujte [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="486df-538">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="486df-539">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="486df-539">Start the web app.</span></span>
* <span data-ttu-id="486df-540">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="486df-540">Start Postman.</span></span>
* <span data-ttu-id="486df-541">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="486df-541">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="486df-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="486df-542">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="486df-543">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="486df-543">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="486df-544">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="486df-544">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="486df-545">V možnosti Předvolba **post** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="486df-545">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="486df-546">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="486df-546">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="486df-547">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="486df-547">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="486df-548">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="486df-548">Create a new request.</span></span>
  * <span data-ttu-id="486df-549">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="486df-549">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="486df-550">Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="486df-550">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="486df-551">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="486df-551">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="486df-552">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="486df-552">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="486df-553">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="486df-553">Select **Send**.</span></span>

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="486df-555">Přidání metody vytvoření</span><span class="sxs-lookup"><span data-stu-id="486df-555">Add a Create method</span></span>

<span data-ttu-id="486df-556">Přidejte následující `PostTodoItem` metodu do Controllers */TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="486df-556">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="486df-557">Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="486df-557">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="486df-558">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="486df-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="486df-559">`CreatedAtAction` Metody:</span><span class="sxs-lookup"><span data-stu-id="486df-559">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="486df-560">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="486df-560">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="486df-561">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="486df-561">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="486df-562">`Location` Přidá hlavičku k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="486df-562">Adds a `Location` header to the response.</span></span> <span data-ttu-id="486df-563">`Location` Záhlaví Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="486df-563">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="486df-564">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="486df-564">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="486df-565">Odkazuje na `GetTodoItem` akci `Location` vytvoření identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="486df-565">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="486df-566">Klíčové slovo se používá k zamezení hardwarového kódování názvu `CreatedAtAction` akce ve volání. C# `nameof`</span><span class="sxs-lookup"><span data-stu-id="486df-566">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="486df-567">Test PostTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="486df-567">Test the PostTodoItem method</span></span>

* <span data-ttu-id="486df-568">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="486df-568">Build the project.</span></span>
* <span data-ttu-id="486df-569">V nástroji Postman, nastavte jako metodu HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="486df-569">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="486df-570">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="486df-570">Select the **Body** tab.</span></span>
* <span data-ttu-id="486df-571">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="486df-571">Select the **raw** radio button.</span></span>
* <span data-ttu-id="486df-572">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="486df-572">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="486df-573">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="486df-573">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="486df-574">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="486df-574">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  <span data-ttu-id="486df-576">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání `PostTodoItem` metody nebude projekt zkompilován.</span><span class="sxs-lookup"><span data-stu-id="486df-576">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="486df-577">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="486df-577">Test the location header URI</span></span>

* <span data-ttu-id="486df-578">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="486df-578">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="486df-579">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="486df-579">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="486df-581">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="486df-581">Set the method to GET.</span></span>
* <span data-ttu-id="486df-582">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="486df-582">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="486df-583">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="486df-583">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="486df-584">Přidejte metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="486df-584">Add a PutTodoItem method</span></span>

<span data-ttu-id="486df-585">Přidejte následující `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="486df-585">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="486df-586">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="486df-586">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="486df-587">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="486df-587">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="486df-588">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="486df-588">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="486df-589">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="486df-589">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="486df-590">Pokud se zobrazí chyba při volání `PutTodoItem` `GET` , zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-590">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="486df-591">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="486df-591">Test the PutTodoItem method</span></span>

<span data-ttu-id="486df-592">Tato ukázka používá databázi v paměti, která musí být při každém spuštění aplikace inicializovaná.</span><span class="sxs-lookup"><span data-stu-id="486df-592">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="486df-593">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-593">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="486df-594">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="486df-594">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="486df-595">Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":</span><span class="sxs-lookup"><span data-stu-id="486df-595">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="486df-596">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="486df-596">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="486df-598">Přidejte metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="486df-598">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="486df-599">Přidejte následující `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="486df-599">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="486df-600">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="486df-600">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="486df-601">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="486df-601">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="486df-602">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="486df-602">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="486df-603">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="486df-603">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="486df-604">Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="486df-604">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="486df-605">Vyberte **odeslat**</span><span class="sxs-lookup"><span data-stu-id="486df-605">Select **Send**</span></span>

<span data-ttu-id="486df-606">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="486df-606">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="486df-607">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-607">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="486df-608">Volání rozhraní API pomocí jQuery</span><span class="sxs-lookup"><span data-stu-id="486df-608">Call the API with jQuery</span></span>

<span data-ttu-id="486df-609">V této části se přidá stránku HTML, který používá jQuery volat webové rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="486df-609">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="486df-610">jQuery zahájí požadavek a aktualizuje stránku s podrobnostmi o z odpovědi rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-610">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="486df-611">Nakonfigurujte aplikaci tak, aby sloužila pro [statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="486df-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="486df-612">Vytvoření *wwwroot* složku v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="486df-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="486df-613">Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="486df-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="486df-614">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="486df-615">Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="486df-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="486df-616">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="486df-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="486df-617">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="486df-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="486df-618">Otevřít *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="486df-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="486df-619">Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="486df-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="486df-620">Existuje několik způsobů, jak získat jQuery.</span><span class="sxs-lookup"><span data-stu-id="486df-620">There are several ways to get jQuery.</span></span> <span data-ttu-id="486df-621">V předchozím fragmentu kódu je načíst knihovnu ze sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="486df-621">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="486df-622">Tato ukázka volá všechny metody CRUD rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-622">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="486df-623">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="486df-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="486df-624">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-624">Get a list of to-do items</span></span>

<span data-ttu-id="486df-625">JQuery [ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `GET` žádosti na rozhraní API, které vrací JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="486df-625">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="486df-626">`success` Funkce zpětného volání je vyvolána, pokud neproběhne.</span><span class="sxs-lookup"><span data-stu-id="486df-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="486df-627">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="486df-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="486df-628">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-628">Add a to-do item</span></span>

<span data-ttu-id="486df-629">[Ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `POST` požadavek se úkol v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="486df-629">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="486df-630">`accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo.</span><span class="sxs-lookup"><span data-stu-id="486df-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="486df-631">Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="486df-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="486df-632">Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="486df-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="486df-633">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="486df-633">Update a to-do item</span></span>

<span data-ttu-id="486df-634">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="486df-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="486df-635">`url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="486df-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="486df-636">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="486df-636">Delete a to-do item</span></span>

<span data-ttu-id="486df-637">Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="486df-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="486df-638">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="486df-638">Additional resources</span></span>

<span data-ttu-id="486df-639">[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="486df-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="486df-640">Zobrazit [stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="486df-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="486df-641">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="486df-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="486df-642">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="486df-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 73e547b014d78dcbcbf1c887ebec16e0743d10b9
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294752"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="9c537-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c537-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="9c537-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="9c537-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="9c537-105">V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9c537-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9c537-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="9c537-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9c537-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-107">Create a web API project.</span></span>
> * <span data-ttu-id="9c537-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="9c537-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9c537-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="9c537-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="9c537-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="9c537-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="9c537-111">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="9c537-111">Call the web API with Postman.</span></span>

<span data-ttu-id="9c537-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="9c537-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="9c537-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="9c537-113">Overview</span></span>

<span data-ttu-id="9c537-114">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="9c537-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9c537-115">API</span><span class="sxs-lookup"><span data-stu-id="9c537-115">API</span></span> | <span data-ttu-id="9c537-116">Popis</span><span class="sxs-lookup"><span data-stu-id="9c537-116">Description</span></span> | <span data-ttu-id="9c537-117">Tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="9c537-117">Request body</span></span> | <span data-ttu-id="9c537-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="9c537-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="9c537-119">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9c537-119">GET /api/TodoItems</span></span> | <span data-ttu-id="9c537-120">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-120">Get all to-do items</span></span> | <span data-ttu-id="9c537-121">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-121">None</span></span> | <span data-ttu-id="9c537-122">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-122">Array of to-do items</span></span>|
|<span data-ttu-id="9c537-123">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9c537-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="9c537-124">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="9c537-124">Get an item by ID</span></span> | <span data-ttu-id="9c537-125">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-125">None</span></span> | <span data-ttu-id="9c537-126">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-126">To-do item</span></span>|
|<span data-ttu-id="9c537-127">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9c537-127">POST /api/TodoItems</span></span> | <span data-ttu-id="9c537-128">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="9c537-128">Add a new item</span></span> | <span data-ttu-id="9c537-129">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-129">To-do item</span></span> | <span data-ttu-id="9c537-130">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-130">To-do item</span></span> |
|<span data-ttu-id="9c537-131">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9c537-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="9c537-132">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9c537-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9c537-133">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-133">To-do item</span></span> | <span data-ttu-id="9c537-134">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-134">None</span></span> |
|<span data-ttu-id="9c537-135">Odstranit &nbsp;/api/TodoItems/{id} &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9c537-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="9c537-136">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9c537-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9c537-137">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-137">None</span></span> | <span data-ttu-id="9c537-138">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-138">None</span></span>|

<span data-ttu-id="9c537-139">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c537-139">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="9c537-145">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9c537-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c537-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c537-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c537-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="9c537-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="9c537-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c537-151">V nabídce **soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="9c537-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9c537-152">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="9c537-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9c537-153">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9c537-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9c537-154">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="9c537-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="9c537-155">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9c537-155">Select the **API** template and click **Create**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c537-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c537-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9c537-158">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9c537-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9c537-159">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="9c537-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9c537-160">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9c537-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="9c537-161">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9c537-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="9c537-162">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="9c537-162">The preceding commands:</span></span>

  * <span data-ttu-id="9c537-163">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9c537-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="9c537-164">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="9c537-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c537-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9c537-166">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="9c537-166">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9c537-168">V **části rozhraní .NET Core** > **App** > **API** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="9c537-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="9c537-170">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \* *.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="9c537-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="9c537-171">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9c537-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="9c537-173">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9c537-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="9c537-174">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="9c537-174">Test the API</span></span>

<span data-ttu-id="9c537-175">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="9c537-176">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c537-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9c537-178">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9c537-179">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="9c537-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="9c537-180">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9c537-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="9c537-181">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9c537-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c537-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c537-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9c537-183">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9c537-184">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/WeatherForecast ](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="9c537-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c537-185">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9c537-186">Vyberte **spustit** > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9c537-187">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="9c537-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9c537-188">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="9c537-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9c537-189">Připojit `/WeatherForecast` na adresu URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="9c537-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="9c537-190">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="9c537-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="9c537-191">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="9c537-191">Add a model class</span></span>

<span data-ttu-id="9c537-192">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="9c537-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9c537-193">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="9c537-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c537-195">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="9c537-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9c537-196">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="9c537-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9c537-197">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="9c537-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="9c537-198">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="9c537-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9c537-199">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9c537-200">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c537-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c537-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9c537-202">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="9c537-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9c537-203">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c537-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9c537-205">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="9c537-205">Right-click the project.</span></span> <span data-ttu-id="9c537-206">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="9c537-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9c537-207">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="9c537-207">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9c537-209">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="9c537-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9c537-210">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="9c537-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9c537-211">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="9c537-212">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="9c537-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9c537-213">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="9c537-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="9c537-214">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="9c537-214">Add a database context</span></span>

<span data-ttu-id="9c537-215">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="9c537-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9c537-216">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="9c537-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="9c537-218">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="9c537-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="9c537-219">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="9c537-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="9c537-220">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="9c537-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="9c537-221">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="9c537-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="9c537-222">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="9c537-223">Pomocí předchozích pokynů přidejte balíček NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="9c537-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="9c537-225">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="9c537-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="9c537-226">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="9c537-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9c537-227">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9c537-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9c537-229">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="9c537-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9c537-230">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="9c537-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="9c537-231">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="9c537-231">Register the database context</span></span>

<span data-ttu-id="9c537-232">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="9c537-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9c537-233">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="9c537-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="9c537-234">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="9c537-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="9c537-235">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="9c537-235">The preceding code:</span></span>

* <span data-ttu-id="9c537-236">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="9c537-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9c537-237">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="9c537-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9c537-238">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="9c537-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="9c537-239">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="9c537-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c537-241">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="9c537-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9c537-242">Vyberte **přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="9c537-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9c537-243">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="9c537-244">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="9c537-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="9c537-245">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="9c537-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="9c537-246">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="9c537-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="9c537-247">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9c537-248">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9c537-249">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9c537-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="9c537-250">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="9c537-250">The preceding commands:</span></span>

* <span data-ttu-id="9c537-251">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9c537-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="9c537-252">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="9c537-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="9c537-253">`TodoItemsController`generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9c537-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="9c537-254">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="9c537-254">The generated code:</span></span>

* <span data-ttu-id="9c537-255">Označí třídu atributem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="9c537-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="9c537-256">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9c537-257">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="9c537-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9c537-258">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9c537-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9c537-259">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9c537-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="9c537-260">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="9c537-260">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="9c537-261">Nahraďte příkaz return v `PostTodoItem` pro použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="9c537-261">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="9c537-262">Předchozí kód je metoda HTTP POST, jak je označena atributem [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="9c537-262">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="9c537-263">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="9c537-263">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9c537-264"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Metody:</span><span class="sxs-lookup"><span data-stu-id="9c537-264">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="9c537-265">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="9c537-265">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="9c537-266">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="9c537-266">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9c537-267">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9c537-267">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="9c537-268">Hlavička `Location` Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="9c537-268">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="9c537-269">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9c537-269">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9c537-270">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="9c537-270">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9c537-271">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="9c537-271">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="9c537-272">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="9c537-272">Install Postman</span></span>

<span data-ttu-id="9c537-273">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-273">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9c537-274">Nainstalujte [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="9c537-274">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="9c537-275">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-275">Start the web app.</span></span>
* <span data-ttu-id="9c537-276">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="9c537-276">Start Postman.</span></span>
* <span data-ttu-id="9c537-277">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="9c537-277">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="9c537-278">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="9c537-278">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="9c537-279">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9c537-279">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="9c537-280">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="9c537-280">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="9c537-281">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="9c537-281">Create a new request.</span></span>
* <span data-ttu-id="9c537-282">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="9c537-282">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9c537-283">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="9c537-283">Select the **Body** tab.</span></span>
* <span data-ttu-id="9c537-284">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="9c537-284">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9c537-285">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="9c537-285">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9c537-286">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="9c537-286">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9c537-287">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-287">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="9c537-289">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="9c537-289">Test the location header URI</span></span>

* <span data-ttu-id="9c537-290">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="9c537-290">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9c537-291">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="9c537-291">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="9c537-293">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="9c537-293">Set the method to GET.</span></span>
* <span data-ttu-id="9c537-294">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="9c537-294">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="9c537-295">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-295">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="9c537-296">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="9c537-296">Examine the GET methods</span></span>

<span data-ttu-id="9c537-297">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="9c537-297">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="9c537-298">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="9c537-298">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="9c537-299">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9c537-299">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="9c537-300">Odpověď podobná následující je vytvořena voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="9c537-300">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="9c537-301">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="9c537-301">Test Get with Postman</span></span>

* <span data-ttu-id="9c537-302">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="9c537-302">Create a new request.</span></span>
* <span data-ttu-id="9c537-303">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-303">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="9c537-304">Nastavení adresy URL požadavku `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9c537-304">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="9c537-305">Například `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="9c537-305">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="9c537-306">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="9c537-306">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9c537-307">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-307">Select **Send**.</span></span>

<span data-ttu-id="9c537-308">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="9c537-308">This app uses an in-memory database.</span></span> <span data-ttu-id="9c537-309">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="9c537-309">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="9c537-310">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c537-310">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="9c537-311">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="9c537-311">Routing and URL paths</span></span>

<span data-ttu-id="9c537-312">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9c537-312">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9c537-313">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="9c537-313">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9c537-314">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="9c537-314">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="9c537-315">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="9c537-315">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9c537-316">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="9c537-316">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="9c537-317">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="9c537-317">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9c537-318">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="9c537-318">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9c537-319">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="9c537-319">This sample doesn't use a template.</span></span> <span data-ttu-id="9c537-320">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9c537-320">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9c537-321">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="9c537-321">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9c537-322">Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.</span><span class="sxs-lookup"><span data-stu-id="9c537-322">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="9c537-323">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="9c537-323">Return values</span></span>

<span data-ttu-id="9c537-324">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="9c537-324">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9c537-325">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="9c537-325">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9c537-326">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="9c537-326">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9c537-327">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="9c537-327">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9c537-328">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="9c537-328">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9c537-329">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="9c537-329">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9c537-330">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="9c537-330">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="9c537-331">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="9c537-331">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9c537-332">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="9c537-332">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="9c537-333">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9c537-333">The PutTodoItem method</span></span>

<span data-ttu-id="9c537-334">Projděte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9c537-334">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="9c537-335">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9c537-335">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9c537-336">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9c537-336">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9c537-337">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="9c537-337">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9c537-338">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="9c537-338">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9c537-339">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="9c537-339">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="9c537-340">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="9c537-340">Test the PutTodoItem method</span></span>

<span data-ttu-id="9c537-341">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c537-341">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="9c537-342">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="9c537-342">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9c537-343">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="9c537-343">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9c537-344">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="9c537-344">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9c537-345">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="9c537-345">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="9c537-347">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9c537-347">The DeleteTodoItem method</span></span>

<span data-ttu-id="9c537-348">Projděte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9c537-348">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="9c537-349">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="9c537-349">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="9c537-350">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="9c537-350">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9c537-351">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="9c537-351">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9c537-352">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="9c537-352">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="9c537-353">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-353">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="9c537-354">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="9c537-354">Call the web API with JavaScript</span></span>

<span data-ttu-id="9c537-355">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="9c537-355">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9c537-356">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="9c537-356">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9c537-357">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-357">Create a web API project.</span></span>
> * <span data-ttu-id="9c537-358">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="9c537-358">Add a model class and a database context.</span></span>
> * <span data-ttu-id="9c537-359">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9c537-359">Add a controller.</span></span>
> * <span data-ttu-id="9c537-360">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="9c537-360">Add CRUD methods.</span></span>
> * <span data-ttu-id="9c537-361">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9c537-361">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="9c537-362">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="9c537-362">Specify return values.</span></span>
> * <span data-ttu-id="9c537-363">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="9c537-363">Call the web API with Postman.</span></span>
> * <span data-ttu-id="9c537-364">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="9c537-364">Call the web API with JavaScript.</span></span>

<span data-ttu-id="9c537-365">Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="9c537-365">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="9c537-366">Přehled</span><span class="sxs-lookup"><span data-stu-id="9c537-366">Overview</span></span>

<span data-ttu-id="9c537-367">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="9c537-367">This tutorial creates the following API:</span></span>

|<span data-ttu-id="9c537-368">API</span><span class="sxs-lookup"><span data-stu-id="9c537-368">API</span></span> | <span data-ttu-id="9c537-369">Popis</span><span class="sxs-lookup"><span data-stu-id="9c537-369">Description</span></span> | <span data-ttu-id="9c537-370">Tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="9c537-370">Request body</span></span> | <span data-ttu-id="9c537-371">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="9c537-371">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="9c537-372">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9c537-372">GET /api/TodoItems</span></span> | <span data-ttu-id="9c537-373">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-373">Get all to-do items</span></span> | <span data-ttu-id="9c537-374">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-374">None</span></span> | <span data-ttu-id="9c537-375">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-375">Array of to-do items</span></span>|
|<span data-ttu-id="9c537-376">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9c537-376">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="9c537-377">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="9c537-377">Get an item by ID</span></span> | <span data-ttu-id="9c537-378">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-378">None</span></span> | <span data-ttu-id="9c537-379">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-379">To-do item</span></span>|
|<span data-ttu-id="9c537-380">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="9c537-380">POST /api/TodoItems</span></span> | <span data-ttu-id="9c537-381">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="9c537-381">Add a new item</span></span> | <span data-ttu-id="9c537-382">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-382">To-do item</span></span> | <span data-ttu-id="9c537-383">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-383">To-do item</span></span> |
|<span data-ttu-id="9c537-384">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="9c537-384">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="9c537-385">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9c537-385">Update an existing item &nbsp;</span></span> | <span data-ttu-id="9c537-386">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-386">To-do item</span></span> | <span data-ttu-id="9c537-387">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-387">None</span></span> |
|<span data-ttu-id="9c537-388">Odstranit &nbsp;/api/TodoItems/{id} &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9c537-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="9c537-389">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="9c537-389">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="9c537-390">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-390">None</span></span> | <span data-ttu-id="9c537-391">Žádné</span><span class="sxs-lookup"><span data-stu-id="9c537-391">None</span></span>|

<span data-ttu-id="9c537-392">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c537-392">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="9c537-398">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9c537-398">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-399">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c537-400">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c537-400">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c537-401">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-401">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="9c537-402">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="9c537-402">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-403">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c537-404">V nabídce **soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="9c537-404">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9c537-405">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="9c537-405">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="9c537-406">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9c537-406">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="9c537-407">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="9c537-407">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="9c537-408">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9c537-408">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="9c537-409">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="9c537-409">**Don't** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c537-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c537-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9c537-412">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9c537-412">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="9c537-413">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="9c537-413">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="9c537-414">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9c537-414">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="9c537-415">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="9c537-415">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="9c537-416">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9c537-416">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c537-417">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-417">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9c537-418">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="9c537-418">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="9c537-420">V **části rozhraní .NET Core** > **App** > **API** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="9c537-420">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="9c537-422">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="9c537-422">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="9c537-423">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9c537-423">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="9c537-425">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="9c537-425">Test the API</span></span>

<span data-ttu-id="9c537-426">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-426">The project template creates a `values` API.</span></span> <span data-ttu-id="9c537-427">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c537-427">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-428">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9c537-429">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-429">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9c537-430">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="9c537-430">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="9c537-431">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9c537-431">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="9c537-432">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9c537-432">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c537-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c537-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9c537-434">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-434">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="9c537-435">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="9c537-435">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c537-436">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9c537-437">Vyberte **spustit** > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-437">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="9c537-438">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="9c537-438">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="9c537-439">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="9c537-439">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="9c537-440">Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="9c537-440">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="9c537-441">Vrátí následující JSON:</span><span class="sxs-lookup"><span data-stu-id="9c537-441">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="9c537-442">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="9c537-442">Add a model class</span></span>

<span data-ttu-id="9c537-443">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="9c537-443">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="9c537-444">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="9c537-444">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-445">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-445">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c537-446">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="9c537-446">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="9c537-447">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="9c537-447">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9c537-448">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="9c537-448">Name the folder *Models*.</span></span>

* <span data-ttu-id="9c537-449">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="9c537-449">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9c537-450">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-450">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="9c537-451">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-451">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c537-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c537-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9c537-453">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="9c537-453">Add a folder named *Models*.</span></span>

* <span data-ttu-id="9c537-454">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-454">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c537-455">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9c537-456">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="9c537-456">Right-click the project.</span></span> <span data-ttu-id="9c537-457">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="9c537-457">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="9c537-458">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="9c537-458">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="9c537-460">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="9c537-460">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="9c537-461">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="9c537-461">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="9c537-462">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-462">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="9c537-463">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="9c537-463">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="9c537-464">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="9c537-464">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="9c537-465">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="9c537-465">Add a database context</span></span>

<span data-ttu-id="9c537-466">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="9c537-466">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="9c537-467">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="9c537-467">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c537-469">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="9c537-469">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="9c537-470">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-470">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9c537-471">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-471">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9c537-472">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="9c537-472">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="9c537-473">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-473">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="9c537-474">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="9c537-474">Register the database context</span></span>

<span data-ttu-id="9c537-475">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="9c537-475">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9c537-476">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="9c537-476">The container provides the service to controllers.</span></span>

<span data-ttu-id="9c537-477">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="9c537-477">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="9c537-478">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="9c537-478">The preceding code:</span></span>

* <span data-ttu-id="9c537-479">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="9c537-479">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="9c537-480">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="9c537-480">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="9c537-481">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="9c537-481">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="9c537-482">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="9c537-482">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c537-484">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="9c537-484">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="9c537-485">Vyberte **přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="9c537-485">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="9c537-486">V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="9c537-486">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="9c537-487">Název třídy *TodoController*a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-487">Name the class *TodoController*, and select **Add**.</span></span>

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9c537-489">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-489">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9c537-490">V *řadiče* složku, vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="9c537-490">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="9c537-491">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-491">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="9c537-492">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="9c537-492">The preceding code:</span></span>

* <span data-ttu-id="9c537-493">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="9c537-493">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="9c537-494">Označí třídu atributem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="9c537-494">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="9c537-495">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-495">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="9c537-496">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="9c537-496">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="9c537-497">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9c537-497">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="9c537-498">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9c537-498">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="9c537-499">Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný.</span><span class="sxs-lookup"><span data-stu-id="9c537-499">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="9c537-500">Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="9c537-500">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="9c537-501">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-501">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="9c537-502">Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.</span><span class="sxs-lookup"><span data-stu-id="9c537-502">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="9c537-503">Přidejte metody Get</span><span class="sxs-lookup"><span data-stu-id="9c537-503">Add Get methods</span></span>

<span data-ttu-id="9c537-504">Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:</span><span class="sxs-lookup"><span data-stu-id="9c537-504">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="9c537-505">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="9c537-505">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="9c537-506">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-506">Stop the app if it's still running.</span></span> <span data-ttu-id="9c537-507">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="9c537-507">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="9c537-508">Testování aplikace pomocí volání dva koncové body v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9c537-508">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="9c537-509">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9c537-509">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="9c537-510">Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="9c537-510">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="9c537-511">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="9c537-511">Routing and URL paths</span></span>

<span data-ttu-id="9c537-512">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9c537-512">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="9c537-513">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="9c537-513">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="9c537-514">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="9c537-514">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="9c537-515">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="9c537-515">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="9c537-516">V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="9c537-516">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="9c537-517">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="9c537-517">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="9c537-518">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="9c537-518">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="9c537-519">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="9c537-519">This sample doesn't use a template.</span></span> <span data-ttu-id="9c537-520">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="9c537-520">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="9c537-521">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="9c537-521">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="9c537-522">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="9c537-522">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="9c537-523">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="9c537-523">Return values</span></span>

<span data-ttu-id="9c537-524">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="9c537-524">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="9c537-525">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="9c537-525">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="9c537-526">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="9c537-526">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="9c537-527">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="9c537-527">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="9c537-528">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="9c537-528">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="9c537-529">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="9c537-529">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="9c537-530">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="9c537-530">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="9c537-531">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="9c537-531">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="9c537-532">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="9c537-532">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="9c537-533">Test GetTodoItems – metoda</span><span class="sxs-lookup"><span data-stu-id="9c537-533">Test the GetTodoItems method</span></span>

<span data-ttu-id="9c537-534">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-534">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="9c537-535">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="9c537-535">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="9c537-536">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c537-536">Start the web app.</span></span>
* <span data-ttu-id="9c537-537">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="9c537-537">Start Postman.</span></span>
* <span data-ttu-id="9c537-538">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="9c537-538">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c537-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c537-539">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c537-540">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="9c537-540">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9c537-541">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9c537-541">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="9c537-542">Na **kartě > \*\* **Předvolby** (karta**Obecné\*\* ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="9c537-542">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="9c537-543">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="9c537-543">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="9c537-544">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9c537-544">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="9c537-545">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="9c537-545">Create a new request.</span></span>
  * <span data-ttu-id="9c537-546">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-546">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="9c537-547">Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="9c537-547">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="9c537-548">Například `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="9c537-548">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="9c537-549">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="9c537-549">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="9c537-550">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-550">Select **Send**.</span></span>

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="9c537-552">Přidání metody vytvoření</span><span class="sxs-lookup"><span data-stu-id="9c537-552">Add a Create method</span></span>

<span data-ttu-id="9c537-553">Do *Controllers/TodoController. cs*přidejte následující metodu `PostTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9c537-553">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="9c537-554">Předchozí kód je metoda HTTP POST, jak je označena atributem [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="9c537-554">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="9c537-555">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="9c537-555">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="9c537-556">`CreatedAtAction` Metody:</span><span class="sxs-lookup"><span data-stu-id="9c537-556">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="9c537-557">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="9c537-557">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="9c537-558">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="9c537-558">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="9c537-559">Přidá hlavičku `Location` k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9c537-559">Adds a `Location` header to the response.</span></span> <span data-ttu-id="9c537-560">Hlavička `Location` Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="9c537-560">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="9c537-561">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9c537-561">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="9c537-562">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="9c537-562">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="9c537-563">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="9c537-563">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="9c537-564">Test PostTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="9c537-564">Test the PostTodoItem method</span></span>

* <span data-ttu-id="9c537-565">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="9c537-565">Build the project.</span></span>
* <span data-ttu-id="9c537-566">V nástroji Postman, nastavte jako metodu HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="9c537-566">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="9c537-567">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="9c537-567">Select the **Body** tab.</span></span>
* <span data-ttu-id="9c537-568">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="9c537-568">Select the **raw** radio button.</span></span>
* <span data-ttu-id="9c537-569">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="9c537-569">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="9c537-570">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="9c537-570">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="9c537-571">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-571">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  <span data-ttu-id="9c537-573">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody `PostTodoItem` nezkompiluje projekt.</span><span class="sxs-lookup"><span data-stu-id="9c537-573">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="9c537-574">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="9c537-574">Test the location header URI</span></span>

* <span data-ttu-id="9c537-575">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="9c537-575">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="9c537-576">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="9c537-576">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="9c537-578">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="9c537-578">Set the method to GET.</span></span>
* <span data-ttu-id="9c537-579">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="9c537-579">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="9c537-580">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-580">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="9c537-581">Přidejte metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="9c537-581">Add a PutTodoItem method</span></span>

<span data-ttu-id="9c537-582">Přidejte následující `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="9c537-582">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="9c537-583">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="9c537-583">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="9c537-584">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9c537-584">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="9c537-585">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="9c537-585">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="9c537-586">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="9c537-586">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="9c537-587">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="9c537-587">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="9c537-588">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="9c537-588">Test the PutTodoItem method</span></span>

<span data-ttu-id="9c537-589">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c537-589">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="9c537-590">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="9c537-590">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="9c537-591">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="9c537-591">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="9c537-592">Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":</span><span class="sxs-lookup"><span data-stu-id="9c537-592">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="9c537-593">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="9c537-593">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="9c537-595">Přidejte metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="9c537-595">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="9c537-596">Přidejte následující `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="9c537-596">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="9c537-597">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="9c537-597">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="9c537-598">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="9c537-598">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="9c537-599">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="9c537-599">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="9c537-600">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="9c537-600">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="9c537-601">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="9c537-601">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="9c537-602">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="9c537-602">Select **Send**.</span></span>

<span data-ttu-id="9c537-603">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="9c537-603">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="9c537-604">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-604">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="9c537-605">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="9c537-605">Call the web API with JavaScript</span></span>

<span data-ttu-id="9c537-606">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-606">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="9c537-607">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="9c537-607">jQuery initiates the request.</span></span> <span data-ttu-id="9c537-608">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-608">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="9c537-609">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="9c537-609">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="9c537-610">Vytvoření *wwwroot* složku v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="9c537-610">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="9c537-611">Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="9c537-611">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="9c537-612">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-612">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="9c537-613">Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="9c537-613">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="9c537-614">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9c537-614">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="9c537-615">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="9c537-615">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="9c537-616">Otevřít *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9c537-616">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="9c537-617">Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="9c537-617">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="9c537-618">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-618">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="9c537-619">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c537-619">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="9c537-620">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-620">Get a list of to-do items</span></span>

<span data-ttu-id="9c537-621">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="9c537-621">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="9c537-622">`success` Funkce zpětného volání je vyvolána, pokud neproběhne.</span><span class="sxs-lookup"><span data-stu-id="9c537-622">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="9c537-623">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="9c537-623">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="9c537-624">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-624">Add a to-do item</span></span>

<span data-ttu-id="9c537-625">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="9c537-625">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="9c537-626">`accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo.</span><span class="sxs-lookup"><span data-stu-id="9c537-626">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="9c537-627">Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="9c537-627">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="9c537-628">Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="9c537-628">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="9c537-629">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="9c537-629">Update a to-do item</span></span>

<span data-ttu-id="9c537-630">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="9c537-630">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="9c537-631">`url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="9c537-631">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="9c537-632">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="9c537-632">Delete a to-do item</span></span>

<span data-ttu-id="9c537-633">Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="9c537-633">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="9c537-634">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="9c537-634">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="9c537-635">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="9c537-635">Additional resources</span></span>

<span data-ttu-id="9c537-636">[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="9c537-636">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="9c537-637">Zobrazit [stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="9c537-637">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="9c537-638">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="9c537-638">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="9c537-639">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="9c537-639">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

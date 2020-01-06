---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 3bf930d19684e84365f0ff0255fccd2939fb3f39
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354921"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="77c3a-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77c3a-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="77c3a-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="77c3a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="77c3a-105">V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="77c3a-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77c3a-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="77c3a-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="77c3a-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-107">Create a web API project.</span></span>
> * <span data-ttu-id="77c3a-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="77c3a-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="77c3a-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="77c3a-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="77c3a-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="77c3a-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="77c3a-111">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="77c3a-111">Call the web API with Postman.</span></span>

<span data-ttu-id="77c3a-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="77c3a-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="77c3a-113">Overview</span></span>

<span data-ttu-id="77c3a-114">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="77c3a-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="77c3a-115">API</span><span class="sxs-lookup"><span data-stu-id="77c3a-115">API</span></span> | <span data-ttu-id="77c3a-116">Popis</span><span class="sxs-lookup"><span data-stu-id="77c3a-116">Description</span></span> | <span data-ttu-id="77c3a-117">Tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="77c3a-117">Request body</span></span> | <span data-ttu-id="77c3a-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="77c3a-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="77c3a-119">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="77c3a-119">GET /api/TodoItems</span></span> | <span data-ttu-id="77c3a-120">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-120">Get all to-do items</span></span> | <span data-ttu-id="77c3a-121">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-121">None</span></span> | <span data-ttu-id="77c3a-122">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-122">Array of to-do items</span></span>|
|<span data-ttu-id="77c3a-123">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="77c3a-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="77c3a-124">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="77c3a-124">Get an item by ID</span></span> | <span data-ttu-id="77c3a-125">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-125">None</span></span> | <span data-ttu-id="77c3a-126">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-126">To-do item</span></span>|
|<span data-ttu-id="77c3a-127">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="77c3a-127">POST /api/TodoItems</span></span> | <span data-ttu-id="77c3a-128">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="77c3a-128">Add a new item</span></span> | <span data-ttu-id="77c3a-129">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-129">To-do item</span></span> | <span data-ttu-id="77c3a-130">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-130">To-do item</span></span> |
|<span data-ttu-id="77c3a-131">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="77c3a-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="77c3a-132">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="77c3a-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="77c3a-133">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-133">To-do item</span></span> | <span data-ttu-id="77c3a-134">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-134">None</span></span> |
|<span data-ttu-id="77c3a-135">Odstranit &nbsp;/api/TodoItems/{id} &nbsp;</span><span class="sxs-lookup"><span data-stu-id="77c3a-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="77c3a-136">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="77c3a-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="77c3a-137">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-137">None</span></span> | <span data-ttu-id="77c3a-138">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-138">None</span></span>|

<span data-ttu-id="77c3a-139">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-139">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="77c3a-145">Požadavky</span><span class="sxs-lookup"><span data-stu-id="77c3a-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="77c3a-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77c3a-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="77c3a-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="77c3a-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="77c3a-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c3a-151">V nabídce **soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="77c3a-152">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="77c3a-153">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="77c3a-154">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="77c3a-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="77c3a-155">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-155">Select the **API** template and click **Create**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="77c3a-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77c3a-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="77c3a-158">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="77c3a-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="77c3a-159">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="77c3a-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="77c3a-160">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="77c3a-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="77c3a-161">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="77c3a-162">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="77c3a-162">The preceding commands:</span></span>

  * <span data-ttu-id="77c3a-163">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="77c3a-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="77c3a-164">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="77c3a-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="77c3a-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="77c3a-166">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-166">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="77c3a-168">V **části rozhraní .NET Core** > **App** > **API** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="77c3a-170">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \* *.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="77c3a-171">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="77c3a-173">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="77c3a-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="77c3a-174">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="77c3a-174">Test the API</span></span>

<span data-ttu-id="77c3a-175">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="77c3a-176">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="77c3a-178">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="77c3a-179">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="77c3a-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="77c3a-180">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="77c3a-181">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="77c3a-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77c3a-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="77c3a-183">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="77c3a-184">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/WeatherForecast ](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="77c3a-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="77c3a-185">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="77c3a-186">Vyberte **spustit** > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="77c3a-187">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="77c3a-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="77c3a-188">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="77c3a-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="77c3a-189">Připojit `/WeatherForecast` na adresu URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="77c3a-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="77c3a-190">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="77c3a-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="77c3a-191">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="77c3a-191">Add a model class</span></span>

<span data-ttu-id="77c3a-192">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="77c3a-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="77c3a-193">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="77c3a-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c3a-195">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="77c3a-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="77c3a-196">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="77c3a-197">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="77c3a-198">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="77c3a-199">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="77c3a-200">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="77c3a-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77c3a-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="77c3a-202">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="77c3a-203">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="77c3a-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="77c3a-205">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="77c3a-205">Right-click the project.</span></span> <span data-ttu-id="77c3a-206">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="77c3a-207">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-207">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="77c3a-209">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="77c3a-210">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="77c3a-211">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="77c3a-212">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="77c3a-213">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="77c3a-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="77c3a-214">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="77c3a-214">Add a database context</span></span>

<span data-ttu-id="77c3a-215">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="77c3a-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="77c3a-216">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="77c3a-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="77c3a-218">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="77c3a-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="77c3a-219">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="77c3a-220">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="77c3a-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="77c3a-221">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="77c3a-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="77c3a-222">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="77c3a-223">Pomocí předchozích pokynů přidejte balíček NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="77c3a-225">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="77c3a-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="77c3a-226">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="77c3a-227">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="77c3a-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="77c3a-229">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="77c3a-230">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="77c3a-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="77c3a-231">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="77c3a-231">Register the database context</span></span>

<span data-ttu-id="77c3a-232">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="77c3a-233">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c3a-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="77c3a-234">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="77c3a-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="77c3a-235">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="77c3a-235">The preceding code:</span></span>

* <span data-ttu-id="77c3a-236">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="77c3a-237">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="77c3a-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="77c3a-238">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="77c3a-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="77c3a-239">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="77c3a-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c3a-241">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="77c3a-242">Vyberte **přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="77c3a-243">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="77c3a-244">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="77c3a-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="77c3a-245">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="77c3a-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="77c3a-246">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="77c3a-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="77c3a-247">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="77c3a-248">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="77c3a-249">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="77c3a-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="77c3a-250">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="77c3a-250">The preceding commands:</span></span>

* <span data-ttu-id="77c3a-251">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="77c3a-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="77c3a-252">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="77c3a-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="77c3a-253">`TodoItemsController`generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="77c3a-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="77c3a-254">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="77c3a-254">The generated code:</span></span>

* <span data-ttu-id="77c3a-255">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="77c3a-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="77c3a-256">Označí třídu atributem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="77c3a-256">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="77c3a-257">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="77c3a-258">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="77c3a-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="77c3a-259">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="77c3a-260">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="77c3a-261">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="77c3a-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="77c3a-262">Nahraďte příkaz return v `PostTodoItem` pro použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="77c3a-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="77c3a-263">Předchozí kód je metoda HTTP POST, jak je označena atributem [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="77c3a-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="77c3a-264">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c3a-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="77c3a-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Metody:</span><span class="sxs-lookup"><span data-stu-id="77c3a-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="77c3a-266">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="77c3a-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="77c3a-267">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="77c3a-268">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="77c3a-269">Hlavička `Location` Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="77c3a-270">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="77c3a-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="77c3a-271">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="77c3a-272">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="77c3a-273">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="77c3a-273">Install Postman</span></span>

<span data-ttu-id="77c3a-274">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="77c3a-275">Nainstalujte [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="77c3a-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="77c3a-276">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-276">Start the web app.</span></span>
* <span data-ttu-id="77c3a-277">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="77c3a-277">Start Postman.</span></span>
* <span data-ttu-id="77c3a-278">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="77c3a-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="77c3a-279">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="77c3a-280">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="77c3a-281">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="77c3a-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="77c3a-282">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="77c3a-282">Create a new request.</span></span>
* <span data-ttu-id="77c3a-283">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="77c3a-284">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="77c3a-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="77c3a-285">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="77c3a-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="77c3a-286">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="77c3a-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="77c3a-287">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="77c3a-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="77c3a-288">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-288">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="77c3a-290">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="77c3a-290">Test the location header URI</span></span>

* <span data-ttu-id="77c3a-291">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="77c3a-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="77c3a-292">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="77c3a-292">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="77c3a-294">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="77c3a-294">Set the method to GET.</span></span>
* <span data-ttu-id="77c3a-295">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="77c3a-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="77c3a-296">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="77c3a-297">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="77c3a-297">Examine the GET methods</span></span>

<span data-ttu-id="77c3a-298">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="77c3a-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="77c3a-299">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="77c3a-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="77c3a-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="77c3a-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="77c3a-301">Odpověď podobná následující je vytvořena voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="77c3a-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="77c3a-302">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="77c3a-302">Test Get with Postman</span></span>

* <span data-ttu-id="77c3a-303">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="77c3a-303">Create a new request.</span></span>
* <span data-ttu-id="77c3a-304">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="77c3a-305">Nastavení adresy URL požadavku `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="77c3a-306">Například `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="77c3a-307">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="77c3a-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="77c3a-308">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-308">Select **Send**.</span></span>

<span data-ttu-id="77c3a-309">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="77c3a-309">This app uses an in-memory database.</span></span> <span data-ttu-id="77c3a-310">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="77c3a-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="77c3a-311">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="77c3a-312">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="77c3a-312">Routing and URL paths</span></span>

<span data-ttu-id="77c3a-313">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="77c3a-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="77c3a-314">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="77c3a-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="77c3a-315">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="77c3a-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="77c3a-316">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="77c3a-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="77c3a-317">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="77c3a-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="77c3a-318">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="77c3a-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="77c3a-319">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="77c3a-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="77c3a-320">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="77c3a-320">This sample doesn't use a template.</span></span> <span data-ttu-id="77c3a-321">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="77c3a-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="77c3a-322">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="77c3a-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="77c3a-323">Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="77c3a-324">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="77c3a-324">Return values</span></span>

<span data-ttu-id="77c3a-325">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="77c3a-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="77c3a-326">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="77c3a-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="77c3a-327">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="77c3a-328">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="77c3a-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="77c3a-329">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="77c3a-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="77c3a-330">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="77c3a-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="77c3a-331">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="77c3a-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="77c3a-332">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="77c3a-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="77c3a-333">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="77c3a-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="77c3a-334">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="77c3a-334">The PutTodoItem method</span></span>

<span data-ttu-id="77c3a-335">Projděte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="77c3a-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="77c3a-336">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="77c3a-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="77c3a-337">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="77c3a-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="77c3a-338">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="77c3a-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="77c3a-339">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="77c3a-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="77c3a-340">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="77c3a-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="77c3a-341">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="77c3a-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="77c3a-342">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="77c3a-343">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="77c3a-344">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="77c3a-345">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="77c3a-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="77c3a-346">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="77c3a-346">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="77c3a-348">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="77c3a-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="77c3a-349">Projděte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="77c3a-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="77c3a-350">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="77c3a-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="77c3a-351">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="77c3a-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="77c3a-352">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="77c3a-353">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="77c3a-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="77c3a-354">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-354">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="77c3a-355">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="77c3a-355">Call the web API with JavaScript</span></span>

<span data-ttu-id="77c3a-356">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="77c3a-356">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77c3a-357">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="77c3a-357">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="77c3a-358">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-358">Create a web API project.</span></span>
> * <span data-ttu-id="77c3a-359">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="77c3a-359">Add a model class and a database context.</span></span>
> * <span data-ttu-id="77c3a-360">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-360">Add a controller.</span></span>
> * <span data-ttu-id="77c3a-361">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="77c3a-361">Add CRUD methods.</span></span>
> * <span data-ttu-id="77c3a-362">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="77c3a-362">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="77c3a-363">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="77c3a-363">Specify return values.</span></span>
> * <span data-ttu-id="77c3a-364">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="77c3a-364">Call the web API with Postman.</span></span>
> * <span data-ttu-id="77c3a-365">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="77c3a-365">Call the web API with JavaScript.</span></span>

<span data-ttu-id="77c3a-366">Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-366">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="77c3a-367">Přehled</span><span class="sxs-lookup"><span data-stu-id="77c3a-367">Overview</span></span>

<span data-ttu-id="77c3a-368">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="77c3a-368">This tutorial creates the following API:</span></span>

|<span data-ttu-id="77c3a-369">API</span><span class="sxs-lookup"><span data-stu-id="77c3a-369">API</span></span> | <span data-ttu-id="77c3a-370">Popis</span><span class="sxs-lookup"><span data-stu-id="77c3a-370">Description</span></span> | <span data-ttu-id="77c3a-371">Tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="77c3a-371">Request body</span></span> | <span data-ttu-id="77c3a-372">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="77c3a-372">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="77c3a-373">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="77c3a-373">GET /api/TodoItems</span></span> | <span data-ttu-id="77c3a-374">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-374">Get all to-do items</span></span> | <span data-ttu-id="77c3a-375">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-375">None</span></span> | <span data-ttu-id="77c3a-376">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-376">Array of to-do items</span></span>|
|<span data-ttu-id="77c3a-377">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="77c3a-377">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="77c3a-378">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="77c3a-378">Get an item by ID</span></span> | <span data-ttu-id="77c3a-379">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-379">None</span></span> | <span data-ttu-id="77c3a-380">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-380">To-do item</span></span>|
|<span data-ttu-id="77c3a-381">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="77c3a-381">POST /api/TodoItems</span></span> | <span data-ttu-id="77c3a-382">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="77c3a-382">Add a new item</span></span> | <span data-ttu-id="77c3a-383">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-383">To-do item</span></span> | <span data-ttu-id="77c3a-384">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-384">To-do item</span></span> |
|<span data-ttu-id="77c3a-385">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="77c3a-385">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="77c3a-386">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="77c3a-386">Update an existing item &nbsp;</span></span> | <span data-ttu-id="77c3a-387">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-387">To-do item</span></span> | <span data-ttu-id="77c3a-388">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-388">None</span></span> |
|<span data-ttu-id="77c3a-389">Odstranit &nbsp;/api/TodoItems/{id} &nbsp;</span><span class="sxs-lookup"><span data-stu-id="77c3a-389">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="77c3a-390">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="77c3a-390">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="77c3a-391">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-391">None</span></span> | <span data-ttu-id="77c3a-392">Žádné</span><span class="sxs-lookup"><span data-stu-id="77c3a-392">None</span></span>|

<span data-ttu-id="77c3a-393">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-393">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="77c3a-399">Požadavky</span><span class="sxs-lookup"><span data-stu-id="77c3a-399">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-400">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-400">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="77c3a-401">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77c3a-401">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="77c3a-402">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-402">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="77c3a-403">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="77c3a-403">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-404">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c3a-405">V nabídce **soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-405">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="77c3a-406">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-406">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="77c3a-407">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-407">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="77c3a-408">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="77c3a-408">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="77c3a-409">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-409">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="77c3a-410">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-410">**Don't** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="77c3a-412">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77c3a-412">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="77c3a-413">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="77c3a-413">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="77c3a-414">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="77c3a-414">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="77c3a-415">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="77c3a-415">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="77c3a-416">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="77c3a-416">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="77c3a-417">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-417">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="77c3a-418">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-418">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="77c3a-419">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-419">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="77c3a-421">V **části rozhraní .NET Core** > **App** > **API** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-421">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="77c3a-423">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-423">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="77c3a-424">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-424">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="77c3a-426">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="77c3a-426">Test the API</span></span>

<span data-ttu-id="77c3a-427">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-427">The project template creates a `values` API.</span></span> <span data-ttu-id="77c3a-428">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-428">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-429">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-429">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="77c3a-430">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-430">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="77c3a-431">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="77c3a-431">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="77c3a-432">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-432">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="77c3a-433">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-433">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="77c3a-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77c3a-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="77c3a-435">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-435">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="77c3a-436">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="77c3a-436">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="77c3a-437">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-437">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="77c3a-438">Vyberte **spustit** > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-438">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="77c3a-439">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="77c3a-439">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="77c3a-440">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="77c3a-440">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="77c3a-441">Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="77c3a-441">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="77c3a-442">Vrátí následující JSON:</span><span class="sxs-lookup"><span data-stu-id="77c3a-442">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="77c3a-443">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="77c3a-443">Add a model class</span></span>

<span data-ttu-id="77c3a-444">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="77c3a-444">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="77c3a-445">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="77c3a-445">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-446">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-446">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c3a-447">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="77c3a-447">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="77c3a-448">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-448">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="77c3a-449">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-449">Name the folder *Models*.</span></span>

* <span data-ttu-id="77c3a-450">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-450">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="77c3a-451">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-451">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="77c3a-452">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-452">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="77c3a-453">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77c3a-453">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="77c3a-454">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-454">Add a folder named *Models*.</span></span>

* <span data-ttu-id="77c3a-455">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-455">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="77c3a-456">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-456">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="77c3a-457">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="77c3a-457">Right-click the project.</span></span> <span data-ttu-id="77c3a-458">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-458">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="77c3a-459">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-459">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="77c3a-461">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-461">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="77c3a-462">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-462">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="77c3a-463">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-463">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="77c3a-464">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-464">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="77c3a-465">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="77c3a-465">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="77c3a-466">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="77c3a-466">Add a database context</span></span>

<span data-ttu-id="77c3a-467">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="77c3a-467">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="77c3a-468">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="77c3a-468">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-469">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c3a-470">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-470">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="77c3a-471">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-471">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="77c3a-472">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-472">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="77c3a-473">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-473">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="77c3a-474">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-474">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="77c3a-475">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="77c3a-475">Register the database context</span></span>

<span data-ttu-id="77c3a-476">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-476">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="77c3a-477">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="77c3a-477">The container provides the service to controllers.</span></span>

<span data-ttu-id="77c3a-478">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="77c3a-478">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="77c3a-479">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="77c3a-479">The preceding code:</span></span>

* <span data-ttu-id="77c3a-480">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-480">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="77c3a-481">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="77c3a-481">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="77c3a-482">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="77c3a-482">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="77c3a-483">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="77c3a-483">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c3a-485">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-485">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="77c3a-486">Vyberte **přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-486">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="77c3a-487">V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="77c3a-487">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="77c3a-488">Název třídy *TodoController*a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-488">Name the class *TodoController*, and select **Add**.</span></span>

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="77c3a-490">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="77c3a-491">V *řadiče* složku, vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-491">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="77c3a-492">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="77c3a-493">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="77c3a-493">The preceding code:</span></span>

* <span data-ttu-id="77c3a-494">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="77c3a-494">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="77c3a-495">Označí třídu atributem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="77c3a-495">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="77c3a-496">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-496">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="77c3a-497">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="77c3a-497">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="77c3a-498">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-498">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="77c3a-499">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-499">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="77c3a-500">Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný.</span><span class="sxs-lookup"><span data-stu-id="77c3a-500">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="77c3a-501">Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c3a-501">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="77c3a-502">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-502">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="77c3a-503">Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.</span><span class="sxs-lookup"><span data-stu-id="77c3a-503">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="77c3a-504">Přidejte metody Get</span><span class="sxs-lookup"><span data-stu-id="77c3a-504">Add Get methods</span></span>

<span data-ttu-id="77c3a-505">Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:</span><span class="sxs-lookup"><span data-stu-id="77c3a-505">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="77c3a-506">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="77c3a-506">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="77c3a-507">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-507">Stop the app if it's still running.</span></span> <span data-ttu-id="77c3a-508">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="77c3a-508">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="77c3a-509">Testování aplikace pomocí volání dva koncové body v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="77c3a-509">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="77c3a-510">Příklad:</span><span class="sxs-lookup"><span data-stu-id="77c3a-510">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="77c3a-511">Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="77c3a-511">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="77c3a-512">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="77c3a-512">Routing and URL paths</span></span>

<span data-ttu-id="77c3a-513">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="77c3a-513">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="77c3a-514">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="77c3a-514">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="77c3a-515">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="77c3a-515">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="77c3a-516">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="77c3a-516">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="77c3a-517">V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="77c3a-517">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="77c3a-518">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="77c3a-518">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="77c3a-519">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="77c3a-519">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="77c3a-520">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="77c3a-520">This sample doesn't use a template.</span></span> <span data-ttu-id="77c3a-521">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="77c3a-521">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="77c3a-522">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="77c3a-522">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="77c3a-523">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-523">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="77c3a-524">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="77c3a-524">Return values</span></span>

<span data-ttu-id="77c3a-525">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="77c3a-525">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="77c3a-526">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="77c3a-526">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="77c3a-527">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-527">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="77c3a-528">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="77c3a-528">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="77c3a-529">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="77c3a-529">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="77c3a-530">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="77c3a-530">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="77c3a-531">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="77c3a-531">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="77c3a-532">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="77c3a-532">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="77c3a-533">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="77c3a-533">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="77c3a-534">Test GetTodoItems – metoda</span><span class="sxs-lookup"><span data-stu-id="77c3a-534">Test the GetTodoItems method</span></span>

<span data-ttu-id="77c3a-535">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-535">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="77c3a-536">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="77c3a-536">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="77c3a-537">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77c3a-537">Start the web app.</span></span>
* <span data-ttu-id="77c3a-538">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="77c3a-538">Start Postman.</span></span>
* <span data-ttu-id="77c3a-539">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-539">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77c3a-540">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77c3a-540">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77c3a-541">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-541">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="77c3a-542">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="77c3a-542">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="77c3a-543">Na **kartě > \*\* **Předvolby** (karta**Obecné\*\* ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-543">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="77c3a-544">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="77c3a-544">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="77c3a-545">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-545">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="77c3a-546">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="77c3a-546">Create a new request.</span></span>
  * <span data-ttu-id="77c3a-547">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-547">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="77c3a-548">Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-548">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="77c3a-549">Například `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-549">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="77c3a-550">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="77c3a-550">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="77c3a-551">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-551">Select **Send**.</span></span>

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="77c3a-553">Přidání metody vytvoření</span><span class="sxs-lookup"><span data-stu-id="77c3a-553">Add a Create method</span></span>

<span data-ttu-id="77c3a-554">Do *Controllers/TodoController. cs*přidejte následující metodu `PostTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="77c3a-554">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="77c3a-555">Předchozí kód je metoda HTTP POST, jak je označena atributem [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="77c3a-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="77c3a-556">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="77c3a-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="77c3a-557">`CreatedAtAction` Metody:</span><span class="sxs-lookup"><span data-stu-id="77c3a-557">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="77c3a-558">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="77c3a-558">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="77c3a-559">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="77c3a-559">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="77c3a-560">Přidá hlavičku `Location` k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-560">Adds a `Location` header to the response.</span></span> <span data-ttu-id="77c3a-561">Hlavička `Location` Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-561">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="77c3a-562">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="77c3a-562">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="77c3a-563">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-563">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="77c3a-564">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-564">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="77c3a-565">Test PostTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="77c3a-565">Test the PostTodoItem method</span></span>

* <span data-ttu-id="77c3a-566">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="77c3a-566">Build the project.</span></span>
* <span data-ttu-id="77c3a-567">V nástroji Postman, nastavte jako metodu HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-567">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="77c3a-568">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="77c3a-568">Select the **Body** tab.</span></span>
* <span data-ttu-id="77c3a-569">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="77c3a-569">Select the **raw** radio button.</span></span>
* <span data-ttu-id="77c3a-570">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="77c3a-570">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="77c3a-571">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="77c3a-571">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="77c3a-572">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-572">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  <span data-ttu-id="77c3a-574">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody `PostTodoItem` nezkompiluje projekt.</span><span class="sxs-lookup"><span data-stu-id="77c3a-574">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="77c3a-575">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="77c3a-575">Test the location header URI</span></span>

* <span data-ttu-id="77c3a-576">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="77c3a-576">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="77c3a-577">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="77c3a-577">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="77c3a-579">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="77c3a-579">Set the method to GET.</span></span>
* <span data-ttu-id="77c3a-580">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="77c3a-580">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="77c3a-581">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-581">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="77c3a-582">Přidejte metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="77c3a-582">Add a PutTodoItem method</span></span>

<span data-ttu-id="77c3a-583">Přidejte následující `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="77c3a-583">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="77c3a-584">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="77c3a-584">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="77c3a-585">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="77c3a-585">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="77c3a-586">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="77c3a-586">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="77c3a-587">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="77c3a-587">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="77c3a-588">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="77c3a-588">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="77c3a-589">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="77c3a-589">Test the PutTodoItem method</span></span>

<span data-ttu-id="77c3a-590">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="77c3a-590">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="77c3a-591">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-591">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="77c3a-592">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="77c3a-592">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="77c3a-593">Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":</span><span class="sxs-lookup"><span data-stu-id="77c3a-593">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="77c3a-594">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="77c3a-594">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="77c3a-596">Přidejte metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="77c3a-596">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="77c3a-597">Přidejte následující `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="77c3a-597">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="77c3a-598">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="77c3a-598">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="77c3a-599">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="77c3a-599">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="77c3a-600">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="77c3a-600">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="77c3a-601">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-601">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="77c3a-602">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="77c3a-602">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="77c3a-603">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="77c3a-603">Select **Send**.</span></span>

<span data-ttu-id="77c3a-604">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="77c3a-604">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="77c3a-605">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-605">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="77c3a-606">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="77c3a-606">Call the web API with JavaScript</span></span>

<span data-ttu-id="77c3a-607">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-607">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="77c3a-608">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="77c3a-608">jQuery initiates the request.</span></span> <span data-ttu-id="77c3a-609">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-609">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="77c3a-610">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="77c3a-610">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="77c3a-611">Vytvoření *wwwroot* složku v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="77c3a-611">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="77c3a-612">Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="77c3a-612">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="77c3a-613">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-613">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="77c3a-614">Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="77c3a-614">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="77c3a-615">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="77c3a-615">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="77c3a-616">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="77c3a-616">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="77c3a-617">Otevřít *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="77c3a-617">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="77c3a-618">Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="77c3a-618">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="77c3a-619">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-619">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="77c3a-620">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="77c3a-620">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="77c3a-621">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-621">Get a list of to-do items</span></span>

<span data-ttu-id="77c3a-622">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="77c3a-622">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="77c3a-623">`success` Funkce zpětného volání je vyvolána, pokud neproběhne.</span><span class="sxs-lookup"><span data-stu-id="77c3a-623">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="77c3a-624">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="77c3a-624">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="77c3a-625">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-625">Add a to-do item</span></span>

<span data-ttu-id="77c3a-626">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="77c3a-626">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="77c3a-627">`accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo.</span><span class="sxs-lookup"><span data-stu-id="77c3a-627">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="77c3a-628">Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="77c3a-628">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="77c3a-629">Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="77c3a-629">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="77c3a-630">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="77c3a-630">Update a to-do item</span></span>

<span data-ttu-id="77c3a-631">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="77c3a-631">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="77c3a-632">`url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="77c3a-632">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="77c3a-633">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="77c3a-633">Delete a to-do item</span></span>

<span data-ttu-id="77c3a-634">Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="77c3a-634">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="77c3a-635">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="77c3a-635">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="77c3a-636">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="77c3a-636">Additional resources</span></span>

<span data-ttu-id="77c3a-637">[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="77c3a-637">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="77c3a-638">Zobrazit [stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="77c3a-638">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="77c3a-639">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="77c3a-639">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="77c3a-640">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="77c3a-640">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

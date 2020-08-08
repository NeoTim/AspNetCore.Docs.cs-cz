---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: ad6eac246e5bc7039158981bbe96036389512e4f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019232"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="30928-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="30928-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="30928-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)a [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="30928-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="30928-105">V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="30928-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="30928-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="30928-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="30928-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-107">Create a web API project.</span></span>
> * <span data-ttu-id="30928-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="30928-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="30928-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="30928-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="30928-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="30928-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="30928-111">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="30928-111">Call the web API with Postman.</span></span>

<span data-ttu-id="30928-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="30928-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="30928-113">Overview</span></span>

<span data-ttu-id="30928-114">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="30928-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="30928-115">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="30928-115">API</span></span> | <span data-ttu-id="30928-116">Popis</span><span class="sxs-lookup"><span data-stu-id="30928-116">Description</span></span> | <span data-ttu-id="30928-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="30928-117">Request body</span></span> | <span data-ttu-id="30928-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="30928-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="30928-119">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-119">Get all to-do items</span></span> | <span data-ttu-id="30928-120">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-120">None</span></span> | <span data-ttu-id="30928-121">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="30928-122">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="30928-122">Get an item by ID</span></span> | <span data-ttu-id="30928-123">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-123">None</span></span> | <span data-ttu-id="30928-124">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="30928-125">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="30928-125">Add a new item</span></span> | <span data-ttu-id="30928-126">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-126">To-do item</span></span> | <span data-ttu-id="30928-127">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="30928-128">Aktualizovat existující položku&nbsp;</span><span class="sxs-lookup"><span data-stu-id="30928-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="30928-129">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-129">To-do item</span></span> | <span data-ttu-id="30928-130">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-130">None</span></span> |
|<span data-ttu-id="30928-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="30928-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="30928-132">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="30928-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="30928-133">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-133">None</span></span> | <span data-ttu-id="30928-134">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-134">None</span></span>|

<span data-ttu-id="30928-135">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="30928-135">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="30928-141">Požadavky</span><span class="sxs-lookup"><span data-stu-id="30928-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="30928-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30928-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30928-144">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="30928-145">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="30928-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30928-147">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="30928-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="30928-148">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="30928-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="30928-149">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="30928-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="30928-150">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="30928-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="30928-151">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="30928-151">Select the **API** template and click **Create**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="30928-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30928-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="30928-154">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="30928-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="30928-155">Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="30928-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="30928-156">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="30928-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="30928-157">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="30928-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="30928-158">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="30928-158">The preceding commands:</span></span>

  * <span data-ttu-id="30928-159">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="30928-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="30928-160">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="30928-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30928-161">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="30928-162">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="30928-162">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="30928-164">V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="30928-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="30928-165">V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next** .</span><span class="sxs-lookup"><span data-stu-id="30928-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Výběr šablony rozhraní API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="30928-167">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="30928-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="30928-168">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="30928-168">Select **Next**.</span></span>

* <span data-ttu-id="30928-169">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="30928-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="30928-171">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="30928-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="30928-172">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="30928-172">Test the API</span></span>

<span data-ttu-id="30928-173">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="30928-174">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30928-176">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="30928-177">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="30928-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="30928-178">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="30928-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="30928-179">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="30928-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="30928-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30928-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="30928-181">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="30928-182">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="30928-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30928-183">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="30928-184">Vyberte **Spustit**  >  **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="30928-185">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="30928-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="30928-186">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="30928-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="30928-187">Připojit `/WeatherForecast` k adrese URL (změnit adresu URL na `https://localhost:<port>/WeatherForecast` )</span><span class="sxs-lookup"><span data-stu-id="30928-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="30928-188">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="30928-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="30928-189">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="30928-189">Add a model class</span></span>

<span data-ttu-id="30928-190">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="30928-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="30928-191">Model pro tuto aplikaci je jediná `TodoItem` Třída.</span><span class="sxs-lookup"><span data-stu-id="30928-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30928-193">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="30928-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="30928-194">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="30928-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="30928-195">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="30928-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="30928-196">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="30928-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="30928-197">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="30928-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="30928-198">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="30928-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30928-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="30928-200">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="30928-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="30928-201">`TodoItem`Do složky *modely* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30928-202">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="30928-203">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="30928-203">Right-click the project.</span></span> <span data-ttu-id="30928-204">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="30928-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="30928-205">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="30928-205">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="30928-207">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="30928-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="30928-208">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="30928-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="30928-209">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="30928-210">`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="30928-211">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="30928-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="30928-212">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="30928-212">Add a database context</span></span>

<span data-ttu-id="30928-213">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="30928-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="30928-214">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="30928-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="30928-216">Přidání balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="30928-216">Add NuGet packages</span></span>

* <span data-ttu-id="30928-217">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="30928-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="30928-218">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="30928-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="30928-219">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="30928-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="30928-220">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="30928-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="30928-221">Pomocí předchozích pokynů přidejte balíček NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="30928-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="30928-223">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="30928-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="30928-224">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="30928-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="30928-225">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="30928-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="30928-226">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="30928-227">Přidejte `TodoContext` třídu do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="30928-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="30928-228">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="30928-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="30928-229">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="30928-229">Register the database context</span></span>

<span data-ttu-id="30928-230">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="30928-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="30928-231">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="30928-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="30928-232">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="30928-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="30928-233">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="30928-233">The preceding code:</span></span>

* <span data-ttu-id="30928-234">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="30928-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="30928-235">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="30928-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="30928-236">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="30928-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="30928-237">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="30928-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30928-239">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="30928-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="30928-240">Vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="30928-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="30928-241">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="30928-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="30928-242">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="30928-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="30928-243">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="30928-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="30928-244">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="30928-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="30928-245">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="30928-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="30928-246">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="30928-247">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="30928-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="30928-248">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="30928-248">The preceding commands:</span></span>

* <span data-ttu-id="30928-249">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="30928-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="30928-250">Nainstaluje modul generování uživatelského rozhraní ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="30928-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="30928-251">Generování uživatelského rozhraní `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="30928-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="30928-252">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="30928-252">The generated code:</span></span>

* <span data-ttu-id="30928-253">Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="30928-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="30928-254">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="30928-255">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="30928-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="30928-256">Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="30928-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="30928-257">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="30928-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="30928-258">Šablony ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="30928-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="30928-259">Řadiče se zobrazeními jsou zahrnuta `[action]` v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="30928-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="30928-260">Řadiče API neobsahují `[action]` šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="30928-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="30928-261">Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy.</span><span class="sxs-lookup"><span data-stu-id="30928-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="30928-262">To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="30928-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="30928-263">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="30928-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="30928-264">Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="30928-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="30928-265">Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="30928-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="30928-266">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="30928-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="30928-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="30928-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="30928-268">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="30928-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="30928-269">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="30928-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="30928-270">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="30928-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="30928-271">`Location`Záhlaví Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="30928-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="30928-272">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="30928-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="30928-273">Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="30928-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="30928-274">`nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="30928-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="30928-275">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="30928-275">Install Postman</span></span>

<span data-ttu-id="30928-276">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="30928-277">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="30928-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="30928-278">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-278">Start the web app.</span></span>
* <span data-ttu-id="30928-279">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="30928-279">Start Postman.</span></span>
* <span data-ttu-id="30928-280">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="30928-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="30928-281">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="30928-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="30928-282">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="30928-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="30928-283">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="30928-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="30928-284">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="30928-284">Create a new request.</span></span>
* <span data-ttu-id="30928-285">Nastavte metodu HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="30928-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="30928-286">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="30928-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="30928-287">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="30928-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="30928-288">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="30928-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="30928-289">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="30928-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="30928-290">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="30928-290">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="30928-292">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="30928-292">Test the location header URI</span></span>

* <span data-ttu-id="30928-293">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="30928-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="30928-294">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="30928-294">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* <span data-ttu-id="30928-296">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="30928-296">Set the method to GET.</span></span>
* <span data-ttu-id="30928-297">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="30928-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="30928-298">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="30928-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="30928-299">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="30928-299">Examine the GET methods</span></span>

<span data-ttu-id="30928-300">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="30928-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="30928-301">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="30928-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="30928-302">Například:</span><span class="sxs-lookup"><span data-stu-id="30928-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="30928-303">Odpověď podobná následující je vytvořena voláním metody `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="30928-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="30928-304">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="30928-304">Test Get with Postman</span></span>

* <span data-ttu-id="30928-305">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="30928-305">Create a new request.</span></span>
* <span data-ttu-id="30928-306">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="30928-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="30928-307">Nastavte adresu URL požadavku na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="30928-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="30928-308">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="30928-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="30928-309">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="30928-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="30928-310">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="30928-310">Select **Send**.</span></span>

<span data-ttu-id="30928-311">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="30928-311">This app uses an in-memory database.</span></span> <span data-ttu-id="30928-312">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="30928-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="30928-313">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="30928-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="30928-314">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="30928-314">Routing and URL paths</span></span>

<span data-ttu-id="30928-315">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="30928-315">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="30928-316">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="30928-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="30928-317">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="30928-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="30928-318">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="30928-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="30928-319">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="30928-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="30928-320">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="30928-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="30928-321">Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="30928-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="30928-322">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="30928-322">This sample doesn't use a template.</span></span> <span data-ttu-id="30928-323">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="30928-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="30928-324">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="30928-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="30928-325">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="30928-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="30928-326">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="30928-326">Return values</span></span>

<span data-ttu-id="30928-327">Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="30928-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="30928-328">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="30928-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="30928-329">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="30928-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="30928-330">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="30928-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="30928-331">`ActionResult`návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="30928-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="30928-332">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="30928-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="30928-333">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> kód chyby 404.</span><span class="sxs-lookup"><span data-stu-id="30928-333">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="30928-334">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="30928-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="30928-335">`item`Výsledkem vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="30928-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="30928-336">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-336">The PutTodoItem method</span></span>

<span data-ttu-id="30928-337">Prohlédněte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="30928-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="30928-338">`PutTodoItem`se podobá `PostTodoItem` , s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="30928-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="30928-339">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="30928-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="30928-340">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="30928-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="30928-341">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="30928-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="30928-342">Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="30928-343">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="30928-344">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="30928-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="30928-345">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="30928-346">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="30928-347">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="30928-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="30928-348">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="30928-348">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="30928-350">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="30928-351">Prohlédněte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="30928-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="30928-352">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="30928-353">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="30928-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="30928-354">Nastavte metodu na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="30928-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="30928-355">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="30928-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="30928-356">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="30928-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="30928-357">Zabránit navýšení příspěvků</span><span class="sxs-lookup"><span data-stu-id="30928-357">Prevent over-posting</span></span>

<span data-ttu-id="30928-358">V současné době ukázková aplikace zveřejňuje celý `TodoItem` objekt.</span><span class="sxs-lookup"><span data-stu-id="30928-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="30928-359">Produkční aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu.</span><span class="sxs-lookup"><span data-stu-id="30928-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="30928-360">Je to několik důvodů na pozadí a zabezpečení je hlavní.</span><span class="sxs-lookup"><span data-stu-id="30928-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="30928-361">Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="30928-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="30928-362">**DTO** se používá v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="30928-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="30928-363">DTO se dá použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="30928-363">A DTO may be used to:</span></span>

* <span data-ttu-id="30928-364">Zabránit přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="30928-364">Prevent over-posting.</span></span>
* <span data-ttu-id="30928-365">Skrytí vlastností, které klienti nemají zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="30928-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="30928-366">Vynechejte některé vlastnosti, aby se snížila velikost datové části.</span><span class="sxs-lookup"><span data-stu-id="30928-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="30928-367">Ploché grafy objektů, které obsahují vnořené objekty.</span><span class="sxs-lookup"><span data-stu-id="30928-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="30928-368">Ploché grafy objektů můžou být pro klienty pohodlnější.</span><span class="sxs-lookup"><span data-stu-id="30928-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="30928-369">Chcete-li předvést DTO přístup, aktualizujte `TodoItem` třídu tak, aby obsahovala tajné pole:</span><span class="sxs-lookup"><span data-stu-id="30928-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="30928-370">Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.</span><span class="sxs-lookup"><span data-stu-id="30928-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="30928-371">Ověřte, že můžete publikovat a získat pole tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="30928-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="30928-372">Vytvoření modelu DTO:</span><span class="sxs-lookup"><span data-stu-id="30928-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="30928-373">Aktualizujte, `TodoItemsController` aby se použil `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="30928-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="30928-374">Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="30928-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="30928-375">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="30928-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="30928-376">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="30928-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="30928-377">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="30928-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="30928-378">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-378">Create a web API project.</span></span>
> * <span data-ttu-id="30928-379">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="30928-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="30928-380">Přidejte kontroler.</span><span class="sxs-lookup"><span data-stu-id="30928-380">Add a controller.</span></span>
> * <span data-ttu-id="30928-381">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="30928-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="30928-382">Nakonfigurujte směrování a cesty URL.</span><span class="sxs-lookup"><span data-stu-id="30928-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="30928-383">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="30928-383">Specify return values.</span></span>
> * <span data-ttu-id="30928-384">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="30928-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="30928-385">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="30928-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="30928-386">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="30928-387">Přehled</span><span class="sxs-lookup"><span data-stu-id="30928-387">Overview</span></span>

<span data-ttu-id="30928-388">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="30928-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="30928-389">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="30928-389">API</span></span> | <span data-ttu-id="30928-390">Popis</span><span class="sxs-lookup"><span data-stu-id="30928-390">Description</span></span> | <span data-ttu-id="30928-391">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="30928-391">Request body</span></span> | <span data-ttu-id="30928-392">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="30928-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="30928-393">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="30928-393">GET /api/TodoItems</span></span> | <span data-ttu-id="30928-394">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-394">Get all to-do items</span></span> | <span data-ttu-id="30928-395">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-395">None</span></span> | <span data-ttu-id="30928-396">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-396">Array of to-do items</span></span>|
|<span data-ttu-id="30928-397">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="30928-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="30928-398">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="30928-398">Get an item by ID</span></span> | <span data-ttu-id="30928-399">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-399">None</span></span> | <span data-ttu-id="30928-400">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-400">To-do item</span></span>|
|<span data-ttu-id="30928-401">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="30928-401">POST /api/TodoItems</span></span> | <span data-ttu-id="30928-402">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="30928-402">Add a new item</span></span> | <span data-ttu-id="30928-403">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-403">To-do item</span></span> | <span data-ttu-id="30928-404">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-404">To-do item</span></span> |
|<span data-ttu-id="30928-405">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="30928-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="30928-406">Aktualizovat existující položku&nbsp;</span><span class="sxs-lookup"><span data-stu-id="30928-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="30928-407">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-407">To-do item</span></span> | <span data-ttu-id="30928-408">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-408">None</span></span> |
|<span data-ttu-id="30928-409">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="30928-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="30928-410">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="30928-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="30928-411">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-411">None</span></span> | <span data-ttu-id="30928-412">Žádné</span><span class="sxs-lookup"><span data-stu-id="30928-412">None</span></span>|

<span data-ttu-id="30928-413">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="30928-413">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="30928-419">Požadavky</span><span class="sxs-lookup"><span data-stu-id="30928-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="30928-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30928-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30928-422">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="30928-423">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="30928-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30928-425">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="30928-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="30928-426">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="30928-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="30928-427">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="30928-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="30928-428">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="30928-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="30928-429">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="30928-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="30928-430">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="30928-430">**Don't** select **Enable Docker Support**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="30928-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30928-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="30928-433">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="30928-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="30928-434">Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="30928-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="30928-435">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="30928-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="30928-436">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="30928-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="30928-437">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="30928-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30928-438">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="30928-439">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="30928-439">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="30928-441">V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="30928-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="30928-442">V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="30928-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="30928-443">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="30928-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="30928-444">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="30928-444">Select **Next**.</span></span>

* <span data-ttu-id="30928-445">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="30928-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="30928-447">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="30928-447">Test the API</span></span>

<span data-ttu-id="30928-448">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-448">The project template creates a `values` API.</span></span> <span data-ttu-id="30928-449">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30928-451">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="30928-452">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="30928-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="30928-453">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="30928-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="30928-454">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="30928-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="30928-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30928-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="30928-456">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="30928-457">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="30928-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30928-458">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="30928-459">Vyberte **Spustit**  >  **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="30928-460">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="30928-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="30928-461">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="30928-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="30928-462">Připojit `/api/values` k adrese URL (změnit adresu URL na `https://localhost:<port>/api/values` )</span><span class="sxs-lookup"><span data-stu-id="30928-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="30928-463">Vrátí se následující JSON:</span><span class="sxs-lookup"><span data-stu-id="30928-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="30928-464">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="30928-464">Add a model class</span></span>

<span data-ttu-id="30928-465">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="30928-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="30928-466">Model pro tuto aplikaci je jediná `TodoItem` Třída.</span><span class="sxs-lookup"><span data-stu-id="30928-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30928-468">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="30928-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="30928-469">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="30928-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="30928-470">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="30928-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="30928-471">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="30928-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="30928-472">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="30928-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="30928-473">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="30928-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30928-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="30928-475">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="30928-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="30928-476">`TodoItem`Do složky *modely* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30928-477">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="30928-478">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="30928-478">Right-click the project.</span></span> <span data-ttu-id="30928-479">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="30928-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="30928-480">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="30928-480">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="30928-482">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="30928-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="30928-483">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="30928-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="30928-484">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="30928-485">`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="30928-486">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="30928-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="30928-487">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="30928-487">Add a database context</span></span>

<span data-ttu-id="30928-488">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="30928-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="30928-489">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="30928-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30928-491">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="30928-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="30928-492">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="30928-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="30928-493">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="30928-494">Přidejte `TodoContext` třídu do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="30928-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="30928-495">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="30928-496">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="30928-496">Register the database context</span></span>

<span data-ttu-id="30928-497">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="30928-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="30928-498">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="30928-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="30928-499">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="30928-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="30928-500">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="30928-500">The preceding code:</span></span>

* <span data-ttu-id="30928-501">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="30928-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="30928-502">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="30928-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="30928-503">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="30928-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="30928-504">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="30928-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30928-506">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="30928-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="30928-507">Vyberte možnost **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="30928-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="30928-508">V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="30928-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="30928-509">Pojmenujte třídu *TodoController*a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="30928-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogové okno Přidat novou položku s řadičem ve vyhledávacím poli a vybraným kontrolérem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="30928-511">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="30928-512">Ve složce *Controllers* vytvořte třídu s názvem `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="30928-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="30928-513">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="30928-514">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="30928-514">The preceding code:</span></span>

* <span data-ttu-id="30928-515">Definuje třídu kontroleru rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="30928-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="30928-516">Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="30928-516">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="30928-517">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="30928-518">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="30928-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="30928-519">Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="30928-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="30928-520">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="30928-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="30928-521">Přidá položku s názvem `Item1` do databáze, pokud je databáze prázdná.</span><span class="sxs-lookup"><span data-stu-id="30928-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="30928-522">Tento kód je v konstruktoru, takže se spustí pokaždé, když se vytvoří nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="30928-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="30928-523">Pokud odstraníte všechny položky, konstruktor se znovu vytvoří `Item1` při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="30928-524">Takže může vypadat, že odstranění nefungovalo, pokud skutečně fungovalo.</span><span class="sxs-lookup"><span data-stu-id="30928-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="30928-525">Přidat metody Get</span><span class="sxs-lookup"><span data-stu-id="30928-525">Add Get methods</span></span>

<span data-ttu-id="30928-526">Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte do třídy následující metody `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="30928-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="30928-527">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="30928-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="30928-528">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-528">Stop the app if it's still running.</span></span> <span data-ttu-id="30928-529">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="30928-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="30928-530">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="30928-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="30928-531">Například:</span><span class="sxs-lookup"><span data-stu-id="30928-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="30928-532">Následující odpověď protokolu HTTP je vytvořena voláním metody `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="30928-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="30928-533">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="30928-533">Routing and URL paths</span></span>

<span data-ttu-id="30928-534">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="30928-534">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="30928-535">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="30928-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="30928-536">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="30928-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="30928-537">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="30928-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="30928-538">V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="30928-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="30928-539">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="30928-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="30928-540">Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="30928-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="30928-541">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="30928-541">This sample doesn't use a template.</span></span> <span data-ttu-id="30928-542">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="30928-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="30928-543">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="30928-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="30928-544">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="30928-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="30928-545">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="30928-545">Return values</span></span>

<span data-ttu-id="30928-546">Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="30928-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="30928-547">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="30928-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="30928-548">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="30928-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="30928-549">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="30928-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="30928-550">`ActionResult`návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="30928-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="30928-551">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="30928-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="30928-552">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> kód chyby 404.</span><span class="sxs-lookup"><span data-stu-id="30928-552">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="30928-553">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="30928-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="30928-554">`item`Výsledkem vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="30928-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="30928-555">Test metody GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="30928-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="30928-556">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="30928-557">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="30928-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="30928-558">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="30928-558">Start the web app.</span></span>
* <span data-ttu-id="30928-559">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="30928-559">Start Postman.</span></span>
* <span data-ttu-id="30928-560">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="30928-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30928-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30928-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30928-562">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="30928-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="30928-563">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="30928-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="30928-564">V **Postman**možnosti  >  **předvolba** post (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="30928-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="30928-565">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="30928-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="30928-566">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="30928-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="30928-567">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="30928-567">Create a new request.</span></span>
  * <span data-ttu-id="30928-568">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="30928-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="30928-569">Nastavte adresu URL požadavku na `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="30928-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="30928-570">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="30928-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="30928-571">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="30928-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="30928-572">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="30928-572">Select **Send**.</span></span>

![Odeslání pomocí žádosti Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="30928-574">Přidání metody Create</span><span class="sxs-lookup"><span data-stu-id="30928-574">Add a Create method</span></span>

<span data-ttu-id="30928-575">Přidejte následující `PostTodoItem` metodu do *Controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="30928-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="30928-576">Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="30928-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="30928-577">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="30928-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="30928-578">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="30928-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="30928-579">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="30928-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="30928-580">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="30928-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="30928-581">Přidá `Location` hlavičku k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="30928-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="30928-582">`Location`Záhlaví Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="30928-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="30928-583">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="30928-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="30928-584">Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="30928-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="30928-585">`nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="30928-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="30928-586">Test metody PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="30928-587">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="30928-587">Build the project.</span></span>
* <span data-ttu-id="30928-588">V části post nastavte metodu HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="30928-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="30928-589">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="30928-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="30928-590">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="30928-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="30928-591">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="30928-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="30928-592">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="30928-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="30928-593">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="30928-593">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

  <span data-ttu-id="30928-595">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody nebude projekt zkompilován `PostTodoItem` .</span><span class="sxs-lookup"><span data-stu-id="30928-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="30928-596">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="30928-596">Test the location header URI</span></span>

* <span data-ttu-id="30928-597">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="30928-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="30928-598">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="30928-598">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/pmc2.png)

* <span data-ttu-id="30928-600">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="30928-600">Set the method to GET.</span></span>
* <span data-ttu-id="30928-601">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="30928-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="30928-602">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="30928-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="30928-603">Přidat metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="30928-604">Přidejte následující `PutTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="30928-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="30928-605">`PutTodoItem`se podobá `PostTodoItem` , s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="30928-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="30928-606">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="30928-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="30928-607">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="30928-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="30928-608">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="30928-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="30928-609">Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="30928-610">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="30928-611">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="30928-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="30928-612">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="30928-613">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="30928-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="30928-614">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="30928-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="30928-615">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="30928-615">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="30928-617">Přidat metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="30928-618">Přidejte následující `DeleteTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="30928-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="30928-619">`DeleteTodoItem`Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="30928-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="30928-620">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="30928-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="30928-621">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="30928-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="30928-622">Nastavte metodu na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="30928-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="30928-623">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="30928-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="30928-624">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="30928-624">Select **Send**.</span></span>

<span data-ttu-id="30928-625">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="30928-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="30928-626">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="30928-627">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="30928-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="30928-628">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="30928-629">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="30928-629">jQuery initiates the request.</span></span> <span data-ttu-id="30928-630">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="30928-631">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) , a [Povolte výchozí mapování souborů](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="30928-631">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="30928-632">Vytvořte složku *wwwroot* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="30928-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="30928-633">Do adresáře *wwwroot* přidejte soubor HTML s názvem *index.html* .</span><span class="sxs-lookup"><span data-stu-id="30928-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="30928-634">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="30928-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="30928-635">Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *site.js* .</span><span class="sxs-lookup"><span data-stu-id="30928-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="30928-636">Jeho obsah nahraďte následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="30928-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="30928-637">Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:</span><span class="sxs-lookup"><span data-stu-id="30928-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="30928-638">Otevřete *Properties\launchSettings.js*.</span><span class="sxs-lookup"><span data-stu-id="30928-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="30928-639">Odeberte `launchUrl` vlastnost, která vynutí otevření aplikace na *index.html* &mdash; výchozího souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="30928-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="30928-640">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="30928-641">Následují vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="30928-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="30928-642">Získat seznam úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-642">Get a list of to-do items</span></span>

<span data-ttu-id="30928-643">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="30928-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="30928-644">`success`Funkce zpětného volání je vyvolána, pokud je požadavek úspěšný.</span><span class="sxs-lookup"><span data-stu-id="30928-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="30928-645">Ve zpětném volání je DOM aktualizován pomocí informací o tom.</span><span class="sxs-lookup"><span data-stu-id="30928-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="30928-646">Přidat položku úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-646">Add a to-do item</span></span>

<span data-ttu-id="30928-647">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="30928-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="30928-648">`accepts`Možnosti a `contentType` jsou nastaveny na `application/json` zadání typu média, který se přijímá a odesílá.</span><span class="sxs-lookup"><span data-stu-id="30928-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="30928-649">Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="30928-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="30928-650">Když rozhraní API vrátí stavový kód úspěšné, `getData` funkce se vyvolá, aby se aktualizovala tabulka HTML.</span><span class="sxs-lookup"><span data-stu-id="30928-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="30928-651">Aktualizace položky úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-651">Update a to-do item</span></span>

<span data-ttu-id="30928-652">Aktualizace položky úkolů je podobná přidání.</span><span class="sxs-lookup"><span data-stu-id="30928-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="30928-653">`url`Změny pro přidání jedinečného identifikátoru položky a `type` jsou `PUT` .</span><span class="sxs-lookup"><span data-stu-id="30928-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="30928-654">Odstranění položky úkolů</span><span class="sxs-lookup"><span data-stu-id="30928-654">Delete a to-do item</span></span>

<span data-ttu-id="30928-655">Odstranění položky úkolů je provedeno nastavením `type` při volání AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="30928-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="30928-656">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="30928-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="30928-657">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="30928-657">Additional resources</span></span>

<span data-ttu-id="30928-658">[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="30928-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="30928-659">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="30928-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="30928-660">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="30928-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="30928-661">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="30928-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

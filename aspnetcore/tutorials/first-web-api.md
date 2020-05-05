---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: ddc14aba14e31c5530cda14b4792736da001246a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767236"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="d93e4-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d93e4-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="d93e4-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)a [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="d93e4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="d93e4-105">V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d93e4-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d93e4-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="d93e4-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d93e4-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-107">Create a web API project.</span></span>
> * <span data-ttu-id="d93e4-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="d93e4-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d93e4-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="d93e4-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="d93e4-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d93e4-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="d93e4-111">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="d93e4-111">Call the web API with Postman.</span></span>

<span data-ttu-id="d93e4-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="d93e4-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="d93e4-113">Overview</span></span>

<span data-ttu-id="d93e4-114">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="d93e4-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d93e4-115">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d93e4-115">API</span></span> | <span data-ttu-id="d93e4-116">Popis</span><span class="sxs-lookup"><span data-stu-id="d93e4-116">Description</span></span> | <span data-ttu-id="d93e4-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="d93e4-117">Request body</span></span> | <span data-ttu-id="d93e4-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="d93e4-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="d93e4-119">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-119">Get all to-do items</span></span> | <span data-ttu-id="d93e4-120">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-120">None</span></span> | <span data-ttu-id="d93e4-121">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="d93e4-122">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="d93e4-122">Get an item by ID</span></span> | <span data-ttu-id="d93e4-123">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-123">None</span></span> | <span data-ttu-id="d93e4-124">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="d93e4-125">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="d93e4-125">Add a new item</span></span> | <span data-ttu-id="d93e4-126">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-126">To-do item</span></span> | <span data-ttu-id="d93e4-127">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="d93e4-128">Aktualizovat existující položku&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d93e4-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d93e4-129">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-129">To-do item</span></span> | <span data-ttu-id="d93e4-130">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-130">None</span></span> |
|<span data-ttu-id="d93e4-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d93e4-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="d93e4-132">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d93e4-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d93e4-133">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-133">None</span></span> | <span data-ttu-id="d93e4-134">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-134">None</span></span>|

<span data-ttu-id="d93e4-135">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="d93e4-135">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="d93e4-141">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d93e4-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d93e4-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d93e4-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-144">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="d93e4-145">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="d93e4-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d93e4-147">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d93e4-148">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d93e4-149">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d93e4-150">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="d93e4-151">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-151">Select the **API** template and click **Create**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d93e4-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d93e4-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d93e4-154">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d93e4-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d93e4-155">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d93e4-156">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d93e4-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="d93e4-157">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="d93e4-158">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="d93e4-158">The preceding commands:</span></span>

  * <span data-ttu-id="d93e4-159">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d93e4-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="d93e4-160">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="d93e4-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-161">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d93e4-162">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-162">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d93e4-164">Vyberte rozhraní > **API** > > **aplikace** **.NET Core** **Další**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="d93e4-166">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \**.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="d93e4-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="d93e4-167">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="d93e4-169">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d93e4-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="d93e4-170">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d93e4-170">Test the API</span></span>

<span data-ttu-id="d93e4-171">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="d93e4-172">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d93e4-174">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d93e4-175">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d93e4-176">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d93e4-177">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d93e4-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d93e4-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d93e4-179">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d93e4-180">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-181">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d93e4-182">Vyberte **Spustit** > **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d93e4-183">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d93e4-184">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="d93e4-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d93e4-185">Připojit `/WeatherForecast` k adrese URL (změnit adresu URL na `https://localhost:<port>/WeatherForecast`)</span><span class="sxs-lookup"><span data-stu-id="d93e4-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="d93e4-186">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="d93e4-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="d93e4-187">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="d93e4-187">Add a model class</span></span>

<span data-ttu-id="d93e4-188">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="d93e4-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d93e4-189">Model pro tuto aplikaci je jediná `TodoItem` třída.</span><span class="sxs-lookup"><span data-stu-id="d93e4-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d93e4-191">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="d93e4-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d93e4-192">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d93e4-193">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="d93e4-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="d93e4-194">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d93e4-195">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d93e4-196">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d93e4-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d93e4-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d93e4-198">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="d93e4-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d93e4-199">Do složky `TodoItem` *modely* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-200">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d93e4-201">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="d93e4-201">Right-click the project.</span></span> <span data-ttu-id="d93e4-202">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d93e4-203">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="d93e4-203">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d93e4-205">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d93e4-206">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d93e4-207">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="d93e4-208">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d93e4-209">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="d93e4-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d93e4-210">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="d93e4-210">Add a database context</span></span>

<span data-ttu-id="d93e4-211">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="d93e4-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d93e4-212">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="d93e4-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="d93e4-214">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="d93e4-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="d93e4-215">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="d93e4-216">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="d93e4-217">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="d93e4-218">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="d93e4-219">Pomocí předchozích pokynů přidejte balíček `Microsoft.EntityFrameworkCore.InMemory` NuGet.</span><span class="sxs-lookup"><span data-stu-id="d93e4-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="d93e4-221">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="d93e4-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="d93e4-222">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d93e4-223">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-224">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d93e4-225">Přidejte `TodoContext` třídu do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="d93e4-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d93e4-226">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="d93e4-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d93e4-227">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="d93e4-227">Register the database context</span></span>

<span data-ttu-id="d93e4-228">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="d93e4-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d93e4-229">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="d93e4-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="d93e4-230">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="d93e4-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="d93e4-231">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="d93e4-231">The preceding code:</span></span>

* <span data-ttu-id="d93e4-232">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="d93e4-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d93e4-233">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="d93e4-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d93e4-234">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="d93e4-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="d93e4-235">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="d93e4-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d93e4-237">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="d93e4-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d93e4-238">Vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d93e4-239">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="d93e4-240">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="d93e4-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="d93e4-241">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="d93e4-242">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="d93e4-243">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-244">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d93e4-245">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d93e4-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="d93e4-246">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="d93e4-246">The preceding commands:</span></span>

* <span data-ttu-id="d93e4-247">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d93e4-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="d93e4-248">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="d93e4-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="d93e4-249">Generování uživatelského rozhraní `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="d93e4-250">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="d93e4-250">The generated code:</span></span>

* <span data-ttu-id="d93e4-251">Označí třídu [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="d93e4-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="d93e4-252">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d93e4-253">Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d93e4-254">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d93e4-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d93e4-255">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d93e4-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="d93e4-256">Šablony ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="d93e4-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="d93e4-257">Řadiče se zobrazeními `[action]` jsou zahrnuta v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="d93e4-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="d93e4-258">Řadiče API neobsahují `[action]` šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="d93e4-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="d93e4-259">Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy.</span><span class="sxs-lookup"><span data-stu-id="d93e4-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="d93e4-260">To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="d93e4-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="d93e4-261">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="d93e4-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="d93e4-262">Nahraďte příkaz return v operátoru `PostTodoItem` k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="d93e4-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="d93e4-263">Předchozí kód je metoda HTTP POST, jak je [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) označena atributem.</span><span class="sxs-lookup"><span data-stu-id="d93e4-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="d93e4-264">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d93e4-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d93e4-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Metoda:</span><span class="sxs-lookup"><span data-stu-id="d93e4-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="d93e4-266">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="d93e4-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="d93e4-267">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="d93e4-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d93e4-268">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="d93e4-269">`Location` Záhlaví Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="d93e4-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="d93e4-270">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="d93e4-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d93e4-271">Odkazuje na `GetTodoItem` akci vytvoření identifikátoru `Location` URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="d93e4-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d93e4-272">Klíčové slovo `nameof` jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="d93e4-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="d93e4-273">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="d93e4-273">Install Postman</span></span>

<span data-ttu-id="d93e4-274">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d93e4-275">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="d93e4-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="d93e4-276">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-276">Start the web app.</span></span>
* <span data-ttu-id="d93e4-277">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="d93e4-277">Start Postman.</span></span>
* <span data-ttu-id="d93e4-278">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="d93e4-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="d93e4-279">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="d93e4-280">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="d93e4-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="d93e4-281">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="d93e4-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="d93e4-282">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="d93e4-282">Create a new request.</span></span>
* <span data-ttu-id="d93e4-283">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d93e4-284">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="d93e4-285">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="d93e4-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d93e4-286">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d93e4-287">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="d93e4-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d93e4-288">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-288">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="d93e4-290">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="d93e4-290">Test the location header URI</span></span>

* <span data-ttu-id="d93e4-291">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d93e4-292">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="d93e4-292">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* <span data-ttu-id="d93e4-294">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="d93e4-294">Set the method to GET.</span></span>
* <span data-ttu-id="d93e4-295">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="d93e4-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="d93e4-296">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="d93e4-297">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="d93e4-297">Examine the GET methods</span></span>

<span data-ttu-id="d93e4-298">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="d93e4-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="d93e4-299">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="d93e4-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="d93e4-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d93e4-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="d93e4-301">Odpověď podobná následující je vytvořena voláním metody `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="d93e4-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="d93e4-302">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="d93e4-302">Test Get with Postman</span></span>

* <span data-ttu-id="d93e4-303">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="d93e4-303">Create a new request.</span></span>
* <span data-ttu-id="d93e4-304">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="d93e4-305">Nastavte adresu URL požadavku na `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="d93e4-306">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="d93e4-307">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d93e4-308">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-308">Select **Send**.</span></span>

<span data-ttu-id="d93e4-309">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="d93e4-309">This app uses an in-memory database.</span></span> <span data-ttu-id="d93e4-310">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="d93e4-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="d93e4-311">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="d93e4-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="d93e4-312">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="d93e4-312">Routing and URL paths</span></span>

<span data-ttu-id="d93e4-313">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="d93e4-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d93e4-314">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="d93e4-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d93e4-315">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="d93e4-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="d93e4-316">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="d93e4-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d93e4-317">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="d93e4-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="d93e4-318">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d93e4-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d93e4-319">Pokud má `[HttpGet]` atribut směrovací šablonu (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="d93e4-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d93e4-320">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-320">This sample doesn't use a template.</span></span> <span data-ttu-id="d93e4-321">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d93e4-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d93e4-322">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="d93e4-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d93e4-323">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="d93e4-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d93e4-324">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="d93e4-324">Return values</span></span>

<span data-ttu-id="d93e4-325">Návratový typ metod `GetTodoItems` a `GetTodoItem` je [ActionResult\<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="d93e4-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d93e4-326">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="d93e4-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d93e4-327">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="d93e4-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d93e4-328">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="d93e4-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d93e4-329">`ActionResult`návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="d93e4-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d93e4-330">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d93e4-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d93e4-331">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="d93e4-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="d93e4-332">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="d93e4-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d93e4-333">Výsledkem `item` vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="d93e4-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="d93e4-334">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-334">The PutTodoItem method</span></span>

<span data-ttu-id="d93e4-335">Projděte `PutTodoItem` si metodu:</span><span class="sxs-lookup"><span data-stu-id="d93e4-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="d93e4-336">`PutTodoItem`se podobá `PostTodoItem`, s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="d93e4-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d93e4-337">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d93e4-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d93e4-338">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="d93e4-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d93e4-339">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="d93e4-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d93e4-340">Pokud se zobrazí chyba při volání `PutTodoItem`, zajistěte, `GET` aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d93e4-341">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="d93e4-342">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="d93e4-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="d93e4-343">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d93e4-344">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d93e4-345">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="d93e4-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d93e4-346">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="d93e4-346">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="d93e4-348">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="d93e4-349">Projděte `DeleteTodoItem` si metodu:</span><span class="sxs-lookup"><span data-stu-id="d93e4-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d93e4-350">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d93e4-351">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="d93e4-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d93e4-352">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d93e4-353">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="d93e4-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="d93e4-354">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="d93e4-355">Zabránit navýšení příspěvků</span><span class="sxs-lookup"><span data-stu-id="d93e4-355">Prevent over-posting</span></span>

<span data-ttu-id="d93e4-356">V současné době ukázková aplikace zveřejňuje celý `TodoItem` objekt.</span><span class="sxs-lookup"><span data-stu-id="d93e4-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="d93e4-357">Výrobní aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="d93e4-358">Je to několik důvodů na pozadí a zabezpečení je hlavní.</span><span class="sxs-lookup"><span data-stu-id="d93e4-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="d93e4-359">Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d93e4-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="d93e4-360">**DTO** se používá v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="d93e4-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="d93e4-361">DTO se dá použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="d93e4-361">A DTO may be used to:</span></span>

* <span data-ttu-id="d93e4-362">Zabránit přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="d93e4-362">Prevent over-posting.</span></span>
* <span data-ttu-id="d93e4-363">Skrytí vlastností, které klienti nemají zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="d93e4-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="d93e4-364">Vynechejte některé vlastnosti, aby se snížila velikost datové části.</span><span class="sxs-lookup"><span data-stu-id="d93e4-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="d93e4-365">Ploché grafy objektů, které obsahují vnořené objekty.</span><span class="sxs-lookup"><span data-stu-id="d93e4-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="d93e4-366">Ploché grafy objektů můžou být pro klienty pohodlnější.</span><span class="sxs-lookup"><span data-stu-id="d93e4-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="d93e4-367">Chcete-li předvést DTO přístup, `TodoItem` aktualizujte třídu tak, aby obsahovala tajné pole:</span><span class="sxs-lookup"><span data-stu-id="d93e4-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="d93e4-368">Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.</span><span class="sxs-lookup"><span data-stu-id="d93e4-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="d93e4-369">Ověřte, že můžete publikovat a získat pole tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="d93e4-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="d93e4-370">Vytvoření modelu DTO:</span><span class="sxs-lookup"><span data-stu-id="d93e4-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="d93e4-371">Aktualizujte `TodoItemsController` , aby `TodoItemDTO`se použil:</span><span class="sxs-lookup"><span data-stu-id="d93e4-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="d93e4-372">Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="d93e4-373">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d93e4-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="d93e4-374">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="d93e4-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d93e4-375">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="d93e4-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d93e4-376">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-376">Create a web API project.</span></span>
> * <span data-ttu-id="d93e4-377">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="d93e4-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d93e4-378">Přidejte kontroler.</span><span class="sxs-lookup"><span data-stu-id="d93e4-378">Add a controller.</span></span>
> * <span data-ttu-id="d93e4-379">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="d93e4-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="d93e4-380">Nakonfigurujte směrování a cesty URL.</span><span class="sxs-lookup"><span data-stu-id="d93e4-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="d93e4-381">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d93e4-381">Specify return values.</span></span>
> * <span data-ttu-id="d93e4-382">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="d93e4-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="d93e4-383">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="d93e4-384">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="d93e4-385">Přehled</span><span class="sxs-lookup"><span data-stu-id="d93e4-385">Overview</span></span>

<span data-ttu-id="d93e4-386">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="d93e4-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d93e4-387">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d93e4-387">API</span></span> | <span data-ttu-id="d93e4-388">Popis</span><span class="sxs-lookup"><span data-stu-id="d93e4-388">Description</span></span> | <span data-ttu-id="d93e4-389">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="d93e4-389">Request body</span></span> | <span data-ttu-id="d93e4-390">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="d93e4-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="d93e4-391">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d93e4-391">GET /api/TodoItems</span></span> | <span data-ttu-id="d93e4-392">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-392">Get all to-do items</span></span> | <span data-ttu-id="d93e4-393">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-393">None</span></span> | <span data-ttu-id="d93e4-394">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-394">Array of to-do items</span></span>|
|<span data-ttu-id="d93e4-395">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d93e4-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="d93e4-396">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="d93e4-396">Get an item by ID</span></span> | <span data-ttu-id="d93e4-397">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-397">None</span></span> | <span data-ttu-id="d93e4-398">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-398">To-do item</span></span>|
|<span data-ttu-id="d93e4-399">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d93e4-399">POST /api/TodoItems</span></span> | <span data-ttu-id="d93e4-400">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="d93e4-400">Add a new item</span></span> | <span data-ttu-id="d93e4-401">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-401">To-do item</span></span> | <span data-ttu-id="d93e4-402">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-402">To-do item</span></span> |
|<span data-ttu-id="d93e4-403">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d93e4-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="d93e4-404">Aktualizovat existující položku&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d93e4-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d93e4-405">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-405">To-do item</span></span> | <span data-ttu-id="d93e4-406">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-406">None</span></span> |
|<span data-ttu-id="d93e4-407">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d93e4-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="d93e4-408">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="d93e4-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d93e4-409">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-409">None</span></span> | <span data-ttu-id="d93e4-410">Žádná</span><span class="sxs-lookup"><span data-stu-id="d93e4-410">None</span></span>|

<span data-ttu-id="d93e4-411">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="d93e4-411">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="d93e4-417">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d93e4-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d93e4-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d93e4-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-420">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="d93e4-421">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="d93e4-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d93e4-423">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d93e4-424">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d93e4-425">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d93e4-426">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="d93e4-427">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="d93e4-428">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-428">**Don't** select **Enable Docker Support**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d93e4-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d93e4-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d93e4-431">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d93e4-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d93e4-432">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d93e4-433">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d93e4-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="d93e4-434">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="d93e4-435">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-436">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d93e4-437">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-437">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d93e4-439">Vyberte rozhraní > **API** > > **aplikace** **.NET Core** **Další**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="d93e4-441">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou** verzi rozhraní \**.NET Core 2,2*.</span><span class="sxs-lookup"><span data-stu-id="d93e4-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="d93e4-442">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="d93e4-444">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d93e4-444">Test the API</span></span>

<span data-ttu-id="d93e4-445">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-445">The project template creates a `values` API.</span></span> <span data-ttu-id="d93e4-446">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d93e4-448">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d93e4-449">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d93e4-450">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d93e4-451">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d93e4-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d93e4-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d93e4-453">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d93e4-454">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-455">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d93e4-456">Vyberte **Spustit** > **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d93e4-457">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d93e4-458">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="d93e4-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d93e4-459">Připojit `/api/values` k adrese URL (změnit adresu URL na `https://localhost:<port>/api/values`)</span><span class="sxs-lookup"><span data-stu-id="d93e4-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="d93e4-460">Vrátí se následující JSON:</span><span class="sxs-lookup"><span data-stu-id="d93e4-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="d93e4-461">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="d93e4-461">Add a model class</span></span>

<span data-ttu-id="d93e4-462">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="d93e4-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d93e4-463">Model pro tuto aplikaci je jediná `TodoItem` třída.</span><span class="sxs-lookup"><span data-stu-id="d93e4-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d93e4-465">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="d93e4-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d93e4-466">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d93e4-467">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="d93e4-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="d93e4-468">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d93e4-469">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d93e4-470">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d93e4-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d93e4-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d93e4-472">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="d93e4-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d93e4-473">Do složky `TodoItem` *modely* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-474">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d93e4-475">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="d93e4-475">Right-click the project.</span></span> <span data-ttu-id="d93e4-476">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d93e4-477">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="d93e4-477">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d93e4-479">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d93e4-480">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d93e4-481">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="d93e4-482">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d93e4-483">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="d93e4-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d93e4-484">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="d93e4-484">Add a database context</span></span>

<span data-ttu-id="d93e4-485">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="d93e4-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d93e4-486">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="d93e4-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d93e4-488">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d93e4-489">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-490">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d93e4-491">Přidejte `TodoContext` třídu do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="d93e4-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d93e4-492">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d93e4-493">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="d93e4-493">Register the database context</span></span>

<span data-ttu-id="d93e4-494">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="d93e4-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d93e4-495">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="d93e4-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="d93e4-496">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="d93e4-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="d93e4-497">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="d93e4-497">The preceding code:</span></span>

* <span data-ttu-id="d93e4-498">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="d93e4-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d93e4-499">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="d93e4-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d93e4-500">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="d93e4-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d93e4-501">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="d93e4-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d93e4-503">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="d93e4-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d93e4-504">Vyberte možnost **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="d93e4-505">V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="d93e4-506">Pojmenujte třídu *TodoController*a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogové okno Přidat novou položku s řadičem ve vyhledávacím poli a vybraným kontrolérem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-508">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d93e4-509">Ve složce *Controllers* vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="d93e4-510">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="d93e4-511">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="d93e4-511">The preceding code:</span></span>

* <span data-ttu-id="d93e4-512">Definuje třídu kontroleru rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="d93e4-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="d93e4-513">Označí třídu [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="d93e4-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="d93e4-514">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d93e4-515">Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d93e4-516">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d93e4-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d93e4-517">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d93e4-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="d93e4-518">Přidá položku s názvem `Item1` do databáze, pokud je databáze prázdná.</span><span class="sxs-lookup"><span data-stu-id="d93e4-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="d93e4-519">Tento kód je v konstruktoru, takže se spustí pokaždé, když se vytvoří nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="d93e4-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="d93e4-520">Pokud odstraníte všechny položky, konstruktor se znovu vytvoří `Item1` při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="d93e4-521">Takže může vypadat, že odstranění nefungovalo, pokud skutečně fungovalo.</span><span class="sxs-lookup"><span data-stu-id="d93e4-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="d93e4-522">Přidat metody Get</span><span class="sxs-lookup"><span data-stu-id="d93e4-522">Add Get methods</span></span>

<span data-ttu-id="d93e4-523">Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte do `TodoController` třídy následující metody:</span><span class="sxs-lookup"><span data-stu-id="d93e4-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="d93e4-524">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="d93e4-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="d93e4-525">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-525">Stop the app if it's still running.</span></span> <span data-ttu-id="d93e4-526">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="d93e4-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="d93e4-527">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d93e4-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="d93e4-528">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d93e4-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="d93e4-529">Následující odpověď protokolu HTTP je vytvořena voláním metody `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="d93e4-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="d93e4-530">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="d93e4-530">Routing and URL paths</span></span>

<span data-ttu-id="d93e4-531">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="d93e4-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d93e4-532">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="d93e4-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d93e4-533">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="d93e4-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="d93e4-534">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="d93e4-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d93e4-535">V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="d93e4-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="d93e4-536">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d93e4-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d93e4-537">Pokud má `[HttpGet]` atribut směrovací šablonu (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="d93e4-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d93e4-538">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-538">This sample doesn't use a template.</span></span> <span data-ttu-id="d93e4-539">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d93e4-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d93e4-540">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="d93e4-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d93e4-541">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="d93e4-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d93e4-542">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="d93e4-542">Return values</span></span>

<span data-ttu-id="d93e4-543">Návratový typ metod `GetTodoItems` a `GetTodoItem` je [ActionResult\<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="d93e4-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d93e4-544">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="d93e4-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d93e4-545">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="d93e4-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d93e4-546">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="d93e4-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d93e4-547">`ActionResult`návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="d93e4-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d93e4-548">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d93e4-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d93e4-549">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="d93e4-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="d93e4-550">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="d93e4-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d93e4-551">Výsledkem `item` vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="d93e4-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="d93e4-552">Test metody GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="d93e4-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="d93e4-553">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d93e4-554">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="d93e4-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="d93e4-555">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d93e4-555">Start the web app.</span></span>
* <span data-ttu-id="d93e4-556">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="d93e4-556">Start Postman.</span></span>
* <span data-ttu-id="d93e4-557">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d93e4-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d93e4-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d93e4-559">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d93e4-560">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d93e4-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d93e4-561">V možnosti**předvolba** **post** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="d93e4-562">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="d93e4-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="d93e4-563">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="d93e4-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="d93e4-564">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="d93e4-564">Create a new request.</span></span>
  * <span data-ttu-id="d93e4-565">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="d93e4-566">Nastavte adresu URL požadavku na `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="d93e4-567">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="d93e4-568">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d93e4-569">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-569">Select **Send**.</span></span>

![Odeslání pomocí žádosti Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="d93e4-571">Přidání metody Create</span><span class="sxs-lookup"><span data-stu-id="d93e4-571">Add a Create method</span></span>

<span data-ttu-id="d93e4-572">Přidejte následující `PostTodoItem` metodu do *Controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="d93e4-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="d93e4-573">Předchozí kód je metoda HTTP POST, jak je [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) označena atributem.</span><span class="sxs-lookup"><span data-stu-id="d93e4-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="d93e4-574">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d93e4-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d93e4-575">`CreatedAtAction` Metoda:</span><span class="sxs-lookup"><span data-stu-id="d93e4-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="d93e4-576">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="d93e4-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="d93e4-577">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="d93e4-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d93e4-578">Přidá `Location` hlavičku k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="d93e4-579">`Location` Záhlaví Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="d93e4-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="d93e4-580">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="d93e4-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d93e4-581">Odkazuje na `GetTodoItem` akci vytvoření identifikátoru `Location` URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="d93e4-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d93e4-582">Klíčové slovo `nameof` jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="d93e4-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="d93e4-583">Test metody PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="d93e4-584">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="d93e4-584">Build the project.</span></span>
* <span data-ttu-id="d93e4-585">V části post nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d93e4-586">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="d93e4-587">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="d93e4-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d93e4-588">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d93e4-589">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="d93e4-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d93e4-590">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-590">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

  <span data-ttu-id="d93e4-592">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání `PostTodoItem` metody nebude projekt zkompilován.</span><span class="sxs-lookup"><span data-stu-id="d93e4-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="d93e4-593">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="d93e4-593">Test the location header URI</span></span>

* <span data-ttu-id="d93e4-594">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="d93e4-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d93e4-595">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="d93e4-595">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/pmc2.png)

* <span data-ttu-id="d93e4-597">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="d93e4-597">Set the method to GET.</span></span>
* <span data-ttu-id="d93e4-598">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="d93e4-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="d93e4-599">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="d93e4-600">Přidat metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="d93e4-601">Přidejte následující `PutTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="d93e4-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="d93e4-602">`PutTodoItem`se podobá `PostTodoItem`, s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="d93e4-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d93e4-603">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d93e4-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d93e4-604">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="d93e4-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d93e4-605">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="d93e4-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d93e4-606">Pokud se zobrazí chyba při volání `PutTodoItem`, zajistěte, `GET` aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d93e4-607">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="d93e4-608">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="d93e4-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="d93e4-609">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d93e4-610">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="d93e4-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d93e4-611">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="d93e4-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d93e4-612">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="d93e4-612">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="d93e4-614">Přidat metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="d93e4-615">Přidejte následující `DeleteTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="d93e4-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="d93e4-616">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d93e4-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d93e4-617">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="d93e4-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d93e4-618">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="d93e4-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d93e4-619">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d93e4-620">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="d93e4-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="d93e4-621">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="d93e4-621">Select **Send**.</span></span>

<span data-ttu-id="d93e4-622">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="d93e4-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="d93e4-623">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="d93e4-624">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d93e4-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="d93e4-625">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="d93e4-626">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="d93e4-626">jQuery initiates the request.</span></span> <span data-ttu-id="d93e4-627">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="d93e4-628">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="d93e4-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="d93e4-629">Vytvořte složku *wwwroot* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="d93e4-630">Do adresáře *wwwroot* přidejte soubor HTML s názvem *index. html* .</span><span class="sxs-lookup"><span data-stu-id="d93e4-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="d93e4-631">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="d93e4-632">Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *Web. js* .</span><span class="sxs-lookup"><span data-stu-id="d93e4-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="d93e4-633">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d93e4-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="d93e4-634">Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:</span><span class="sxs-lookup"><span data-stu-id="d93e4-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="d93e4-635">Otevřete *Properties\launchSettings.JSON*.</span><span class="sxs-lookup"><span data-stu-id="d93e4-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="d93e4-636">Odeberte `launchUrl` vlastnost, která vynutí otevření aplikace v *indexu. html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="d93e4-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="d93e4-637">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="d93e4-638">Následují vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d93e4-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="d93e4-639">Získat seznam úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-639">Get a list of to-do items</span></span>

<span data-ttu-id="d93e4-640">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="d93e4-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="d93e4-641">Funkce `success` zpětného volání je vyvolána, pokud je požadavek úspěšný.</span><span class="sxs-lookup"><span data-stu-id="d93e4-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="d93e4-642">Ve zpětném volání je DOM aktualizován pomocí informací o tom.</span><span class="sxs-lookup"><span data-stu-id="d93e4-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="d93e4-643">Přidat položku úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-643">Add a to-do item</span></span>

<span data-ttu-id="d93e4-644">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="d93e4-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="d93e4-645">Možnosti `accepts` a `contentType` jsou nastaveny na `application/json` zadání typu média, který se přijímá a odesílá.</span><span class="sxs-lookup"><span data-stu-id="d93e4-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="d93e4-646">Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="d93e4-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="d93e4-647">Když rozhraní API vrátí stavový kód úspěšné, `getData` funkce se vyvolá, aby se aktualizovala tabulka HTML.</span><span class="sxs-lookup"><span data-stu-id="d93e4-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="d93e4-648">Aktualizace položky úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-648">Update a to-do item</span></span>

<span data-ttu-id="d93e4-649">Aktualizace položky úkolů je podobná přidání.</span><span class="sxs-lookup"><span data-stu-id="d93e4-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="d93e4-650">`url` Změny pro přidání jedinečného identifikátoru položky a `type` jsou `PUT`.</span><span class="sxs-lookup"><span data-stu-id="d93e4-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="d93e4-651">Odstranění položky úkolů</span><span class="sxs-lookup"><span data-stu-id="d93e4-651">Delete a to-do item</span></span>

<span data-ttu-id="d93e4-652">Odstranění položky úkolů je provedeno nastavením `type` při volání AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="d93e4-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="d93e4-653">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d93e4-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="d93e4-654">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d93e4-654">Additional resources</span></span>

<span data-ttu-id="d93e4-655">[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="d93e4-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="d93e4-656">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="d93e4-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="d93e4-657">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="d93e4-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="d93e4-658">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="d93e4-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

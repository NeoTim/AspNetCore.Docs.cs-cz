---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 4e205c737f606579590854b679e669cbdd0cd5ab
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727798"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="3b4d6-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b4d6-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="3b4d6-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)a [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="3b4d6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="3b4d6-105">V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3b4d6-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3b4d6-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-107">Create a web API project.</span></span>
> * <span data-ttu-id="3b4d6-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3b4d6-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="3b4d6-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="3b4d6-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="3b4d6-111">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-111">Call the web API with Postman.</span></span>

<span data-ttu-id="3b4d6-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="3b4d6-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="3b4d6-113">Overview</span></span>

<span data-ttu-id="3b4d6-114">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3b4d6-115">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3b4d6-115">API</span></span> | <span data-ttu-id="3b4d6-116">Popis</span><span class="sxs-lookup"><span data-stu-id="3b4d6-116">Description</span></span> | <span data-ttu-id="3b4d6-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="3b4d6-117">Request body</span></span> | <span data-ttu-id="3b4d6-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="3b4d6-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="3b4d6-119">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-119">Get all to-do items</span></span> | <span data-ttu-id="3b4d6-120">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-120">None</span></span> | <span data-ttu-id="3b4d6-121">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="3b4d6-122">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="3b4d6-122">Get an item by ID</span></span> | <span data-ttu-id="3b4d6-123">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-123">None</span></span> | <span data-ttu-id="3b4d6-124">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="3b4d6-125">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="3b4d6-125">Add a new item</span></span> | <span data-ttu-id="3b4d6-126">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-126">To-do item</span></span> | <span data-ttu-id="3b4d6-127">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="3b4d6-128">Aktualizovat existující položku&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3b4d6-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3b4d6-129">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-129">To-do item</span></span> | <span data-ttu-id="3b4d6-130">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-130">None</span></span> |
|<span data-ttu-id="3b4d6-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3b4d6-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="3b4d6-132">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3b4d6-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3b4d6-133">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-133">None</span></span> | <span data-ttu-id="3b4d6-134">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-134">None</span></span>|

<span data-ttu-id="3b4d6-135">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-135">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="3b4d6-141">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3b4d6-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b4d6-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b4d6-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-144">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3b4d6-145">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="3b4d6-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b4d6-147">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3b4d6-148">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3b4d6-149">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3b4d6-150">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="3b4d6-151">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-151">Select the **API** template and click **Create**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b4d6-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b4d6-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3b4d6-154">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3b4d6-155">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3b4d6-156">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="3b4d6-157">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="3b4d6-158">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-158">The preceding commands:</span></span>

  * <span data-ttu-id="3b4d6-159">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="3b4d6-160">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-161">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3b4d6-162">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-162">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3b4d6-164">Vyberte rozhraní > **API** > > **aplikace** **.NET Core** **Další**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="3b4d6-166">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \**.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="3b4d6-167">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="3b4d6-169">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="3b4d6-170">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3b4d6-170">Test the API</span></span>

<span data-ttu-id="3b4d6-171">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="3b4d6-172">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b4d6-174">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3b4d6-175">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="3b4d6-176">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="3b4d6-177">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b4d6-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b4d6-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b4d6-179">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3b4d6-180">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-181">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3b4d6-182">Vyberte **Spustit** > **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3b4d6-183">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3b4d6-184">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3b4d6-185">Připojit `/WeatherForecast` k adrese URL (změnit adresu URL na `https://localhost:<port>/WeatherForecast`)</span><span class="sxs-lookup"><span data-stu-id="3b4d6-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="3b4d6-186">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="3b4d6-187">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="3b4d6-187">Add a model class</span></span>

<span data-ttu-id="3b4d6-188">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3b4d6-189">Model pro tuto aplikaci je jediná `TodoItem` třída.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b4d6-191">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3b4d6-192">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3b4d6-193">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="3b4d6-194">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3b4d6-195">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3b4d6-196">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b4d6-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b4d6-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3b4d6-198">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3b4d6-199">Do složky `TodoItem` *modely* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-200">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3b4d6-201">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-201">Right-click the project.</span></span> <span data-ttu-id="3b4d6-202">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3b4d6-203">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-203">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3b4d6-205">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3b4d6-206">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3b4d6-207">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="3b4d6-208">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3b4d6-209">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="3b4d6-210">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="3b4d6-210">Add a database context</span></span>

<span data-ttu-id="3b4d6-211">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3b4d6-212">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="3b4d6-214">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="3b4d6-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="3b4d6-215">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="3b4d6-216">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="3b4d6-217">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="3b4d6-218">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="3b4d6-219">Pomocí předchozích pokynů přidejte balíček `Microsoft.EntityFrameworkCore.InMemory` NuGet.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="3b4d6-221">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="3b4d6-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="3b4d6-222">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3b4d6-223">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-224">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3b4d6-225">Přidejte `TodoContext` třídu do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3b4d6-226">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="3b4d6-227">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="3b4d6-227">Register the database context</span></span>

<span data-ttu-id="3b4d6-228">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3b4d6-229">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="3b4d6-230">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="3b4d6-231">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-231">The preceding code:</span></span>

* <span data-ttu-id="3b4d6-232">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3b4d6-233">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3b4d6-234">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="3b4d6-235">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="3b4d6-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b4d6-237">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3b4d6-238">Vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3b4d6-239">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="3b4d6-240">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="3b4d6-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="3b4d6-241">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="3b4d6-242">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="3b4d6-243">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-244">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3b4d6-245">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="3b4d6-246">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-246">The preceding commands:</span></span>

* <span data-ttu-id="3b4d6-247">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="3b4d6-248">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="3b4d6-249">Generování uživatelského rozhraní `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="3b4d6-250">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-250">The generated code:</span></span>

* <span data-ttu-id="3b4d6-251">Označí třídu [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="3b4d6-252">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3b4d6-253">Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3b4d6-254">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3b4d6-255">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="3b4d6-256">Šablony ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="3b4d6-257">Řadiče se zobrazeními `[action]` jsou zahrnuta v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="3b4d6-258">Řadiče API neobsahují `[action]` šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="3b4d6-259">Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="3b4d6-260">To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="3b4d6-261">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="3b4d6-262">Nahraďte příkaz return v operátoru `PostTodoItem` k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="3b4d6-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="3b4d6-263">Předchozí kód je metoda HTTP POST, jak je [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) označena atributem.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="3b4d6-264">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3b4d6-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Metoda:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="3b4d6-266">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="3b4d6-267">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3b4d6-268">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="3b4d6-269">`Location` Záhlaví Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="3b4d6-270">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3b4d6-271">Odkazuje na `GetTodoItem` akci vytvoření identifikátoru `Location` URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3b4d6-272">Klíčové slovo `nameof` jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="3b4d6-273">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="3b4d6-273">Install Postman</span></span>

<span data-ttu-id="3b4d6-274">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3b4d6-275">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="3b4d6-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="3b4d6-276">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-276">Start the web app.</span></span>
* <span data-ttu-id="3b4d6-277">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-277">Start Postman.</span></span>
* <span data-ttu-id="3b4d6-278">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="3b4d6-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="3b4d6-279">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="3b4d6-280">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="3b4d6-281">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="3b4d6-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="3b4d6-282">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-282">Create a new request.</span></span>
* <span data-ttu-id="3b4d6-283">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3b4d6-284">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="3b4d6-285">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3b4d6-286">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3b4d6-287">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3b4d6-288">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-288">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="3b4d6-290">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="3b4d6-290">Test the location header URI</span></span>

* <span data-ttu-id="3b4d6-291">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3b4d6-292">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="3b4d6-292">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* <span data-ttu-id="3b4d6-294">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-294">Set the method to GET.</span></span>
* <span data-ttu-id="3b4d6-295">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="3b4d6-296">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="3b4d6-297">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-297">Examine the GET methods</span></span>

<span data-ttu-id="3b4d6-298">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="3b4d6-299">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="3b4d6-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="3b4d6-301">Odpověď podobná následující je vytvořena voláním metody `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="3b4d6-302">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="3b4d6-302">Test Get with Postman</span></span>

* <span data-ttu-id="3b4d6-303">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-303">Create a new request.</span></span>
* <span data-ttu-id="3b4d6-304">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="3b4d6-305">Nastavte adresu URL požadavku na `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3b4d6-306">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3b4d6-307">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3b4d6-308">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-308">Select **Send**.</span></span>

<span data-ttu-id="3b4d6-309">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-309">This app uses an in-memory database.</span></span> <span data-ttu-id="3b4d6-310">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="3b4d6-311">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="3b4d6-312">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="3b4d6-312">Routing and URL paths</span></span>

<span data-ttu-id="3b4d6-313">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3b4d6-314">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3b4d6-315">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="3b4d6-316">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3b4d6-317">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="3b4d6-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="3b4d6-318">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3b4d6-319">Pokud má `[HttpGet]` atribut směrovací šablonu (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3b4d6-320">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-320">This sample doesn't use a template.</span></span> <span data-ttu-id="3b4d6-321">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3b4d6-322">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3b4d6-323">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="3b4d6-324">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="3b4d6-324">Return values</span></span>

<span data-ttu-id="3b4d6-325">Návratový typ metod `GetTodoItems` a `GetTodoItem` je [ActionResult\<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3b4d6-326">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3b4d6-327">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3b4d6-328">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3b4d6-329">`ActionResult`návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3b4d6-330">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3b4d6-331">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="3b4d6-332">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3b4d6-333">Výsledkem `item` vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="3b4d6-334">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-334">The PutTodoItem method</span></span>

<span data-ttu-id="3b4d6-335">Projděte `PutTodoItem` si metodu:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="3b4d6-336">`PutTodoItem`se podobá `PostTodoItem`, s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3b4d6-337">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3b4d6-338">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3b4d6-339">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3b4d6-340">Pokud se zobrazí chyba při volání `PutTodoItem`, zajistěte, `GET` aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="3b4d6-341">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="3b4d6-342">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3b4d6-343">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3b4d6-344">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3b4d6-345">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="3b4d6-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3b4d6-346">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-346">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="3b4d6-348">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="3b4d6-349">Projděte `DeleteTodoItem` si metodu:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="3b4d6-350">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="3b4d6-351">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3b4d6-352">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3b4d6-353">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="3b4d6-354">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="3b4d6-355">Zabránit navýšení příspěvků</span><span class="sxs-lookup"><span data-stu-id="3b4d6-355">Prevent over-posting</span></span>

<span data-ttu-id="3b4d6-356">V současné době ukázková aplikace zveřejňuje celý `TodoItem` objekt.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="3b4d6-357">Výrobní aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="3b4d6-358">Je to několik důvodů na pozadí a zabezpečení je hlavní.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="3b4d6-359">Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="3b4d6-360">**DTO** se používá v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="3b4d6-361">DTO se dá použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-361">A DTO may be used to:</span></span>

* <span data-ttu-id="3b4d6-362">Zabránit přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-362">Prevent over-posting.</span></span>
* <span data-ttu-id="3b4d6-363">Skrytí vlastností, které klienti nemají zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="3b4d6-364">Vynechejte některé vlastnosti, aby se snížila velikost datové části.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="3b4d6-365">Ploché grafy objektů, které obsahují vnořené objekty.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="3b4d6-366">Ploché grafy objektů můžou být pro klienty pohodlnější.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="3b4d6-367">Chcete-li předvést DTO přístup, `TodoItem` aktualizujte třídu tak, aby obsahovala tajné pole:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="3b4d6-368">Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="3b4d6-369">Ověřte, že můžete publikovat a získat pole tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="3b4d6-370">Vytvoření modelu DTO:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="3b4d6-371">Aktualizujte `TodoItemsController` , aby `TodoItemDTO`se použil:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="3b4d6-372">Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="3b4d6-373">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="3b4d6-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="3b4d6-374">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3b4d6-375">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3b4d6-376">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-376">Create a web API project.</span></span>
> * <span data-ttu-id="3b4d6-377">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3b4d6-378">Přidejte kontroler.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-378">Add a controller.</span></span>
> * <span data-ttu-id="3b4d6-379">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="3b4d6-380">Nakonfigurujte směrování a cesty URL.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="3b4d6-381">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-381">Specify return values.</span></span>
> * <span data-ttu-id="3b4d6-382">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="3b4d6-383">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="3b4d6-384">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="3b4d6-385">Přehled</span><span class="sxs-lookup"><span data-stu-id="3b4d6-385">Overview</span></span>

<span data-ttu-id="3b4d6-386">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3b4d6-387">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3b4d6-387">API</span></span> | <span data-ttu-id="3b4d6-388">Popis</span><span class="sxs-lookup"><span data-stu-id="3b4d6-388">Description</span></span> | <span data-ttu-id="3b4d6-389">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="3b4d6-389">Request body</span></span> | <span data-ttu-id="3b4d6-390">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="3b4d6-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="3b4d6-391">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3b4d6-391">GET /api/TodoItems</span></span> | <span data-ttu-id="3b4d6-392">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-392">Get all to-do items</span></span> | <span data-ttu-id="3b4d6-393">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-393">None</span></span> | <span data-ttu-id="3b4d6-394">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-394">Array of to-do items</span></span>|
|<span data-ttu-id="3b4d6-395">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3b4d6-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="3b4d6-396">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="3b4d6-396">Get an item by ID</span></span> | <span data-ttu-id="3b4d6-397">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-397">None</span></span> | <span data-ttu-id="3b4d6-398">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-398">To-do item</span></span>|
|<span data-ttu-id="3b4d6-399">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3b4d6-399">POST /api/TodoItems</span></span> | <span data-ttu-id="3b4d6-400">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="3b4d6-400">Add a new item</span></span> | <span data-ttu-id="3b4d6-401">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-401">To-do item</span></span> | <span data-ttu-id="3b4d6-402">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-402">To-do item</span></span> |
|<span data-ttu-id="3b4d6-403">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3b4d6-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="3b4d6-404">Aktualizovat existující položku&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3b4d6-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3b4d6-405">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-405">To-do item</span></span> | <span data-ttu-id="3b4d6-406">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-406">None</span></span> |
|<span data-ttu-id="3b4d6-407">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3b4d6-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="3b4d6-408">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3b4d6-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3b4d6-409">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-409">None</span></span> | <span data-ttu-id="3b4d6-410">Žádná</span><span class="sxs-lookup"><span data-stu-id="3b4d6-410">None</span></span>|

<span data-ttu-id="3b4d6-411">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-411">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="3b4d6-417">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3b4d6-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b4d6-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b4d6-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-420">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3b4d6-421">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="3b4d6-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b4d6-423">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3b4d6-424">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3b4d6-425">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3b4d6-426">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="3b4d6-427">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="3b4d6-428">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-428">**Don't** select **Enable Docker Support**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b4d6-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b4d6-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3b4d6-431">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3b4d6-432">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3b4d6-433">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="3b4d6-434">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="3b4d6-435">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-436">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3b4d6-437">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-437">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3b4d6-439">Vyberte rozhraní > **API** > > **aplikace** **.NET Core** **Další**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="3b4d6-441">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou** verzi rozhraní \**.NET Core 2,2*.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="3b4d6-442">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="3b4d6-444">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3b4d6-444">Test the API</span></span>

<span data-ttu-id="3b4d6-445">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-445">The project template creates a `values` API.</span></span> <span data-ttu-id="3b4d6-446">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b4d6-448">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3b4d6-449">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="3b4d6-450">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="3b4d6-451">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b4d6-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b4d6-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b4d6-453">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3b4d6-454">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-455">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3b4d6-456">Vyberte **Spustit** > **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3b4d6-457">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3b4d6-458">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3b4d6-459">Připojit `/api/values` k adrese URL (změnit adresu URL na `https://localhost:<port>/api/values`)</span><span class="sxs-lookup"><span data-stu-id="3b4d6-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="3b4d6-460">Vrátí se následující JSON:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="3b4d6-461">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="3b4d6-461">Add a model class</span></span>

<span data-ttu-id="3b4d6-462">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3b4d6-463">Model pro tuto aplikaci je jediná `TodoItem` třída.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b4d6-465">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3b4d6-466">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3b4d6-467">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="3b4d6-468">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3b4d6-469">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3b4d6-470">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b4d6-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b4d6-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3b4d6-472">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3b4d6-473">Do složky `TodoItem` *modely* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-474">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3b4d6-475">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-475">Right-click the project.</span></span> <span data-ttu-id="3b4d6-476">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3b4d6-477">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-477">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3b4d6-479">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3b4d6-480">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3b4d6-481">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="3b4d6-482">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3b4d6-483">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="3b4d6-484">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="3b4d6-484">Add a database context</span></span>

<span data-ttu-id="3b4d6-485">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3b4d6-486">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b4d6-488">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3b4d6-489">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-490">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3b4d6-491">Přidejte `TodoContext` třídu do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3b4d6-492">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="3b4d6-493">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="3b4d6-493">Register the database context</span></span>

<span data-ttu-id="3b4d6-494">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3b4d6-495">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="3b4d6-496">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="3b4d6-497">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-497">The preceding code:</span></span>

* <span data-ttu-id="3b4d6-498">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3b4d6-499">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3b4d6-500">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3b4d6-501">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="3b4d6-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b4d6-503">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3b4d6-504">Vyberte možnost **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="3b4d6-505">V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="3b4d6-506">Pojmenujte třídu *TodoController*a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogové okno Přidat novou položku s řadičem ve vyhledávacím poli a vybraným kontrolérem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-508">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3b4d6-509">Ve složce *Controllers* vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="3b4d6-510">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="3b4d6-511">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-511">The preceding code:</span></span>

* <span data-ttu-id="3b4d6-512">Definuje třídu kontroleru rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="3b4d6-513">Označí třídu [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="3b4d6-514">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3b4d6-515">Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3b4d6-516">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3b4d6-517">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="3b4d6-518">Přidá položku s názvem `Item1` do databáze, pokud je databáze prázdná.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="3b4d6-519">Tento kód je v konstruktoru, takže se spustí pokaždé, když se vytvoří nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="3b4d6-520">Pokud odstraníte všechny položky, konstruktor se znovu vytvoří `Item1` při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="3b4d6-521">Takže může vypadat, že odstranění nefungovalo, pokud skutečně fungovalo.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="3b4d6-522">Přidat metody Get</span><span class="sxs-lookup"><span data-stu-id="3b4d6-522">Add Get methods</span></span>

<span data-ttu-id="3b4d6-523">Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte do `TodoController` třídy následující metody:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="3b4d6-524">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="3b4d6-525">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-525">Stop the app if it's still running.</span></span> <span data-ttu-id="3b4d6-526">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="3b4d6-527">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="3b4d6-528">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="3b4d6-529">Následující odpověď protokolu HTTP je vytvořena voláním metody `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="3b4d6-530">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="3b4d6-530">Routing and URL paths</span></span>

<span data-ttu-id="3b4d6-531">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3b4d6-532">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3b4d6-533">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="3b4d6-534">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3b4d6-535">V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="3b4d6-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="3b4d6-536">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3b4d6-537">Pokud má `[HttpGet]` atribut směrovací šablonu (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3b4d6-538">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-538">This sample doesn't use a template.</span></span> <span data-ttu-id="3b4d6-539">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3b4d6-540">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3b4d6-541">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="3b4d6-542">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="3b4d6-542">Return values</span></span>

<span data-ttu-id="3b4d6-543">Návratový typ metod `GetTodoItems` a `GetTodoItem` je [ActionResult\<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3b4d6-544">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3b4d6-545">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3b4d6-546">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3b4d6-547">`ActionResult`návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3b4d6-548">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3b4d6-549">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="3b4d6-550">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3b4d6-551">Výsledkem `item` vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="3b4d6-552">Test metody GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="3b4d6-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="3b4d6-553">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3b4d6-554">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="3b4d6-555">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-555">Start the web app.</span></span>
* <span data-ttu-id="3b4d6-556">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-556">Start Postman.</span></span>
* <span data-ttu-id="3b4d6-557">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b4d6-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b4d6-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b4d6-559">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3b4d6-560">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3b4d6-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3b4d6-561">V možnosti**předvolba** **post** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="3b4d6-562">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="3b4d6-563">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="3b4d6-564">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-564">Create a new request.</span></span>
  * <span data-ttu-id="3b4d6-565">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="3b4d6-566">Nastavte adresu URL požadavku na `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="3b4d6-567">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="3b4d6-568">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3b4d6-569">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-569">Select **Send**.</span></span>

![Odeslání pomocí žádosti Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="3b4d6-571">Přidání metody Create</span><span class="sxs-lookup"><span data-stu-id="3b4d6-571">Add a Create method</span></span>

<span data-ttu-id="3b4d6-572">Přidejte následující `PostTodoItem` metodu do *Controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="3b4d6-573">Předchozí kód je metoda HTTP POST, jak je [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) označena atributem.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="3b4d6-574">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3b4d6-575">`CreatedAtAction` Metoda:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="3b4d6-576">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="3b4d6-577">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3b4d6-578">Přidá `Location` hlavičku k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="3b4d6-579">`Location` Záhlaví Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="3b4d6-580">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3b4d6-581">Odkazuje na `GetTodoItem` akci vytvoření identifikátoru `Location` URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3b4d6-582">Klíčové slovo `nameof` jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="3b4d6-583">Test metody PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="3b4d6-584">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-584">Build the project.</span></span>
* <span data-ttu-id="3b4d6-585">V části post nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3b4d6-586">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="3b4d6-587">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3b4d6-588">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3b4d6-589">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3b4d6-590">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-590">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

  <span data-ttu-id="3b4d6-592">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání `PostTodoItem` metody nebude projekt zkompilován.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="3b4d6-593">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="3b4d6-593">Test the location header URI</span></span>

* <span data-ttu-id="3b4d6-594">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3b4d6-595">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="3b4d6-595">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/pmc2.png)

* <span data-ttu-id="3b4d6-597">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-597">Set the method to GET.</span></span>
* <span data-ttu-id="3b4d6-598">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="3b4d6-599">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="3b4d6-600">Přidat metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="3b4d6-601">Přidejte následující `PutTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="3b4d6-602">`PutTodoItem`se podobá `PostTodoItem`, s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3b4d6-603">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3b4d6-604">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3b4d6-605">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3b4d6-606">Pokud se zobrazí chyba při volání `PutTodoItem`, zajistěte, `GET` aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="3b4d6-607">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="3b4d6-608">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3b4d6-609">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3b4d6-610">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3b4d6-611">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="3b4d6-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3b4d6-612">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-612">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="3b4d6-614">Přidat metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="3b4d6-615">Přidejte následující `DeleteTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="3b4d6-616">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="3b4d6-617">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="3b4d6-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="3b4d6-618">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3b4d6-619">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3b4d6-620">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="3b4d6-621">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-621">Select **Send**.</span></span>

<span data-ttu-id="3b4d6-622">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="3b4d6-623">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="3b4d6-624">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="3b4d6-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="3b4d6-625">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="3b4d6-626">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-626">jQuery initiates the request.</span></span> <span data-ttu-id="3b4d6-627">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="3b4d6-628">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="3b4d6-629">Vytvořte složku *wwwroot* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="3b4d6-630">Do adresáře *wwwroot* přidejte soubor HTML s názvem *index. html* .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="3b4d6-631">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="3b4d6-632">Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *Web. js* .</span><span class="sxs-lookup"><span data-stu-id="3b4d6-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="3b4d6-633">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="3b4d6-634">Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="3b4d6-635">Otevřete *Properties\launchSettings.JSON*.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="3b4d6-636">Odeberte `launchUrl` vlastnost, která vynutí otevření aplikace v *indexu. html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="3b4d6-637">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="3b4d6-638">Následují vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="3b4d6-639">Získat seznam úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-639">Get a list of to-do items</span></span>

<span data-ttu-id="3b4d6-640">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="3b4d6-641">Funkce `success` zpětného volání je vyvolána, pokud je požadavek úspěšný.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="3b4d6-642">Ve zpětném volání je DOM aktualizován pomocí informací o tom.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="3b4d6-643">Přidat položku úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-643">Add a to-do item</span></span>

<span data-ttu-id="3b4d6-644">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="3b4d6-645">Možnosti `accepts` a `contentType` jsou nastaveny na `application/json` zadání typu média, který se přijímá a odesílá.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="3b4d6-646">Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="3b4d6-647">Když rozhraní API vrátí stavový kód úspěšné, `getData` funkce se vyvolá, aby se aktualizovala tabulka HTML.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="3b4d6-648">Aktualizace položky úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-648">Update a to-do item</span></span>

<span data-ttu-id="3b4d6-649">Aktualizace položky úkolů je podobná přidání.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="3b4d6-650">`url` Změny pro přidání jedinečného identifikátoru položky a `type` jsou `PUT`.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="3b4d6-651">Odstranění položky úkolů</span><span class="sxs-lookup"><span data-stu-id="3b4d6-651">Delete a to-do item</span></span>

<span data-ttu-id="3b4d6-652">Odstranění položky úkolů je provedeno nastavením `type` při volání AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="3b4d6-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="3b4d6-653">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3b4d6-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="3b4d6-654">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="3b4d6-654">Additional resources</span></span>

<span data-ttu-id="3b4d6-655">[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="3b4d6-656">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3b4d6-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="3b4d6-657">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="3b4d6-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="3b4d6-658">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="3b4d6-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

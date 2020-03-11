---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 55dfc05b5c96f7fa060d537745bac969e92daa9b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655587"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="558ce-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="558ce-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="558ce-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)a [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="558ce-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5),  and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="558ce-105">V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="558ce-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="558ce-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="558ce-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="558ce-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-107">Create a web API project.</span></span>
> * <span data-ttu-id="558ce-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="558ce-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="558ce-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="558ce-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="558ce-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="558ce-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="558ce-111">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="558ce-111">Call the web API with Postman.</span></span>

<span data-ttu-id="558ce-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="558ce-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="558ce-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="558ce-113">Overview</span></span>

<span data-ttu-id="558ce-114">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="558ce-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="558ce-115">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="558ce-115">API</span></span> | <span data-ttu-id="558ce-116">Popis</span><span class="sxs-lookup"><span data-stu-id="558ce-116">Description</span></span> | <span data-ttu-id="558ce-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="558ce-117">Request body</span></span> | <span data-ttu-id="558ce-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="558ce-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="558ce-119">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="558ce-119">GET /api/TodoItems</span></span> | <span data-ttu-id="558ce-120">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-120">Get all to-do items</span></span> | <span data-ttu-id="558ce-121">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-121">None</span></span> | <span data-ttu-id="558ce-122">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-122">Array of to-do items</span></span>|
|<span data-ttu-id="558ce-123">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="558ce-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="558ce-124">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="558ce-124">Get an item by ID</span></span> | <span data-ttu-id="558ce-125">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-125">None</span></span> | <span data-ttu-id="558ce-126">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-126">To-do item</span></span>|
|<span data-ttu-id="558ce-127">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="558ce-127">POST /api/TodoItems</span></span> | <span data-ttu-id="558ce-128">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="558ce-128">Add a new item</span></span> | <span data-ttu-id="558ce-129">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-129">To-do item</span></span> | <span data-ttu-id="558ce-130">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-130">To-do item</span></span> |
|<span data-ttu-id="558ce-131">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="558ce-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="558ce-132">Aktualizovat existující &nbsp; položky</span><span class="sxs-lookup"><span data-stu-id="558ce-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="558ce-133">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-133">To-do item</span></span> | <span data-ttu-id="558ce-134">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-134">None</span></span> |
|<span data-ttu-id="558ce-135">Odstranit &nbsp;/api/TodoItems/{id} &nbsp;</span><span class="sxs-lookup"><span data-stu-id="558ce-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="558ce-136">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="558ce-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="558ce-137">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-137">None</span></span> | <span data-ttu-id="558ce-138">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-138">None</span></span>|

<span data-ttu-id="558ce-139">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="558ce-139">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="558ce-145">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="558ce-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="558ce-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="558ce-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="558ce-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="558ce-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="558ce-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="558ce-151">V nabídce **soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="558ce-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="558ce-152">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="558ce-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="558ce-153">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="558ce-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="558ce-154">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="558ce-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="558ce-155">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="558ce-155">Select the **API** template and click **Create**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="558ce-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="558ce-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="558ce-158">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="558ce-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="558ce-159">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="558ce-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="558ce-160">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="558ce-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="558ce-161">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="558ce-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="558ce-162">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="558ce-162">The preceding commands:</span></span>

  * <span data-ttu-id="558ce-163">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="558ce-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="558ce-164">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="558ce-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="558ce-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="558ce-166">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="558ce-166">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="558ce-168">V **části rozhraní .NET Core** > **App** > **API** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="558ce-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="558ce-170">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \* *.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="558ce-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="558ce-171">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="558ce-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="558ce-173">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="558ce-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="558ce-174">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="558ce-174">Test the API</span></span>

<span data-ttu-id="558ce-175">Šablona projektu vytvoří rozhraní `WeatherForecast` API.</span><span class="sxs-lookup"><span data-stu-id="558ce-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="558ce-176">Voláním metody `Get` z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="558ce-178">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="558ce-179">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="558ce-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="558ce-180">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="558ce-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="558ce-181">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="558ce-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="558ce-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="558ce-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="558ce-183">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="558ce-184">V prohlížeči přejdete na následující adresu URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="558ce-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="558ce-185">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="558ce-186">Vyberte **spustit** > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="558ce-187">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="558ce-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="558ce-188">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="558ce-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="558ce-189">Přidejte `/WeatherForecast` k adrese URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="558ce-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="558ce-190">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="558ce-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="558ce-191">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="558ce-191">Add a model class</span></span>

<span data-ttu-id="558ce-192">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="558ce-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="558ce-193">Model pro tuto aplikaci je jediná třída `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="558ce-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="558ce-195">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="558ce-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="558ce-196">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="558ce-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="558ce-197">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="558ce-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="558ce-198">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="558ce-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="558ce-199">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="558ce-200">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="558ce-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="558ce-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="558ce-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="558ce-202">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="558ce-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="558ce-203">Přidejte třídu `TodoItem` do složky *modely* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="558ce-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="558ce-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="558ce-205">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="558ce-205">Right-click the project.</span></span> <span data-ttu-id="558ce-206">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="558ce-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="558ce-207">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="558ce-207">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="558ce-209">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="558ce-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="558ce-210">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="558ce-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="558ce-211">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="558ce-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="558ce-212">Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="558ce-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="558ce-213">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="558ce-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="558ce-214">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="558ce-214">Add a database context</span></span>

<span data-ttu-id="558ce-215">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="558ce-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="558ce-216">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="558ce-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="558ce-218">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="558ce-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="558ce-219">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="558ce-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="558ce-220">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="558ce-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="558ce-221">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="558ce-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="558ce-222">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="558ce-223">Pomocí předchozích pokynů přidejte balíček NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="558ce-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="558ce-225">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="558ce-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="558ce-226">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="558ce-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="558ce-227">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="558ce-228">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="558ce-229">Přidejte třídu `TodoContext` do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="558ce-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="558ce-230">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="558ce-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="558ce-231">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="558ce-231">Register the database context</span></span>

<span data-ttu-id="558ce-232">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="558ce-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="558ce-233">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="558ce-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="558ce-234">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="558ce-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="558ce-235">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="558ce-235">The preceding code:</span></span>

* <span data-ttu-id="558ce-236">Odebere nepoužívané deklarace `using`.</span><span class="sxs-lookup"><span data-stu-id="558ce-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="558ce-237">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="558ce-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="558ce-238">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="558ce-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="558ce-239">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="558ce-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="558ce-241">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="558ce-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="558ce-242">Vyberte **přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="558ce-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="558ce-243">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="558ce-244">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="558ce-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="558ce-245">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="558ce-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="558ce-246">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="558ce-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="558ce-247">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="558ce-248">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="558ce-249">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="558ce-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="558ce-250">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="558ce-250">The preceding commands:</span></span>

* <span data-ttu-id="558ce-251">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="558ce-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="558ce-252">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="558ce-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="558ce-253">`TodoItemsController`generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="558ce-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="558ce-254">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="558ce-254">The generated code:</span></span>

* <span data-ttu-id="558ce-255">Označí třídu atributem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="558ce-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="558ce-256">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="558ce-257">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="558ce-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="558ce-258">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="558ce-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="558ce-259">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="558ce-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="558ce-260">Šablony ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="558ce-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="558ce-261">Řadiče s zobrazeními zahrnují `[action]` v šabloně směrování.</span><span class="sxs-lookup"><span data-stu-id="558ce-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="558ce-262">Řadiče API neobsahují `[action]` v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="558ce-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="558ce-263">Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy.</span><span class="sxs-lookup"><span data-stu-id="558ce-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="558ce-264">To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="558ce-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="558ce-265">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="558ce-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="558ce-266">Nahraďte příkaz return v `PostTodoItem` pro použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="558ce-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="558ce-267">Předchozí kód je metoda HTTP POST, jak je označena atributem [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="558ce-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="558ce-268">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="558ce-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="558ce-269">Metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="558ce-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="558ce-270">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="558ce-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="558ce-271">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="558ce-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="558ce-272">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="558ce-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="558ce-273">Hlavička `Location` Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="558ce-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="558ce-274">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="558ce-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="558ce-275">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="558ce-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="558ce-276">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="558ce-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="558ce-277">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="558ce-277">Install Postman</span></span>

<span data-ttu-id="558ce-278">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="558ce-279">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="558ce-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="558ce-280">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-280">Start the web app.</span></span>
* <span data-ttu-id="558ce-281">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="558ce-281">Start Postman.</span></span>
* <span data-ttu-id="558ce-282">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="558ce-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="558ce-283">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="558ce-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="558ce-284">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="558ce-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="558ce-285">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="558ce-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="558ce-286">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="558ce-286">Create a new request.</span></span>
* <span data-ttu-id="558ce-287">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="558ce-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="558ce-288">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="558ce-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="558ce-289">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="558ce-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="558ce-290">Nastavte typ na **JSON (Application/JSON)** .</span><span class="sxs-lookup"><span data-stu-id="558ce-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="558ce-291">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="558ce-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="558ce-292">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-292">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="558ce-294">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="558ce-294">Test the location header URI</span></span>

* <span data-ttu-id="558ce-295">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="558ce-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="558ce-296">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="558ce-296">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="558ce-298">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="558ce-298">Set the method to GET.</span></span>
* <span data-ttu-id="558ce-299">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="558ce-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="558ce-300">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="558ce-301">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="558ce-301">Examine the GET methods</span></span>

<span data-ttu-id="558ce-302">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="558ce-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="558ce-303">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="558ce-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="558ce-304">Příklad:</span><span class="sxs-lookup"><span data-stu-id="558ce-304">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="558ce-305">Odpověď podobná následující je vytvořena voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="558ce-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="558ce-306">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="558ce-306">Test Get with Postman</span></span>

* <span data-ttu-id="558ce-307">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="558ce-307">Create a new request.</span></span>
* <span data-ttu-id="558ce-308">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="558ce-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="558ce-309">Nastavte adresu URL požadavku na `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="558ce-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="558ce-310">například `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="558ce-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="558ce-311">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="558ce-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="558ce-312">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-312">Select **Send**.</span></span>

<span data-ttu-id="558ce-313">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="558ce-313">This app uses an in-memory database.</span></span> <span data-ttu-id="558ce-314">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="558ce-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="558ce-315">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="558ce-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="558ce-316">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="558ce-316">Routing and URL paths</span></span>

<span data-ttu-id="558ce-317">Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="558ce-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="558ce-318">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="558ce-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="558ce-319">Začněte s řetězcem šablony v atributu `Route` kontroleru:</span><span class="sxs-lookup"><span data-stu-id="558ce-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="558ce-320">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="558ce-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="558ce-321">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="558ce-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="558ce-322">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="558ce-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="558ce-323">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="558ce-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="558ce-324">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="558ce-324">This sample doesn't use a template.</span></span> <span data-ttu-id="558ce-325">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="558ce-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="558ce-326">V následující metodě `GetTodoItem` `"{id}"` je zástupnou proměnnou pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="558ce-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="558ce-327">Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.</span><span class="sxs-lookup"><span data-stu-id="558ce-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="558ce-328">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="558ce-328">Return values</span></span>

<span data-ttu-id="558ce-329">Typ vrácené metody `GetTodoItems` a `GetTodoItem` je [ActionResult\<t > typu](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="558ce-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="558ce-330">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="558ce-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="558ce-331">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="558ce-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="558ce-332">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="558ce-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="558ce-333">návratové typy `ActionResult` mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="558ce-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="558ce-334">`GetTodoItem` může například vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="558ce-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="558ce-335">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="558ce-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="558ce-336">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="558ce-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="558ce-337">Vrácení `item` způsobí odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="558ce-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="558ce-338">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="558ce-338">The PutTodoItem method</span></span>

<span data-ttu-id="558ce-339">Projděte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="558ce-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="558ce-340">`PutTodoItem` se podobá `PostTodoItem`, s tím rozdílem, že používá PUT HTTP.</span><span class="sxs-lookup"><span data-stu-id="558ce-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="558ce-341">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="558ce-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="558ce-342">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="558ce-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="558ce-343">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="558ce-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="558ce-344">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="558ce-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="558ce-345">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="558ce-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="558ce-346">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="558ce-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="558ce-347">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="558ce-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="558ce-348">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="558ce-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="558ce-349">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="558ce-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="558ce-350">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="558ce-350">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="558ce-352">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="558ce-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="558ce-353">Projděte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="558ce-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="558ce-354">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="558ce-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="558ce-355">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="558ce-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="558ce-356">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="558ce-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="558ce-357">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="558ce-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="558ce-358">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="558ce-359">Zabránit navýšení příspěvků</span><span class="sxs-lookup"><span data-stu-id="558ce-359">Prevent over-posting</span></span>

<span data-ttu-id="558ce-360">V současné době je v ukázkové aplikaci vystavení celého objektu `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="558ce-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="558ce-361">Výrobní aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu.</span><span class="sxs-lookup"><span data-stu-id="558ce-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="558ce-362">Je to několik důvodů na pozadí a zabezpečení je hlavní.</span><span class="sxs-lookup"><span data-stu-id="558ce-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="558ce-363">Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="558ce-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="558ce-364">**DTO** se používá v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="558ce-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="558ce-365">DTO se dá použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="558ce-365">A DTO may be used to:</span></span>

* <span data-ttu-id="558ce-366">Zabránit přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="558ce-366">Prevent over-posting.</span></span>
* <span data-ttu-id="558ce-367">Skrytí vlastností, které klienti nemají zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="558ce-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="558ce-368">Vynechejte některé vlastnosti, aby se snížila velikost datové části.</span><span class="sxs-lookup"><span data-stu-id="558ce-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="558ce-369">Ploché grafy objektů, které obsahují vnořené objekty.</span><span class="sxs-lookup"><span data-stu-id="558ce-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="558ce-370">Ploché grafy objektů můžou být pro klienty pohodlnější.</span><span class="sxs-lookup"><span data-stu-id="558ce-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="558ce-371">Chcete-li předvést DTO přístup, aktualizujte třídu `TodoItem` tak, aby zahrnovala tajné pole:</span><span class="sxs-lookup"><span data-stu-id="558ce-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="558ce-372">Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.</span><span class="sxs-lookup"><span data-stu-id="558ce-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="558ce-373">Ověřte, že můžete publikovat a získat pole tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="558ce-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="558ce-374">Vytvoření modelu DTO:</span><span class="sxs-lookup"><span data-stu-id="558ce-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="558ce-375">Aktualizujte `TodoItemsController` pro použití `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="558ce-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="558ce-376">Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="558ce-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="558ce-377">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="558ce-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="558ce-378">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="558ce-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="558ce-379">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="558ce-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="558ce-380">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-380">Create a web API project.</span></span>
> * <span data-ttu-id="558ce-381">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="558ce-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="558ce-382">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="558ce-382">Add a controller.</span></span>
> * <span data-ttu-id="558ce-383">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="558ce-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="558ce-384">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="558ce-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="558ce-385">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="558ce-385">Specify return values.</span></span>
> * <span data-ttu-id="558ce-386">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="558ce-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="558ce-387">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="558ce-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="558ce-388">Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="558ce-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="558ce-389">Přehled</span><span class="sxs-lookup"><span data-stu-id="558ce-389">Overview</span></span>

<span data-ttu-id="558ce-390">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="558ce-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="558ce-391">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="558ce-391">API</span></span> | <span data-ttu-id="558ce-392">Popis</span><span class="sxs-lookup"><span data-stu-id="558ce-392">Description</span></span> | <span data-ttu-id="558ce-393">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="558ce-393">Request body</span></span> | <span data-ttu-id="558ce-394">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="558ce-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="558ce-395">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="558ce-395">GET /api/TodoItems</span></span> | <span data-ttu-id="558ce-396">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-396">Get all to-do items</span></span> | <span data-ttu-id="558ce-397">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-397">None</span></span> | <span data-ttu-id="558ce-398">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-398">Array of to-do items</span></span>|
|<span data-ttu-id="558ce-399">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="558ce-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="558ce-400">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="558ce-400">Get an item by ID</span></span> | <span data-ttu-id="558ce-401">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-401">None</span></span> | <span data-ttu-id="558ce-402">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-402">To-do item</span></span>|
|<span data-ttu-id="558ce-403">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="558ce-403">POST /api/TodoItems</span></span> | <span data-ttu-id="558ce-404">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="558ce-404">Add a new item</span></span> | <span data-ttu-id="558ce-405">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-405">To-do item</span></span> | <span data-ttu-id="558ce-406">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-406">To-do item</span></span> |
|<span data-ttu-id="558ce-407">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="558ce-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="558ce-408">Aktualizovat existující &nbsp; položky</span><span class="sxs-lookup"><span data-stu-id="558ce-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="558ce-409">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-409">To-do item</span></span> | <span data-ttu-id="558ce-410">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-410">None</span></span> |
|<span data-ttu-id="558ce-411">Odstranit &nbsp;/api/TodoItems/{id} &nbsp;</span><span class="sxs-lookup"><span data-stu-id="558ce-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="558ce-412">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="558ce-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="558ce-413">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-413">None</span></span> | <span data-ttu-id="558ce-414">Žádná</span><span class="sxs-lookup"><span data-stu-id="558ce-414">None</span></span>|

<span data-ttu-id="558ce-415">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="558ce-415">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="558ce-421">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="558ce-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="558ce-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="558ce-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="558ce-424">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="558ce-425">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="558ce-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-426">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="558ce-427">V nabídce **soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="558ce-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="558ce-428">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="558ce-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="558ce-429">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="558ce-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="558ce-430">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="558ce-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="558ce-431">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="558ce-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="558ce-432">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="558ce-432">**Don't** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="558ce-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="558ce-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="558ce-435">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="558ce-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="558ce-436">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="558ce-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="558ce-437">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="558ce-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="558ce-438">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="558ce-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="558ce-439">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="558ce-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="558ce-440">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="558ce-441">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="558ce-441">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="558ce-443">V **části rozhraní .NET Core** > **App** > **API** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="558ce-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="558ce-445">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou** verzi rozhraní \* *.NET Core 2,2*.</span><span class="sxs-lookup"><span data-stu-id="558ce-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="558ce-446">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="558ce-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="558ce-448">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="558ce-448">Test the API</span></span>

<span data-ttu-id="558ce-449">Šablona projektu vytvoří rozhraní `values` API.</span><span class="sxs-lookup"><span data-stu-id="558ce-449">The project template creates a `values` API.</span></span> <span data-ttu-id="558ce-450">Voláním metody `Get` z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="558ce-452">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="558ce-453">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="558ce-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="558ce-454">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="558ce-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="558ce-455">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="558ce-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="558ce-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="558ce-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="558ce-457">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="558ce-458">V prohlížeči přejdete na následující adresu URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="558ce-458">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="558ce-459">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="558ce-460">Vyberte **spustit** > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="558ce-461">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="558ce-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="558ce-462">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="558ce-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="558ce-463">Přidejte `/api/values` k adrese URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="558ce-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="558ce-464">Vrátí následující JSON:</span><span class="sxs-lookup"><span data-stu-id="558ce-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="558ce-465">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="558ce-465">Add a model class</span></span>

<span data-ttu-id="558ce-466">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="558ce-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="558ce-467">Model pro tuto aplikaci je jediná třída `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="558ce-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="558ce-469">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="558ce-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="558ce-470">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="558ce-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="558ce-471">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="558ce-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="558ce-472">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="558ce-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="558ce-473">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="558ce-474">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="558ce-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="558ce-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="558ce-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="558ce-476">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="558ce-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="558ce-477">Přidejte třídu `TodoItem` do složky *modely* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="558ce-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="558ce-478">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="558ce-479">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="558ce-479">Right-click the project.</span></span> <span data-ttu-id="558ce-480">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="558ce-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="558ce-481">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="558ce-481">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="558ce-483">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="558ce-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="558ce-484">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="558ce-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="558ce-485">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="558ce-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="558ce-486">Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="558ce-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="558ce-487">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="558ce-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="558ce-488">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="558ce-488">Add a database context</span></span>

<span data-ttu-id="558ce-489">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="558ce-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="558ce-490">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="558ce-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-491">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="558ce-492">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="558ce-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="558ce-493">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="558ce-494">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="558ce-495">Přidejte třídu `TodoContext` do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="558ce-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="558ce-496">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="558ce-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="558ce-497">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="558ce-497">Register the database context</span></span>

<span data-ttu-id="558ce-498">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="558ce-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="558ce-499">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="558ce-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="558ce-500">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="558ce-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="558ce-501">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="558ce-501">The preceding code:</span></span>

* <span data-ttu-id="558ce-502">Odebere nepoužívané deklarace `using`.</span><span class="sxs-lookup"><span data-stu-id="558ce-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="558ce-503">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="558ce-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="558ce-504">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="558ce-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="558ce-505">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="558ce-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="558ce-507">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="558ce-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="558ce-508">Vyberte **přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="558ce-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="558ce-509">V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="558ce-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="558ce-510">Pojmenujte třídu *TodoController*a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="558ce-512">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="558ce-513">Ve složce *Controllers* vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="558ce-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="558ce-514">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="558ce-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="558ce-515">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="558ce-515">The preceding code:</span></span>

* <span data-ttu-id="558ce-516">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="558ce-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="558ce-517">Označí třídu atributem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="558ce-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="558ce-518">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="558ce-519">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="558ce-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="558ce-520">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="558ce-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="558ce-521">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="558ce-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="558ce-522">Pokud je databáze prázdná, přidá do databáze položku s názvem `Item1`.</span><span class="sxs-lookup"><span data-stu-id="558ce-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="558ce-523">Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="558ce-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="558ce-524">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="558ce-525">Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.</span><span class="sxs-lookup"><span data-stu-id="558ce-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="558ce-526">Přidejte metody Get</span><span class="sxs-lookup"><span data-stu-id="558ce-526">Add Get methods</span></span>

<span data-ttu-id="558ce-527">Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte následující metody do třídy `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="558ce-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="558ce-528">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="558ce-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="558ce-529">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-529">Stop the app if it's still running.</span></span> <span data-ttu-id="558ce-530">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="558ce-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="558ce-531">Testování aplikace pomocí volání dva koncové body v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="558ce-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="558ce-532">Příklad:</span><span class="sxs-lookup"><span data-stu-id="558ce-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="558ce-533">Následující odpověď protokolu HTTP je vytvořena voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="558ce-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="558ce-534">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="558ce-534">Routing and URL paths</span></span>

<span data-ttu-id="558ce-535">Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="558ce-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="558ce-536">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="558ce-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="558ce-537">Začněte s řetězcem šablony v atributu `Route` kontroleru:</span><span class="sxs-lookup"><span data-stu-id="558ce-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="558ce-538">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="558ce-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="558ce-539">V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="558ce-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="558ce-540">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="558ce-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="558ce-541">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="558ce-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="558ce-542">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="558ce-542">This sample doesn't use a template.</span></span> <span data-ttu-id="558ce-543">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="558ce-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="558ce-544">V následující metodě `GetTodoItem` `"{id}"` je zástupnou proměnnou pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="558ce-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="558ce-545">Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru`id`.</span><span class="sxs-lookup"><span data-stu-id="558ce-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="558ce-546">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="558ce-546">Return values</span></span>

<span data-ttu-id="558ce-547">Typ vrácené metody `GetTodoItems` a `GetTodoItem` je [ActionResult\<t > typu](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="558ce-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="558ce-548">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="558ce-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="558ce-549">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="558ce-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="558ce-550">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="558ce-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="558ce-551">návratové typy `ActionResult` mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="558ce-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="558ce-552">`GetTodoItem` může například vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="558ce-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="558ce-553">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="558ce-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="558ce-554">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="558ce-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="558ce-555">Vrácení `item` způsobí odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="558ce-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="558ce-556">Test GetTodoItems – metoda</span><span class="sxs-lookup"><span data-stu-id="558ce-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="558ce-557">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="558ce-558">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="558ce-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="558ce-559">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="558ce-559">Start the web app.</span></span>
* <span data-ttu-id="558ce-560">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="558ce-560">Start Postman.</span></span>
* <span data-ttu-id="558ce-561">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="558ce-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="558ce-562">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="558ce-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="558ce-563">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="558ce-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="558ce-564">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="558ce-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="558ce-565">Na **kartě > \*\* **Předvolby** (karta**Obecné\*\* ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="558ce-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="558ce-566">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="558ce-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="558ce-567">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="558ce-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="558ce-568">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="558ce-568">Create a new request.</span></span>
  * <span data-ttu-id="558ce-569">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="558ce-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="558ce-570">Nastavte adresu URL požadavku na `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="558ce-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="558ce-571">například `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="558ce-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="558ce-572">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="558ce-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="558ce-573">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-573">Select **Send**.</span></span>

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="558ce-575">Přidání metody vytvoření</span><span class="sxs-lookup"><span data-stu-id="558ce-575">Add a Create method</span></span>

<span data-ttu-id="558ce-576">Do *Controllers/TodoController. cs*přidejte následující metodu `PostTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="558ce-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="558ce-577">Předchozí kód je metoda HTTP POST, jak je označena atributem [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="558ce-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="558ce-578">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="558ce-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="558ce-579">Metoda `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="558ce-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="558ce-580">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="558ce-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="558ce-581">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="558ce-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="558ce-582">Přidá hlavičku `Location` k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="558ce-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="558ce-583">Hlavička `Location` Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="558ce-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="558ce-584">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="558ce-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="558ce-585">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="558ce-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="558ce-586">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="558ce-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="558ce-587">Test PostTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="558ce-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="558ce-588">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="558ce-588">Build the project.</span></span>
* <span data-ttu-id="558ce-589">V poli post nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="558ce-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="558ce-590">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="558ce-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="558ce-591">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="558ce-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="558ce-592">Nastavte typ na **JSON (Application/JSON)** .</span><span class="sxs-lookup"><span data-stu-id="558ce-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="558ce-593">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="558ce-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="558ce-594">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-594">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  <span data-ttu-id="558ce-596">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody `PostTodoItem` nezkompiluje projekt.</span><span class="sxs-lookup"><span data-stu-id="558ce-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="558ce-597">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="558ce-597">Test the location header URI</span></span>

* <span data-ttu-id="558ce-598">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="558ce-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="558ce-599">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="558ce-599">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="558ce-601">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="558ce-601">Set the method to GET.</span></span>
* <span data-ttu-id="558ce-602">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="558ce-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="558ce-603">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="558ce-604">Přidejte metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="558ce-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="558ce-605">Přidejte následující metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="558ce-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="558ce-606">`PutTodoItem` se podobá `PostTodoItem`, s tím rozdílem, že používá PUT HTTP.</span><span class="sxs-lookup"><span data-stu-id="558ce-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="558ce-607">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="558ce-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="558ce-608">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="558ce-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="558ce-609">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="558ce-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="558ce-610">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="558ce-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="558ce-611">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="558ce-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="558ce-612">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="558ce-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="558ce-613">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="558ce-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="558ce-614">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="558ce-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="558ce-615">Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":</span><span class="sxs-lookup"><span data-stu-id="558ce-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="558ce-616">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="558ce-616">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="558ce-618">Přidejte metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="558ce-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="558ce-619">Přidejte následující metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="558ce-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="558ce-620">`DeleteTodoItem` odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="558ce-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="558ce-621">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="558ce-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="558ce-622">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="558ce-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="558ce-623">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="558ce-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="558ce-624">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="558ce-624">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="558ce-625">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="558ce-625">Select **Send**.</span></span>

<span data-ttu-id="558ce-626">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="558ce-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="558ce-627">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="558ce-628">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="558ce-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="558ce-629">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="558ce-630">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="558ce-630">jQuery initiates the request.</span></span> <span data-ttu-id="558ce-631">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="558ce-632">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="558ce-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="558ce-633">Vytvořte složku *wwwroot* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="558ce-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="558ce-634">Do adresáře *wwwroot* přidejte soubor HTML s názvem *index. html* .</span><span class="sxs-lookup"><span data-stu-id="558ce-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="558ce-635">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="558ce-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="558ce-636">Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *Web. js* .</span><span class="sxs-lookup"><span data-stu-id="558ce-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="558ce-637">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="558ce-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="558ce-638">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="558ce-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="558ce-639">Otevřete *Properties\launchSettings.JSON*.</span><span class="sxs-lookup"><span data-stu-id="558ce-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="558ce-640">Odebráním vlastnosti `launchUrl` vynutíte otevření aplikace v *indexu. html*&mdash;výchozím souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="558ce-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="558ce-641">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="558ce-642">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="558ce-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="558ce-643">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-643">Get a list of to-do items</span></span>

<span data-ttu-id="558ce-644">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="558ce-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="558ce-645">Funkce zpětného volání `success` se vyvolá, pokud je požadavek úspěšný.</span><span class="sxs-lookup"><span data-stu-id="558ce-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="558ce-646">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="558ce-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="558ce-647">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-647">Add a to-do item</span></span>

<span data-ttu-id="558ce-648">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="558ce-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="558ce-649">Možnosti `accepts` a `contentType` jsou nastaveny na `application/json` k určení typu média, který přijímá a odesílá.</span><span class="sxs-lookup"><span data-stu-id="558ce-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="558ce-650">Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="558ce-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="558ce-651">Když rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `getData`, která aktualizuje tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="558ce-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="558ce-652">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="558ce-652">Update a to-do item</span></span>

<span data-ttu-id="558ce-653">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="558ce-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="558ce-654">`url` se změní, aby se přidal jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="558ce-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="558ce-655">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="558ce-655">Delete a to-do item</span></span>

<span data-ttu-id="558ce-656">Odstranění položky úkolů je provedeno nastavením `type` v volání jazyka AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="558ce-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="558ce-657">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="558ce-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="558ce-658">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="558ce-658">Additional resources</span></span>

<span data-ttu-id="558ce-659">[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="558ce-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="558ce-660">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="558ce-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="558ce-661">Další informace najdete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="558ce-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="558ce-662">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="558ce-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

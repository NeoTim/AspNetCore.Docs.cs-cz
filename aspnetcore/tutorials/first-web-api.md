---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: ce0dfdf1ce88b55790d33918a2d20bc19a09b288
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626893"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="7c1a3-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c1a3-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="7c1a3-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)a [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="7c1a3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="7c1a3-105">V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c1a3-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7c1a3-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-107">Create a web API project.</span></span>
> * <span data-ttu-id="7c1a3-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="7c1a3-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="7c1a3-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="7c1a3-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="7c1a3-111">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-111">Call the web API with Postman.</span></span>

<span data-ttu-id="7c1a3-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="7c1a3-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="7c1a3-113">Overview</span></span>

<span data-ttu-id="7c1a3-114">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7c1a3-115">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="7c1a3-115">API</span></span> | <span data-ttu-id="7c1a3-116">Popis</span><span class="sxs-lookup"><span data-stu-id="7c1a3-116">Description</span></span> | <span data-ttu-id="7c1a3-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="7c1a3-117">Request body</span></span> | <span data-ttu-id="7c1a3-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="7c1a3-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="7c1a3-119">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-119">Get all to-do items</span></span> | <span data-ttu-id="7c1a3-120">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-120">None</span></span> | <span data-ttu-id="7c1a3-121">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="7c1a3-122">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="7c1a3-122">Get an item by ID</span></span> | <span data-ttu-id="7c1a3-123">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-123">None</span></span> | <span data-ttu-id="7c1a3-124">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="7c1a3-125">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="7c1a3-125">Add a new item</span></span> | <span data-ttu-id="7c1a3-126">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-126">To-do item</span></span> | <span data-ttu-id="7c1a3-127">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="7c1a3-128">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7c1a3-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7c1a3-129">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-129">To-do item</span></span> | <span data-ttu-id="7c1a3-130">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-130">None</span></span> |
|<span data-ttu-id="7c1a3-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7c1a3-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="7c1a3-132">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7c1a3-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7c1a3-133">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-133">None</span></span> | <span data-ttu-id="7c1a3-134">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-134">None</span></span>|

<span data-ttu-id="7c1a3-135">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-135">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7c1a3-141">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="7c1a3-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c1a3-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c1a3-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-144">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7c1a3-145">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="7c1a3-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c1a3-147">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7c1a3-148">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7c1a3-149">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7c1a3-150">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="7c1a3-151">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-151">Select the **API** template and click **Create**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c1a3-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c1a3-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c1a3-154">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7c1a3-155">Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7c1a3-156">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="7c1a3-157">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="7c1a3-158">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-158">The preceding commands:</span></span>

  * <span data-ttu-id="7c1a3-159">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="7c1a3-160">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-161">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c1a3-162">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-162">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7c1a3-164">V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="7c1a3-165">V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Výběr šablony rozhraní API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="7c1a3-167">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="7c1a3-168">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-168">Select **Next**.</span></span>

* <span data-ttu-id="7c1a3-169">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="7c1a3-171">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="7c1a3-172">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="7c1a3-172">Test the API</span></span>

<span data-ttu-id="7c1a3-173">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="7c1a3-174">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c1a3-176">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7c1a3-177">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7c1a3-178">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7c1a3-179">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c1a3-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c1a3-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c1a3-181">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7c1a3-182">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-183">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7c1a3-184">Vyberte **Spustit**  >  **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="7c1a3-185">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7c1a3-186">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7c1a3-187">Připojit `/WeatherForecast` k adrese URL (změnit adresu URL na `https://localhost:<port>/WeatherForecast` )</span><span class="sxs-lookup"><span data-stu-id="7c1a3-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="7c1a3-188">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="7c1a3-189">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="7c1a3-189">Add a model class</span></span>

<span data-ttu-id="7c1a3-190">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7c1a3-191">Model pro tuto aplikaci je jediná `TodoItem` Třída.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c1a3-193">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7c1a3-194">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7c1a3-195">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="7c1a3-196">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7c1a3-197">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7c1a3-198">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c1a3-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c1a3-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c1a3-200">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7c1a3-201">`TodoItem`Do složky *modely* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-202">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c1a3-203">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-203">Right-click the project.</span></span> <span data-ttu-id="7c1a3-204">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7c1a3-205">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-205">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7c1a3-207">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7c1a3-208">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7c1a3-209">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="7c1a3-210">`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7c1a3-211">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7c1a3-212">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="7c1a3-212">Add a database context</span></span>

<span data-ttu-id="7c1a3-213">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7c1a3-214">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="7c1a3-216">Přidání balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="7c1a3-216">Add NuGet packages</span></span>

* <span data-ttu-id="7c1a3-217">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="7c1a3-218">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="7c1a3-219">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="7c1a3-220">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="7c1a3-221">Pomocí předchozích pokynů přidejte balíček NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="7c1a3-223">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="7c1a3-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="7c1a3-224">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7c1a3-225">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-226">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7c1a3-227">Přidejte `TodoContext` třídu do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7c1a3-228">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7c1a3-229">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="7c1a3-229">Register the database context</span></span>

<span data-ttu-id="7c1a3-230">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7c1a3-231">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="7c1a3-232">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="7c1a3-233">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-233">The preceding code:</span></span>

* <span data-ttu-id="7c1a3-234">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7c1a3-235">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7c1a3-236">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="7c1a3-237">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="7c1a3-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c1a3-239">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7c1a3-240">Vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7c1a3-241">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="7c1a3-242">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="7c1a3-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="7c1a3-243">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="7c1a3-244">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="7c1a3-245">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-246">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7c1a3-247">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet-aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="7c1a3-248">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-248">The preceding commands:</span></span>

* <span data-ttu-id="7c1a3-249">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="7c1a3-250">Nainstaluje modul generování uživatelského rozhraní ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="7c1a3-251">Generování uživatelského rozhraní `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="7c1a3-252">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-252">The generated code:</span></span>

* <span data-ttu-id="7c1a3-253">Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="7c1a3-254">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7c1a3-255">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7c1a3-256">Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7c1a3-257">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="7c1a3-258">Šablony ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="7c1a3-259">Řadiče se zobrazeními jsou zahrnuta `[action]` v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="7c1a3-260">Řadiče API neobsahují `[action]` šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="7c1a3-261">Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="7c1a3-262">To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="7c1a3-263">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="7c1a3-264">Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="7c1a3-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="7c1a3-265">Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="7c1a3-266">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7c1a3-267">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7c1a3-268"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="7c1a3-269">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="7c1a3-270">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7c1a3-271">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="7c1a3-272">`Location`Záhlaví Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="7c1a3-273">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7c1a3-274">Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7c1a3-275">`nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="7c1a3-276">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="7c1a3-276">Install Postman</span></span>

<span data-ttu-id="7c1a3-277">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7c1a3-278">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="7c1a3-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="7c1a3-279">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-279">Start the web app.</span></span>
* <span data-ttu-id="7c1a3-280">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-280">Start Postman.</span></span>
* <span data-ttu-id="7c1a3-281">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="7c1a3-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="7c1a3-282">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="7c1a3-283">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="7c1a3-284">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="7c1a3-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="7c1a3-285">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-285">Create a new request.</span></span>
* <span data-ttu-id="7c1a3-286">Nastavte metodu HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7c1a3-287">Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-287">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="7c1a3-288">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-288">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="7c1a3-289">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="7c1a3-290">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7c1a3-291">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7c1a3-292">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7c1a3-293">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-293">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="7c1a3-295">Otestování identifikátoru URI hlaviček umístění pomocí metody post</span><span class="sxs-lookup"><span data-stu-id="7c1a3-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="7c1a3-296">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7c1a3-297">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="7c1a3-297">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* <span data-ttu-id="7c1a3-299">Nastavte metodu HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="7c1a3-300">Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="7c1a3-301">Například, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="7c1a3-302">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="7c1a3-303">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-303">Examine the GET methods</span></span>

<span data-ttu-id="7c1a3-304">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="7c1a3-305">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="7c1a3-306">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="7c1a3-307">Odpověď podobná následující je vytvořena voláním metody `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="7c1a3-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="7c1a3-308">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="7c1a3-308">Test Get with Postman</span></span>

* <span data-ttu-id="7c1a3-309">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-309">Create a new request.</span></span>
* <span data-ttu-id="7c1a3-310">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="7c1a3-311">Nastavte identifikátor URI žádosti na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="7c1a3-312">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="7c1a3-313">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7c1a3-314">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-314">Select **Send**.</span></span>

<span data-ttu-id="7c1a3-315">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-315">This app uses an in-memory database.</span></span> <span data-ttu-id="7c1a3-316">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="7c1a3-317">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="7c1a3-318">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="7c1a3-318">Routing and URL paths</span></span>

<span data-ttu-id="7c1a3-319">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7c1a3-320">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7c1a3-321">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="7c1a3-322">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7c1a3-323">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="7c1a3-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="7c1a3-324">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7c1a3-325">Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7c1a3-326">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-326">This sample doesn't use a template.</span></span> <span data-ttu-id="7c1a3-327">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7c1a3-328">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7c1a3-329">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7c1a3-330">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="7c1a3-330">Return values</span></span>

<span data-ttu-id="7c1a3-331">Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7c1a3-332">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7c1a3-333">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7c1a3-334">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7c1a3-335">`ActionResult` návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7c1a3-336">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7c1a3-337">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> kód chyby 404.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="7c1a3-338">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7c1a3-339">`item`Výsledkem vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="7c1a3-340">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-340">The PutTodoItem method</span></span>

<span data-ttu-id="7c1a3-341">Prohlédněte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="7c1a3-342">`PutTodoItem` se podobá `PostTodoItem` , s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7c1a3-343">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7c1a3-344">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7c1a3-345">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7c1a3-346">Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7c1a3-347">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="7c1a3-348">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="7c1a3-349">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7c1a3-350">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7c1a3-351">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="7c1a3-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7c1a3-352">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-352">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="7c1a3-354">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="7c1a3-355">Prohlédněte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7c1a3-356">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7c1a3-357">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7c1a3-358">Nastavte metodu na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7c1a3-359">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="7c1a3-360">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="7c1a3-361">Zabránit navýšení příspěvků</span><span class="sxs-lookup"><span data-stu-id="7c1a3-361">Prevent over-posting</span></span>

<span data-ttu-id="7c1a3-362">V současné době ukázková aplikace zveřejňuje celý `TodoItem` objekt.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="7c1a3-363">Produkční aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="7c1a3-364">Je to několik důvodů na pozadí a zabezpečení je hlavní.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="7c1a3-365">Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="7c1a3-366">**DTO** se používá v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="7c1a3-367">DTO se dá použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-367">A DTO may be used to:</span></span>

* <span data-ttu-id="7c1a3-368">Zabránit přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-368">Prevent over-posting.</span></span>
* <span data-ttu-id="7c1a3-369">Skrytí vlastností, které klienti nemají zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="7c1a3-370">Vynechejte některé vlastnosti, aby se snížila velikost datové části.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="7c1a3-371">Ploché grafy objektů, které obsahují vnořené objekty.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="7c1a3-372">Ploché grafy objektů můžou být pro klienty pohodlnější.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="7c1a3-373">Chcete-li předvést DTO přístup, aktualizujte `TodoItem` třídu tak, aby obsahovala tajné pole:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="7c1a3-374">Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="7c1a3-375">Ověřte, že můžete publikovat a získat pole tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="7c1a3-376">Vytvoření modelu DTO:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="7c1a3-377">Aktualizujte, `TodoItemsController` aby se použil `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="7c1a3-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="7c1a3-378">Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="7c1a3-379">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="7c1a3-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="7c1a3-380">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c1a3-381">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7c1a3-382">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-382">Create a web API project.</span></span>
> * <span data-ttu-id="7c1a3-383">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="7c1a3-384">Přidejte kontroler.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-384">Add a controller.</span></span>
> * <span data-ttu-id="7c1a3-385">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="7c1a3-386">Nakonfigurujte směrování a cesty URL.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="7c1a3-387">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-387">Specify return values.</span></span>
> * <span data-ttu-id="7c1a3-388">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="7c1a3-389">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="7c1a3-390">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="7c1a3-391">Přehled</span><span class="sxs-lookup"><span data-stu-id="7c1a3-391">Overview</span></span>

<span data-ttu-id="7c1a3-392">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7c1a3-393">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="7c1a3-393">API</span></span> | <span data-ttu-id="7c1a3-394">Popis</span><span class="sxs-lookup"><span data-stu-id="7c1a3-394">Description</span></span> | <span data-ttu-id="7c1a3-395">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="7c1a3-395">Request body</span></span> | <span data-ttu-id="7c1a3-396">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="7c1a3-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="7c1a3-397">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7c1a3-397">GET /api/TodoItems</span></span> | <span data-ttu-id="7c1a3-398">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-398">Get all to-do items</span></span> | <span data-ttu-id="7c1a3-399">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-399">None</span></span> | <span data-ttu-id="7c1a3-400">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-400">Array of to-do items</span></span>|
|<span data-ttu-id="7c1a3-401">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7c1a3-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="7c1a3-402">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="7c1a3-402">Get an item by ID</span></span> | <span data-ttu-id="7c1a3-403">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-403">None</span></span> | <span data-ttu-id="7c1a3-404">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-404">To-do item</span></span>|
|<span data-ttu-id="7c1a3-405">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7c1a3-405">POST /api/TodoItems</span></span> | <span data-ttu-id="7c1a3-406">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="7c1a3-406">Add a new item</span></span> | <span data-ttu-id="7c1a3-407">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-407">To-do item</span></span> | <span data-ttu-id="7c1a3-408">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-408">To-do item</span></span> |
|<span data-ttu-id="7c1a3-409">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7c1a3-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="7c1a3-410">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7c1a3-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7c1a3-411">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-411">To-do item</span></span> | <span data-ttu-id="7c1a3-412">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-412">None</span></span> |
|<span data-ttu-id="7c1a3-413">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7c1a3-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="7c1a3-414">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="7c1a3-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7c1a3-415">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-415">None</span></span> | <span data-ttu-id="7c1a3-416">Žádné</span><span class="sxs-lookup"><span data-stu-id="7c1a3-416">None</span></span>|

<span data-ttu-id="7c1a3-417">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-417">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7c1a3-423">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="7c1a3-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c1a3-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c1a3-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-426">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7c1a3-427">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="7c1a3-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c1a3-429">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7c1a3-430">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7c1a3-431">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7c1a3-432">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="7c1a3-433">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="7c1a3-434">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-434">**Don't** select **Enable Docker Support**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c1a3-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c1a3-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c1a3-437">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7c1a3-438">Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7c1a3-439">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="7c1a3-440">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="7c1a3-441">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-442">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c1a3-443">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-443">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7c1a3-445">V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="7c1a3-446">V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="7c1a3-447">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="7c1a3-448">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-448">Select **Next**.</span></span>

* <span data-ttu-id="7c1a3-449">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="7c1a3-451">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="7c1a3-451">Test the API</span></span>

<span data-ttu-id="7c1a3-452">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-452">The project template creates a `values` API.</span></span> <span data-ttu-id="7c1a3-453">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c1a3-455">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7c1a3-456">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7c1a3-457">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7c1a3-458">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c1a3-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c1a3-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c1a3-460">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7c1a3-461">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-462">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7c1a3-463">Vyberte **Spustit**  >  **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="7c1a3-464">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7c1a3-465">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7c1a3-466">Připojit `/api/values` k adrese URL (změnit adresu URL na `https://localhost:<port>/api/values` )</span><span class="sxs-lookup"><span data-stu-id="7c1a3-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="7c1a3-467">Vrátí se následující JSON:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="7c1a3-468">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="7c1a3-468">Add a model class</span></span>

<span data-ttu-id="7c1a3-469">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7c1a3-470">Model pro tuto aplikaci je jediná `TodoItem` Třída.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c1a3-472">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7c1a3-473">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7c1a3-474">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="7c1a3-475">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7c1a3-476">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7c1a3-477">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c1a3-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c1a3-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c1a3-479">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7c1a3-480">`TodoItem`Do složky *modely* přidejte třídu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-481">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c1a3-482">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-482">Right-click the project.</span></span> <span data-ttu-id="7c1a3-483">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7c1a3-484">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-484">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7c1a3-486">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7c1a3-487">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7c1a3-488">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="7c1a3-489">`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7c1a3-490">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7c1a3-491">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="7c1a3-491">Add a database context</span></span>

<span data-ttu-id="7c1a3-492">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7c1a3-493">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c1a3-495">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7c1a3-496">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-497">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7c1a3-498">Přidejte `TodoContext` třídu do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7c1a3-499">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7c1a3-500">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="7c1a3-500">Register the database context</span></span>

<span data-ttu-id="7c1a3-501">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7c1a3-502">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="7c1a3-503">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="7c1a3-504">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-504">The preceding code:</span></span>

* <span data-ttu-id="7c1a3-505">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7c1a3-506">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7c1a3-507">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7c1a3-508">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="7c1a3-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c1a3-510">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7c1a3-511">Vyberte možnost **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="7c1a3-512">V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="7c1a3-513">Pojmenujte třídu *TodoController*a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogové okno Přidat novou položku s řadičem ve vyhledávacím poli a vybraným kontrolérem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-515">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7c1a3-516">Ve složce *Controllers* vytvořte třídu s názvem `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="7c1a3-517">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="7c1a3-518">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-518">The preceding code:</span></span>

* <span data-ttu-id="7c1a3-519">Definuje třídu kontroleru rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="7c1a3-520">Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="7c1a3-521">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7c1a3-522">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7c1a3-523">Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7c1a3-524">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="7c1a3-525">Přidá položku s názvem `Item1` do databáze, pokud je databáze prázdná.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="7c1a3-526">Tento kód je v konstruktoru, takže se spustí pokaždé, když se vytvoří nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="7c1a3-527">Pokud odstraníte všechny položky, konstruktor se znovu vytvoří `Item1` při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="7c1a3-528">Takže může vypadat, že odstranění nefungovalo, pokud skutečně fungovalo.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="7c1a3-529">Přidat metody Get</span><span class="sxs-lookup"><span data-stu-id="7c1a3-529">Add Get methods</span></span>

<span data-ttu-id="7c1a3-530">Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte do třídy následující metody `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="7c1a3-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="7c1a3-531">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="7c1a3-532">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-532">Stop the app if it's still running.</span></span> <span data-ttu-id="7c1a3-533">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="7c1a3-534">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="7c1a3-535">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="7c1a3-536">Následující odpověď protokolu HTTP je vytvořena voláním metody `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="7c1a3-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="7c1a3-537">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="7c1a3-537">Routing and URL paths</span></span>

<span data-ttu-id="7c1a3-538">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7c1a3-539">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7c1a3-540">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="7c1a3-541">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7c1a3-542">V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="7c1a3-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="7c1a3-543">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7c1a3-544">Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7c1a3-545">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-545">This sample doesn't use a template.</span></span> <span data-ttu-id="7c1a3-546">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7c1a3-547">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7c1a3-548">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7c1a3-549">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="7c1a3-549">Return values</span></span>

<span data-ttu-id="7c1a3-550">Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7c1a3-551">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7c1a3-552">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7c1a3-553">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7c1a3-554">`ActionResult` návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7c1a3-555">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7c1a3-556">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> kód chyby 404.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="7c1a3-557">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7c1a3-558">`item`Výsledkem vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="7c1a3-559">Test metody GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="7c1a3-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="7c1a3-560">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7c1a3-561">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="7c1a3-562">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-562">Start the web app.</span></span>
* <span data-ttu-id="7c1a3-563">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-563">Start Postman.</span></span>
* <span data-ttu-id="7c1a3-564">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c1a3-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c1a3-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7c1a3-566">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7c1a3-567">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="7c1a3-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7c1a3-568">V **Postman**možnosti  >  **předvolba** post (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="7c1a3-569">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="7c1a3-570">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="7c1a3-571">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-571">Create a new request.</span></span>
  * <span data-ttu-id="7c1a3-572">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="7c1a3-573">Nastavte identifikátor URI žádosti na `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="7c1a3-574">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="7c1a3-575">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7c1a3-576">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-576">Select **Send**.</span></span>

![Odeslání pomocí žádosti Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="7c1a3-578">Přidání metody Create</span><span class="sxs-lookup"><span data-stu-id="7c1a3-578">Add a Create method</span></span>

<span data-ttu-id="7c1a3-579">Přidejte následující `PostTodoItem` metodu do *Controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="7c1a3-580">Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="7c1a3-581">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7c1a3-582">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="7c1a3-583">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="7c1a3-584">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7c1a3-585">Přidá `Location` hlavičku k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="7c1a3-586">`Location`Záhlaví Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="7c1a3-587">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7c1a3-588">Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7c1a3-589">`nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="7c1a3-590">Test metody PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="7c1a3-591">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-591">Build the project.</span></span>
* <span data-ttu-id="7c1a3-592">V části post nastavte metodu HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7c1a3-593">Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="7c1a3-594">Například, `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="7c1a3-595">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="7c1a3-596">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7c1a3-597">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7c1a3-598">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7c1a3-599">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-599">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

  <span data-ttu-id="7c1a3-601">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody nebude projekt zkompilován `PostTodoItem` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="7c1a3-602">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="7c1a3-602">Test the location header URI</span></span>

* <span data-ttu-id="7c1a3-603">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7c1a3-604">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="7c1a3-604">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/pmc2.png)

* <span data-ttu-id="7c1a3-606">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-606">Set the method to GET.</span></span>
<span data-ttu-id="7c1a3-607">\* Nastavte identifikátor URI na  `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="7c1a3-608">Například  `https://localhost:5001/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="7c1a3-609">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="7c1a3-610">Přidat metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="7c1a3-611">Přidejte následující `PutTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="7c1a3-612">`PutTodoItem` se podobá `PostTodoItem` , s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7c1a3-613">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7c1a3-614">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7c1a3-615">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7c1a3-616">Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7c1a3-617">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="7c1a3-618">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="7c1a3-619">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7c1a3-620">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7c1a3-621">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="7c1a3-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7c1a3-622">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-622">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="7c1a3-624">Přidat metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="7c1a3-625">Přidejte následující `DeleteTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="7c1a3-626">`DeleteTodoItem`Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7c1a3-627">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7c1a3-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7c1a3-628">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7c1a3-629">Nastavte metodu na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7c1a3-630">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="7c1a3-631">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-631">Select **Send**.</span></span>

<span data-ttu-id="7c1a3-632">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="7c1a3-633">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="7c1a3-634">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="7c1a3-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="7c1a3-635">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="7c1a3-636">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-636">jQuery initiates the request.</span></span> <span data-ttu-id="7c1a3-637">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="7c1a3-638">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) , a [Povolte výchozí mapování souborů](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="7c1a3-639">Vytvořte složku *wwwroot* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="7c1a3-640">Do adresáře *wwwroot* přidejte soubor HTML s názvem *index.html* .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="7c1a3-641">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="7c1a3-642">Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *site.js* .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="7c1a3-643">Jeho obsah nahraďte následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="7c1a3-644">Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="7c1a3-645">Otevřete *Properties\launchSettings.js*.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="7c1a3-646">Odeberte `launchUrl` vlastnost, která vynutí otevření aplikace na *index.html* &mdash; výchozího souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="7c1a3-647">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="7c1a3-648">Následují vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="7c1a3-649">Získat seznam úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-649">Get a list of to-do items</span></span>

<span data-ttu-id="7c1a3-650">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="7c1a3-651">`success`Funkce zpětného volání je vyvolána, pokud je požadavek úspěšný.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="7c1a3-652">Ve zpětném volání je DOM aktualizován pomocí informací o tom.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="7c1a3-653">Přidat položku úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-653">Add a to-do item</span></span>

<span data-ttu-id="7c1a3-654">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="7c1a3-655">`accepts`Možnosti a `contentType` jsou nastaveny na `application/json` zadání typu média, který se přijímá a odesílá.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="7c1a3-656">Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="7c1a3-657">Když rozhraní API vrátí stavový kód úspěšné, `getData` funkce se vyvolá, aby se aktualizovala tabulka HTML.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="7c1a3-658">Aktualizace položky úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-658">Update a to-do item</span></span>

<span data-ttu-id="7c1a3-659">Aktualizace položky úkolů je podobná přidání.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="7c1a3-660">`url`Změny pro přidání jedinečného identifikátoru položky a `type` jsou `PUT` .</span><span class="sxs-lookup"><span data-stu-id="7c1a3-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="7c1a3-661">Odstranění položky úkolů</span><span class="sxs-lookup"><span data-stu-id="7c1a3-661">Delete a to-do item</span></span>

<span data-ttu-id="7c1a3-662">Odstranění položky úkolů je provedeno nastavením `type` při volání AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="7c1a3-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="7c1a3-663">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="7c1a3-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="7c1a3-664">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="7c1a3-664">Additional resources</span></span>

<span data-ttu-id="7c1a3-665">[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="7c1a3-666">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7c1a3-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="7c1a3-667">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="7c1a3-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="7c1a3-668">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="7c1a3-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

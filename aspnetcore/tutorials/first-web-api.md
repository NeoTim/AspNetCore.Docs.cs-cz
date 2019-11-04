---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2019
uid: tutorials/first-web-api
ms.openlocfilehash: abb55ea12583374639f28945037cb6aa41a5a32d
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73427043"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4ef0f-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ef0f-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4ef0f-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4ef0f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4ef0f-105">V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ef0f-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ef0f-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-107">Create a web API project.</span></span>
> * <span data-ttu-id="4ef0f-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4ef0f-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="4ef0f-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4ef0f-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4ef0f-111">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-111">Call the web API with Postman.</span></span>

<span data-ttu-id="4ef0f-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4ef0f-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="4ef0f-113">Overview</span></span>

<span data-ttu-id="4ef0f-114">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4ef0f-115">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4ef0f-115">API</span></span> | <span data-ttu-id="4ef0f-116">Popis</span><span class="sxs-lookup"><span data-stu-id="4ef0f-116">Description</span></span> | <span data-ttu-id="4ef0f-117">Text žádosti</span><span class="sxs-lookup"><span data-stu-id="4ef0f-117">Request body</span></span> | <span data-ttu-id="4ef0f-118">Tělo odpovědi</span><span class="sxs-lookup"><span data-stu-id="4ef0f-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4ef0f-119">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4ef0f-119">GET /api/TodoItems</span></span> | <span data-ttu-id="4ef0f-120">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-120">Get all to-do items</span></span> | <span data-ttu-id="4ef0f-121">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-121">None</span></span> | <span data-ttu-id="4ef0f-122">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-122">Array of to-do items</span></span>|
|<span data-ttu-id="4ef0f-123">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4ef0f-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4ef0f-124">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="4ef0f-124">Get an item by ID</span></span> | <span data-ttu-id="4ef0f-125">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-125">None</span></span> | <span data-ttu-id="4ef0f-126">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-126">To-do item</span></span>|
|<span data-ttu-id="4ef0f-127">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4ef0f-127">POST /api/TodoItems</span></span> | <span data-ttu-id="4ef0f-128">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="4ef0f-128">Add a new item</span></span> | <span data-ttu-id="4ef0f-129">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-129">To-do item</span></span> | <span data-ttu-id="4ef0f-130">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-130">To-do item</span></span> |
|<span data-ttu-id="4ef0f-131">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4ef0f-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4ef0f-132">Aktualizovat existující &nbsp; položky</span><span class="sxs-lookup"><span data-stu-id="4ef0f-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4ef0f-133">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-133">To-do item</span></span> | <span data-ttu-id="4ef0f-134">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-134">None</span></span> |
|<span data-ttu-id="4ef0f-135">Odstranit &nbsp;/api/TodoItems/{id} &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4ef0f-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4ef0f-136">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4ef0f-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4ef0f-137">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-137">None</span></span> | <span data-ttu-id="4ef0f-138">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-138">None</span></span>|

<span data-ttu-id="4ef0f-139">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-139">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4ef0f-145">Požadavky</span><span class="sxs-lookup"><span data-stu-id="4ef0f-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ef0f-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef0f-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ef0f-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4ef0f-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="4ef0f-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef0f-151">V nabídce **soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4ef0f-152">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4ef0f-153">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4ef0f-154">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="4ef0f-155">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-155">Select the **API** template and click **Create**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ef0f-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef0f-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ef0f-158">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4ef0f-159">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4ef0f-160">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="4ef0f-161">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4ef0f-162">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-162">The preceding commands:</span></span>

  * <span data-ttu-id="4ef0f-163">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4ef0f-164">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ef0f-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4ef0f-166">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-166">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4ef0f-168">V **části rozhraní .NET Core** > **App** > **API** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4ef0f-170">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \* *.NET Core 3,0*.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="4ef0f-171">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="4ef0f-173">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="4ef0f-174">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4ef0f-174">Test the API</span></span>

<span data-ttu-id="4ef0f-175">Šablona projektu vytvoří rozhraní `WeatherForecast` API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="4ef0f-176">Voláním metody `Get` z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4ef0f-178">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4ef0f-179">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4ef0f-180">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4ef0f-181">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ef0f-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef0f-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4ef0f-183">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4ef0f-184">V prohlížeči přejdete na následující adresu URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ef0f-185">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4ef0f-186">Vyberte **spustit**  > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4ef0f-187">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4ef0f-188">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4ef0f-189">Přidejte `/WeatherForecast` k adrese URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="4ef0f-190">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="4ef0f-191">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="4ef0f-191">Add a model class</span></span>

<span data-ttu-id="4ef0f-192">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4ef0f-193">Model pro tuto aplikaci je jediná třída `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef0f-195">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4ef0f-196">Vyberte **přidat**  > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4ef0f-197">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="4ef0f-198">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4ef0f-199">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4ef0f-200">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ef0f-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef0f-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ef0f-202">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4ef0f-203">Přidejte třídu `TodoItem` do složky *modely* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ef0f-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4ef0f-205">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-205">Right-click the project.</span></span> <span data-ttu-id="4ef0f-206">Vyberte **přidat**  > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4ef0f-207">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-207">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4ef0f-209">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4ef0f-210">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4ef0f-211">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4ef0f-212">Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4ef0f-213">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4ef0f-214">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="4ef0f-214">Add a database context</span></span>

<span data-ttu-id="4ef0f-215">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4ef0f-216">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="4ef0f-218">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="4ef0f-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="4ef0f-219">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4ef0f-220">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="4ef0f-221">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="4ef0f-222">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="4ef0f-223">Pomocí předchozích pokynů přidejte balíček NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4ef0f-225">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="4ef0f-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="4ef0f-226">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4ef0f-227">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4ef0f-228">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4ef0f-229">Přidejte třídu `TodoContext` do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4ef0f-230">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4ef0f-231">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="4ef0f-231">Register the database context</span></span>

<span data-ttu-id="4ef0f-232">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4ef0f-233">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="4ef0f-234">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4ef0f-235">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-235">The preceding code:</span></span>

* <span data-ttu-id="4ef0f-236">Odebere nepoužívané deklarace `using`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4ef0f-237">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4ef0f-238">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4ef0f-239">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="4ef0f-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef0f-241">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4ef0f-242">Vyberte **přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4ef0f-243">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4ef0f-244">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="4ef0f-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4ef0f-245">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4ef0f-246">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4ef0f-247">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4ef0f-248">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4ef0f-249">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="4ef0f-250">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-250">The preceding commands:</span></span>

* <span data-ttu-id="4ef0f-251">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4ef0f-252">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4ef0f-253">`TodoItemsController`generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4ef0f-254">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-254">The generated code:</span></span>

* <span data-ttu-id="4ef0f-255">Definuje třídu kontroleru rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4ef0f-256">Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-256">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4ef0f-257">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4ef0f-258">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4ef0f-259">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4ef0f-260">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="4ef0f-261">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="4ef0f-262">Nahraďte příkaz return v `PostTodoItem` pro použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="4ef0f-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4ef0f-263">Předchozí kód je metoda HTTP POST, která je označena atributem [[HTTPPOST]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4ef0f-264">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4ef0f-265">Metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4ef0f-266">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="4ef0f-267">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4ef0f-268">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4ef0f-269">Hlavička `Location` Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4ef0f-270">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4ef0f-271">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4ef0f-272">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4ef0f-273">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="4ef0f-273">Install Postman</span></span>

<span data-ttu-id="4ef0f-274">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4ef0f-275">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="4ef0f-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4ef0f-276">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-276">Start the web app.</span></span>
* <span data-ttu-id="4ef0f-277">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-277">Start Postman.</span></span>
* <span data-ttu-id="4ef0f-278">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="4ef0f-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="4ef0f-279">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4ef0f-280">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4ef0f-281">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="4ef0f-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4ef0f-282">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-282">Create a new request.</span></span>
* <span data-ttu-id="4ef0f-283">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4ef0f-284">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="4ef0f-285">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4ef0f-286">Nastavte typ na **JSON (Application/JSON)** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4ef0f-287">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4ef0f-288">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-288">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4ef0f-290">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="4ef0f-290">Test the location header URI</span></span>

* <span data-ttu-id="4ef0f-291">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4ef0f-292">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="4ef0f-292">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* <span data-ttu-id="4ef0f-294">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-294">Set the method to GET.</span></span>
* <span data-ttu-id="4ef0f-295">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4ef0f-296">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4ef0f-297">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-297">Examine the GET methods</span></span>

<span data-ttu-id="4ef0f-298">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4ef0f-299">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4ef0f-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="4ef0f-301">Odpověď podobná následující je vytvořena voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4ef0f-302">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="4ef0f-302">Test Get with Postman</span></span>

* <span data-ttu-id="4ef0f-303">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-303">Create a new request.</span></span>
* <span data-ttu-id="4ef0f-304">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4ef0f-305">Nastavte adresu URL požadavku na `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4ef0f-306">Například `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4ef0f-307">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4ef0f-308">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-308">Select **Send**.</span></span>

<span data-ttu-id="4ef0f-309">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-309">This app uses an in-memory database.</span></span> <span data-ttu-id="4ef0f-310">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4ef0f-311">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4ef0f-312">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="4ef0f-312">Routing and URL paths</span></span>

<span data-ttu-id="4ef0f-313">Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4ef0f-314">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4ef0f-315">Začněte s řetězcem šablony v atributu `Route` kontroleru:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4ef0f-316">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4ef0f-317">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="4ef0f-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4ef0f-318">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4ef0f-319">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4ef0f-320">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-320">This sample doesn't use a template.</span></span> <span data-ttu-id="4ef0f-321">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4ef0f-322">V následující metodě `GetTodoItem` `"{id}"` je zástupnou proměnnou pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4ef0f-323">Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4ef0f-324">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="4ef0f-324">Return values</span></span>

<span data-ttu-id="4ef0f-325">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult \<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4ef0f-326">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4ef0f-327">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4ef0f-328">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4ef0f-329">návratové typy `ActionResult` mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4ef0f-330">`GetTodoItem` může například vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4ef0f-331">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4ef0f-332">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4ef0f-333">Vrácení `item` způsobí odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4ef0f-334">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-334">The PutTodoItem method</span></span>

<span data-ttu-id="4ef0f-335">Projděte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4ef0f-336">`PutTodoItem` se podobá `PostTodoItem`, s tím rozdílem, že používá PUT HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4ef0f-337">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4ef0f-338">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4ef0f-339">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4ef0f-340">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4ef0f-341">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="4ef0f-342">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4ef0f-343">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4ef0f-344">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4ef0f-345">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="4ef0f-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4ef0f-346">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-346">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4ef0f-348">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="4ef0f-349">Projděte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="4ef0f-350">`DeleteTodoItem` odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4ef0f-351">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4ef0f-352">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4ef0f-353">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4ef0f-354">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-354">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4ef0f-355">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-355">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4ef0f-356">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4ef0f-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="4ef0f-357">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ef0f-358">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ef0f-359">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-359">Create a web API project.</span></span>
> * <span data-ttu-id="4ef0f-360">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4ef0f-361">Přidejte kontroler.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-361">Add a controller.</span></span>
> * <span data-ttu-id="4ef0f-362">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="4ef0f-363">Nakonfigurujte směrování a cesty URL.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4ef0f-364">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-364">Specify return values.</span></span>
> * <span data-ttu-id="4ef0f-365">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4ef0f-366">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="4ef0f-367">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="4ef0f-368">Přehled</span><span class="sxs-lookup"><span data-stu-id="4ef0f-368">Overview</span></span>

<span data-ttu-id="4ef0f-369">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4ef0f-370">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4ef0f-370">API</span></span> | <span data-ttu-id="4ef0f-371">Popis</span><span class="sxs-lookup"><span data-stu-id="4ef0f-371">Description</span></span> | <span data-ttu-id="4ef0f-372">Text žádosti</span><span class="sxs-lookup"><span data-stu-id="4ef0f-372">Request body</span></span> | <span data-ttu-id="4ef0f-373">Tělo odpovědi</span><span class="sxs-lookup"><span data-stu-id="4ef0f-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4ef0f-374">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4ef0f-374">GET /api/TodoItems</span></span> | <span data-ttu-id="4ef0f-375">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-375">Get all to-do items</span></span> | <span data-ttu-id="4ef0f-376">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-376">None</span></span> | <span data-ttu-id="4ef0f-377">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-377">Array of to-do items</span></span>|
|<span data-ttu-id="4ef0f-378">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4ef0f-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4ef0f-379">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="4ef0f-379">Get an item by ID</span></span> | <span data-ttu-id="4ef0f-380">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-380">None</span></span> | <span data-ttu-id="4ef0f-381">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-381">To-do item</span></span>|
|<span data-ttu-id="4ef0f-382">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4ef0f-382">POST /api/TodoItems</span></span> | <span data-ttu-id="4ef0f-383">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="4ef0f-383">Add a new item</span></span> | <span data-ttu-id="4ef0f-384">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-384">To-do item</span></span> | <span data-ttu-id="4ef0f-385">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-385">To-do item</span></span> |
|<span data-ttu-id="4ef0f-386">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4ef0f-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4ef0f-387">Aktualizovat existující &nbsp; položky</span><span class="sxs-lookup"><span data-stu-id="4ef0f-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4ef0f-388">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-388">To-do item</span></span> | <span data-ttu-id="4ef0f-389">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-389">None</span></span> |
|<span data-ttu-id="4ef0f-390">Odstranit &nbsp;/api/TodoItems/{id} &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4ef0f-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4ef0f-391">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4ef0f-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4ef0f-392">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-392">None</span></span> | <span data-ttu-id="4ef0f-393">Žádné</span><span class="sxs-lookup"><span data-stu-id="4ef0f-393">None</span></span>|

<span data-ttu-id="4ef0f-394">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-394">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4ef0f-400">Požadavky</span><span class="sxs-lookup"><span data-stu-id="4ef0f-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ef0f-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef0f-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ef0f-403">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4ef0f-404">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="4ef0f-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef0f-406">V nabídce **soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4ef0f-407">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4ef0f-408">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4ef0f-409">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4ef0f-410">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4ef0f-411">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-411">**Don't** select **Enable Docker Support**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ef0f-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef0f-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ef0f-414">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4ef0f-415">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4ef0f-416">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4ef0f-417">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4ef0f-418">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ef0f-419">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4ef0f-420">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-420">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4ef0f-422">V **části rozhraní .NET Core** > **App** > **API** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4ef0f-424">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** přijměte výchozí **cílovou** verzi rozhraní \* *.NET Core 2,2*.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="4ef0f-425">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4ef0f-427">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4ef0f-427">Test the API</span></span>

<span data-ttu-id="4ef0f-428">Šablona projektu vytvoří rozhraní `values` API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-428">The project template creates a `values` API.</span></span> <span data-ttu-id="4ef0f-429">Voláním metody `Get` z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4ef0f-431">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4ef0f-432">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4ef0f-433">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4ef0f-434">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ef0f-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef0f-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4ef0f-436">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4ef0f-437">V prohlížeči přejdete na následující adresu URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ef0f-438">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4ef0f-439">Vyberte **spustit**  > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4ef0f-440">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4ef0f-441">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4ef0f-442">Přidejte `/api/values` k adrese URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4ef0f-443">Vrátí se následující kód JSON:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4ef0f-444">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="4ef0f-444">Add a model class</span></span>

<span data-ttu-id="4ef0f-445">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4ef0f-446">Model pro tuto aplikaci je jediná třída `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef0f-448">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4ef0f-449">Vyberte **přidat**  > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4ef0f-450">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="4ef0f-451">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4ef0f-452">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4ef0f-453">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ef0f-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ef0f-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ef0f-455">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4ef0f-456">Přidejte třídu `TodoItem` do složky *modely* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ef0f-457">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4ef0f-458">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-458">Right-click the project.</span></span> <span data-ttu-id="4ef0f-459">Vyberte **přidat**  > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4ef0f-460">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-460">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4ef0f-462">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecné** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4ef0f-463">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4ef0f-464">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4ef0f-465">Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4ef0f-466">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4ef0f-467">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="4ef0f-467">Add a database context</span></span>

<span data-ttu-id="4ef0f-468">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4ef0f-469">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef0f-471">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4ef0f-472">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4ef0f-473">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4ef0f-474">Přidejte třídu `TodoContext` do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4ef0f-475">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4ef0f-476">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="4ef0f-476">Register the database context</span></span>

<span data-ttu-id="4ef0f-477">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4ef0f-478">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="4ef0f-479">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4ef0f-480">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-480">The preceding code:</span></span>

* <span data-ttu-id="4ef0f-481">Odebere nepoužívané deklarace `using`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4ef0f-482">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4ef0f-483">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4ef0f-484">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="4ef0f-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef0f-486">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4ef0f-487">Vyberte **přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4ef0f-488">V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4ef0f-489">Pojmenujte třídu *TodoController*a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogové okno Přidat novou položku s řadičem ve vyhledávacím poli a vybraným kontrolérem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4ef0f-491">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4ef0f-492">Ve složce *Controllers* vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4ef0f-493">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4ef0f-494">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-494">The preceding code:</span></span>

* <span data-ttu-id="4ef0f-495">Definuje třídu kontroleru rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4ef0f-496">Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-496">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4ef0f-497">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4ef0f-498">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4ef0f-499">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4ef0f-500">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4ef0f-501">Pokud je databáze prázdná, přidá do databáze položku s názvem `Item1`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4ef0f-502">Tento kód je v konstruktoru, takže se spustí pokaždé, když se vytvoří nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4ef0f-503">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4ef0f-504">Takže může vypadat, že odstranění nefungovalo, pokud skutečně fungovalo.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4ef0f-505">Přidat metody Get</span><span class="sxs-lookup"><span data-stu-id="4ef0f-505">Add Get methods</span></span>

<span data-ttu-id="4ef0f-506">Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte následující metody do třídy `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4ef0f-507">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4ef0f-508">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-508">Stop the app if it's still running.</span></span> <span data-ttu-id="4ef0f-509">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="4ef0f-510">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4ef0f-511">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4ef0f-512">Následující odpověď protokolu HTTP je vytvořena voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4ef0f-513">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="4ef0f-513">Routing and URL paths</span></span>

<span data-ttu-id="4ef0f-514">Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4ef0f-515">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4ef0f-516">Začněte s řetězcem šablony v atributu `Route` kontroleru:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4ef0f-517">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4ef0f-518">V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="4ef0f-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4ef0f-519">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4ef0f-520">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4ef0f-521">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-521">This sample doesn't use a template.</span></span> <span data-ttu-id="4ef0f-522">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4ef0f-523">V následující metodě `GetTodoItem` `"{id}"` je zástupnou proměnnou pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4ef0f-524">Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4ef0f-525">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="4ef0f-525">Return values</span></span>

<span data-ttu-id="4ef0f-526">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult \<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4ef0f-527">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4ef0f-528">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4ef0f-529">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4ef0f-530">návratové typy `ActionResult` mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4ef0f-531">`GetTodoItem` může například vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4ef0f-532">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4ef0f-533">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4ef0f-534">Vrácení `item` způsobí odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4ef0f-535">Test metody GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="4ef0f-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="4ef0f-536">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4ef0f-537">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-537">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="4ef0f-538">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-538">Start the web app.</span></span>
* <span data-ttu-id="4ef0f-539">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-539">Start Postman.</span></span>
* <span data-ttu-id="4ef0f-540">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-540">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ef0f-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ef0f-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ef0f-542">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4ef0f-543">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4ef0f-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4ef0f-544">Na **kartě  > \*\* **Předvolby** (karta**Obecné\*\* ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="4ef0f-545">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="4ef0f-546">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4ef0f-547">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-547">Create a new request.</span></span>
  * <span data-ttu-id="4ef0f-548">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4ef0f-549">Nastavte adresu URL požadavku na `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4ef0f-550">Například `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4ef0f-551">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4ef0f-552">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-552">Select **Send**.</span></span>

![Odeslání pomocí žádosti Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4ef0f-554">Přidání metody Create</span><span class="sxs-lookup"><span data-stu-id="4ef0f-554">Add a Create method</span></span>

<span data-ttu-id="4ef0f-555">Do *Controllers/TodoController. cs*přidejte následující metodu `PostTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4ef0f-556">Předchozí kód je metoda HTTP POST, která je označena atributem [[HTTPPOST]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-556">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4ef0f-557">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4ef0f-558">Metoda `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4ef0f-559">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4ef0f-560">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4ef0f-561">Přidá hlavičku `Location` k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4ef0f-562">Hlavička `Location` Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4ef0f-563">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4ef0f-564">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4ef0f-565">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4ef0f-566">Test metody PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4ef0f-567">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-567">Build the project.</span></span>
* <span data-ttu-id="4ef0f-568">V poli post nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4ef0f-569">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="4ef0f-570">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4ef0f-571">Nastavte typ na **JSON (Application/JSON)** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4ef0f-572">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4ef0f-573">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-573">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

  <span data-ttu-id="4ef0f-575">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody `PostTodoItem` nezkompiluje projekt.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4ef0f-576">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="4ef0f-576">Test the location header URI</span></span>

* <span data-ttu-id="4ef0f-577">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4ef0f-578">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="4ef0f-578">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4ef0f-580">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-580">Set the method to GET.</span></span>
* <span data-ttu-id="4ef0f-581">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="4ef0f-582">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4ef0f-583">Přidat metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="4ef0f-584">Přidejte následující metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4ef0f-585">`PutTodoItem` se podobá `PostTodoItem`, s tím rozdílem, že používá PUT HTTP.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4ef0f-586">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4ef0f-587">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4ef0f-588">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4ef0f-589">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4ef0f-590">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="4ef0f-591">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-591">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4ef0f-592">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4ef0f-593">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4ef0f-594">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="4ef0f-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4ef0f-595">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-595">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4ef0f-597">Přidat metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4ef0f-598">Přidejte následující metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4ef0f-599">`DeleteTodoItem` odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4ef0f-600">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="4ef0f-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4ef0f-601">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4ef0f-602">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4ef0f-603">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-603">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="4ef0f-604">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-604">Select **Send**.</span></span>

<span data-ttu-id="4ef0f-605">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4ef0f-606">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4ef0f-607">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4ef0f-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="4ef0f-608">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="4ef0f-609">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-609">jQuery initiates the request.</span></span> <span data-ttu-id="4ef0f-610">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="4ef0f-611">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="4ef0f-612">Vytvořte složku *wwwroot* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="4ef0f-613">Do adresáře *wwwroot* přidejte soubor HTML s názvem *index. html* .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4ef0f-614">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4ef0f-615">Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *Web. js* .</span><span class="sxs-lookup"><span data-stu-id="4ef0f-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4ef0f-616">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4ef0f-617">Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4ef0f-618">Otevřete *Properties\launchSettings.JSON*.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4ef0f-619">Odebráním vlastnosti `launchUrl` vynutíte otevření aplikace v *indexu. html* &mdash;the výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4ef0f-620">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="4ef0f-621">Následují vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4ef0f-622">Získat seznam úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-622">Get a list of to-do items</span></span>

<span data-ttu-id="4ef0f-623">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-623">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4ef0f-624">Funkce zpětného volání `success` se vyvolá, pokud je požadavek úspěšný.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4ef0f-625">Ve zpětném volání je DOM aktualizován pomocí informací o tom.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4ef0f-626">Přidat položku úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-626">Add a to-do item</span></span>

<span data-ttu-id="4ef0f-627">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-627">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="4ef0f-628">Možnosti `accepts` a `contentType` jsou nastaveny na `application/json` k určení typu média, který přijímá a odesílá.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4ef0f-629">Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4ef0f-630">Když rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `getData`, která aktualizuje tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4ef0f-631">Aktualizace položky úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-631">Update a to-do item</span></span>

<span data-ttu-id="4ef0f-632">Aktualizace položky úkolů je podobná přidání.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4ef0f-633">`url` se změní, aby se přidal jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4ef0f-634">Odstranění položky úkolů</span><span class="sxs-lookup"><span data-stu-id="4ef0f-634">Delete a to-do item</span></span>

<span data-ttu-id="4ef0f-635">Odstranění položky úkolů je provedeno nastavením `type` v volání jazyka AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="4ef0f-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="4ef0f-636">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4ef0f-636">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="4ef0f-637">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4ef0f-637">Additional resources</span></span>

<span data-ttu-id="4ef0f-638">[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-638">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4ef0f-639">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4ef0f-639">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4ef0f-640">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="4ef0f-640">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4ef0f-641">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="4ef0f-641">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

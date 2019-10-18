---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: tutorials/first-web-api
ms.openlocfilehash: b4c88f5dc7853396448a2a6122f3652f92079e68
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72541759"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="dd474-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd474-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="dd474-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="dd474-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="dd474-105">V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd474-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="dd474-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="dd474-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dd474-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="dd474-107">Create a web API project.</span></span>
> * <span data-ttu-id="dd474-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="dd474-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="dd474-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="dd474-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="dd474-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="dd474-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="dd474-111">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="dd474-111">Call the web API with Postman.</span></span>

<span data-ttu-id="dd474-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="dd474-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="dd474-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="dd474-113">Overview</span></span>

<span data-ttu-id="dd474-114">V tomto kurzu se vytvoří webové rozhraní API obsahující následující koncové body:</span><span class="sxs-lookup"><span data-stu-id="dd474-114">This tutorial creates a web API containing the following endpoints:</span></span>

|<span data-ttu-id="dd474-115">Koncový bod</span><span class="sxs-lookup"><span data-stu-id="dd474-115">Endpoint</span></span>                  |<span data-ttu-id="dd474-116">Popis</span><span class="sxs-lookup"><span data-stu-id="dd474-116">Description</span></span>            |<span data-ttu-id="dd474-117">Text žádosti</span><span class="sxs-lookup"><span data-stu-id="dd474-117">Request body</span></span>|<span data-ttu-id="dd474-118">Tělo odpovědi</span><span class="sxs-lookup"><span data-stu-id="dd474-118">Response body</span></span>       |
|--------------------------|-----------------------|------------|--------------------|
|<span data-ttu-id="dd474-119">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="dd474-119">GET /api/TodoItems</span></span>        |<span data-ttu-id="dd474-120">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="dd474-120">Get all to-do items</span></span>    |<span data-ttu-id="dd474-121">Žádné</span><span class="sxs-lookup"><span data-stu-id="dd474-121">None</span></span>        |<span data-ttu-id="dd474-122">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="dd474-122">Array of to-do items</span></span>|
|<span data-ttu-id="dd474-123">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="dd474-123">GET /api/TodoItems/{id}</span></span>   |<span data-ttu-id="dd474-124">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="dd474-124">Get an item by ID</span></span>      |<span data-ttu-id="dd474-125">Žádné</span><span class="sxs-lookup"><span data-stu-id="dd474-125">None</span></span>        |<span data-ttu-id="dd474-126">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="dd474-126">To-do item</span></span>          |
|<span data-ttu-id="dd474-127">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="dd474-127">POST /api/TodoItems</span></span>       |<span data-ttu-id="dd474-128">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="dd474-128">Add a new item</span></span>         |<span data-ttu-id="dd474-129">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="dd474-129">To-do item</span></span>  |<span data-ttu-id="dd474-130">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="dd474-130">To-do item</span></span>          |
|<span data-ttu-id="dd474-131">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="dd474-131">PUT /api/TodoItems/{id}</span></span>   |<span data-ttu-id="dd474-132">Aktualizovat existující položku</span><span class="sxs-lookup"><span data-stu-id="dd474-132">Update an existing item</span></span>|<span data-ttu-id="dd474-133">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="dd474-133">To-do item</span></span>  |<span data-ttu-id="dd474-134">Žádné</span><span class="sxs-lookup"><span data-stu-id="dd474-134">None</span></span>                |
|<span data-ttu-id="dd474-135">Odstranit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="dd474-135">DELETE /api/TodoItems/{id}</span></span>|<span data-ttu-id="dd474-136">Odstranění položky</span><span class="sxs-lookup"><span data-stu-id="dd474-136">Delete an item</span></span>         |<span data-ttu-id="dd474-137">Žádné</span><span class="sxs-lookup"><span data-stu-id="dd474-137">None</span></span>        |<span data-ttu-id="dd474-138">Žádné</span><span class="sxs-lookup"><span data-stu-id="dd474-138">None</span></span>                |

<span data-ttu-id="dd474-139">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="dd474-139">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="dd474-145">Požadavky</span><span class="sxs-lookup"><span data-stu-id="dd474-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dd474-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd474-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dd474-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd474-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dd474-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dd474-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="dd474-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="dd474-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dd474-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd474-150">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dd474-151">V nabídce **soubor** vyberte **Nový**  > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="dd474-151">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="dd474-152">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="dd474-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
1. <span data-ttu-id="dd474-153">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="dd474-153">Name the project *TodoApi* and click **Create**.</span></span>
1. <span data-ttu-id="dd474-154">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="dd474-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="dd474-155">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="dd474-155">Select the **API** template and click **Create**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dd474-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd474-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="dd474-158">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="dd474-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
1. <span data-ttu-id="dd474-159">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="dd474-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
1. <span data-ttu-id="dd474-160">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="dd474-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

1. <span data-ttu-id="dd474-161">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="dd474-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="dd474-162">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="dd474-162">The preceding commands:</span></span>

  * <span data-ttu-id="dd474-163">Vytvořte nový projekt webového rozhraní API a otevřete ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dd474-163">Create a new web API project and open it in Visual Studio Code.</span></span>
  * <span data-ttu-id="dd474-164">Přidejte balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="dd474-164">Add the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dd474-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dd474-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dd474-166">Vyberte **soubor**  > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="dd474-166">Select **File** > **New Solution**.</span></span>

    ![macOS nové řešení](first-web-api-mac/_static/sln.png)

1. <span data-ttu-id="dd474-168">V **části rozhraní .NET Core**  > **App**  > **API**  > **Další**.</span><span class="sxs-lookup"><span data-stu-id="dd474-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

    ![dialog pro nový projekt v macOS](first-web-api-mac/_static/1.png)
  
1. <span data-ttu-id="dd474-170">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \* *.NET Core 3,0*.</span><span class="sxs-lookup"><span data-stu-id="dd474-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>
1. <span data-ttu-id="dd474-171">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="dd474-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="dd474-173">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="dd474-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="dd474-174">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="dd474-174">Test the API</span></span>

<span data-ttu-id="dd474-175">Šablona projektu vytvoří rozhraní `WeatherForecast` API.</span><span class="sxs-lookup"><span data-stu-id="dd474-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="dd474-176">Voláním metody `Get` z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dd474-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dd474-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd474-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd474-178">Stisknutím <kbd>kombinace kláves CTRL + F5</kbd> spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dd474-178">Press <kbd>Ctrl+F5</kbd> to run the app.</span></span> <span data-ttu-id="dd474-179">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="dd474-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="dd474-180">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="dd474-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="dd474-181">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="dd474-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dd474-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd474-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dd474-183">Stisknutím <kbd>kombinace kláves CTRL + F5</kbd> spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dd474-183">Press <kbd>Ctrl+F5</kbd> to run the app.</span></span> <span data-ttu-id="dd474-184">V prohlížeči přejdete na následující adresu URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="dd474-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dd474-185">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dd474-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dd474-186">Vyberte **spustit**  > **Spustit ladění** a spusťte tak aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dd474-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="dd474-187">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>`, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="dd474-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="dd474-188">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="dd474-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="dd474-189">Přidejte `/WeatherForecast` k adrese URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="dd474-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="dd474-190">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="dd474-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="dd474-191">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="dd474-191">Add a model class</span></span>

<span data-ttu-id="dd474-192">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="dd474-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="dd474-193">Model pro tuto aplikaci je jediná třída `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="dd474-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dd474-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd474-194">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dd474-195">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="dd474-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="dd474-196">Vyberte **přidat**  > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="dd474-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dd474-197">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="dd474-197">Name the folder *Models*.</span></span>
1. <span data-ttu-id="dd474-198">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="dd474-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dd474-199">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-199">Name the class *TodoItem* and select **Add**.</span></span>
1. <span data-ttu-id="dd474-200">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="dd474-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dd474-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd474-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="dd474-202">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="dd474-202">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="dd474-203">Přidejte třídu `TodoItem` do složky *modely* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="dd474-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dd474-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dd474-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dd474-205">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="dd474-205">Right-click the project.</span></span> <span data-ttu-id="dd474-206">Vyberte **přidat**  > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="dd474-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dd474-207">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="dd474-207">Name the folder *Models*.</span></span>

    ![Nová složka](first-web-api-mac/_static/folder.png)

1. <span data-ttu-id="dd474-209">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **nový soubor**  > **Obecné**  > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="dd474-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>
1. <span data-ttu-id="dd474-210">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="dd474-210">Name the class *TodoItem*, and then click **New**.</span></span>
1. <span data-ttu-id="dd474-211">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="dd474-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="dd474-212">Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="dd474-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="dd474-213">Třídy modelu mohou jít kdekoli v projektu, ale složka *modely* je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="dd474-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="dd474-214">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="dd474-214">Add a database context</span></span>

<span data-ttu-id="dd474-215">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="dd474-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="dd474-216">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="dd474-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dd474-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd474-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="dd474-218">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="dd474-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

1. <span data-ttu-id="dd474-219">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="dd474-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
1. <span data-ttu-id="dd474-220">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="dd474-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
1. <span data-ttu-id="dd474-221">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="dd474-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
1. <span data-ttu-id="dd474-222">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
1. <span data-ttu-id="dd474-223">Pomocí předchozích pokynů přidejte balíček NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="dd474-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3nuget.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="dd474-225">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="dd474-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="dd474-226">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat**  > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="dd474-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dd474-227">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="dd474-228">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dd474-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dd474-229">Přidejte třídu `TodoContext` do složky *modely* .</span><span class="sxs-lookup"><span data-stu-id="dd474-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="dd474-230">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="dd474-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="dd474-231">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="dd474-231">Register the database context</span></span>

<span data-ttu-id="dd474-232">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány pomocí kontejneru [Injektáže (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="dd474-232">In ASP.NET Core, services such as the database context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dd474-233">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="dd474-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="dd474-234">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="dd474-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="dd474-235">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="dd474-235">The preceding code:</span></span>

* <span data-ttu-id="dd474-236">Odebere nepoužívané deklarace `using`.</span><span class="sxs-lookup"><span data-stu-id="dd474-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="dd474-237">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="dd474-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="dd474-238">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="dd474-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="dd474-239">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="dd474-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dd474-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd474-240">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dd474-241">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="dd474-241">Right-click the *Controllers* folder.</span></span>
1. <span data-ttu-id="dd474-242">Vyberte **přidat**  > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="dd474-242">Select **Add** > **New Scaffolded Item**.</span></span>
1. <span data-ttu-id="dd474-243">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
1. <span data-ttu-id="dd474-244">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="dd474-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>
    * <span data-ttu-id="dd474-245">V **třídě modelu**vyberte **TodoItem (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="dd474-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
    * <span data-ttu-id="dd474-246">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models)** .</span><span class="sxs-lookup"><span data-stu-id="dd474-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
    * <span data-ttu-id="dd474-247">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="dd474-248">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dd474-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dd474-249">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="dd474-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="dd474-250">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="dd474-250">The preceding commands:</span></span>

* <span data-ttu-id="dd474-251">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="dd474-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="dd474-252">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="dd474-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="dd474-253">@No__t_0 generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="dd474-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="dd474-254">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="dd474-254">The generated code:</span></span>

* <span data-ttu-id="dd474-255">Definuje třídu kontroleru rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="dd474-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="dd474-256">Upraví třídu atributem [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) .</span><span class="sxs-lookup"><span data-stu-id="dd474-256">Decorates the class with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="dd474-257">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="dd474-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="dd474-258">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="dd474-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="dd474-259">Pomocí DI vloží kontext databáze (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="dd474-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="dd474-260">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="dd474-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="dd474-261">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="dd474-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="dd474-262">Nahraďte příkaz return v `PostTodoItem` pro použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="dd474-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="dd474-263">Předchozí kód je metoda HTTP POST, která je označena atributem [[HTTPPOST]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="dd474-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="dd474-264">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="dd474-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="dd474-265">Metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="dd474-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="dd474-266">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="dd474-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="dd474-267">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="dd474-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="dd474-268">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="dd474-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="dd474-269">Hlavička `Location` Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="dd474-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="dd474-270">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="dd474-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="dd474-271">Odkazuje na akci `GetTodoItem` pro vytvoření identifikátoru URI `Location` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="dd474-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="dd474-272">Klíčové C# slovo `nameof` slouží k tomu, aby se předešlo zakódování názvu akce ve volání `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="dd474-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="dd474-273">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="dd474-273">Install Postman</span></span>

<span data-ttu-id="dd474-274">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="dd474-274">This tutorial uses Postman to test the web API.</span></span>

1. <span data-ttu-id="dd474-275">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="dd474-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
1. <span data-ttu-id="dd474-276">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dd474-276">Start the web app.</span></span>
1. <span data-ttu-id="dd474-277">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="dd474-277">Start Postman.</span></span>
1. <span data-ttu-id="dd474-278">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="dd474-278">Disable **SSL certificate verification**</span></span>
1. <span data-ttu-id="dd474-279">V**Nastavení** **souboru**  >  (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="dd474-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

    > [!WARNING]
    > <span data-ttu-id="dd474-280">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="dd474-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="dd474-281">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="dd474-281">Test PostTodoItem with Postman</span></span>

1. <span data-ttu-id="dd474-282">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="dd474-282">Create a new request.</span></span>
1. <span data-ttu-id="dd474-283">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="dd474-283">Set the HTTP method to `POST`.</span></span>
1. <span data-ttu-id="dd474-284">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="dd474-284">Select the **Body** tab.</span></span>
1. <span data-ttu-id="dd474-285">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="dd474-285">Select the **raw** radio button.</span></span>
1. <span data-ttu-id="dd474-286">Nastavte typ na **JSON (Application/JSON)** .</span><span class="sxs-lookup"><span data-stu-id="dd474-286">Set the type to **JSON (application/json)**.</span></span>
1. <span data-ttu-id="dd474-287">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="dd474-287">In the request body, enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

1. <span data-ttu-id="dd474-288">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-288">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="dd474-290">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="dd474-290">Test the location header URI</span></span>

1. <span data-ttu-id="dd474-291">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="dd474-291">Select the **Headers** tab in the **Response** pane.</span></span>
1. <span data-ttu-id="dd474-292">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="dd474-292">Copy the **Location** header value:</span></span>

    ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/create.png)

1. <span data-ttu-id="dd474-294">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="dd474-294">Set the method to GET.</span></span>
1. <span data-ttu-id="dd474-295">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="dd474-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
1. <span data-ttu-id="dd474-296">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="dd474-297">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="dd474-297">Examine the GET methods</span></span>

<span data-ttu-id="dd474-298">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="dd474-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="dd474-299">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="dd474-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="dd474-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="dd474-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="dd474-301">Odpověď podobná následující je vytvořena voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="dd474-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="dd474-302">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="dd474-302">Test Get with Postman</span></span>

1. <span data-ttu-id="dd474-303">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="dd474-303">Create a new request.</span></span>
1. <span data-ttu-id="dd474-304">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="dd474-304">Set the HTTP method to **GET**.</span></span>
1. <span data-ttu-id="dd474-305">Nastavte adresu URL požadavku na `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="dd474-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="dd474-306">Například `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="dd474-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
1. <span data-ttu-id="dd474-307">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="dd474-307">Set **Two pane view** in Postman.</span></span>
1. <span data-ttu-id="dd474-308">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-308">Select **Send**.</span></span>

<span data-ttu-id="dd474-309">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="dd474-309">This app uses an in-memory database.</span></span> <span data-ttu-id="dd474-310">Pokud je aplikace zastavená a spuštěná, předchozí požadavek GET nevrátí žádná data.</span><span class="sxs-lookup"><span data-stu-id="dd474-310">If the app is stopped and started, the preceding GET request won't return any data.</span></span> <span data-ttu-id="dd474-311">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="dd474-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="dd474-312">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="dd474-312">Routing and URL paths</span></span>

<span data-ttu-id="dd474-313">Atribut [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="dd474-313">The [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="dd474-314">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="dd474-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="dd474-315">Začněte s řetězcem šablony v atributu `Route` kontroleru:</span><span class="sxs-lookup"><span data-stu-id="dd474-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="dd474-316">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="dd474-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="dd474-317">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="dd474-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="dd474-318">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="dd474-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="dd474-319">Pokud má atribut `[HttpGet]` šablonu směrování (například `[HttpGet("products")]`), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="dd474-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="dd474-320">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="dd474-320">This sample doesn't use a template.</span></span> <span data-ttu-id="dd474-321">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="dd474-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="dd474-322">V následující metodě `GetTodoItem` `"{id}"` je zástupnou proměnnou pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="dd474-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="dd474-323">Když je vyvoláno `GetTodoItem`, hodnota `"{id}"` v adrese URL je poskytnuta metodě v parametru `id`.</span><span class="sxs-lookup"><span data-stu-id="dd474-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="dd474-324">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="dd474-324">Return values</span></span>

<span data-ttu-id="dd474-325">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult \<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="dd474-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="dd474-326">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="dd474-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="dd474-327">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="dd474-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="dd474-328">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="dd474-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="dd474-329">návratové typy `ActionResult` mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="dd474-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="dd474-330">@No__t_0 může například vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="dd474-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="dd474-331">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound>.</span><span class="sxs-lookup"><span data-stu-id="dd474-331">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound> error code.</span></span>
* <span data-ttu-id="dd474-332">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="dd474-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="dd474-333">Vrácení `item` způsobí odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="dd474-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="dd474-334">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="dd474-334">The PutTodoItem method</span></span>

<span data-ttu-id="dd474-335">Projděte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="dd474-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="dd474-336">`PutTodoItem` se podobá `PostTodoItem`, s tím rozdílem, že používá PUT HTTP.</span><span class="sxs-lookup"><span data-stu-id="dd474-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="dd474-337">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="dd474-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="dd474-338">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="dd474-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="dd474-339">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="dd474-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="dd474-340">Pokud se zobrazí chyba s voláním `PutTodoItem`, zavoláním `GET` zajistěte, aby v databázi byla nějaká položka.</span><span class="sxs-lookup"><span data-stu-id="dd474-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="dd474-341">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="dd474-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="dd474-342">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="dd474-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="dd474-343">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="dd474-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="dd474-344">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="dd474-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="dd474-345">Aktualizuje položku úkolů s ID 1.</span><span class="sxs-lookup"><span data-stu-id="dd474-345">Update the to-do item that has an ID of 1.</span></span> <span data-ttu-id="dd474-346">Nastavte jeho název na "informační ryby".</span><span class="sxs-lookup"><span data-stu-id="dd474-346">Set its name to "feed fish":</span></span>

```json
{
  "ID":1,
  "name":"feed fish",
  "isComplete":true
}
```

<span data-ttu-id="dd474-347">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="dd474-347">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="dd474-349">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="dd474-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="dd474-350">Projděte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="dd474-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="dd474-351">@No__t_0 odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="dd474-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="dd474-352">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="dd474-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="dd474-353">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="dd474-353">Use Postman to delete a to-do item:</span></span>

1. <span data-ttu-id="dd474-354">Nastavte metodu na `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="dd474-354">Set the method to `DELETE`.</span></span>
1. <span data-ttu-id="dd474-355">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="dd474-355">Set the URI of the object to delete (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
1. <span data-ttu-id="dd474-356">Vyberte **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="dd474-356">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="dd474-357">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="dd474-357">Call the web API with JavaScript</span></span>

<span data-ttu-id="dd474-358">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="dd474-358">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="dd474-359">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="dd474-359">Add authentication support to a web API</span></span>

<span data-ttu-id="dd474-360">Viz kurz k [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) .</span><span class="sxs-lookup"><span data-stu-id="dd474-360">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd474-361">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dd474-361">Additional resources</span></span>

<span data-ttu-id="dd474-362">[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="dd474-362">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="dd474-363">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="dd474-363">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="dd474-364">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="dd474-364">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="dd474-365">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="dd474-365">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

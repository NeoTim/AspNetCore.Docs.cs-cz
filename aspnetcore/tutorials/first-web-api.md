---
title: 'Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se vytvářet webové rozhraní API pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 5190fa30373c75e6358d9643cfe0042c7fc53557
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68915125"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="14ec2-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14ec2-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="14ec2-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="14ec2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="14ec2-105">V tomto kurzu se naučíte se základy vytváření webových rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14ec2-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14ec2-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="14ec2-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="14ec2-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-107">Create a web API project.</span></span>
> * <span data-ttu-id="14ec2-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="14ec2-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="14ec2-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="14ec2-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="14ec2-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="14ec2-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="14ec2-111">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="14ec2-111">Call the web API with Postman.</span></span>

<span data-ttu-id="14ec2-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="14ec2-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="14ec2-113">Overview</span></span>

<span data-ttu-id="14ec2-114">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="14ec2-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="14ec2-115">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="14ec2-115">API</span></span> | <span data-ttu-id="14ec2-116">Popis</span><span class="sxs-lookup"><span data-stu-id="14ec2-116">Description</span></span> | <span data-ttu-id="14ec2-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="14ec2-117">Request body</span></span> | <span data-ttu-id="14ec2-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="14ec2-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="14ec2-119">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="14ec2-119">GET /api/TodoItems</span></span> | <span data-ttu-id="14ec2-120">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-120">Get all to-do items</span></span> | <span data-ttu-id="14ec2-121">Žádné</span><span class="sxs-lookup"><span data-stu-id="14ec2-121">None</span></span> | <span data-ttu-id="14ec2-122">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-122">Array of to-do items</span></span>|
|<span data-ttu-id="14ec2-123">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="14ec2-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="14ec2-124">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="14ec2-124">Get an item by ID</span></span> | <span data-ttu-id="14ec2-125">Žádná</span><span class="sxs-lookup"><span data-stu-id="14ec2-125">None</span></span> | <span data-ttu-id="14ec2-126">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-126">To-do item</span></span>|
|<span data-ttu-id="14ec2-127">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="14ec2-127">POST /api/TodoItems</span></span> | <span data-ttu-id="14ec2-128">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="14ec2-128">Add a new item</span></span> | <span data-ttu-id="14ec2-129">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-129">To-do item</span></span> | <span data-ttu-id="14ec2-130">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-130">To-do item</span></span> |
|<span data-ttu-id="14ec2-131">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="14ec2-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="14ec2-132">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="14ec2-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="14ec2-133">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-133">To-do item</span></span> | <span data-ttu-id="14ec2-134">Žádné</span><span class="sxs-lookup"><span data-stu-id="14ec2-134">None</span></span> |
|<span data-ttu-id="14ec2-135">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="14ec2-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="14ec2-136">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="14ec2-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="14ec2-137">Žádné</span><span class="sxs-lookup"><span data-stu-id="14ec2-137">None</span></span> | <span data-ttu-id="14ec2-138">Žádná</span><span class="sxs-lookup"><span data-stu-id="14ec2-138">None</span></span>|

<span data-ttu-id="14ec2-139">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ec2-139">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="14ec2-145">Požadavky</span><span class="sxs-lookup"><span data-stu-id="14ec2-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14ec2-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14ec2-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14ec2-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="14ec2-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="14ec2-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14ec2-151">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="14ec2-152">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="14ec2-153">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="14ec2-154">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="14ec2-155">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="14ec2-156">Nevybírejte možnost **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-156">**Don't** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14ec2-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14ec2-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="14ec2-159">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="14ec2-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="14ec2-160">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="14ec2-161">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="14ec2-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="14ec2-162">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="14ec2-163">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="14ec2-163">The preceding commands:</span></span>

  * <span data-ttu-id="14ec2-164">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="14ec2-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="14ec2-165">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="14ec2-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14ec2-166">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="14ec2-167">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-167">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="14ec2-169">Vyberte rozhraní > **API** > aplikace> .NET Core další.</span><span class="sxs-lookup"><span data-stu-id="14ec2-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="14ec2-171">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** \* *.NET Core 3,0*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="14ec2-172">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="14ec2-174">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="14ec2-174">Open a command terminal in the project folder and run the following commands:</span></span>

   ```console
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="14ec2-175">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="14ec2-175">Test the API</span></span>

<span data-ttu-id="14ec2-176">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-176">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="14ec2-177">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ec2-177">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-178">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14ec2-179">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="14ec2-180">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="14ec2-180">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="14ec2-181">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-181">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="14ec2-182">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-182">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14ec2-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14ec2-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="14ec2-184">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-184">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="14ec2-185">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/WeatherForecast ](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="14ec2-185">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14ec2-186">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="14ec2-187">Vyberte **Spustit** > **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-187">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="14ec2-188">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="14ec2-188">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="14ec2-189">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="14ec2-189">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="14ec2-190">Připojit `/WeatherForecast` na adresu URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="14ec2-190">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="14ec2-191">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="14ec2-191">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="14ec2-192">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="14ec2-192">Add a model class</span></span>

<span data-ttu-id="14ec2-193">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="14ec2-193">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="14ec2-194">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="14ec2-194">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14ec2-196">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="14ec2-196">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="14ec2-197">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-197">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="14ec2-198">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-198">Name the folder *Models*.</span></span>

* <span data-ttu-id="14ec2-199">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-199">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="14ec2-200">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-200">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="14ec2-201">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-201">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14ec2-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14ec2-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="14ec2-203">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-203">Add a folder named *Models*.</span></span>

* <span data-ttu-id="14ec2-204">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-204">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14ec2-205">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="14ec2-206">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="14ec2-206">Right-click the project.</span></span> <span data-ttu-id="14ec2-207">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="14ec2-208">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-208">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="14ec2-210">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-210">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="14ec2-211">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-211">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="14ec2-212">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-212">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="14ec2-213">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-213">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="14ec2-214">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="14ec2-214">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="14ec2-215">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="14ec2-215">Add a database context</span></span>

<span data-ttu-id="14ec2-216">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="14ec2-216">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="14ec2-217">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="14ec2-217">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-218">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="14ec2-219">Přidat Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="14ec2-219">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="14ec2-220">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-220">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="14ec2-221">Zaškrtněte políčko **zahrnout předběžné verze** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-221">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="14ec2-222">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-222">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="14ec2-223">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer v 3.0.0-Preview** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-223">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="14ec2-224">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-224">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="14ec2-225">Pomocí předchozích pokynů přidejte `Microsoft.EntityFrameworkCore.InMemory` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="14ec2-225">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="14ec2-227">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="14ec2-227">Add the TodoContext database context</span></span>

* <span data-ttu-id="14ec2-228">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-228">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="14ec2-229">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-229">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="14ec2-230">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="14ec2-231">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-231">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="14ec2-232">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="14ec2-232">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="14ec2-233">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="14ec2-233">Register the database context</span></span>

<span data-ttu-id="14ec2-234">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-234">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="14ec2-235">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="14ec2-235">The container provides the service to controllers.</span></span>

<span data-ttu-id="14ec2-236">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="14ec2-236">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="14ec2-237">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="14ec2-237">The preceding code:</span></span>

* <span data-ttu-id="14ec2-238">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="14ec2-238">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="14ec2-239">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="14ec2-239">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="14ec2-240">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="14ec2-240">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="14ec2-241">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="14ec2-241">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-242">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14ec2-243">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-243">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="14ec2-244">Vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-244">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="14ec2-245">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-245">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="14ec2-246">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="14ec2-246">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="14ec2-247">V **třídě modelu**vyberte **TodoItem (TodoAPI. Models)** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-247">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="14ec2-248">Ve **třídě Context data**vyberte **TodoContext (TodoAPI. Models)** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-248">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="14ec2-249">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="14ec2-249">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="14ec2-250">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="14ec2-251">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="14ec2-251">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="14ec2-252">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="14ec2-252">The preceding commands:</span></span>

* <span data-ttu-id="14ec2-253">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="14ec2-253">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="14ec2-254">Nainstaluje modul generování uživatelského rozhraní (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="14ec2-254">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="14ec2-255">Generování uživatelského rozhraní `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-255">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="14ec2-256">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="14ec2-256">The generated code:</span></span>

* <span data-ttu-id="14ec2-257">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="14ec2-257">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="14ec2-258">Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="14ec2-258">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="14ec2-259">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-259">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="14ec2-260">Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-260">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="14ec2-261">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-261">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="14ec2-262">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-262">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="14ec2-263">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="14ec2-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="14ec2-264">Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="14ec2-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="14ec2-265">Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="14ec2-265">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="14ec2-266">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="14ec2-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="14ec2-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Metody:</span><span class="sxs-lookup"><span data-stu-id="14ec2-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="14ec2-268">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="14ec2-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="14ec2-269">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="14ec2-270">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="14ec2-271">Záhlaví Určuje identifikátor URI nově vytvořené položky. [](https://developer.mozilla.org/docs/Glossary/URI) `Location`</span><span class="sxs-lookup"><span data-stu-id="14ec2-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="14ec2-272">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="14ec2-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="14ec2-273">Odkazuje na `GetTodoItem` akci `Location` vytvoření identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="14ec2-274">Klíčové slovo se používá k zamezení hardwarového kódování názvu `CreatedAtAction` akce ve volání. C# `nameof`</span><span class="sxs-lookup"><span data-stu-id="14ec2-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="14ec2-275">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="14ec2-275">Install Postman</span></span>

<span data-ttu-id="14ec2-276">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="14ec2-277">Nainstalujte [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="14ec2-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="14ec2-278">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-278">Start the web app.</span></span>
* <span data-ttu-id="14ec2-279">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="14ec2-279">Start Postman.</span></span>
* <span data-ttu-id="14ec2-280">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="14ec2-280">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="14ec2-281">Z **soubor > Nastavení** (\**Obecné* kartu), zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-281">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="14ec2-282">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="14ec2-283">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="14ec2-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="14ec2-284">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="14ec2-284">Create a new request.</span></span>
* <span data-ttu-id="14ec2-285">Nastavte metodu HTTP na `POST`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="14ec2-286">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="14ec2-287">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="14ec2-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="14ec2-288">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="14ec2-289">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="14ec2-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="14ec2-290">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-290">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="14ec2-292">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="14ec2-292">Test the location header URI</span></span>

* <span data-ttu-id="14ec2-293">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="14ec2-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="14ec2-294">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="14ec2-294">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="14ec2-296">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="14ec2-296">Set the method to GET.</span></span>
* <span data-ttu-id="14ec2-297">Vložte identifikátor URI (například `https://localhost:5001/api/TodoItems/1`)</span><span class="sxs-lookup"><span data-stu-id="14ec2-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="14ec2-298">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="14ec2-299">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="14ec2-299">Examine the GET methods</span></span>

<span data-ttu-id="14ec2-300">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="14ec2-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="14ec2-301">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="14ec2-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="14ec2-302">Příklad:</span><span class="sxs-lookup"><span data-stu-id="14ec2-302">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="14ec2-303">Odpověď podobná následující je vytvořena voláním metody `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="14ec2-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="14ec2-304">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="14ec2-304">Test Get with Postman</span></span>

* <span data-ttu-id="14ec2-305">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="14ec2-305">Create a new request.</span></span>
* <span data-ttu-id="14ec2-306">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="14ec2-307">Nastavení adresy URL požadavku `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="14ec2-308">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="14ec2-309">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="14ec2-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="14ec2-310">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-310">Select **Send**.</span></span>

<span data-ttu-id="14ec2-311">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="14ec2-311">This app uses an in-memory database.</span></span> <span data-ttu-id="14ec2-312">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="14ec2-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="14ec2-313">Pokud se nevrátí žádná data [](#post) , odešlete data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ec2-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="14ec2-314">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="14ec2-314">Routing and URL paths</span></span>

<span data-ttu-id="14ec2-315">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="14ec2-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="14ec2-316">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="14ec2-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="14ec2-317">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="14ec2-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="14ec2-318">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="14ec2-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="14ec2-319">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="14ec2-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="14ec2-320">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="14ec2-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="14ec2-321">`[HttpGet("products")]`Pokud má `[HttpGet]` atribut směrovací šablonu (například), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="14ec2-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="14ec2-322">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="14ec2-322">This sample doesn't use a template.</span></span> <span data-ttu-id="14ec2-323">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="14ec2-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="14ec2-324">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="14ec2-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="14ec2-325">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="14ec2-326">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="14ec2-326">Return values</span></span>

<span data-ttu-id="14ec2-327">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="14ec2-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="14ec2-328">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="14ec2-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="14ec2-329">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="14ec2-330">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="14ec2-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="14ec2-331">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="14ec2-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="14ec2-332">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="14ec2-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="14ec2-333">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="14ec2-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="14ec2-334">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="14ec2-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="14ec2-335">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="14ec2-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="14ec2-336">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="14ec2-336">The PutTodoItem method</span></span>

<span data-ttu-id="14ec2-337">Projděte `PutTodoItem` si metodu:</span><span class="sxs-lookup"><span data-stu-id="14ec2-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="14ec2-338">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="14ec2-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="14ec2-339">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="14ec2-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="14ec2-340">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="14ec2-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="14ec2-341">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="14ec2-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="14ec2-342">Pokud se zobrazí chyba při volání `PutTodoItem` `GET` , zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="14ec2-343">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="14ec2-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="14ec2-344">Tato ukázka používá databázi v paměti, která musí být při každém spuštění aplikace inicializovaná.</span><span class="sxs-lookup"><span data-stu-id="14ec2-344">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="14ec2-345">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="14ec2-346">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="14ec2-347">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="14ec2-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="14ec2-348">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="14ec2-348">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="14ec2-350">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="14ec2-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="14ec2-351">Projděte `DeleteTodoItem` si metodu:</span><span class="sxs-lookup"><span data-stu-id="14ec2-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="14ec2-352">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="14ec2-352">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="14ec2-353">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="14ec2-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="14ec2-354">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="14ec2-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="14ec2-355">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="14ec2-356">Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="14ec2-356">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="14ec2-357">Vyberte **odeslat**</span><span class="sxs-lookup"><span data-stu-id="14ec2-357">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="14ec2-358">Volání rozhraní API ze jQuery</span><span class="sxs-lookup"><span data-stu-id="14ec2-358">Call the API from jQuery</span></span>

<span data-ttu-id="14ec2-359">Viz [kurz: Zavolejte ASP.NET Core webového rozhraní API pomocí jQuery](xref:tutorials/web-api-jquery).</span><span class="sxs-lookup"><span data-stu-id="14ec2-359">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14ec2-360">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="14ec2-360">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="14ec2-361">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-361">Create a web API project.</span></span>
> * <span data-ttu-id="14ec2-362">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="14ec2-362">Add a model class and a database context.</span></span>
> * <span data-ttu-id="14ec2-363">Přidání kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-363">Add a controller.</span></span>
> * <span data-ttu-id="14ec2-364">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="14ec2-364">Add CRUD methods.</span></span>
> * <span data-ttu-id="14ec2-365">Konfigurace směrování a cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="14ec2-365">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="14ec2-366">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="14ec2-366">Specify return values.</span></span>
> * <span data-ttu-id="14ec2-367">Volání webového rozhraní API pomocí nástroje Postman.</span><span class="sxs-lookup"><span data-stu-id="14ec2-367">Call the web API with Postman.</span></span>
> * <span data-ttu-id="14ec2-368">Zavolejte webové rozhraní API pomocí jQuery.</span><span class="sxs-lookup"><span data-stu-id="14ec2-368">Call the web API with jQuery.</span></span>

<span data-ttu-id="14ec2-369">Na konci máte webové rozhraní API, která může spravovat "úkolů" položky uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-369">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="14ec2-370">Přehled</span><span class="sxs-lookup"><span data-stu-id="14ec2-370">Overview</span></span>

<span data-ttu-id="14ec2-371">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="14ec2-371">This tutorial creates the following API:</span></span>

|<span data-ttu-id="14ec2-372">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="14ec2-372">API</span></span> | <span data-ttu-id="14ec2-373">Popis</span><span class="sxs-lookup"><span data-stu-id="14ec2-373">Description</span></span> | <span data-ttu-id="14ec2-374">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="14ec2-374">Request body</span></span> | <span data-ttu-id="14ec2-375">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="14ec2-375">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="14ec2-376">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="14ec2-376">GET /api/TodoItems</span></span> | <span data-ttu-id="14ec2-377">Získat všechny položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-377">Get all to-do items</span></span> | <span data-ttu-id="14ec2-378">Žádné</span><span class="sxs-lookup"><span data-stu-id="14ec2-378">None</span></span> | <span data-ttu-id="14ec2-379">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-379">Array of to-do items</span></span>|
|<span data-ttu-id="14ec2-380">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="14ec2-380">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="14ec2-381">Získat položky podle ID</span><span class="sxs-lookup"><span data-stu-id="14ec2-381">Get an item by ID</span></span> | <span data-ttu-id="14ec2-382">Žádná</span><span class="sxs-lookup"><span data-stu-id="14ec2-382">None</span></span> | <span data-ttu-id="14ec2-383">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-383">To-do item</span></span>|
|<span data-ttu-id="14ec2-384">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="14ec2-384">POST /api/TodoItems</span></span> | <span data-ttu-id="14ec2-385">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="14ec2-385">Add a new item</span></span> | <span data-ttu-id="14ec2-386">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-386">To-do item</span></span> | <span data-ttu-id="14ec2-387">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-387">To-do item</span></span> |
|<span data-ttu-id="14ec2-388">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="14ec2-388">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="14ec2-389">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="14ec2-389">Update an existing item &nbsp;</span></span> | <span data-ttu-id="14ec2-390">Položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-390">To-do item</span></span> | <span data-ttu-id="14ec2-391">Žádné</span><span class="sxs-lookup"><span data-stu-id="14ec2-391">None</span></span> |
|<span data-ttu-id="14ec2-392">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="14ec2-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="14ec2-393">Odstranění položky &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="14ec2-393">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="14ec2-394">Žádné</span><span class="sxs-lookup"><span data-stu-id="14ec2-394">None</span></span> | <span data-ttu-id="14ec2-395">Žádná</span><span class="sxs-lookup"><span data-stu-id="14ec2-395">None</span></span>|

<span data-ttu-id="14ec2-396">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ec2-396">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="14ec2-402">Požadavky</span><span class="sxs-lookup"><span data-stu-id="14ec2-402">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-403">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14ec2-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14ec2-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14ec2-405">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-405">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="14ec2-406">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="14ec2-406">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-407">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-407">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14ec2-408">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-408">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="14ec2-409">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-409">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="14ec2-410">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-410">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="14ec2-411">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-411">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="14ec2-412">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-412">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="14ec2-413">Nevybírejte možnost **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-413">**Don't** select **Enable Docker Support**.</span></span>

![VS – dialogové okno nového projektu](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14ec2-415">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14ec2-415">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="14ec2-416">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="14ec2-416">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="14ec2-417">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-417">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="14ec2-418">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="14ec2-418">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="14ec2-419">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-419">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="14ec2-420">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-420">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14ec2-421">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-421">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="14ec2-422">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-422">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="14ec2-424">Vyberte rozhraní > **API** > aplikace> .NET Core další.</span><span class="sxs-lookup"><span data-stu-id="14ec2-424">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="14ec2-426">V **nakonfigurovat nové technologie ASP.NET Core webové rozhraní API** dialogového okna, přijměte výchozí nastavení **Cílová architektura** z \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-426">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="14ec2-427">Zadejte *TodoApi* pro **název projektu** a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-427">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="14ec2-429">Testovat rozhraní API</span><span class="sxs-lookup"><span data-stu-id="14ec2-429">Test the API</span></span>

<span data-ttu-id="14ec2-430">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-430">The project template creates a `values` API.</span></span> <span data-ttu-id="14ec2-431">Volání `Get` metoda v prohlížeči a testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ec2-431">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-432">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-432">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14ec2-433">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-433">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="14ec2-434">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="14ec2-434">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="14ec2-435">Pokud se zobrazí dialogové okno s dotazem, pokud by měla důvěřovat certifikátu služby IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-435">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="14ec2-436">V **upozornění zabezpečení** dialogové okno, které se zobrazí další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-436">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14ec2-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14ec2-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="14ec2-438">Stisknutím kláves Ctrl + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-438">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="14ec2-439">V prohlížeči přejděte na následující adrese URL: [ https://localhost:5001/api/values ](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="14ec2-439">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14ec2-440">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="14ec2-441">Vyberte **Spustit** > **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-441">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="14ec2-442">Spuštění prohlížeče Visual Studio pro Mac a přejde na `https://localhost:<port>`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="14ec2-442">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="14ec2-443">Vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="14ec2-443">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="14ec2-444">Připojit `/api/values` na adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="14ec2-444">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="14ec2-445">Vrátí následující JSON:</span><span class="sxs-lookup"><span data-stu-id="14ec2-445">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="14ec2-446">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="14ec2-446">Add a model class</span></span>

<span data-ttu-id="14ec2-447">A *modelu* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="14ec2-447">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="14ec2-448">Model pro tuto aplikaci je jedinou `TodoItem` třídy.</span><span class="sxs-lookup"><span data-stu-id="14ec2-448">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-449">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-449">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14ec2-450">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="14ec2-450">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="14ec2-451">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-451">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="14ec2-452">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-452">Name the folder *Models*.</span></span>

* <span data-ttu-id="14ec2-453">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-453">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="14ec2-454">Název třídy *TodoItem* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-454">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="14ec2-455">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-455">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14ec2-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14ec2-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="14ec2-457">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-457">Add a folder named *Models*.</span></span>

* <span data-ttu-id="14ec2-458">Přidat `TodoItem` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-458">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14ec2-459">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="14ec2-460">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="14ec2-460">Right-click the project.</span></span> <span data-ttu-id="14ec2-461">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-461">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="14ec2-462">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-462">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="14ec2-464">Klikněte pravým tlačítkem na složku *modely* a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-464">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="14ec2-465">Název třídy *TodoItem*a potom klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-465">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="14ec2-466">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-466">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="14ec2-467">`Id` Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-467">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="14ec2-468">Třídy modelu můžete kamkoliv v projektu, ale *modely* složky používají konvence.</span><span class="sxs-lookup"><span data-stu-id="14ec2-468">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="14ec2-469">Přidat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="14ec2-469">Add a database context</span></span>

<span data-ttu-id="14ec2-470">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="14ec2-470">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="14ec2-471">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="14ec2-471">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-472">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14ec2-473">Klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-473">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="14ec2-474">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-474">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="14ec2-475">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-475">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="14ec2-476">Přidat `TodoContext` třídu *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-476">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="14ec2-477">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-477">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="14ec2-478">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="14ec2-478">Register the database context</span></span>

<span data-ttu-id="14ec2-479">V ASP.NET Core, služeb, jako je kontext databáze, musí zaregistrovat [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-479">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="14ec2-480">Kontejner poskytuje služby řadiče.</span><span class="sxs-lookup"><span data-stu-id="14ec2-480">The container provides the service to controllers.</span></span>

<span data-ttu-id="14ec2-481">Aktualizace *Startup.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="14ec2-481">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="14ec2-482">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="14ec2-482">The preceding code:</span></span>

* <span data-ttu-id="14ec2-483">Odebere nevyužité `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="14ec2-483">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="14ec2-484">Přidá do kontejneru DI kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="14ec2-484">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="14ec2-485">Určuje, zda kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="14ec2-485">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="14ec2-486">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="14ec2-486">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14ec2-488">Klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-488">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="14ec2-489">Vyberte možnost **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-489">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="14ec2-490">V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="14ec2-490">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="14ec2-491">Název třídy *TodoController*a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-491">Name the class *TodoController*, and select **Add**.</span></span>

  ![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní api kontroleru vybrané](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="14ec2-493">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="14ec2-494">V *řadiče* složku, vytvořte třídu s názvem `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-494">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="14ec2-495">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="14ec2-496">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="14ec2-496">The preceding code:</span></span>

* <span data-ttu-id="14ec2-497">Definuje třídu kontroleru rozhraní API bez metody.</span><span class="sxs-lookup"><span data-stu-id="14ec2-497">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="14ec2-498">Upraví třídu atributem [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="14ec2-498">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="14ec2-499">Tento atribut označuje, že kontroler reaguje na požadavky webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-499">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="14ec2-500">Informace o konkrétním chování, které atribut povoluje, naleznete v <xref:web-api/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-500">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="14ec2-501">Vložit kontext databáze používá DI (`TodoContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-501">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="14ec2-502">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-502">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="14ec2-503">Přidá položku s názvem `Item1` k databázi, pokud databáze je prázdný.</span><span class="sxs-lookup"><span data-stu-id="14ec2-503">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="14ec2-504">Tento kód je v konstruktoru, proto se spustí pokaždé, když se nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="14ec2-504">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="14ec2-505">Pokud odstraníte všechny položky, vytvoří konstruktor `Item1` znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-505">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="14ec2-506">Proto může účet vypadat třeba odstranění akce nebyla úspěšná, když se ve skutečnosti fungovalo.</span><span class="sxs-lookup"><span data-stu-id="14ec2-506">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="14ec2-507">Přidejte metody Get</span><span class="sxs-lookup"><span data-stu-id="14ec2-507">Add Get methods</span></span>

<span data-ttu-id="14ec2-508">Chcete-li poskytují rozhraní API, který načte položky, přidejte následující metody, které `TodoController` třídy:</span><span class="sxs-lookup"><span data-stu-id="14ec2-508">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="14ec2-509">Tyto metody implementovat dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="14ec2-509">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="14ec2-510">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-510">Stop the app if it's still running.</span></span> <span data-ttu-id="14ec2-511">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="14ec2-511">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="14ec2-512">Testování aplikace pomocí volání dva koncové body v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="14ec2-512">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="14ec2-513">Příklad:</span><span class="sxs-lookup"><span data-stu-id="14ec2-513">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="14ec2-514">Následující odpověď HTTP je vytvořen voláním `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="14ec2-514">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="14ec2-515">Směrování a adresa URL cesty</span><span class="sxs-lookup"><span data-stu-id="14ec2-515">Routing and URL paths</span></span>

<span data-ttu-id="14ec2-516">[ `[HttpGet]` ](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="14ec2-516">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="14ec2-517">Cesta adresy URL pro každou metodu se vypočte takto:</span><span class="sxs-lookup"><span data-stu-id="14ec2-517">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="14ec2-518">Začněte s řetězec šablony v kontroleru `Route` atribut:</span><span class="sxs-lookup"><span data-stu-id="14ec2-518">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="14ec2-519">Nahraďte `[controller]` s názvem kontroleru, který je název třídy kontroleru minus příponu "Kontroleru".</span><span class="sxs-lookup"><span data-stu-id="14ec2-519">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="14ec2-520">V tomto příkladu je název třídy kontroleru **Todo**Kontroleru, názvu kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="14ec2-520">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="14ec2-521">ASP.NET Core [směrování](xref:mvc/controllers/routing) velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="14ec2-521">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="14ec2-522">`[HttpGet("products")]`Pokud má `[HttpGet]` atribut směrovací šablonu (například), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="14ec2-522">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="14ec2-523">Tato ukázka nepoužívá šablony.</span><span class="sxs-lookup"><span data-stu-id="14ec2-523">This sample doesn't use a template.</span></span> <span data-ttu-id="14ec2-524">Další informace najdete v tématu [atribut směrování pomocí protokolu Http [příkaz] atributy](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="14ec2-524">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="14ec2-525">V následujícím `GetTodoItem` metody `"{id}"` je proměnná zástupný symbol pro jedinečný identifikátor položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="14ec2-525">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="14ec2-526">Když `GetTodoItem` je vyvolána, hodnota `"{id}"` v adrese URL je k dispozici v metodě jeho`id` parametru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-526">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="14ec2-527">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="14ec2-527">Return values</span></span>

<span data-ttu-id="14ec2-528">Návratový typ `GetTodoItems` a `GetTodoItem` metody je [ActionResult\<T > typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="14ec2-528">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="14ec2-529">ASP.NET Core automaticky serializuje objekt, který má [JSON](https://www.json.org/) a zapíše do datové části zprávy s odpovědí JSON.</span><span class="sxs-lookup"><span data-stu-id="14ec2-529">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="14ec2-530">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že nejsou žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-530">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="14ec2-531">Nezpracované výjimky jsou přeloženy do chyby 5xx.</span><span class="sxs-lookup"><span data-stu-id="14ec2-531">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="14ec2-532">`ActionResult` typy vrácených hodnot může představovat stavové kódy HTTP široký rozsah.</span><span class="sxs-lookup"><span data-stu-id="14ec2-532">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="14ec2-533">Například `GetTodoItem` může vrátit hodnoty dvou různých stavu:</span><span class="sxs-lookup"><span data-stu-id="14ec2-533">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="14ec2-534">Pokud žádná položka odpovídá požadovaným ID, vrátí metoda 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) kód chyby.</span><span class="sxs-lookup"><span data-stu-id="14ec2-534">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="14ec2-535">V opačném případě vrátí metoda 200 s těla odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="14ec2-535">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="14ec2-536">Vrací `item` výsledkem odpověď HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="14ec2-536">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="14ec2-537">Test GetTodoItems – metoda</span><span class="sxs-lookup"><span data-stu-id="14ec2-537">Test the GetTodoItems method</span></span>

<span data-ttu-id="14ec2-538">Tento kurz používá Postman k otestování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-538">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="14ec2-539">Nainstalujte [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="14ec2-539">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="14ec2-540">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ec2-540">Start the web app.</span></span>
* <span data-ttu-id="14ec2-541">Spusťte Postman.</span><span class="sxs-lookup"><span data-stu-id="14ec2-541">Start Postman.</span></span>
* <span data-ttu-id="14ec2-542">Zakázat **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="14ec2-542">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14ec2-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ec2-543">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14ec2-544">V **Nastavení** **souboru** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-544">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="14ec2-545">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14ec2-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="14ec2-546">V možnosti Předvolba **post** > (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-546">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="14ec2-547">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="14ec2-547">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="14ec2-548">Znovu povolte ověření certifikátu SSL po otestování kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-548">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="14ec2-549">Vytvořte novou žádost.</span><span class="sxs-lookup"><span data-stu-id="14ec2-549">Create a new request.</span></span>
  * <span data-ttu-id="14ec2-550">Nastavte jako metodu HTTP **získat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-550">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="14ec2-551">Nastavení adresy URL požadavku `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-551">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="14ec2-552">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-552">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="14ec2-553">Nastavte **dvě podokna zobrazení** v nástroji Postman.</span><span class="sxs-lookup"><span data-stu-id="14ec2-553">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="14ec2-554">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-554">Select **Send**.</span></span>

![Postman se požadavek Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="14ec2-556">Přidání metody vytvoření</span><span class="sxs-lookup"><span data-stu-id="14ec2-556">Add a Create method</span></span>

<span data-ttu-id="14ec2-557">Přidejte následující `PostTodoItem` metodu do Controllers */TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="14ec2-557">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="14ec2-558">Předchozí kód je metoda HTTP POST, je určeno [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="14ec2-558">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="14ec2-559">Metoda získá hodnotu položky úkolů z textu požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="14ec2-559">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="14ec2-560">`CreatedAtAction` Metody:</span><span class="sxs-lookup"><span data-stu-id="14ec2-560">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="14ec2-561">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="14ec2-561">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="14ec2-562">HTTP 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="14ec2-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="14ec2-563">`Location` Přidá hlavičku k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-563">Adds a `Location` header to the response.</span></span> <span data-ttu-id="14ec2-564">`Location` Záhlaví Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-564">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="14ec2-565">Další informace najdete v tématu [10.2.2 201 – vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="14ec2-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="14ec2-566">Odkazuje na `GetTodoItem` akci `Location` vytvoření identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="14ec2-567">Klíčové slovo se používá k zamezení hardwarového kódování názvu `CreatedAtAction` akce ve volání. C# `nameof`</span><span class="sxs-lookup"><span data-stu-id="14ec2-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="14ec2-568">Test PostTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="14ec2-568">Test the PostTodoItem method</span></span>

* <span data-ttu-id="14ec2-569">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="14ec2-569">Build the project.</span></span>
* <span data-ttu-id="14ec2-570">V nástroji Postman, nastavte jako metodu HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-570">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="14ec2-571">Vyberte **tělo** kartu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-571">Select the **Body** tab.</span></span>
* <span data-ttu-id="14ec2-572">Vyberte **nezpracovaná** přepínač.</span><span class="sxs-lookup"><span data-stu-id="14ec2-572">Select the **raw** radio button.</span></span>
* <span data-ttu-id="14ec2-573">Nastavte typ **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="14ec2-573">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="14ec2-574">V textu požadavku zadejte JSON pro úkol:</span><span class="sxs-lookup"><span data-stu-id="14ec2-574">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="14ec2-575">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-575">Select **Send**.</span></span>

  ![Postman se vytvořit žádost](first-web-api/_static/create.png)

  <span data-ttu-id="14ec2-577">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání `PostTodoItem` metody nebude projekt zkompilován.</span><span class="sxs-lookup"><span data-stu-id="14ec2-577">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="14ec2-578">Testování hlavičku location identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="14ec2-578">Test the location header URI</span></span>

* <span data-ttu-id="14ec2-579">Vyberte **záhlaví** kartu **odpovědi** podokně.</span><span class="sxs-lookup"><span data-stu-id="14ec2-579">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="14ec2-580">Kopírovat **umístění** hodnota hlavičky:</span><span class="sxs-lookup"><span data-stu-id="14ec2-580">Copy the **Location** header value:</span></span>

  ![Karta hlavičky z konzoly nástroje Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="14ec2-582">Nastavte jako metodu GET.</span><span class="sxs-lookup"><span data-stu-id="14ec2-582">Set the method to GET.</span></span>
* <span data-ttu-id="14ec2-583">Vložte identifikátor URI (například `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="14ec2-583">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="14ec2-584">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="14ec2-584">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="14ec2-585">Přidejte metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="14ec2-585">Add a PutTodoItem method</span></span>

<span data-ttu-id="14ec2-586">Přidejte následující `PutTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="14ec2-586">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="14ec2-587">`PutTodoItem` je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="14ec2-587">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="14ec2-588">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="14ec2-588">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="14ec2-589">Podle specifikace HTTP vyžaduje požadavek PUT klientovi umožní odeslat celý aktualizovanou entitu, nikoliv pouze změny.</span><span class="sxs-lookup"><span data-stu-id="14ec2-589">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="14ec2-590">Chcete-li podporovat částečné aktualizace, použijte [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="14ec2-590">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="14ec2-591">Pokud se zobrazí chyba při volání `PutTodoItem` `GET` , zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-591">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="14ec2-592">Test PutTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="14ec2-592">Test the PutTodoItem method</span></span>

<span data-ttu-id="14ec2-593">Tato ukázka používá databázi v paměti, která musí být při každém spuštění aplikace inicializovaná.</span><span class="sxs-lookup"><span data-stu-id="14ec2-593">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="14ec2-594">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-594">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="14ec2-595">Před provedením volání metody PUT zavolejte funkci GET k instalování položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="14ec2-595">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="14ec2-596">Aktualizovat položku seznamu úkolů, která má id = 1 a nastavte její název na "informačního kanálu ryb":</span><span class="sxs-lookup"><span data-stu-id="14ec2-596">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="14ec2-597">Následující obrázek ukazuje Postman aktualizace:</span><span class="sxs-lookup"><span data-stu-id="14ec2-597">The following image shows the Postman update:</span></span>

![Postman konzola znázorňující 204 (žádný obsah) odpovědi](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="14ec2-599">Přidejte metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="14ec2-599">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="14ec2-600">Přidejte následující `DeleteTodoItem` metody:</span><span class="sxs-lookup"><span data-stu-id="14ec2-600">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="14ec2-601">`DeleteTodoItem` Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="14ec2-601">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="14ec2-602">Test DeleteTodoItem – metoda</span><span class="sxs-lookup"><span data-stu-id="14ec2-602">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="14ec2-603">Pomocí nástroje Postman odstraňte položku úkolu:</span><span class="sxs-lookup"><span data-stu-id="14ec2-603">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="14ec2-604">Nastavte jako metodu `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-604">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="14ec2-605">Nastavte identifikátor URI objektu odstranit, například `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="14ec2-605">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="14ec2-606">Vyberte **odeslat**</span><span class="sxs-lookup"><span data-stu-id="14ec2-606">Select **Send**</span></span>

<span data-ttu-id="14ec2-607">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="14ec2-607">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="14ec2-608">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-608">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="14ec2-609">Volání rozhraní API pomocí jQuery</span><span class="sxs-lookup"><span data-stu-id="14ec2-609">Call the API with jQuery</span></span>

<span data-ttu-id="14ec2-610">V této části se přidá stránku HTML, který používá jQuery volat webové rozhraní api.</span><span class="sxs-lookup"><span data-stu-id="14ec2-610">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="14ec2-611">jQuery zahájí požadavek a aktualizuje stránku s podrobnostmi o z odpovědi rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-611">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="14ec2-612">Nakonfigurujte aplikaci tak, aby sloužila pro [statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="14ec2-612">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="14ec2-613">Vytvoření *wwwroot* složku v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-613">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="14ec2-614">Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="14ec2-614">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="14ec2-615">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-615">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="14ec2-616">Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře.</span><span class="sxs-lookup"><span data-stu-id="14ec2-616">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="14ec2-617">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="14ec2-617">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="14ec2-618">Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:</span><span class="sxs-lookup"><span data-stu-id="14ec2-618">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="14ec2-619">Otevřít *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="14ec2-619">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="14ec2-620">Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="14ec2-620">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="14ec2-621">Existuje několik způsobů, jak získat jQuery.</span><span class="sxs-lookup"><span data-stu-id="14ec2-621">There are several ways to get jQuery.</span></span> <span data-ttu-id="14ec2-622">V předchozím fragmentu kódu je načíst knihovnu ze sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="14ec2-622">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="14ec2-623">Tato ukázka volá všechny metody CRUD rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-623">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="14ec2-624">Následuje vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ec2-624">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="14ec2-625">Získání seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-625">Get a list of to-do items</span></span>

<span data-ttu-id="14ec2-626">JQuery [ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `GET` žádosti na rozhraní API, které vrací JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="14ec2-626">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="14ec2-627">`success` Funkce zpětného volání je vyvolána, pokud neproběhne.</span><span class="sxs-lookup"><span data-stu-id="14ec2-627">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="14ec2-628">Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.</span><span class="sxs-lookup"><span data-stu-id="14ec2-628">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="14ec2-629">Přidat položku seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-629">Add a to-do item</span></span>

<span data-ttu-id="14ec2-630">[Ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `POST` požadavek se úkol v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="14ec2-630">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="14ec2-631">`accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo.</span><span class="sxs-lookup"><span data-stu-id="14ec2-631">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="14ec2-632">Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="14ec2-632">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="14ec2-633">Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.</span><span class="sxs-lookup"><span data-stu-id="14ec2-633">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="14ec2-634">Aktualizace položky seznamu úkolů</span><span class="sxs-lookup"><span data-stu-id="14ec2-634">Update a to-do item</span></span>

<span data-ttu-id="14ec2-635">Aktualizace položky úkolu je podobné jako přidání jednoho.</span><span class="sxs-lookup"><span data-stu-id="14ec2-635">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="14ec2-636">`url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="14ec2-636">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="14ec2-637">Odstranit úkol</span><span class="sxs-lookup"><span data-stu-id="14ec2-637">Delete a to-do item</span></span>

<span data-ttu-id="14ec2-638">Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="14ec2-638">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="14ec2-639">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="14ec2-639">Additional resources</span></span>

<span data-ttu-id="14ec2-640">[Zobrazení nebo stažení ukázkového kódu pro účely tohoto kurzu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="14ec2-640">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="14ec2-641">Zobrazit [stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="14ec2-641">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="14ec2-642">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="14ec2-642">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="14ec2-643">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="14ec2-643">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

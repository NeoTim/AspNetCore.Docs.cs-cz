---
title: 'Kurz: Vytvoření webového rozhraní API s ASP.NET jádrem'
author: rick-anderson
description: Přečtěte si, jak vytvořit webové rozhraní API s ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417663"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="e5677-103">Kurz: Vytvoření webového rozhraní API s ASP.NET jádrem</span><span class="sxs-lookup"><span data-stu-id="e5677-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="e5677-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="e5677-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="e5677-105">Tento kurz učí základy vytváření webového rozhraní API s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e5677-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e5677-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="e5677-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e5677-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-107">Create a web API project.</span></span>
> * <span data-ttu-id="e5677-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="e5677-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e5677-109">Lešení regulátor s metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="e5677-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="e5677-110">Nakonfigurujte směrování, cesty adres URL a vrácené hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e5677-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="e5677-111">Zavolejte webové rozhraní API s Postman.</span><span class="sxs-lookup"><span data-stu-id="e5677-111">Call the web API with Postman.</span></span>

<span data-ttu-id="e5677-112">Na konci máte webové rozhraní API, které můžete spravovat položky "to-do" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5677-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="e5677-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="e5677-113">Overview</span></span>

<span data-ttu-id="e5677-114">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e5677-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e5677-115">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5677-115">API</span></span> | <span data-ttu-id="e5677-116">Popis</span><span class="sxs-lookup"><span data-stu-id="e5677-116">Description</span></span> | <span data-ttu-id="e5677-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="e5677-117">Request body</span></span> | <span data-ttu-id="e5677-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="e5677-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="e5677-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e5677-119">GET /api/TodoItems</span></span> | <span data-ttu-id="e5677-120">Získat všechny položky v satojáši</span><span class="sxs-lookup"><span data-stu-id="e5677-120">Get all to-do items</span></span> | <span data-ttu-id="e5677-121">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-121">None</span></span> | <span data-ttu-id="e5677-122">Pole položek, které chcete provést</span><span class="sxs-lookup"><span data-stu-id="e5677-122">Array of to-do items</span></span>|
|<span data-ttu-id="e5677-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e5677-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="e5677-124">Získání položky podle ID</span><span class="sxs-lookup"><span data-stu-id="e5677-124">Get an item by ID</span></span> | <span data-ttu-id="e5677-125">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-125">None</span></span> | <span data-ttu-id="e5677-126">Položka k práci</span><span class="sxs-lookup"><span data-stu-id="e5677-126">To-do item</span></span>|
|<span data-ttu-id="e5677-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e5677-127">POST /api/TodoItems</span></span> | <span data-ttu-id="e5677-128">Přidání nové položky</span><span class="sxs-lookup"><span data-stu-id="e5677-128">Add a new item</span></span> | <span data-ttu-id="e5677-129">Položka k práci</span><span class="sxs-lookup"><span data-stu-id="e5677-129">To-do item</span></span> | <span data-ttu-id="e5677-130">Položka k práci</span><span class="sxs-lookup"><span data-stu-id="e5677-130">To-do item</span></span> |
|<span data-ttu-id="e5677-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e5677-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="e5677-132">Aktualizace existující položky&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5677-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e5677-133">Položka k práci</span><span class="sxs-lookup"><span data-stu-id="e5677-133">To-do item</span></span> | <span data-ttu-id="e5677-134">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-134">None</span></span> |
|<span data-ttu-id="e5677-135">DELETE /api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5677-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5677-136">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5677-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5677-137">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-137">None</span></span> | <span data-ttu-id="e5677-138">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-138">None</span></span>|

<span data-ttu-id="e5677-139">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5677-139">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e5677-145">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e5677-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5677-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5677-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5677-148">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e5677-149">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="e5677-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5677-151">V nabídce **Soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="e5677-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e5677-152">Vyberte šablonu **ASP.NET Základní webová aplikace** a klepněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="e5677-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e5677-153">Pojmenujte projekt *TodoApi* a klepněte na tlačítko **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5677-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e5677-154">V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci** zkontrolujte, zda jsou vybrány základní a ASP.NET **jádra 3.1.** **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="e5677-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="e5677-155">Vyberte šablonu **rozhraní API** a klepněte na **tlačítko Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5677-155">Select the **API** template and click **Create**.</span></span>

![Dialogové okno nového projektu VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5677-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5677-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5677-158">Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e5677-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e5677-159">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="e5677-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e5677-160">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5677-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="e5677-161">Když se dialogové okno zeptá, jestli chcete do projektu přidat požadované datové zdroje, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5677-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="e5677-162">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5677-162">The preceding commands:</span></span>

  * <span data-ttu-id="e5677-163">Vytvoří nový projekt webového rozhraní API a otevře jej v kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e5677-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="e5677-164">Přidá Balíčky NuGet, které jsou požadovány v další části.</span><span class="sxs-lookup"><span data-stu-id="e5677-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5677-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5677-166">Vyberte **možnost Nové řešení souboru** > **New Solution**.</span><span class="sxs-lookup"><span data-stu-id="e5677-166">Select **File** > **New Solution**.</span></span>

  ![macOS Nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e5677-168">Vyberte **rozhraní API** > **aplikace** > **.NET Core** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="e5677-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS Dialogové okno Nový projekt](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="e5677-170">V dialogovém **okně Konfigurovat nové webové rozhraní API ASP.NET** vyberte **Cílová architektura** \**.NET Core 3.1*.</span><span class="sxs-lookup"><span data-stu-id="e5677-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="e5677-171">Zadejte *TodoApi* pro **název projektu** a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5677-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![config dialogové okno](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="e5677-173">Otevřete terminál příkazů ve složce projektu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5677-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="e5677-174">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5677-174">Test the API</span></span>

<span data-ttu-id="e5677-175">Šablona projektu `WeatherForecast` vytvoří rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="e5677-176">Volání `Get` metody z prohlížeče k testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5677-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5677-178">Stisknutím kláves Ctrl+F5 aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="e5677-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5677-179">Visual Studio spustí prohlížeč a `https://localhost:<port>/WeatherForecast`přejde `<port>` na , kde je náhodně vybrané číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5677-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e5677-180">Pokud se zobrazí dialogové okno s dotazem, zda byste měli důvěřovat certifikátu IIS Express, vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="e5677-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e5677-181">V dialogovém **okně Upozornění zabezpečení,** které se zobrazí jako další, vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="e5677-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5677-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5677-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5677-183">Stisknutím kláves Ctrl+F5 aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="e5677-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5677-184">V prohlížeči přejděte na `https://localhost:5001/WeatherForecast`následující adresu URL: .</span><span class="sxs-lookup"><span data-stu-id="e5677-184">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5677-185">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5677-186">Chcete-li aplikaci spustit**spouštět ladění,** vyberte spustit ladění. **Run** > </span><span class="sxs-lookup"><span data-stu-id="e5677-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e5677-187">Visual Studio for Mac spustí prohlížeč `https://localhost:<port>`a `<port>` přejde na , kde je náhodně vybrané číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5677-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e5677-188">Je vrácena chyba HTTP 404 (Nebyla nalezena).</span><span class="sxs-lookup"><span data-stu-id="e5677-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e5677-189">Připojte `/WeatherForecast` adresu URL (změňte adresu URL na `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="e5677-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="e5677-190">JSON podobné následující je vrácena:</span><span class="sxs-lookup"><span data-stu-id="e5677-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="e5677-191">Přidání třídy modelu</span><span class="sxs-lookup"><span data-stu-id="e5677-191">Add a model class</span></span>

<span data-ttu-id="e5677-192">*Model* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="e5677-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e5677-193">Model pro tuto aplikaci `TodoItem` je jedna třída.</span><span class="sxs-lookup"><span data-stu-id="e5677-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5677-195">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5677-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e5677-196">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5677-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5677-197">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="e5677-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="e5677-198">Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5677-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5677-199">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e5677-200">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5677-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5677-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5677-202">Přidejte složku s názvem *Modely*.</span><span class="sxs-lookup"><span data-stu-id="e5677-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e5677-203">Přidejte `TodoItem` třídu do složky *Modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5677-204">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5677-205">Klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5677-205">Right-click the project.</span></span> <span data-ttu-id="e5677-206">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5677-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5677-207">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="e5677-207">Name the folder *Models*.</span></span>

  ![nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e5677-209">Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > novou **obecnou prázdnou** > **třídu** **souboru** > .</span><span class="sxs-lookup"><span data-stu-id="e5677-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e5677-210">Pojmenujte třídu *TodoItem*a klepněte na tlačítko **Nový**.</span><span class="sxs-lookup"><span data-stu-id="e5677-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e5677-211">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="e5677-212">Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="e5677-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e5677-213">Třídy modelu může jít kdekoli v projektu, ale *modely* složky se používá konvence.</span><span class="sxs-lookup"><span data-stu-id="e5677-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e5677-214">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e5677-214">Add a database context</span></span>

<span data-ttu-id="e5677-215">*Kontext databáze* je hlavní třída, která koordinuje entity framework funkce pro datový model.</span><span class="sxs-lookup"><span data-stu-id="e5677-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e5677-216">Tato třída je vytvořena odvozením `Microsoft.EntityFrameworkCore.DbContext` z třídy.</span><span class="sxs-lookup"><span data-stu-id="e5677-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="e5677-218">Přidat Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e5677-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="e5677-219">V nabídce **Nástroje** vyberte **Správce balíčků NuGet > Spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="e5677-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="e5677-220">Vyberte kartu **Procházet** a do vyhledávacího pole zadejte **Microsoft.EntityFrameworkCore.SqlServer.**</span><span class="sxs-lookup"><span data-stu-id="e5677-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="e5677-221">V levém podokně vyberte **microsoft.EntityFrameworkCore.SqlServer.**</span><span class="sxs-lookup"><span data-stu-id="e5677-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="e5677-222">Zaškrtněte **políčko Project** v pravém podokně a pak vyberte **Instalovat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="e5677-223">Pomocí předchozích pokynů přidejte balíček `Microsoft.EntityFrameworkCore.InMemory` NuGet.</span><span class="sxs-lookup"><span data-stu-id="e5677-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="e5677-225">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="e5677-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="e5677-226">Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5677-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5677-227">Pojmenujte třídu *TodoContext* a klepněte na tlačítko **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5677-228">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5677-229">Přidejte `TodoContext` třídu do složky *Modely.*</span><span class="sxs-lookup"><span data-stu-id="e5677-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e5677-230">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="e5677-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e5677-231">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e5677-231">Register the database context</span></span>

<span data-ttu-id="e5677-232">V ASP.NET Core, služby, jako je kontext DB musí být registrovány s kontejnerem [vkládání závislostí (DI).](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="e5677-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e5677-233">Kontejner poskytuje službu řadičům.</span><span class="sxs-lookup"><span data-stu-id="e5677-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="e5677-234">Aktualizace *Startup.cs* s následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="e5677-235">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e5677-235">The preceding code:</span></span>

* <span data-ttu-id="e5677-236">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="e5677-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e5677-237">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="e5677-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e5677-238">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="e5677-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="e5677-239">Lešení regulátor</span><span class="sxs-lookup"><span data-stu-id="e5677-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5677-241">Klikněte pravým tlačítkem myši na složku *Řadiče.*</span><span class="sxs-lookup"><span data-stu-id="e5677-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e5677-242">Vyberte **Přidat** > **novou položku scaffolded .**</span><span class="sxs-lookup"><span data-stu-id="e5677-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e5677-243">Vyberte **řadič rozhraní API s akcemi pomocí entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="e5677-244">V dialogovém **okně Přidat řadič rozhraní API s akcemi pomocí dialogového** okna Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="e5677-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="e5677-245">Vyberte **položku TodoItem (TodoApi.Models)** ve **třídě Model**.</span><span class="sxs-lookup"><span data-stu-id="e5677-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="e5677-246">Vyberte **TodoContext (TodoApi.Models)** ve **třídě kontextu Data**.</span><span class="sxs-lookup"><span data-stu-id="e5677-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="e5677-247">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5677-248">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e5677-249">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5677-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="e5677-250">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5677-250">The preceding commands:</span></span>

* <span data-ttu-id="e5677-251">Přidejte balíčky NuGet potřebné pro lešení.</span><span class="sxs-lookup"><span data-stu-id="e5677-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="e5677-252">Nainstaluje lešení motoru`dotnet-aspnet-codegenerator`( ).</span><span class="sxs-lookup"><span data-stu-id="e5677-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="e5677-253">Lešení `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="e5677-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="e5677-254">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="e5677-254">The generated code:</span></span>

* <span data-ttu-id="e5677-255">Označí třídu atributem. [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)</span><span class="sxs-lookup"><span data-stu-id="e5677-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="e5677-256">Tento atribut označuje, že řadič reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e5677-257">Informace o konkrétních chováních, které <xref:web-api/index>atribut povoluje, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e5677-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e5677-258">Používá DI vložit kontext`TodoContext`databáze ( ) do řadiče.</span><span class="sxs-lookup"><span data-stu-id="e5677-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e5677-259">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.</span><span class="sxs-lookup"><span data-stu-id="e5677-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="e5677-260">Základní ASP.NET pro:</span><span class="sxs-lookup"><span data-stu-id="e5677-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="e5677-261">Kontrolidy `[action]` se zobrazeními zahrnují v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="e5677-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="e5677-262">Řadiče rozhraní API `[action]` nezahrnují do šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="e5677-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="e5677-263">Pokud `[action]` token není v šabloně trasy, název [akce](xref:mvc/controllers/routing#action) je vyloučen z trasy.</span><span class="sxs-lookup"><span data-stu-id="e5677-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="e5677-264">To znamená, že název přidružené metody akce se nepoužívá v odpovídající trase.</span><span class="sxs-lookup"><span data-stu-id="e5677-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="e5677-265">Prozkoumejte metodu vytvoření posttodoitem</span><span class="sxs-lookup"><span data-stu-id="e5677-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="e5677-266">Nahraďte příkaz `PostTodoItem` return v příkazu použít [název operátoru:](/dotnet/csharp/language-reference/operators/nameof)</span><span class="sxs-lookup"><span data-stu-id="e5677-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="e5677-267">Předchozí kód je metoda HTTP POST, jak [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) je uvedeno atributem.</span><span class="sxs-lookup"><span data-stu-id="e5677-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="e5677-268">Metoda získá hodnotu položky pro do z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5677-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e5677-269">Metoda: <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*></span><span class="sxs-lookup"><span data-stu-id="e5677-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="e5677-270">Vrátí stavový kód HTTP 201, pokud je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="e5677-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="e5677-271">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="e5677-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e5677-272">Přidá k odpovědi hlavičku [Umístění.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)</span><span class="sxs-lookup"><span data-stu-id="e5677-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="e5677-273">Záhlaví `Location` určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky pro práci.</span><span class="sxs-lookup"><span data-stu-id="e5677-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="e5677-274">Další informace naleznete v tématu [10.2.2 201 Vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e5677-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e5677-275">Odkazuje na `GetTodoItem` akci k `Location` vytvoření identifikátoru URI záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e5677-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e5677-276">Klíčové slovo `nameof` C# se používá k zabránění `CreatedAtAction` pevnékódování název akce v volání.</span><span class="sxs-lookup"><span data-stu-id="e5677-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="e5677-277">Instalace Pošťáka</span><span class="sxs-lookup"><span data-stu-id="e5677-277">Install Postman</span></span>

<span data-ttu-id="e5677-278">Tento kurz používá Postman k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e5677-279">Instalace [Pošťáka](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="e5677-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="e5677-280">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5677-280">Start the web app.</span></span>
* <span data-ttu-id="e5677-281">Založ pošťáka.</span><span class="sxs-lookup"><span data-stu-id="e5677-281">Start Postman.</span></span>
* <span data-ttu-id="e5677-282">Zakázání **ověření certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="e5677-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="e5677-283">Z **nastavení** **souboru** > **(karta Obecné)** zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5677-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="e5677-284">Po otestování řadiče znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="e5677-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="e5677-285">Test PostTodoItem s pošťákem</span><span class="sxs-lookup"><span data-stu-id="e5677-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="e5677-286">Vytvořte nový požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5677-286">Create a new request.</span></span>
* <span data-ttu-id="e5677-287">Nastavte metodu `POST`HTTP na .</span><span class="sxs-lookup"><span data-stu-id="e5677-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e5677-288">Vyberte kartu **Tělo.**</span><span class="sxs-lookup"><span data-stu-id="e5677-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="e5677-289">Vyberte **nezpracované** přepínací tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e5677-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e5677-290">Nastavte typ na **JSON (aplikace/json)**.</span><span class="sxs-lookup"><span data-stu-id="e5677-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e5677-291">V těle požadavku zadejte JSON pro položku s cílem:</span><span class="sxs-lookup"><span data-stu-id="e5677-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e5677-292">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-292">Select **Send**.</span></span>

  ![Pošťák s požadavkem na vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e5677-294">Testování identifikátoru URI záhlaví umístění</span><span class="sxs-lookup"><span data-stu-id="e5677-294">Test the location header URI</span></span>

* <span data-ttu-id="e5677-295">V podokně **Odpověď** vyberte kartu **Záhlaví.**</span><span class="sxs-lookup"><span data-stu-id="e5677-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e5677-296">Zkopírujte hodnotu hlavičky **Umístění:**</span><span class="sxs-lookup"><span data-stu-id="e5677-296">Copy the **Location** header value:</span></span>

  ![Karta Záhlaví konzoly Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="e5677-298">Nastavte metodu GET.</span><span class="sxs-lookup"><span data-stu-id="e5677-298">Set the method to GET.</span></span>
* <span data-ttu-id="e5677-299">Vložte identifikátor URI `https://localhost:5001/api/TodoItems/1`(například).</span><span class="sxs-lookup"><span data-stu-id="e5677-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e5677-300">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="e5677-301">Prozkoumejte metody GET</span><span class="sxs-lookup"><span data-stu-id="e5677-301">Examine the GET methods</span></span>

<span data-ttu-id="e5677-302">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="e5677-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="e5677-303">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo Pošťáka.</span><span class="sxs-lookup"><span data-stu-id="e5677-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="e5677-304">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e5677-304">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="e5677-305">Výzva k `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="e5677-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="e5677-306">Test Get s Pošťák</span><span class="sxs-lookup"><span data-stu-id="e5677-306">Test Get with Postman</span></span>

* <span data-ttu-id="e5677-307">Vytvořte nový požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5677-307">Create a new request.</span></span>
* <span data-ttu-id="e5677-308">Nastavte metodu HTTP na **GET**.</span><span class="sxs-lookup"><span data-stu-id="e5677-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="e5677-309">Nastavte adresu URL `https://localhost:<port>/api/TodoItems`požadavku na .</span><span class="sxs-lookup"><span data-stu-id="e5677-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="e5677-310">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e5677-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="e5677-311">Nastavte **dvě zobrazení podokna** v Pošťákovi.</span><span class="sxs-lookup"><span data-stu-id="e5677-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e5677-312">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-312">Select **Send**.</span></span>

<span data-ttu-id="e5677-313">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="e5677-313">This app uses an in-memory database.</span></span> <span data-ttu-id="e5677-314">Pokud je aplikace zastavena a spuštěna, předchozí požadavek GET nevrátí žádná data.</span><span class="sxs-lookup"><span data-stu-id="e5677-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="e5677-315">Pokud nejsou vrácena žádná data, [post](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5677-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="e5677-316">Cesty směrování a adresy URL</span><span class="sxs-lookup"><span data-stu-id="e5677-316">Routing and URL paths</span></span>

<span data-ttu-id="e5677-317">Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e5677-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e5677-318">Cesta URL pro každou metodu je vytvořena takto:</span><span class="sxs-lookup"><span data-stu-id="e5677-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e5677-319">Začněte řetězcem šablony v `Route` atributu řadiče:</span><span class="sxs-lookup"><span data-stu-id="e5677-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="e5677-320">Nahraďte `[controller]` název řadiče, který podle konvence je název třídy řadiče mínus přípona "Controller".</span><span class="sxs-lookup"><span data-stu-id="e5677-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e5677-321">Pro tuto ukázku je název třídy řadiče **TodoItems**Controller, takže název řadiče je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="e5677-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="e5677-322">ASP.NET [Směrování](xref:mvc/controllers/routing) jádra nerozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="e5677-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e5677-323">Pokud `[HttpGet]` má atribut šablonu trasy `[HttpGet("products")]`(například), připojte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="e5677-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e5677-324">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="e5677-324">This sample doesn't use a template.</span></span> <span data-ttu-id="e5677-325">Další informace naleznete v [tématu Směrování atributů s atributy Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e5677-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e5677-326">V následující `GetTodoItem` metodě `"{id}"` je zástupná proměnná pro jedinečný identifikátor položky s cílem.</span><span class="sxs-lookup"><span data-stu-id="e5677-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e5677-327">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL k dispozici `id` metodě v jejím parametru.</span><span class="sxs-lookup"><span data-stu-id="e5677-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e5677-328">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="e5677-328">Return values</span></span>

<span data-ttu-id="e5677-329">Návratový typ `GetTodoItems` metody `GetTodoItem` a je [ActionResult\<T> typu](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e5677-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e5677-330">ASP.NET Core automaticky serializuje objekt [JSON](https://www.json.org/) a zapíše JSON do těla zprávy odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5677-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e5677-331">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="e5677-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e5677-332">Neošetřené výjimky jsou přeloženy do chyb 5xx.</span><span class="sxs-lookup"><span data-stu-id="e5677-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e5677-333">`ActionResult`návratové typy mohou představovat širokou škálu stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5677-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e5677-334">Můžete například `GetTodoItem` vrátit dvě různé hodnoty stavu:</span><span class="sxs-lookup"><span data-stu-id="e5677-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e5677-335">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound.](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)</span><span class="sxs-lookup"><span data-stu-id="e5677-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="e5677-336">V opačném případě metoda vrátí 200 s tělo odezvy JSON.</span><span class="sxs-lookup"><span data-stu-id="e5677-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e5677-337">Vracející `item` se výsledky odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e5677-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="e5677-338">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-338">The PutTodoItem method</span></span>

<span data-ttu-id="e5677-339">Zkontrolujte `PutTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="e5677-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="e5677-340">`PutTodoItem`je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="e5677-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e5677-341">Odpověď je [204 (Žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e5677-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e5677-342">Podle specifikace HTTP požadavek PUT vyžaduje, aby klient odeslal celou aktualizovanou entitu, nikoli pouze změny.</span><span class="sxs-lookup"><span data-stu-id="e5677-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e5677-343">Chcete-li podporovat částečné aktualizace, použijte [protokol HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e5677-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e5677-344">Pokud se zobrazí `PutTodoItem`chyba `GET` volání , volání zajistit, že je položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5677-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e5677-345">Otestovat metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="e5677-346">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5677-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e5677-347">Před voláním PUT musí být v databázi položka.</span><span class="sxs-lookup"><span data-stu-id="e5677-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e5677-348">Volání GET pojistit, že je položka v databázi před provedením volání PUT.</span><span class="sxs-lookup"><span data-stu-id="e5677-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e5677-349">Aktualizujte položku, která má ID = 1 a nastavte její název na "krmné ryby":</span><span class="sxs-lookup"><span data-stu-id="e5677-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e5677-350">Následující obrázek znázorňuje pošťákovou aktualizaci:</span><span class="sxs-lookup"><span data-stu-id="e5677-350">The following image shows the Postman update:</span></span>

![Pošťák konzole zobrazující 204 (žádný obsah) odpověď](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="e5677-352">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="e5677-353">Zkontrolujte `DeleteTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="e5677-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e5677-354">Otestovat metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e5677-355">Pomocí pošťáku odstraňte položku, kterou chcete provést:</span><span class="sxs-lookup"><span data-stu-id="e5677-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e5677-356">Nastavte metodu `DELETE`na .</span><span class="sxs-lookup"><span data-stu-id="e5677-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e5677-357">Nastavte identifikátor URI objektu, který `https://localhost:5001/api/TodoItems/1`chcete odstranit (například).</span><span class="sxs-lookup"><span data-stu-id="e5677-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e5677-358">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="e5677-359">Zabránit nadměrnému zaúčtování</span><span class="sxs-lookup"><span data-stu-id="e5677-359">Prevent over-posting</span></span>

<span data-ttu-id="e5677-360">V současné době ukázková `TodoItem` aplikace zpřístupňuje celý objekt.</span><span class="sxs-lookup"><span data-stu-id="e5677-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="e5677-361">Produkční aplikace obvykle omezují data, která je vstupní a vrácená pomocí podmnožiny modelu.</span><span class="sxs-lookup"><span data-stu-id="e5677-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="e5677-362">Existuje několik důvodů za to a bezpečnost je hlavní.</span><span class="sxs-lookup"><span data-stu-id="e5677-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="e5677-363">Podmnožina modelu se obvykle označuje jako objekt přenosu dat (DTO), vstupní model nebo model pohledu.</span><span class="sxs-lookup"><span data-stu-id="e5677-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="e5677-364">**DTO** se používá v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="e5677-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="e5677-365">DTO lze použít k:</span><span class="sxs-lookup"><span data-stu-id="e5677-365">A DTO may be used to:</span></span>

* <span data-ttu-id="e5677-366">Zabránit nadměrnému zaúčtování.</span><span class="sxs-lookup"><span data-stu-id="e5677-366">Prevent over-posting.</span></span>
* <span data-ttu-id="e5677-367">Skrýt vlastnosti, které klienti nemají zobrazit.</span><span class="sxs-lookup"><span data-stu-id="e5677-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="e5677-368">Vynechejte některé vlastnosti, aby se zmenšila velikost datové části.</span><span class="sxs-lookup"><span data-stu-id="e5677-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="e5677-369">Sloučí grafy objektů, které obsahují vnořené objekty.</span><span class="sxs-lookup"><span data-stu-id="e5677-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="e5677-370">Grafy složených objektů mohou být pro klienty pohodlnější.</span><span class="sxs-lookup"><span data-stu-id="e5677-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="e5677-371">Chcete-li demonstrovat přístup `TodoItem` DTO, aktualizujte třídu tak, aby zahrnovala tajné pole:</span><span class="sxs-lookup"><span data-stu-id="e5677-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="e5677-372">Tajné pole musí být z této aplikace skryto, ale aplikace pro správu se může rozhodnout, že ji odhalí.</span><span class="sxs-lookup"><span data-stu-id="e5677-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="e5677-373">Ověřte, zda můžete zveřejnit a získat tajné pole.</span><span class="sxs-lookup"><span data-stu-id="e5677-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="e5677-374">Vytvořte model DTO:</span><span class="sxs-lookup"><span data-stu-id="e5677-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="e5677-375">Aktualizace `TodoItemsController` chcete-li použít `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="e5677-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="e5677-376">Ověřte, zda nemůžete zveřejnit nebo získat tajné pole.</span><span class="sxs-lookup"><span data-stu-id="e5677-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e5677-377">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e5677-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="e5677-378">Viz [výuka: Volání ASP.NET základního webového rozhraní API s JavaScriptem](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="e5677-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5677-379">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="e5677-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e5677-380">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-380">Create a web API project.</span></span>
> * <span data-ttu-id="e5677-381">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="e5677-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e5677-382">Přidejte ovladač.</span><span class="sxs-lookup"><span data-stu-id="e5677-382">Add a controller.</span></span>
> * <span data-ttu-id="e5677-383">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="e5677-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="e5677-384">Nakonfigurujte cesty směrování a adresy URL.</span><span class="sxs-lookup"><span data-stu-id="e5677-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="e5677-385">Zadejte vrácené hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e5677-385">Specify return values.</span></span>
> * <span data-ttu-id="e5677-386">Zavolejte webové rozhraní API s Postman.</span><span class="sxs-lookup"><span data-stu-id="e5677-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="e5677-387">Volání webového rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e5677-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="e5677-388">Na konci máte webové rozhraní API, které můžete spravovat položky "to-do" uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="e5677-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="e5677-389">Přehled</span><span class="sxs-lookup"><span data-stu-id="e5677-389">Overview</span></span>

<span data-ttu-id="e5677-390">Tento kurz vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e5677-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e5677-391">rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5677-391">API</span></span> | <span data-ttu-id="e5677-392">Popis</span><span class="sxs-lookup"><span data-stu-id="e5677-392">Description</span></span> | <span data-ttu-id="e5677-393">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="e5677-393">Request body</span></span> | <span data-ttu-id="e5677-394">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="e5677-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="e5677-395">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e5677-395">GET /api/TodoItems</span></span> | <span data-ttu-id="e5677-396">Získat všechny položky v satojáši</span><span class="sxs-lookup"><span data-stu-id="e5677-396">Get all to-do items</span></span> | <span data-ttu-id="e5677-397">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-397">None</span></span> | <span data-ttu-id="e5677-398">Pole položek, které chcete provést</span><span class="sxs-lookup"><span data-stu-id="e5677-398">Array of to-do items</span></span>|
|<span data-ttu-id="e5677-399">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e5677-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="e5677-400">Získání položky podle ID</span><span class="sxs-lookup"><span data-stu-id="e5677-400">Get an item by ID</span></span> | <span data-ttu-id="e5677-401">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-401">None</span></span> | <span data-ttu-id="e5677-402">Položka k práci</span><span class="sxs-lookup"><span data-stu-id="e5677-402">To-do item</span></span>|
|<span data-ttu-id="e5677-403">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e5677-403">POST /api/TodoItems</span></span> | <span data-ttu-id="e5677-404">Přidání nové položky</span><span class="sxs-lookup"><span data-stu-id="e5677-404">Add a new item</span></span> | <span data-ttu-id="e5677-405">Položka k práci</span><span class="sxs-lookup"><span data-stu-id="e5677-405">To-do item</span></span> | <span data-ttu-id="e5677-406">Položka k práci</span><span class="sxs-lookup"><span data-stu-id="e5677-406">To-do item</span></span> |
|<span data-ttu-id="e5677-407">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e5677-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="e5677-408">Aktualizace existující položky&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5677-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e5677-409">Položka k práci</span><span class="sxs-lookup"><span data-stu-id="e5677-409">To-do item</span></span> | <span data-ttu-id="e5677-410">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-410">None</span></span> |
|<span data-ttu-id="e5677-411">DELETE /api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5677-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5677-412">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5677-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5677-413">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-413">None</span></span> | <span data-ttu-id="e5677-414">Žádný</span><span class="sxs-lookup"><span data-stu-id="e5677-414">None</span></span>|

<span data-ttu-id="e5677-415">Následující diagram znázorňuje návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5677-415">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e5677-421">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e5677-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5677-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5677-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5677-424">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e5677-425">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="e5677-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-426">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5677-427">V nabídce **Soubor** vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="e5677-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e5677-428">Vyberte šablonu **ASP.NET Základní webová aplikace** a klepněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="e5677-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e5677-429">Pojmenujte projekt *TodoApi* a klepněte na tlačítko **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5677-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e5677-430">V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci** zkontrolujte, zda jsou vybrány základní a **ASP.NET jádra 2.2.Net.** **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="e5677-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="e5677-431">Vyberte šablonu **rozhraní API** a klepněte na **tlačítko Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5677-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="e5677-432">**Nevybírejte** **povolit podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="e5677-432">**Don't** select **Enable Docker Support**.</span></span>

![Dialogové okno nového projektu VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5677-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5677-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5677-435">Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e5677-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e5677-436">Změňte adresáře (`cd`) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="e5677-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e5677-437">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5677-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="e5677-438">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci kódu sady Visual Studio v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="e5677-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="e5677-439">Když se dialogové okno zeptá, jestli chcete do projektu přidat požadované datové zdroje, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5677-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5677-440">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5677-441">Vyberte **možnost Nové řešení souboru** > **New Solution**.</span><span class="sxs-lookup"><span data-stu-id="e5677-441">Select **File** > **New Solution**.</span></span>

  ![macOS Nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e5677-443">Vyberte **rozhraní API** > **aplikace** > **.NET Core** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="e5677-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS Dialogové okno Nový projekt](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="e5677-445">V dialogovém **okně Konfigurovat nové ASP.NET základní webové rozhraní API** přijměte výchozí **cílovou architekturu** \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="e5677-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="e5677-446">Zadejte *TodoApi* pro **název projektu** a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5677-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![config dialogové okno](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="e5677-448">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5677-448">Test the API</span></span>

<span data-ttu-id="e5677-449">Šablona projektu `values` vytvoří rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-449">The project template creates a `values` API.</span></span> <span data-ttu-id="e5677-450">Volání `Get` metody z prohlížeče k testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5677-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5677-452">Stisknutím kláves Ctrl+F5 aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="e5677-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5677-453">Visual Studio spustí prohlížeč a `https://localhost:<port>/api/values`přejde `<port>` na , kde je náhodně vybrané číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5677-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e5677-454">Pokud se zobrazí dialogové okno s dotazem, zda byste měli důvěřovat certifikátu IIS Express, vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="e5677-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e5677-455">V dialogovém **okně Upozornění zabezpečení,** které se zobrazí jako další, vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="e5677-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5677-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5677-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5677-457">Stisknutím kláves Ctrl+F5 aplikaci spusťte.</span><span class="sxs-lookup"><span data-stu-id="e5677-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5677-458">V prohlížeči přejděte na `https://localhost:5001/api/values`následující adresu URL: .</span><span class="sxs-lookup"><span data-stu-id="e5677-458">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5677-459">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5677-460">Chcete-li aplikaci spustit**spouštět ladění,** vyberte spustit ladění. **Run** > </span><span class="sxs-lookup"><span data-stu-id="e5677-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e5677-461">Visual Studio for Mac spustí prohlížeč `https://localhost:<port>`a `<port>` přejde na , kde je náhodně vybrané číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5677-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e5677-462">Je vrácena chyba HTTP 404 (Nebyla nalezena).</span><span class="sxs-lookup"><span data-stu-id="e5677-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e5677-463">Připojte `/api/values` adresu URL (změňte adresu URL na `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="e5677-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="e5677-464">Vrátí se následující JSON:</span><span class="sxs-lookup"><span data-stu-id="e5677-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="e5677-465">Přidání třídy modelu</span><span class="sxs-lookup"><span data-stu-id="e5677-465">Add a model class</span></span>

<span data-ttu-id="e5677-466">*Model* je sada tříd, které představují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="e5677-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e5677-467">Model pro tuto aplikaci `TodoItem` je jedna třída.</span><span class="sxs-lookup"><span data-stu-id="e5677-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5677-469">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5677-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e5677-470">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5677-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5677-471">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="e5677-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="e5677-472">Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5677-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5677-473">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e5677-474">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5677-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5677-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5677-476">Přidejte složku s názvem *Modely*.</span><span class="sxs-lookup"><span data-stu-id="e5677-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e5677-477">Přidejte `TodoItem` třídu do složky *Modely* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5677-478">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5677-479">Klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5677-479">Right-click the project.</span></span> <span data-ttu-id="e5677-480">Vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5677-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5677-481">Pojmenujte složku *Modely*.</span><span class="sxs-lookup"><span data-stu-id="e5677-481">Name the folder *Models*.</span></span>

  ![nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e5677-483">Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > novou **obecnou prázdnou** > **třídu** **souboru** > .</span><span class="sxs-lookup"><span data-stu-id="e5677-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e5677-484">Pojmenujte třídu *TodoItem*a klepněte na tlačítko **Nový**.</span><span class="sxs-lookup"><span data-stu-id="e5677-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e5677-485">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="e5677-486">Vlastnost `Id` funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="e5677-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e5677-487">Třídy modelu může jít kdekoli v projektu, ale *modely* složky se používá konvence.</span><span class="sxs-lookup"><span data-stu-id="e5677-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e5677-488">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e5677-488">Add a database context</span></span>

<span data-ttu-id="e5677-489">*Kontext databáze* je hlavní třída, která koordinuje entity framework funkce pro datový model.</span><span class="sxs-lookup"><span data-stu-id="e5677-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e5677-490">Tato třída je vytvořena odvozením `Microsoft.EntityFrameworkCore.DbContext` z třídy.</span><span class="sxs-lookup"><span data-stu-id="e5677-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-491">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5677-492">Klepněte pravým tlačítkem myši na složku *Modely* a vyberte **přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5677-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5677-493">Pojmenujte třídu *TodoContext* a klepněte na tlačítko **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5677-494">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5677-495">Přidejte `TodoContext` třídu do složky *Modely.*</span><span class="sxs-lookup"><span data-stu-id="e5677-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e5677-496">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e5677-497">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e5677-497">Register the database context</span></span>

<span data-ttu-id="e5677-498">V ASP.NET Core, služby, jako je kontext DB musí být registrovány s kontejnerem [vkládání závislostí (DI).](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="e5677-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e5677-499">Kontejner poskytuje službu řadičům.</span><span class="sxs-lookup"><span data-stu-id="e5677-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="e5677-500">Aktualizace *Startup.cs* s následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="e5677-501">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e5677-501">The preceding code:</span></span>

* <span data-ttu-id="e5677-502">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="e5677-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e5677-503">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="e5677-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e5677-504">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="e5677-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e5677-505">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="e5677-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5677-507">Klikněte pravým tlačítkem myši na složku *Řadiče.*</span><span class="sxs-lookup"><span data-stu-id="e5677-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e5677-508">Vyberte **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="e5677-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="e5677-509">V dialogovém okně **Přidat novou položku** vyberte šablonu **třídy řadiče rozhraní API.**</span><span class="sxs-lookup"><span data-stu-id="e5677-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="e5677-510">Pojmenujte třídu *TodoController*a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogové okno Přidat novou položku s ovladačem ve vyhledávacím poli a vybraným řadičem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5677-512">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5677-513">Ve složce *Controllers* vytvořte `TodoController`třídu s názvem .</span><span class="sxs-lookup"><span data-stu-id="e5677-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="e5677-514">Nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="e5677-515">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e5677-515">The preceding code:</span></span>

* <span data-ttu-id="e5677-516">Definuje třídu řadiče rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="e5677-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="e5677-517">Označí třídu atributem. [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)</span><span class="sxs-lookup"><span data-stu-id="e5677-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="e5677-518">Tento atribut označuje, že řadič reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e5677-519">Informace o konkrétních chováních, které <xref:web-api/index>atribut povoluje, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e5677-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e5677-520">Používá DI vložit kontext`TodoContext`databáze ( ) do řadiče.</span><span class="sxs-lookup"><span data-stu-id="e5677-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e5677-521">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.</span><span class="sxs-lookup"><span data-stu-id="e5677-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="e5677-522">Přidá položku `Item1` pojmenovanou do databáze, pokud je databáze prázdná.</span><span class="sxs-lookup"><span data-stu-id="e5677-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="e5677-523">Tento kód je v konstruktoru, takže se spustí pokaždé, když je nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5677-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="e5677-524">Pokud odstraníte všechny položky, `Item1` konstruktor vytvoří znovu při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="e5677-525">Takže to může vypadat, že odstranění nefungovalo, když to skutečně fungovalo.</span><span class="sxs-lookup"><span data-stu-id="e5677-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="e5677-526">Přidat metody Get</span><span class="sxs-lookup"><span data-stu-id="e5677-526">Add Get methods</span></span>

<span data-ttu-id="e5677-527">Chcete-li poskytnout rozhraní API, které načítá položky, přidejte do `TodoController` třídy následující metody:</span><span class="sxs-lookup"><span data-stu-id="e5677-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="e5677-528">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="e5677-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="e5677-529">Pokud je aplikace stále spuštěná, zastavte ji.</span><span class="sxs-lookup"><span data-stu-id="e5677-529">Stop the app if it's still running.</span></span> <span data-ttu-id="e5677-530">Pak jej spusťte znovu, aby zahrnovalnejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="e5677-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="e5677-531">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e5677-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="e5677-532">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e5677-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="e5677-533">Volání mj. `GetTodoItems`</span><span class="sxs-lookup"><span data-stu-id="e5677-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="e5677-534">Cesty směrování a adresy URL</span><span class="sxs-lookup"><span data-stu-id="e5677-534">Routing and URL paths</span></span>

<span data-ttu-id="e5677-535">Atribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e5677-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e5677-536">Cesta URL pro každou metodu je vytvořena takto:</span><span class="sxs-lookup"><span data-stu-id="e5677-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e5677-537">Začněte řetězcem šablony v `Route` atributu řadiče:</span><span class="sxs-lookup"><span data-stu-id="e5677-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="e5677-538">Nahraďte `[controller]` název řadiče, který podle konvence je název třídy řadiče mínus přípona "Controller".</span><span class="sxs-lookup"><span data-stu-id="e5677-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e5677-539">Pro tuto ukázku je název třídy řadiče **Todo**Controller, takže název řadiče je "todo".</span><span class="sxs-lookup"><span data-stu-id="e5677-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="e5677-540">ASP.NET [Směrování](xref:mvc/controllers/routing) jádra nerozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="e5677-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e5677-541">Pokud `[HttpGet]` má atribut šablonu trasy `[HttpGet("products")]`(například), připojte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="e5677-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e5677-542">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="e5677-542">This sample doesn't use a template.</span></span> <span data-ttu-id="e5677-543">Další informace naleznete v [tématu Směrování atributů s atributy Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e5677-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e5677-544">V následující `GetTodoItem` metodě `"{id}"` je zástupná proměnná pro jedinečný identifikátor položky s cílem.</span><span class="sxs-lookup"><span data-stu-id="e5677-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e5677-545">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL k dispozici`id` metodě v jejím parametru.</span><span class="sxs-lookup"><span data-stu-id="e5677-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e5677-546">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="e5677-546">Return values</span></span>

<span data-ttu-id="e5677-547">Návratový typ `GetTodoItems` metody `GetTodoItem` a je [ActionResult\<T> typu](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e5677-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e5677-548">ASP.NET Core automaticky serializuje objekt [JSON](https://www.json.org/) a zapíše JSON do těla zprávy odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5677-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e5677-549">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="e5677-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e5677-550">Neošetřené výjimky jsou přeloženy do chyb 5xx.</span><span class="sxs-lookup"><span data-stu-id="e5677-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e5677-551">`ActionResult`návratové typy mohou představovat širokou škálu stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5677-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e5677-552">Můžete například `GetTodoItem` vrátit dvě různé hodnoty stavu:</span><span class="sxs-lookup"><span data-stu-id="e5677-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e5677-553">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby 404 [NotFound.](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)</span><span class="sxs-lookup"><span data-stu-id="e5677-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="e5677-554">V opačném případě metoda vrátí 200 s tělo odezvy JSON.</span><span class="sxs-lookup"><span data-stu-id="e5677-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e5677-555">Vracející `item` se výsledky odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e5677-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="e5677-556">Otestovat metodu GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="e5677-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="e5677-557">Tento kurz používá Postman k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e5677-558">Nainstalujte [Pošťáka](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="e5677-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="e5677-559">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5677-559">Start the web app.</span></span>
* <span data-ttu-id="e5677-560">Založ pošťáka.</span><span class="sxs-lookup"><span data-stu-id="e5677-560">Start Postman.</span></span>
* <span data-ttu-id="e5677-561">Zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5677-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5677-562">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5677-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5677-563">Z **nastavení** **souboru** > **(karta Obecné)** zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5677-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5677-564">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5677-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5677-565">Z **předvoleb Pošťáka** > **Preferences** (**karta Obecné)** zakažte **ověření certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5677-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="e5677-566">Případně vyberte klíč a vyberte **Nastavení**a zakažte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="e5677-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="e5677-567">Po otestování řadiče znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="e5677-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="e5677-568">Vytvořte nový požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5677-568">Create a new request.</span></span>
  * <span data-ttu-id="e5677-569">Nastavte metodu HTTP na **GET**.</span><span class="sxs-lookup"><span data-stu-id="e5677-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="e5677-570">Nastavte adresu URL `https://localhost:<port>/api/todo`požadavku na .</span><span class="sxs-lookup"><span data-stu-id="e5677-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="e5677-571">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="e5677-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="e5677-572">Nastavte **dvě zobrazení podokna** v Pošťákovi.</span><span class="sxs-lookup"><span data-stu-id="e5677-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e5677-573">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-573">Select **Send**.</span></span>

![Pošťák s žádostí O get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="e5677-575">Přidat metodu Vytvořit</span><span class="sxs-lookup"><span data-stu-id="e5677-575">Add a Create method</span></span>

<span data-ttu-id="e5677-576">Do `PostTodoItem` *controllers/TodoController.cs*přidejte následující metodu :</span><span class="sxs-lookup"><span data-stu-id="e5677-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="e5677-577">Předchozí kód je metoda HTTP POST, jak [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) je uvedeno atributem.</span><span class="sxs-lookup"><span data-stu-id="e5677-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="e5677-578">Metoda získá hodnotu položky pro do z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5677-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e5677-579">Metoda: `CreatedAtAction`</span><span class="sxs-lookup"><span data-stu-id="e5677-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="e5677-580">Vrátí stavový kód HTTP 201, pokud je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="e5677-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="e5677-581">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="e5677-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e5677-582">Přidá `Location` záhlaví k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5677-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="e5677-583">Záhlaví `Location` určuje identifikátor URI nově vytvořené položky pro práci.</span><span class="sxs-lookup"><span data-stu-id="e5677-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="e5677-584">Další informace naleznete v tématu [10.2.2 201 Vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e5677-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e5677-585">Odkazuje na `GetTodoItem` akci k `Location` vytvoření identifikátoru URI záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e5677-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e5677-586">Klíčové slovo `nameof` C# se používá k zabránění `CreatedAtAction` pevnékódování název akce v volání.</span><span class="sxs-lookup"><span data-stu-id="e5677-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="e5677-587">Otestovat metodu PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="e5677-588">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="e5677-588">Build the project.</span></span>
* <span data-ttu-id="e5677-589">V Pošťáku nastavte `POST`metodu HTTP na .</span><span class="sxs-lookup"><span data-stu-id="e5677-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e5677-590">Vyberte kartu **Tělo.**</span><span class="sxs-lookup"><span data-stu-id="e5677-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="e5677-591">Vyberte **nezpracované** přepínací tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e5677-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e5677-592">Nastavte typ na **JSON (aplikace/json)**.</span><span class="sxs-lookup"><span data-stu-id="e5677-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e5677-593">V těle požadavku zadejte JSON pro položku s cílem:</span><span class="sxs-lookup"><span data-stu-id="e5677-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e5677-594">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-594">Select **Send**.</span></span>

  ![Pošťák s požadavkem na vytvoření](first-web-api/_static/create.png)

  <span data-ttu-id="e5677-596">Pokud se zobrazí chyba 405 Method Not Allowed, je pravděpodobně výsledkem nekompilace projektu po přidání `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="e5677-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e5677-597">Testování identifikátoru URI záhlaví umístění</span><span class="sxs-lookup"><span data-stu-id="e5677-597">Test the location header URI</span></span>

* <span data-ttu-id="e5677-598">V podokně **Odpověď** vyberte kartu **Záhlaví.**</span><span class="sxs-lookup"><span data-stu-id="e5677-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e5677-599">Zkopírujte hodnotu hlavičky **Umístění:**</span><span class="sxs-lookup"><span data-stu-id="e5677-599">Copy the **Location** header value:</span></span>

  ![Karta Záhlaví konzoly Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="e5677-601">Nastavte metodu GET.</span><span class="sxs-lookup"><span data-stu-id="e5677-601">Set the method to GET.</span></span>
* <span data-ttu-id="e5677-602">Vložte identifikátor URI `https://localhost:5001/api/Todo/2`(například).</span><span class="sxs-lookup"><span data-stu-id="e5677-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="e5677-603">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="e5677-604">Přidat metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="e5677-605">Přidejte `PutTodoItem` následující metodu:</span><span class="sxs-lookup"><span data-stu-id="e5677-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="e5677-606">`PutTodoItem`je podobný `PostTodoItem`, s výjimkou používá HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="e5677-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e5677-607">Odpověď je [204 (Žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e5677-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e5677-608">Podle specifikace HTTP požadavek PUT vyžaduje, aby klient odeslal celou aktualizovanou entitu, nikoli pouze změny.</span><span class="sxs-lookup"><span data-stu-id="e5677-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e5677-609">Chcete-li podporovat částečné aktualizace, použijte [protokol HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e5677-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e5677-610">Pokud se zobrazí `PutTodoItem`chyba `GET` volání , volání zajistit, že je položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5677-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e5677-611">Otestovat metodu PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="e5677-612">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5677-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e5677-613">Před voláním PUT musí být v databázi položka.</span><span class="sxs-lookup"><span data-stu-id="e5677-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e5677-614">Volání GET pojistit, že je položka v databázi před provedením volání PUT.</span><span class="sxs-lookup"><span data-stu-id="e5677-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e5677-615">Aktualizujte položku, která má id = 1 a nastavte její název na "krmné ryby":</span><span class="sxs-lookup"><span data-stu-id="e5677-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e5677-616">Následující obrázek znázorňuje pošťákovou aktualizaci:</span><span class="sxs-lookup"><span data-stu-id="e5677-616">The following image shows the Postman update:</span></span>

![Pošťák konzole zobrazující 204 (žádný obsah) odpověď](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="e5677-618">Přidat metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="e5677-619">Přidejte `DeleteTodoItem` následující metodu:</span><span class="sxs-lookup"><span data-stu-id="e5677-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="e5677-620">Odpověď `DeleteTodoItem` je [204 (Žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e5677-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e5677-621">Otestovat metodu DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5677-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e5677-622">Pomocí pošťáku odstraňte položku, kterou chcete provést:</span><span class="sxs-lookup"><span data-stu-id="e5677-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e5677-623">Nastavte metodu `DELETE`na .</span><span class="sxs-lookup"><span data-stu-id="e5677-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e5677-624">Nastavte identifikátor URI objektu, který `https://localhost:5001/api/todo/1`chcete odstranit (například).</span><span class="sxs-lookup"><span data-stu-id="e5677-624">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="e5677-625">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5677-625">Select **Send**.</span></span>

<span data-ttu-id="e5677-626">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="e5677-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="e5677-627">Však při odstranění poslední položky, je vytvořen nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e5677-628">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e5677-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="e5677-629">V této části je přidána stránka HTML, která používá JavaScript k volání webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="e5677-630">jQuery iniciuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5677-630">jQuery initiates the request.</span></span> <span data-ttu-id="e5677-631">JavaScript aktualizuje stránku podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="e5677-632">Nakonfigurujte aplikaci tak, aby [zobrazovala statické soubory,](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* s následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="e5677-633">Vytvořte složku *wwwroot* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="e5677-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="e5677-634">Přidejte soubor HTML s názvem *index.html* do adresáře *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="e5677-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="e5677-635">Nahraďte jeho obsah následujícími značkami:</span><span class="sxs-lookup"><span data-stu-id="e5677-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="e5677-636">Přidejte soubor JavaScript s názvem *site.js* do adresáře *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="e5677-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="e5677-637">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5677-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="e5677-638">Ke místnímu testování stránky HTML může být nutná změna nastavení spuštění projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e5677-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="e5677-639">*Spusťte položku Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e5677-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="e5677-640">Odeberte `launchUrl` vlastnost, chcete-li aplikaci vynutit otevření na *adrese index.html*&mdash;výchozího souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="e5677-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="e5677-641">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="e5677-642">Následují vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5677-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="e5677-643">Získání seznamu položek úkolů</span><span class="sxs-lookup"><span data-stu-id="e5677-643">Get a list of to-do items</span></span>

<span data-ttu-id="e5677-644">jQuery odešle požadavek HTTP GET do webového rozhraní API, které vrací JSON představující pole položek, které chcete.</span><span class="sxs-lookup"><span data-stu-id="e5677-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="e5677-645">Funkce `success` zpětného volání je vyvolána, pokud je požadavek úspěšný.</span><span class="sxs-lookup"><span data-stu-id="e5677-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="e5677-646">V zpětném volání dom je aktualizován s informacemi o do do.</span><span class="sxs-lookup"><span data-stu-id="e5677-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="e5677-647">Přidání položky s cílem</span><span class="sxs-lookup"><span data-stu-id="e5677-647">Add a to-do item</span></span>

<span data-ttu-id="e5677-648">jQuery odešle požadavek HTTP POST s položkou k dokončení v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="e5677-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="e5677-649">`accepts` Možnosti `contentType` a jsou `application/json` nastaveny tak, aby určovat typ média, které jsou přijímány a odesílány.</span><span class="sxs-lookup"><span data-stu-id="e5677-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="e5677-650">Položka převodu je převedena na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="e5677-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="e5677-651">Pokud rozhraní API vrátí úspěšný `getData` stavový kód, funkce je vyvolána k aktualizaci tabulky HTML.</span><span class="sxs-lookup"><span data-stu-id="e5677-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="e5677-652">Aktualizace položky s cílem</span><span class="sxs-lookup"><span data-stu-id="e5677-652">Update a to-do item</span></span>

<span data-ttu-id="e5677-653">Aktualizace položky s cílem je podobná přidání.</span><span class="sxs-lookup"><span data-stu-id="e5677-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="e5677-654">Změny `url` přidat jedinečný identifikátor položky a `type` je `PUT`.</span><span class="sxs-lookup"><span data-stu-id="e5677-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="e5677-655">Odstranění položky pro práci</span><span class="sxs-lookup"><span data-stu-id="e5677-655">Delete a to-do item</span></span>

<span data-ttu-id="e5677-656">Odstranění položky pro provedení se provádí nastavením `type` volání AJAX `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="e5677-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="e5677-657">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5677-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="e5677-658">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e5677-658">Additional resources</span></span>

<span data-ttu-id="e5677-659">[Zobrazit nebo stáhnout ukázkový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="e5677-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="e5677-660">Podívejte [se, jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e5677-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e5677-661">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="e5677-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="e5677-662">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="e5677-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

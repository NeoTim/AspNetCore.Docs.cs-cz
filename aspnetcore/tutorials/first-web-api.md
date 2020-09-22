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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 7f684d96ee9fd640abdc750503bed1b2a836a459
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847733"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="e5f21-103">Kurz: Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5f21-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="e5f21-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)a [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="e5f21-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="e5f21-105">V tomto kurzu se naučíte základy vytváření webového rozhraní API pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e5f21-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e5f21-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="e5f21-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e5f21-107">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-107">Create a web API project.</span></span>
> * <span data-ttu-id="e5f21-108">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="e5f21-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e5f21-109">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="e5f21-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="e5f21-110">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e5f21-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="e5f21-111">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="e5f21-111">Call the web API with Postman.</span></span>

<span data-ttu-id="e5f21-112">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="e5f21-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="e5f21-113">Overview</span></span>

<span data-ttu-id="e5f21-114">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e5f21-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e5f21-115">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5f21-115">API</span></span> | <span data-ttu-id="e5f21-116">Popis</span><span class="sxs-lookup"><span data-stu-id="e5f21-116">Description</span></span> | <span data-ttu-id="e5f21-117">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="e5f21-117">Request body</span></span> | <span data-ttu-id="e5f21-118">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="e5f21-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="e5f21-119">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-119">Get all to-do items</span></span> | <span data-ttu-id="e5f21-120">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-120">None</span></span> | <span data-ttu-id="e5f21-121">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="e5f21-122">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="e5f21-122">Get an item by ID</span></span> | <span data-ttu-id="e5f21-123">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-123">None</span></span> | <span data-ttu-id="e5f21-124">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="e5f21-125">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="e5f21-125">Add a new item</span></span> | <span data-ttu-id="e5f21-126">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-126">To-do item</span></span> | <span data-ttu-id="e5f21-127">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="e5f21-128">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e5f21-129">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-129">To-do item</span></span> | <span data-ttu-id="e5f21-130">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-130">None</span></span> |
|<span data-ttu-id="e5f21-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5f21-132">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5f21-133">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-133">None</span></span> | <span data-ttu-id="e5f21-134">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-134">None</span></span>|

<span data-ttu-id="e5f21-135">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-135">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e5f21-141">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e5f21-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-144">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e5f21-145">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="e5f21-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-147">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e5f21-148">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e5f21-149">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e5f21-150">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 5,0** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="e5f21-151">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-151">Select the **API** template and click **Create**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5f21-154">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e5f21-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e5f21-155">Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e5f21-156">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="e5f21-157">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="e5f21-158">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-158">The preceding commands:</span></span>

  * <span data-ttu-id="e5f21-159">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e5f21-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="e5f21-160">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="e5f21-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-161">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5f21-162">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-162">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e5f21-164">V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="e5f21-165">V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Výběr šablony rozhraní API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="e5f21-167">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="e5f21-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="e5f21-168">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-168">Select **Next**.</span></span>

* <span data-ttu-id="e5f21-169">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="e5f21-171">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="e5f21-172">Testování projektu</span><span class="sxs-lookup"><span data-stu-id="e5f21-172">Test the project</span></span>

<span data-ttu-id="e5f21-173">Šablona projektu vytvoří `WeatherForecast` rozhraní API s podporou pro [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="e5f21-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5f21-175">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="e5f21-176">Spustí se Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e5f21-176">Visual Studio launches:</span></span>

* <span data-ttu-id="e5f21-177">IIS Express webový server.</span><span class="sxs-lookup"><span data-stu-id="e5f21-177">The IIS Express web server.</span></span>
* <span data-ttu-id="e5f21-178">Výchozí prohlížeč a přejít na `https://localhost:<port>/https://localhost:5001/swagger/index.html` , kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="e5f21-180">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5f21-181">V prohlížeči přejít na následující adresu URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="e5f21-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-182">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5f21-183">Vyberte **Spustit**  >  **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e5f21-184">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e5f21-185">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="e5f21-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e5f21-186">Připojit `/swagger` k adrese URL (změnit adresu URL na `https://localhost:<port>/swagger` )</span><span class="sxs-lookup"><span data-stu-id="e5f21-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="e5f21-187">Zobrazí se stránka Swagger `/swagger/index.html` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="e5f21-188">Vyberte **Get**  >  **Try**on  >  **Execute**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="e5f21-189">Stránka zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="e5f21-189">The page displays:</span></span>

* <span data-ttu-id="e5f21-190">Příkaz [složeného](https://curl.haxx.se/) pro otestování rozhraní WeatherForecast API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="e5f21-191">Adresa URL pro otestování rozhraní WeatherForecast API</span><span class="sxs-lookup"><span data-stu-id="e5f21-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="e5f21-192">Kód odpovědi, tělo a záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e5f21-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="e5f21-193">Rozevírací seznam s typy médií a ukázkovou hodnotou a schématem.</span><span class="sxs-lookup"><span data-stu-id="e5f21-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="e5f21-194">Swagger se používá ke generování užitečné dokumentace a stránek s nápovědu pro webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="e5f21-195">Tento kurz se zaměřuje na vytvoření webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="e5f21-196">Další informace o Swagger najdete v tématu <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="e5f21-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="e5f21-197">Zkopírujte a za **adresu URL žádosti** v prohlížeči:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="e5f21-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="e5f21-198">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="e5f21-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="e5f21-199">Aktualizace launchUrl</span><span class="sxs-lookup"><span data-stu-id="e5f21-199">Update the launchUrl</span></span>

<span data-ttu-id="e5f21-200">V *Properties\launchSettings.jszapnuto*, aktualizovat `launchUrl` z `"swagger"` na `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="e5f21-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="e5f21-201">Protože Swagger byl odebrán, předchozí kód změní adresu URL, která se spustí do metody GET kontroleru přidané v následujících oddílech.</span><span class="sxs-lookup"><span data-stu-id="e5f21-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="e5f21-202">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="e5f21-202">Add a model class</span></span>

<span data-ttu-id="e5f21-203">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="e5f21-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e5f21-204">Model pro tuto aplikaci je jediná `TodoItem` Třída.</span><span class="sxs-lookup"><span data-stu-id="e5f21-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-206">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e5f21-207">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5f21-208">Pojmenujte složku *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="e5f21-209">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5f21-210">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e5f21-211">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5f21-213">Přidejte složku s názvem *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e5f21-214">Přidejte `TodoItem` třídu do *Models* složky s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-215">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5f21-216">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-216">Right-click the project.</span></span> <span data-ttu-id="e5f21-217">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5f21-218">Pojmenujte složku *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-218">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e5f21-220">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e5f21-221">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e5f21-222">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="e5f21-223">`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e5f21-224">Třídy modelu mohou jít kdekoli v projektu, ale *Models* Složka je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="e5f21-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e5f21-225">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e5f21-225">Add a database context</span></span>

<span data-ttu-id="e5f21-226">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="e5f21-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e5f21-227">Tato třída je vytvořena odvozením z <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> třídy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="e5f21-229">Přidání balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="e5f21-229">Add NuGet packages</span></span>

* <span data-ttu-id="e5f21-230">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="e5f21-231">Vyberte kartu **Procházet** a pak zadejte \* \* Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e5f21-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="e5f21-232">**EntityFrameworkCore. SqlServer** do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="e5f21-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="e5f21-233">Zaškrtněte políčko **zahrnout předběžné verze** , aby byla k dispozici verze 5,0 RC.</span><span class="sxs-lookup"><span data-stu-id="e5f21-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="e5f21-234">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="e5f21-235">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="e5f21-236">Pomocí předchozích pokynů přidejte balíček NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="e5f21-237">![Správce balíčků NuGet](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="e5f21-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="e5f21-238">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="e5f21-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="e5f21-239">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-239">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5f21-240">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-241">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5f21-242">Přidejte `TodoContext` do *Models* složky třídu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-242">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e5f21-243">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e5f21-244">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e5f21-244">Register the database context</span></span>

<span data-ttu-id="e5f21-245">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="e5f21-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e5f21-246">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="e5f21-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="e5f21-247">*Startup.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="e5f21-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="e5f21-248">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-248">The preceding code:</span></span>

* <span data-ttu-id="e5f21-249">Odebere volání Swagger.</span><span class="sxs-lookup"><span data-stu-id="e5f21-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="e5f21-250">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e5f21-251">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="e5f21-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e5f21-252">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="e5f21-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="e5f21-253">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="e5f21-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-255">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e5f21-256">Vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e5f21-257">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-257">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="e5f21-258">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="e5f21-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="e5f21-259">V **třídě modelu**vyberte **TodoItem (TodoApi. Models )** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-259">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="e5f21-260">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models )** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-260">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="e5f21-261">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-262">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e5f21-263">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="e5f21-264">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-264">The preceding commands:</span></span>

* <span data-ttu-id="e5f21-265">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5f21-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="e5f21-266">Nainstaluje modul generování uživatelského rozhraní ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="e5f21-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="e5f21-267">Generování uživatelského rozhraní `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="e5f21-268">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-268">The generated code:</span></span>

* <span data-ttu-id="e5f21-269">Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="e5f21-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="e5f21-270">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e5f21-271">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="e5f21-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e5f21-272">Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e5f21-273">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="e5f21-274">Šablony ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="e5f21-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="e5f21-275">Řadiče se zobrazeními jsou zahrnuta `[action]` v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="e5f21-276">Řadiče API neobsahují `[action]` šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="e5f21-277">Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="e5f21-278">To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="e5f21-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="e5f21-279">Aktualizace metody Create PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="e5f21-280">Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="e5f21-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="e5f21-281">Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="e5f21-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="e5f21-282">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5f21-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e5f21-283">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e5f21-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e5f21-284"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="e5f21-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="e5f21-285">Pokud je úspěšná, vrátí [stavový kód HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) .</span><span class="sxs-lookup"><span data-stu-id="e5f21-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="e5f21-286">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e5f21-287">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="e5f21-288">`Location`Záhlaví Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="e5f21-289">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e5f21-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e5f21-290">Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e5f21-291">`nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="e5f21-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="e5f21-292">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="e5f21-292">Install Postman</span></span>

<span data-ttu-id="e5f21-293">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e5f21-294">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="e5f21-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="e5f21-295">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-295">Start the web app.</span></span>
* <span data-ttu-id="e5f21-296">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="e5f21-296">Start Postman.</span></span>
* <span data-ttu-id="e5f21-297">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="e5f21-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="e5f21-298">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-298">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="e5f21-299">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="e5f21-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="e5f21-300">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="e5f21-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="e5f21-301">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="e5f21-301">Create a new request.</span></span>
* <span data-ttu-id="e5f21-302">Nastavte metodu HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e5f21-303">Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="e5f21-304">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e5f21-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="e5f21-305">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="e5f21-306">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="e5f21-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e5f21-307">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e5f21-308">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="e5f21-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e5f21-309">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-309">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e5f21-311">Otestování identifikátoru URI hlavičky umístění</span><span class="sxs-lookup"><span data-stu-id="e5f21-311">Test the location header URI</span></span>

<span data-ttu-id="e5f21-312">Identifikátor URI hlaviček umístění je možné otestovat v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e5f21-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="e5f21-313">Zkopírujte identifikátor URI hlavičky umístění a vložte ho do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e5f21-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="e5f21-314">Testování v nástroji post:</span><span class="sxs-lookup"><span data-stu-id="e5f21-314">To test in Postman:</span></span>

* <span data-ttu-id="e5f21-315">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e5f21-316">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="e5f21-316">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* <span data-ttu-id="e5f21-318">Nastavte metodu HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="e5f21-319">Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="e5f21-320">Například, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="e5f21-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="e5f21-321">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="e5f21-322">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="e5f21-322">Examine the GET methods</span></span>

<span data-ttu-id="e5f21-323">Jsou implementovány dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="e5f21-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="e5f21-324">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="e5f21-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="e5f21-325">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e5f21-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="e5f21-326">Odpověď podobná následující je vytvořena voláním metody `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="e5f21-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="e5f21-327">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="e5f21-327">Test Get with Postman</span></span>

* <span data-ttu-id="e5f21-328">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="e5f21-328">Create a new request.</span></span>
* <span data-ttu-id="e5f21-329">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="e5f21-330">Nastavte identifikátor URI žádosti na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="e5f21-331">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e5f21-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="e5f21-332">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e5f21-333">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-333">Select **Send**.</span></span>

<span data-ttu-id="e5f21-334">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="e5f21-334">This app uses an in-memory database.</span></span> <span data-ttu-id="e5f21-335">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="e5f21-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="e5f21-336">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="e5f21-337">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="e5f21-337">Routing and URL paths</span></span>

<span data-ttu-id="e5f21-338">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e5f21-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e5f21-339">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="e5f21-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e5f21-340">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="e5f21-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="e5f21-341">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="e5f21-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e5f21-342">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="e5f21-342">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="e5f21-343">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="e5f21-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e5f21-344">Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="e5f21-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e5f21-345">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-345">This sample doesn't use a template.</span></span> <span data-ttu-id="e5f21-346">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e5f21-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e5f21-347">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="e5f21-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e5f21-348">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e5f21-349">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="e5f21-349">Return values</span></span>

<span data-ttu-id="e5f21-350">Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e5f21-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e5f21-351">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="e5f21-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e5f21-352">Kód odpovědi pro tento návratový typ je [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e5f21-353">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="e5f21-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e5f21-354">`ActionResult` návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5f21-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e5f21-355">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="e5f21-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e5f21-356">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda kód chyby [stavu 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="e5f21-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="e5f21-357">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="e5f21-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e5f21-358">`item`Výsledkem vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e5f21-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="e5f21-359">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-359">The PutTodoItem method</span></span>

<span data-ttu-id="e5f21-360">Prohlédněte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e5f21-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="e5f21-361">`PutTodoItem` se podobá `PostTodoItem` , s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="e5f21-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e5f21-362">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e5f21-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e5f21-363">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="e5f21-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e5f21-364">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e5f21-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e5f21-365">Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e5f21-366">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="e5f21-367">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e5f21-368">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e5f21-369">Voláním metody GET zajistěte, aby byla položka v databázi před provedením volání PUT.</span><span class="sxs-lookup"><span data-stu-id="e5f21-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e5f21-370">Aktualizujte položku úkolů s ID = 1 a nastavte její název na `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="e5f21-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e5f21-371">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="e5f21-371">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="e5f21-373">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="e5f21-374">Prohlédněte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e5f21-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e5f21-375">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e5f21-376">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="e5f21-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e5f21-377">Nastavte metodu na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e5f21-378">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="e5f21-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e5f21-379">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-379">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="e5f21-380">Zabránit navýšení příspěvků</span><span class="sxs-lookup"><span data-stu-id="e5f21-380">Prevent over-posting</span></span>

<span data-ttu-id="e5f21-381">V současné době ukázková aplikace zveřejňuje celý `TodoItem` objekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="e5f21-382">Produkční aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="e5f21-383">Je to několik důvodů na pozadí a zabezpečení je hlavní.</span><span class="sxs-lookup"><span data-stu-id="e5f21-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="e5f21-384">Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e5f21-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="e5f21-385">**DTO** se používá v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="e5f21-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="e5f21-386">DTO se dá použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="e5f21-386">A DTO may be used to:</span></span>

* <span data-ttu-id="e5f21-387">Zabránit přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="e5f21-387">Prevent over-posting.</span></span>
* <span data-ttu-id="e5f21-388">Skrytí vlastností, které klienti nemají zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="e5f21-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="e5f21-389">Vynechejte některé vlastnosti, aby se snížila velikost datové části.</span><span class="sxs-lookup"><span data-stu-id="e5f21-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="e5f21-390">Ploché grafy objektů, které obsahují vnořené objekty.</span><span class="sxs-lookup"><span data-stu-id="e5f21-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="e5f21-391">Ploché grafy objektů můžou být pro klienty pohodlnější.</span><span class="sxs-lookup"><span data-stu-id="e5f21-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="e5f21-392">Chcete-li předvést DTO přístup, aktualizujte `TodoItem` třídu tak, aby obsahovala tajné pole:</span><span class="sxs-lookup"><span data-stu-id="e5f21-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="e5f21-393">Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.</span><span class="sxs-lookup"><span data-stu-id="e5f21-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="e5f21-394">Ověřte, že můžete publikovat a získat pole tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="e5f21-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="e5f21-395">Vytvoření modelu DTO:</span><span class="sxs-lookup"><span data-stu-id="e5f21-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="e5f21-396">Aktualizujte, `TodoItemsController` aby se použil `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="e5f21-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="e5f21-397">Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e5f21-398">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e5f21-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="e5f21-399">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="e5f21-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e5f21-400">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="e5f21-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e5f21-401">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-401">Create a web API project.</span></span>
> * <span data-ttu-id="e5f21-402">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="e5f21-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e5f21-403">Generování uživatelského rozhraní pro kontroler pomocí metod CRUD</span><span class="sxs-lookup"><span data-stu-id="e5f21-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="e5f21-404">Nakonfigurujte směrování, cesty URL a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e5f21-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="e5f21-405">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="e5f21-405">Call the web API with Postman.</span></span>

<span data-ttu-id="e5f21-406">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="e5f21-407">Přehled</span><span class="sxs-lookup"><span data-stu-id="e5f21-407">Overview</span></span>

<span data-ttu-id="e5f21-408">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e5f21-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e5f21-409">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5f21-409">API</span></span> | <span data-ttu-id="e5f21-410">Popis</span><span class="sxs-lookup"><span data-stu-id="e5f21-410">Description</span></span> | <span data-ttu-id="e5f21-411">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="e5f21-411">Request body</span></span> | <span data-ttu-id="e5f21-412">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="e5f21-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="e5f21-413">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-413">Get all to-do items</span></span> | <span data-ttu-id="e5f21-414">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-414">None</span></span> | <span data-ttu-id="e5f21-415">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="e5f21-416">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="e5f21-416">Get an item by ID</span></span> | <span data-ttu-id="e5f21-417">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-417">None</span></span> | <span data-ttu-id="e5f21-418">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="e5f21-419">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="e5f21-419">Add a new item</span></span> | <span data-ttu-id="e5f21-420">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-420">To-do item</span></span> | <span data-ttu-id="e5f21-421">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="e5f21-422">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e5f21-423">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-423">To-do item</span></span> | <span data-ttu-id="e5f21-424">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-424">None</span></span> |
|<span data-ttu-id="e5f21-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5f21-426">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5f21-427">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-427">None</span></span> | <span data-ttu-id="e5f21-428">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-428">None</span></span>|

<span data-ttu-id="e5f21-429">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-429">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e5f21-435">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e5f21-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-438">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e5f21-439">Vytvoření webového projektu</span><span class="sxs-lookup"><span data-stu-id="e5f21-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-441">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e5f21-442">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e5f21-443">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e5f21-444">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="e5f21-445">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-445">Select the **API** template and click **Create**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5f21-448">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e5f21-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e5f21-449">Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e5f21-450">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="e5f21-451">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="e5f21-452">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-452">The preceding commands:</span></span>

  * <span data-ttu-id="e5f21-453">Vytvoří nový projekt webového rozhraní API a otevře ho v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e5f21-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="e5f21-454">Přidá balíčky NuGet, které jsou nutné v další části.</span><span class="sxs-lookup"><span data-stu-id="e5f21-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-455">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5f21-456">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-456">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e5f21-458">V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="e5f21-459">V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Výběr šablony rozhraní API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="e5f21-461">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="e5f21-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="e5f21-462">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-462">Select **Next**.</span></span>

* <span data-ttu-id="e5f21-463">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="e5f21-465">Ve složce projektu otevřete terminál příkazu a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="e5f21-466">Testování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5f21-466">Test the API</span></span>

<span data-ttu-id="e5f21-467">Šablona projektu vytvoří `WeatherForecast` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="e5f21-468">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5f21-470">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5f21-471">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/WeatherForecast` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e5f21-472">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e5f21-473">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5f21-475">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5f21-476">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-477">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5f21-478">Vyberte **Spustit**  >  **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e5f21-479">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e5f21-480">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="e5f21-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e5f21-481">Připojit `/WeatherForecast` k adrese URL (změnit adresu URL na `https://localhost:<port>/WeatherForecast` )</span><span class="sxs-lookup"><span data-stu-id="e5f21-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="e5f21-482">Vrátí se JSON podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="e5f21-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="e5f21-483">Přidat třídu modelu</span><span class="sxs-lookup"><span data-stu-id="e5f21-483">Add a model class</span></span>

<span data-ttu-id="e5f21-484">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="e5f21-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e5f21-485">Model pro tuto aplikaci je jediná `TodoItem` Třída.</span><span class="sxs-lookup"><span data-stu-id="e5f21-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-487">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-487">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e5f21-488">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5f21-489">Pojmenujte složku *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-489">Name the folder *Models*.</span></span>

* <span data-ttu-id="e5f21-490">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-490">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5f21-491">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e5f21-492">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5f21-494">Přidejte složku s názvem *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-494">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e5f21-495">Přidejte `TodoItem` třídu do *Models* složky s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-495">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-496">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5f21-497">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-497">Right-click the project.</span></span> <span data-ttu-id="e5f21-498">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5f21-499">Pojmenujte složku *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-499">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e5f21-501">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-501">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e5f21-502">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-502">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e5f21-503">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="e5f21-504">`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e5f21-505">Třídy modelu mohou jít kdekoli v projektu, ale *Models* Složka je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="e5f21-505">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e5f21-506">Přidání kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e5f21-506">Add a database context</span></span>

<span data-ttu-id="e5f21-507">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="e5f21-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e5f21-508">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="e5f21-510">Přidání balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="e5f21-510">Add NuGet packages</span></span>

* <span data-ttu-id="e5f21-511">V nabídce **nástroje** vyberte **správce balíčků NuGet > spravovat balíčky NuGet pro řešení**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="e5f21-512">Vyberte kartu **Procházet** a potom do vyhledávacího pole zadejte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="e5f21-513">V levém podokně vyberte **Microsoft. EntityFrameworkCore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="e5f21-514">Zaškrtněte políčko **projekt** v pravém podokně a pak vyberte **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="e5f21-515">Pomocí předchozích pokynů přidejte balíček NuGet **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![Správce balíčků NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="e5f21-517">Přidání kontextu databáze TodoContext</span><span class="sxs-lookup"><span data-stu-id="e5f21-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="e5f21-518">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-518">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5f21-519">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-520">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5f21-521">Přidejte `TodoContext` do *Models* složky třídu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-521">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e5f21-522">Zadejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e5f21-523">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="e5f21-523">Register the database context</span></span>

<span data-ttu-id="e5f21-524">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="e5f21-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e5f21-525">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="e5f21-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="e5f21-526">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="e5f21-527">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-527">The preceding code:</span></span>

* <span data-ttu-id="e5f21-528">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e5f21-529">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="e5f21-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e5f21-530">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="e5f21-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="e5f21-531">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="e5f21-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-533">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e5f21-534">Vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e5f21-535">Vyberte možnost **kontroler API s akcemi, pomocí Entity Framework**a pak vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-535">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="e5f21-536">V dialogovém okně **Přidání kontroleru rozhraní API pomocí akcí Entity Framework** :</span><span class="sxs-lookup"><span data-stu-id="e5f21-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="e5f21-537">V **třídě modelu**vyberte **TodoItem (TodoApi. Models )** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-537">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="e5f21-538">Ve **třídě Context data**vyberte **TodoContext (TodoApi. Models )** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-538">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="e5f21-539">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-540">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e5f21-541">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="e5f21-542">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-542">The preceding commands:</span></span>

* <span data-ttu-id="e5f21-543">Přidejte balíčky NuGet vyžadované pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e5f21-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="e5f21-544">Nainstaluje modul generování uživatelského rozhraní ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="e5f21-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="e5f21-545">Generování uživatelského rozhraní `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="e5f21-546">Generovaný kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-546">The generated code:</span></span>

* <span data-ttu-id="e5f21-547">Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="e5f21-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="e5f21-548">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e5f21-549">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="e5f21-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e5f21-550">Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e5f21-551">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="e5f21-552">Šablony ASP.NET Core pro:</span><span class="sxs-lookup"><span data-stu-id="e5f21-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="e5f21-553">Řadiče se zobrazeními jsou zahrnuta `[action]` v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="e5f21-554">Řadiče API neobsahují `[action]` šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="e5f21-555">Pokud `[action]` token není v šabloně směrování, název [Akce](xref:mvc/controllers/routing#action) je vyloučený z trasy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="e5f21-556">To znamená, že název přidružené metody akce se ve shodě trasy nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="e5f21-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="e5f21-557">Projděte si metodu PostTodoItem Create.</span><span class="sxs-lookup"><span data-stu-id="e5f21-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="e5f21-558">Nahraďte příkaz return v `PostTodoItem` operátoru k použití operátoru [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="e5f21-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="e5f21-559">Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="e5f21-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="e5f21-560">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5f21-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e5f21-561">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e5f21-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e5f21-562"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="e5f21-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="e5f21-563">Pokud je úspěšná, vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="e5f21-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="e5f21-564">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e5f21-565">Přidá hlavičku [umístění](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="e5f21-566">`Location`Záhlaví Určuje [identifikátor URI](https://developer.mozilla.org/docs/Glossary/URI) nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="e5f21-567">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e5f21-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e5f21-568">Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e5f21-569">`nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="e5f21-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="e5f21-570">Nainstalovat post</span><span class="sxs-lookup"><span data-stu-id="e5f21-570">Install Postman</span></span>

<span data-ttu-id="e5f21-571">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e5f21-572">Nainstalovat [post](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="e5f21-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="e5f21-573">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-573">Start the web app.</span></span>
* <span data-ttu-id="e5f21-574">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="e5f21-574">Start Postman.</span></span>
* <span data-ttu-id="e5f21-575">Zakázat **ověřování certifikátu SSL**</span><span class="sxs-lookup"><span data-stu-id="e5f21-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="e5f21-576">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-576">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="e5f21-577">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="e5f21-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="e5f21-578">PostTodoItem testu s použitím post</span><span class="sxs-lookup"><span data-stu-id="e5f21-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="e5f21-579">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="e5f21-579">Create a new request.</span></span>
* <span data-ttu-id="e5f21-580">Nastavte metodu HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e5f21-581">Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="e5f21-582">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e5f21-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="e5f21-583">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="e5f21-584">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="e5f21-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e5f21-585">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e5f21-586">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="e5f21-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e5f21-587">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-587">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="e5f21-589">Otestování identifikátoru URI hlaviček umístění pomocí metody post</span><span class="sxs-lookup"><span data-stu-id="e5f21-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="e5f21-590">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e5f21-591">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="e5f21-591">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/3/create.png)

* <span data-ttu-id="e5f21-593">Nastavte metodu HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="e5f21-594">Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="e5f21-595">Například, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="e5f21-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="e5f21-596">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="e5f21-597">Projděte si metody GET.</span><span class="sxs-lookup"><span data-stu-id="e5f21-597">Examine the GET methods</span></span>

<span data-ttu-id="e5f21-598">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="e5f21-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="e5f21-599">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče nebo po odeslání.</span><span class="sxs-lookup"><span data-stu-id="e5f21-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="e5f21-600">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e5f21-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="e5f21-601">Odpověď podobná následující je vytvořena voláním metody `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="e5f21-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="e5f21-602">Test Get s použitím metody post</span><span class="sxs-lookup"><span data-stu-id="e5f21-602">Test Get with Postman</span></span>

* <span data-ttu-id="e5f21-603">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="e5f21-603">Create a new request.</span></span>
* <span data-ttu-id="e5f21-604">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="e5f21-605">Nastavte identifikátor URI žádosti na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="e5f21-606">Například, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e5f21-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="e5f21-607">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e5f21-608">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-608">Select **Send**.</span></span>

<span data-ttu-id="e5f21-609">Tato aplikace používá databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="e5f21-609">This app uses an in-memory database.</span></span> <span data-ttu-id="e5f21-610">Pokud se aplikace zastaví a spustí, předchozí požadavek GET nebude vracet žádná data.</span><span class="sxs-lookup"><span data-stu-id="e5f21-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="e5f21-611">Pokud se nevrátí žádná data, [odešlete](#post) data do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="e5f21-612">Směrování a cesty URL</span><span class="sxs-lookup"><span data-stu-id="e5f21-612">Routing and URL paths</span></span>

<span data-ttu-id="e5f21-613">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e5f21-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e5f21-614">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="e5f21-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e5f21-615">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="e5f21-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="e5f21-616">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="e5f21-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e5f21-617">V této ukázce je název třídy kontroleru **TodoItems**Controller, takže název kontroleru je "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="e5f21-617">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="e5f21-618">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="e5f21-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e5f21-619">Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="e5f21-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e5f21-620">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-620">This sample doesn't use a template.</span></span> <span data-ttu-id="e5f21-621">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e5f21-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e5f21-622">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="e5f21-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e5f21-623">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e5f21-624">Vrácené hodnoty</span><span class="sxs-lookup"><span data-stu-id="e5f21-624">Return values</span></span> 

<span data-ttu-id="e5f21-625">Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e5f21-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e5f21-626">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="e5f21-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e5f21-627">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e5f21-628">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="e5f21-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e5f21-629">`ActionResult` návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5f21-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e5f21-630">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="e5f21-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e5f21-631">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> kód chyby 404.</span><span class="sxs-lookup"><span data-stu-id="e5f21-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="e5f21-632">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="e5f21-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e5f21-633">`item`Výsledkem vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e5f21-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="e5f21-634">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-634">The PutTodoItem method</span></span>

<span data-ttu-id="e5f21-635">Prohlédněte si metodu `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e5f21-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="e5f21-636">`PutTodoItem` se podobá `PostTodoItem` , s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="e5f21-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e5f21-637">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e5f21-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e5f21-638">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="e5f21-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e5f21-639">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e5f21-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e5f21-640">Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e5f21-641">Test metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="e5f21-642">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e5f21-643">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e5f21-644">Voláním metody GET zajistěte, aby byla položka v databázi před provedením volání PUT.</span><span class="sxs-lookup"><span data-stu-id="e5f21-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e5f21-645">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="e5f21-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e5f21-646">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="e5f21-646">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="e5f21-648">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="e5f21-649">Prohlédněte si metodu `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e5f21-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e5f21-650">Test metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e5f21-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e5f21-651">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="e5f21-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e5f21-652">Nastavte metodu na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e5f21-653">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="e5f21-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e5f21-654">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-654">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="e5f21-655">Zabránit navýšení příspěvků</span><span class="sxs-lookup"><span data-stu-id="e5f21-655">Prevent over-posting</span></span>

<span data-ttu-id="e5f21-656">V současné době ukázková aplikace zveřejňuje celý `TodoItem` objekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="e5f21-657">Produkční aplikace obvykle omezují zadaná data a vracejí je pomocí podmnožiny modelu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="e5f21-658">Je to několik důvodů na pozadí a zabezpečení je hlavní.</span><span class="sxs-lookup"><span data-stu-id="e5f21-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="e5f21-659">Podmnožina modelu je obvykle označována jako objekt Přenos dat (DTO), vstupní model nebo model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e5f21-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="e5f21-660">**DTO** se používá v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="e5f21-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="e5f21-661">DTO se dá použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="e5f21-661">A DTO may be used to:</span></span>

* <span data-ttu-id="e5f21-662">Zabránit přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="e5f21-662">Prevent over-posting.</span></span>
* <span data-ttu-id="e5f21-663">Skrytí vlastností, které klienti nemají zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="e5f21-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="e5f21-664">Vynechejte některé vlastnosti, aby se snížila velikost datové části.</span><span class="sxs-lookup"><span data-stu-id="e5f21-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="e5f21-665">Ploché grafy objektů, které obsahují vnořené objekty.</span><span class="sxs-lookup"><span data-stu-id="e5f21-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="e5f21-666">Ploché grafy objektů můžou být pro klienty pohodlnější.</span><span class="sxs-lookup"><span data-stu-id="e5f21-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="e5f21-667">Chcete-li předvést DTO přístup, aktualizujte `TodoItem` třídu tak, aby obsahovala tajné pole:</span><span class="sxs-lookup"><span data-stu-id="e5f21-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="e5f21-668">Pole tajného klíče musí být z této aplikace skryté, ale aplikace pro správu může tuto možnost vystavit.</span><span class="sxs-lookup"><span data-stu-id="e5f21-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="e5f21-669">Ověřte, že můžete publikovat a získat pole tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="e5f21-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="e5f21-670">Vytvoření modelu DTO:</span><span class="sxs-lookup"><span data-stu-id="e5f21-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="e5f21-671">Aktualizujte, `TodoItemsController` aby se použil `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="e5f21-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="e5f21-672">Ověřte, že nemůžete publikovat nebo získat pole tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e5f21-673">Volání webového rozhraní API pomocí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e5f21-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="e5f21-674">Viz [kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="e5f21-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5f21-675">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="e5f21-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e5f21-676">Vytvořte projekt webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-676">Create a web API project.</span></span>
> * <span data-ttu-id="e5f21-677">Přidejte třídu modelu a kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="e5f21-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e5f21-678">Přidejte kontroler.</span><span class="sxs-lookup"><span data-stu-id="e5f21-678">Add a controller.</span></span>
> * <span data-ttu-id="e5f21-679">Přidejte metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="e5f21-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="e5f21-680">Nakonfigurujte směrování a cesty URL.</span><span class="sxs-lookup"><span data-stu-id="e5f21-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="e5f21-681">Zadejte návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e5f21-681">Specify return values.</span></span>
> * <span data-ttu-id="e5f21-682">Zavolejte webové rozhraní API pomocí post.</span><span class="sxs-lookup"><span data-stu-id="e5f21-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="e5f21-683">Zavolejte webové rozhraní API pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="e5f21-684">Na konci máte webové rozhraní API, které může spravovat položky "k tomu" uložené v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="e5f21-685">Přehled 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-685">Overview 2.1</span></span>

<span data-ttu-id="e5f21-686">V tomto kurzu se vytvoří následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e5f21-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e5f21-687">Rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e5f21-687">API</span></span> | <span data-ttu-id="e5f21-688">Popis</span><span class="sxs-lookup"><span data-stu-id="e5f21-688">Description</span></span> | <span data-ttu-id="e5f21-689">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="e5f21-689">Request body</span></span> | <span data-ttu-id="e5f21-690">Text odpovědi</span><span class="sxs-lookup"><span data-stu-id="e5f21-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="e5f21-691">ZÍSKAT/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e5f21-691">GET /api/TodoItems</span></span> | <span data-ttu-id="e5f21-692">Získat všechny položky úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-692">Get all to-do items</span></span> | <span data-ttu-id="e5f21-693">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-693">None</span></span> | <span data-ttu-id="e5f21-694">Pole položek úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-694">Array of to-do items</span></span>|
|<span data-ttu-id="e5f21-695">ZÍSKAT/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e5f21-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="e5f21-696">Získat položku podle ID</span><span class="sxs-lookup"><span data-stu-id="e5f21-696">Get an item by ID</span></span> | <span data-ttu-id="e5f21-697">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-697">None</span></span> | <span data-ttu-id="e5f21-698">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-698">To-do item</span></span>|
|<span data-ttu-id="e5f21-699">PŘÍSPĚVEK/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e5f21-699">POST /api/TodoItems</span></span> | <span data-ttu-id="e5f21-700">Přidat novou položku</span><span class="sxs-lookup"><span data-stu-id="e5f21-700">Add a new item</span></span> | <span data-ttu-id="e5f21-701">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-701">To-do item</span></span> | <span data-ttu-id="e5f21-702">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-702">To-do item</span></span> |
|<span data-ttu-id="e5f21-703">Vložit/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e5f21-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="e5f21-704">Aktualizovat existující položku &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e5f21-705">Položka úkolů</span><span class="sxs-lookup"><span data-stu-id="e5f21-705">To-do item</span></span> | <span data-ttu-id="e5f21-706">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-706">None</span></span> |
|<span data-ttu-id="e5f21-707">Odstranit/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5f21-708">Odstranění položky &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="e5f21-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e5f21-709">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-709">None</span></span> | <span data-ttu-id="e5f21-710">Žádné</span><span class="sxs-lookup"><span data-stu-id="e5f21-710">None</span></span>|

<span data-ttu-id="e5f21-711">V následujícím diagramu vidíte návrh aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-711">The following diagram shows the design of the app.</span></span>

![Klient je reprezentován polem vlevo.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="e5f21-717">Požadavky 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-720">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="e5f21-721">Vytvoření webového projektu 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-723">V nabídce **soubor** vyberte možnost **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e5f21-724">Vyberte šablonu **ASP.NET Core webové aplikace** a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e5f21-725">Pojmenujte projekt *TodoApi* a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e5f21-726">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="e5f21-727">Vyberte šablonu **rozhraní API** a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="e5f21-728">**Nevybírejte možnost** **Povolit podporu Docker**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-728">**Don't** select **Enable Docker Support**.</span></span>

![Dialogové okno VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5f21-731">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e5f21-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e5f21-732">Změňte adresáře ( `cd` ) na složku, která bude obsahovat složku projektu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e5f21-733">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e5f21-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="e5f21-734">Tyto příkazy vytvoří nový projekt webového rozhraní API a otevřou novou instanci Visual Studio Code v nové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="e5f21-735">Pokud se zobrazí dialogové okno s dotazem, zda chcete přidat požadované prostředky do projektu, vyberte možnost **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-736">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5f21-737">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-737">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e5f21-739">V Visual Studio pro Mac starší než verze 8,6 Vyberte rozhraní **.NET Core**  >  **App**  >  **API**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="e5f21-740">V části verze 8,6 nebo novější vyberte **webové a konzolové**  >  **App**  >  **rozhraní API**aplikace  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="e5f21-741">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte nejnovější **verzi rozhraní .NET**Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="e5f21-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="e5f21-742">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-742">Select **Next**.</span></span>

* <span data-ttu-id="e5f21-743">Jako **název projektu** zadejte *TodoApi* a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogové okno Konfigurace](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="e5f21-745">Test rozhraní API 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-745">Test the API 2.1</span></span>

<span data-ttu-id="e5f21-746">Šablona projektu vytvoří `values` rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-746">The project template creates a `values` API.</span></span> <span data-ttu-id="e5f21-747">Voláním `Get` metody z prohlížeče otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5f21-749">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5f21-750">Visual Studio spustí prohlížeč a přejde na `https://localhost:<port>/api/values` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e5f21-751">Pokud se zobrazí dialogové okno s dotazem, jestli byste měli důvěřovat certifikátu IIS Express, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e5f21-752">V dialogovém okně **Upozornění zabezpečení** , které se zobrazí jako další, vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5f21-754">Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e5f21-755">V prohlížeči přejdete na následující adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-756">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5f21-757">Vyberte **Spustit**  >  **ladění** a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e5f21-758">Visual Studio pro Mac spustí prohlížeč a přejde na `https://localhost:<port>` místo, kde `<port>` je náhodně zvolené číslo portu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e5f21-759">Vrátí se chyba HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="e5f21-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e5f21-760">Připojit `/api/values` k adrese URL (změnit adresu URL na `https://localhost:<port>/api/values` )</span><span class="sxs-lookup"><span data-stu-id="e5f21-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="e5f21-761">Vrátí se následující JSON:</span><span class="sxs-lookup"><span data-stu-id="e5f21-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="e5f21-762">Přidat třídu modelu 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-762">Add a model class 2.1</span></span>

<span data-ttu-id="e5f21-763">*Model* je sada tříd, které reprezentují data, která aplikace spravuje.</span><span class="sxs-lookup"><span data-stu-id="e5f21-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e5f21-764">Model pro tuto aplikaci je jediná `TodoItem` Třída.</span><span class="sxs-lookup"><span data-stu-id="e5f21-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-766">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-766">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e5f21-767">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5f21-768">Pojmenujte složku *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-768">Name the folder *Models*.</span></span>

* <span data-ttu-id="e5f21-769">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-769">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5f21-770">Pojmenujte třídu *TodoItem* a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e5f21-771">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5f21-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5f21-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5f21-773">Přidejte složku s názvem *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-773">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e5f21-774">Přidejte `TodoItem` třídu do *Models* složky s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-774">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-775">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5f21-776">Klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-776">Right-click the project.</span></span> <span data-ttu-id="e5f21-777">Vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5f21-778">Pojmenujte složku *Models* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-778">Name the folder *Models*.</span></span>

  ![Nová složka](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e5f21-780">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat** > **nový soubor** > **Obecná** > **prázdná třída**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-780">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e5f21-781">Pojmenujte třídu *TodoItem*a potom klikněte na **Nový**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-781">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e5f21-782">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="e5f21-783">`Id`Vlastnost funguje jako jedinečný klíč v relační databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e5f21-784">Třídy modelu mohou jít kdekoli v projektu, ale *Models* Složka je používána konvencí.</span><span class="sxs-lookup"><span data-stu-id="e5f21-784">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="e5f21-785">Přidání kontextu databáze 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-785">Add a database context 2.1</span></span>

<span data-ttu-id="e5f21-786">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro datový model.</span><span class="sxs-lookup"><span data-stu-id="e5f21-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e5f21-787">Tato třída je vytvořena odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="e5f21-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-789">Klikněte pravým tlačítkem na *Models* složku a vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-789">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e5f21-790">Pojmenujte třídu *TodoContext* a klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-791">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5f21-792">Přidejte `TodoContext` do *Models* složky třídu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-792">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e5f21-793">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="e5f21-794">Registrace kontextu databáze 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-794">Register the database context 2.1</span></span>

<span data-ttu-id="e5f21-795">V ASP.NET Core musí být služby, jako je například kontext databáze, registrovány s kontejnerem [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="e5f21-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e5f21-796">Kontejner poskytuje službu pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="e5f21-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="e5f21-797">Aktualizujte *Startup.cs* o následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="e5f21-798">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-798">The preceding code:</span></span>

* <span data-ttu-id="e5f21-799">Odebere nepoužívané `using` deklarace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e5f21-800">Přidá kontext databáze do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="e5f21-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e5f21-801">Určuje, že kontext databáze bude používat databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="e5f21-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="e5f21-802">Přidat kontroler 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-804">Klikněte pravým tlačítkem na složku *řadiče* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e5f21-805">Vyberte možnost **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="e5f21-806">V dialogovém okně **Přidat novou položku** vyberte šablonu **Třída kontroleru rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="e5f21-807">Pojmenujte třídu *TodoController*a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-807">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogové okno Přidat novou položku s řadičem ve vyhledávacím poli a vybraným kontrolérem webového rozhraní API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-809">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5f21-810">Ve složce *Controllers* vytvořte třídu s názvem `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="e5f21-811">Kód šablony nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="e5f21-812">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e5f21-812">The preceding code:</span></span>

* <span data-ttu-id="e5f21-813">Definuje třídu kontroleru rozhraní API bez metod.</span><span class="sxs-lookup"><span data-stu-id="e5f21-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="e5f21-814">Označí třídu [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="e5f21-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="e5f21-815">Tento atribut označuje, že kontroler reaguje na požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e5f21-816">Informace o konkrétním chování, které atribut povoluje, naleznete v tématu <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="e5f21-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e5f21-817">Pomocí DI vloží kontext databáze ( `TodoContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e5f21-818">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="e5f21-819">Přidá položku s názvem `Item1` do databáze, pokud je databáze prázdná.</span><span class="sxs-lookup"><span data-stu-id="e5f21-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="e5f21-820">Tento kód je v konstruktoru, takže se spustí pokaždé, když se vytvoří nový požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5f21-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="e5f21-821">Pokud odstraníte všechny položky, konstruktor se znovu vytvoří `Item1` při příštím volání metody rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="e5f21-822">Takže může vypadat, že odstranění nefungovalo, pokud skutečně fungovalo.</span><span class="sxs-lookup"><span data-stu-id="e5f21-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="e5f21-823">Přidat metody Get 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-823">Add Get methods 2.1</span></span>

<span data-ttu-id="e5f21-824">Chcete-li poskytnout rozhraní API, které načítá položky úkolů, přidejte do třídy následující metody `TodoController` :</span><span class="sxs-lookup"><span data-stu-id="e5f21-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="e5f21-825">Tyto metody implementují dva koncové body GET:</span><span class="sxs-lookup"><span data-stu-id="e5f21-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="e5f21-826">Pokud je pořád spuštěná, zastavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-826">Stop the app if it's still running.</span></span> <span data-ttu-id="e5f21-827">Pak ji znovu spusťte, aby obsahovala nejnovější změny.</span><span class="sxs-lookup"><span data-stu-id="e5f21-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="e5f21-828">Otestujte aplikaci voláním dvou koncových bodů z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e5f21-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="e5f21-829">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e5f21-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="e5f21-830">Následující odpověď protokolu HTTP je vytvořena voláním metody `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="e5f21-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="e5f21-831">Cesty směrování a adresy URL 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="e5f21-832">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Atribut označuje metodu, která reaguje na požadavek HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e5f21-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e5f21-833">Cesta URL pro každou metodu je konstruována takto:</span><span class="sxs-lookup"><span data-stu-id="e5f21-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e5f21-834">Začněte s řetězcem šablony v `Route` atributu kontroleru:</span><span class="sxs-lookup"><span data-stu-id="e5f21-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="e5f21-835">Nahraďte `[controller]` názvem kontroleru, který je podle konvence názvem třídy kontroleru minus přípona Controller.</span><span class="sxs-lookup"><span data-stu-id="e5f21-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e5f21-836">V této ukázce je názvem třídy kontroleru kontroler **TODO**, takže název kontroleru je "todo".</span><span class="sxs-lookup"><span data-stu-id="e5f21-836">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="e5f21-837">[Směrování](xref:mvc/controllers/routing) ASP.NET Core rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="e5f21-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e5f21-838">Pokud `[HttpGet]` má atribut směrovací šablonu (například `[HttpGet("products")]` ), přidejte ji k cestě.</span><span class="sxs-lookup"><span data-stu-id="e5f21-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e5f21-839">Tato ukázka nepoužívá šablonu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-839">This sample doesn't use a template.</span></span> <span data-ttu-id="e5f21-840">Další informace najdete v tématu [Směrování atributů s atributy http [příkaz]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e5f21-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e5f21-841">V následující `GetTodoItem` metodě `"{id}"` je proměnná zástupného symbolu pro jedinečný identifikátor položky k provedení.</span><span class="sxs-lookup"><span data-stu-id="e5f21-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e5f21-842">Při `GetTodoItem` vyvolání je hodnota `"{id}"` v adrese URL poskytnuta metodě v `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="e5f21-843">Návratové hodnoty 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-843">Return values 2.1</span></span>

<span data-ttu-id="e5f21-844">Návratový typ `GetTodoItems` `GetTodoItem` metod a je [ActionResult \<T> typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e5f21-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e5f21-845">ASP.NET Core automaticky serializovat objekt do formátu [JSON](https://www.json.org/) a zapíše JSON do textu zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="e5f21-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e5f21-846">Kód odpovědi pro tento návratový typ je 200, za předpokladu, že neexistují žádné neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e5f21-847">Neošetřené výjimky jsou přeloženy na 5xx chyby.</span><span class="sxs-lookup"><span data-stu-id="e5f21-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e5f21-848">`ActionResult` návratové typy mohou představovat široké spektrum stavových kódů HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5f21-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e5f21-849">Například `GetTodoItem` může vracet dvě různé stavové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="e5f21-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e5f21-850">Pokud žádná položka neodpovídá požadovanému ID, vrátí metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> kód chyby 404.</span><span class="sxs-lookup"><span data-stu-id="e5f21-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="e5f21-851">V opačném případě metoda vrátí 200 text odpovědi JSON.</span><span class="sxs-lookup"><span data-stu-id="e5f21-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e5f21-852">`item`Výsledkem vrácení výsledků odpovědi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e5f21-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="e5f21-853">Otestujte metodu GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="e5f21-854">V tomto kurzu se používá post k testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e5f21-855">Nainstalujte [post](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="e5f21-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="e5f21-856">Spusťte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e5f21-856">Start the web app.</span></span>
* <span data-ttu-id="e5f21-857">Spusťte post.</span><span class="sxs-lookup"><span data-stu-id="e5f21-857">Start Postman.</span></span>
* <span data-ttu-id="e5f21-858">Zakáže **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5f21-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5f21-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5f21-860">V **File** > **Nastavení** souboru (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-860">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e5f21-861">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e5f21-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e5f21-862">V **Postman**možnosti  >  **předvolba** post (karta**Obecné** ) zakažte **ověřování certifikátu SSL**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-862">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="e5f21-863">Případně vyberte klíče a vyberte **Nastavení**a pak zakažte ověřování certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="e5f21-863">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="e5f21-864">Po otestování kontroleru znovu povolte ověření certifikátu SSL.</span><span class="sxs-lookup"><span data-stu-id="e5f21-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="e5f21-865">Vytvoří novou žádost.</span><span class="sxs-lookup"><span data-stu-id="e5f21-865">Create a new request.</span></span>
  * <span data-ttu-id="e5f21-866">Nastavte metodu HTTP na **Get**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="e5f21-867">Nastavte identifikátor URI žádosti na `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="e5f21-868">Například, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="e5f21-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="e5f21-869">Nastavte v příspěvku **dva zobrazení podokna** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e5f21-870">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-870">Select **Send**.</span></span>

![Odeslání pomocí žádosti Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="e5f21-872">Přidejte metodu create 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-872">Add a Create method 2.1</span></span>

<span data-ttu-id="e5f21-873">Přidejte následující `PostTodoItem` metodu do *Controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="e5f21-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="e5f21-874">Předchozí kód je metoda HTTP POST, jak je označena [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="e5f21-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="e5f21-875">Metoda získá hodnotu položky k seřízení z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="e5f21-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e5f21-876">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="e5f21-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="e5f21-877">Vrátí stavový kód HTTP 201, pokud bylo úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e5f21-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="e5f21-878">HTTP 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="e5f21-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e5f21-879">Přidá `Location` hlavičku k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="e5f21-880">`Location`Záhlaví Určuje identifikátor URI nově vytvořené položky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="e5f21-881">Další informace najdete v tématu [10.2.2 201 vytvořeno](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e5f21-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e5f21-882">Odkazuje na `GetTodoItem` akci vytvoření `Location` identifikátoru URI hlavičky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e5f21-883">`nameof`Klíčové slovo jazyka C# se používá k zamezení hardwarového kódování názvu akce ve `CreatedAtAction` volání.</span><span class="sxs-lookup"><span data-stu-id="e5f21-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="e5f21-884">Otestujte metodu PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="e5f21-885">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="e5f21-885">Build the project.</span></span>
* <span data-ttu-id="e5f21-886">V části post nastavte metodu HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e5f21-887">Nastavte identifikátor URI na `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="e5f21-888">Například, `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e5f21-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="e5f21-889">Vyberte kartu **tělo** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="e5f21-890">Vyberte **nezpracovaný** přepínač.</span><span class="sxs-lookup"><span data-stu-id="e5f21-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e5f21-891">Nastavte typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e5f21-892">Do textu žádosti zadejte JSON pro položku úkolů:</span><span class="sxs-lookup"><span data-stu-id="e5f21-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e5f21-893">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-893">Select **Send**.</span></span>

  ![Poslat pomocí žádosti o vytvoření](first-web-api/_static/create.png)

  <span data-ttu-id="e5f21-895">Pokud se zobrazí chyba metoda 405 není povolená, je pravděpodobné, že po přidání metody nebude projekt zkompilován `PostTodoItem` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="e5f21-896">Otestování identifikátoru URI hlavičky umístění 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="e5f21-897">V podokně **odpověď** vyberte kartu **hlavičky** .</span><span class="sxs-lookup"><span data-stu-id="e5f21-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e5f21-898">Zkopírujte hodnotu hlavičky **umístění** :</span><span class="sxs-lookup"><span data-stu-id="e5f21-898">Copy the **Location** header value:</span></span>

  ![Karta hlavičky v konzole pro odesílání](first-web-api/_static/pmc2.png)

* <span data-ttu-id="e5f21-900">Nastavte metodu, která má být ZÍSKÁNa.</span><span class="sxs-lookup"><span data-stu-id="e5f21-900">Set the method to GET.</span></span>
<span data-ttu-id="e5f21-901">\* Nastavte identifikátor URI na  `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-901">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="e5f21-902">Například  `https://localhost:5001/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-902"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="e5f21-903">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="e5f21-904">Přidat metodu PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="e5f21-905">Přidejte následující `PutTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="e5f21-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="e5f21-906">`PutTodoItem` se podobá `PostTodoItem` , s tím rozdílem, že používá Put http.</span><span class="sxs-lookup"><span data-stu-id="e5f21-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e5f21-907">Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e5f21-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e5f21-908">V souladu se specifikací HTTP vyžaduje požadavek PUT klientovi, aby odesílal celou aktualizovanou entitu, a ne jenom změny.</span><span class="sxs-lookup"><span data-stu-id="e5f21-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e5f21-909">K podpoře částečných aktualizací použijte [opravu http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e5f21-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e5f21-910">Pokud se zobrazí chyba při volání `PutTodoItem` , `GET` zajistěte, aby byla položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="e5f21-911">Otestujte metodu PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="e5f21-912">Tato ukázka používá databázi v paměti, která musí být inicializována při každém spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e5f21-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e5f21-913">Před provedením volání PUT musí existovat položka v databázi.</span><span class="sxs-lookup"><span data-stu-id="e5f21-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e5f21-914">Voláním metody GET zajistěte, aby byla položka v databázi před provedením volání PUT.</span><span class="sxs-lookup"><span data-stu-id="e5f21-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e5f21-915">Aktualizujte položku úkolů s ID = 1 a nastavte její název na "rybí ryby":</span><span class="sxs-lookup"><span data-stu-id="e5f21-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e5f21-916">Na následujícím obrázku je znázorněná aktualizace po odeslání:</span><span class="sxs-lookup"><span data-stu-id="e5f21-916">The following image shows the Postman update:</span></span>

![Konzola pro publikování zobrazující 204 (bez obsahu)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="e5f21-918">Přidat metodu DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="e5f21-919">Přidejte následující `DeleteTodoItem` metodu:</span><span class="sxs-lookup"><span data-stu-id="e5f21-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="e5f21-920">`DeleteTodoItem`Odpověď je [204 (žádný obsah)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e5f21-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="e5f21-921">Otestujte metodu DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="e5f21-922">Odstranění položky úkolů pomocí metody post:</span><span class="sxs-lookup"><span data-stu-id="e5f21-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e5f21-923">Nastavte metodu na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e5f21-924">Nastavte identifikátor URI objektu, který má být odstraněn (například `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="e5f21-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="e5f21-925">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="e5f21-925">Select **Send**.</span></span>

<span data-ttu-id="e5f21-926">Ukázková aplikace umožňuje odstranit všechny položky.</span><span class="sxs-lookup"><span data-stu-id="e5f21-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="e5f21-927">Když je však poslední položka odstraněna, vytvoří se nový konstruktor třídy modelu při příštím volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="e5f21-928">Volání webového rozhraní API pomocí JavaScriptu 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="e5f21-929">V této části se přidá stránka HTML, která pomocí JavaScriptu volá webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="e5f21-930">jQuery inicializuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="e5f21-930">jQuery initiates the request.</span></span> <span data-ttu-id="e5f21-931">JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="e5f21-932">Nakonfigurujte aplikaci tak, aby [sloužila pro statické soubory](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) , a [Povolte výchozí mapování souborů](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="e5f21-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="e5f21-933">Vytvořte složku *wwwroot* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="e5f21-934">Do adresáře *wwwroot* přidejte soubor HTML s názvem *index.html* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="e5f21-935">Nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="e5f21-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="e5f21-936">Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *site.js* .</span><span class="sxs-lookup"><span data-stu-id="e5f21-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="e5f21-937">Jeho obsah nahraďte následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="e5f21-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="e5f21-938">Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:</span><span class="sxs-lookup"><span data-stu-id="e5f21-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="e5f21-939">Otevřete *Properties\launchSettings.js*.</span><span class="sxs-lookup"><span data-stu-id="e5f21-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="e5f21-940">Odeberte `launchUrl` vlastnost, která vynutí otevření aplikace na *index.html* &mdash; výchozího souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="e5f21-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="e5f21-941">Tato ukázka volá všechny metody CRUD webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="e5f21-942">Následují vysvětlení volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e5f21-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="e5f21-943">Získat seznam položek úkolů 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="e5f21-944">jQuery pošle požadavek HTTP GET do webového rozhraní API, které vrátí JSON představující pole položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="e5f21-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="e5f21-945">`success`Funkce zpětného volání je vyvolána, pokud je požadavek úspěšný.</span><span class="sxs-lookup"><span data-stu-id="e5f21-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="e5f21-946">Ve zpětném volání je DOM aktualizován pomocí informací o tom.</span><span class="sxs-lookup"><span data-stu-id="e5f21-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="e5f21-947">Přidat položku úkolů 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="e5f21-948">jQuery pošle požadavek HTTP POST s položkou k žádosti v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="e5f21-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="e5f21-949">`accepts`Možnosti a `contentType` jsou nastaveny na `application/json` zadání typu média, který se přijímá a odesílá.</span><span class="sxs-lookup"><span data-stu-id="e5f21-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="e5f21-950">Položka úkolů se převede na JSON pomocí [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="e5f21-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="e5f21-951">Když rozhraní API vrátí stavový kód úspěšné, `getData` funkce se vyvolá, aby se aktualizovala tabulka HTML.</span><span class="sxs-lookup"><span data-stu-id="e5f21-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="e5f21-952">Aktualizace položky úkolů 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="e5f21-953">Aktualizace položky úkolů je podobná přidání.</span><span class="sxs-lookup"><span data-stu-id="e5f21-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="e5f21-954">`url`Změny pro přidání jedinečného identifikátoru položky a `type` jsou `PUT` .</span><span class="sxs-lookup"><span data-stu-id="e5f21-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="e5f21-955">Odstranění položky úkolů 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="e5f21-956">Odstranění položky úkolů je provedeno nastavením `type` při volání AJAX na `DELETE` a zadáním jedinečného identifikátoru položky v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="e5f21-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="e5f21-957">Přidání podpory ověřování do webového rozhraní API 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="e5f21-958">Další zdroje 2,1</span><span class="sxs-lookup"><span data-stu-id="e5f21-958">Additional resources 2.1</span></span>

<span data-ttu-id="e5f21-959">[Zobrazit nebo stáhnout vzorový kód pro tento kurz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="e5f21-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="e5f21-960">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e5f21-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e5f21-961">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="e5f21-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="e5f21-962">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="e5f21-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

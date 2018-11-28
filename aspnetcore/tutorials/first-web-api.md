---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core a Visual Studio
author: rick-anderson
description: Vytvoření webového rozhraní API pomocí ASP.NET Core MVC a sady Visual Studio ve Windows
ms.author: riande
ms.custom: mvc
ms.date: 05/17/2018
uid: tutorials/first-web-api
ms.openlocfilehash: eb23d5376742e04712f714263815582fddc5d18e
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450694"
---
# <a name="create-a-web-api-with-aspnet-core-and-visual-studio"></a><span data-ttu-id="d6047-103">Vytvoření webového rozhraní API pomocí ASP.NET Core a Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6047-103">Create a Web API with ASP.NET Core and Visual Studio</span></span>

<span data-ttu-id="d6047-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="d6047-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="d6047-105">V tomto kurzu sestavení webového rozhraní API pro správu seznam "úkolů".</span><span class="sxs-lookup"><span data-stu-id="d6047-105">This tutorial builds a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="d6047-106">Uživatelské rozhraní (UI) není vytvořena.</span><span class="sxs-lookup"><span data-stu-id="d6047-106">A user interface (UI) isn't created.</span></span>

<span data-ttu-id="d6047-107">Existují tři verze tohoto kurzu:</span><span class="sxs-lookup"><span data-stu-id="d6047-107">There are three versions of this tutorial:</span></span>

* <span data-ttu-id="d6047-108">Windows: Webové rozhraní API pomocí sady Visual Studio na Windows (kurz, najdete v článku [videoverzi](https://www.youtube.com/watch?v=TTkhEyGBfAk))</span><span class="sxs-lookup"><span data-stu-id="d6047-108">Windows: Web API with Visual Studio on Windows (This tutorial, see the [video version](https://www.youtube.com/watch?v=TTkhEyGBfAk))</span></span>
* <span data-ttu-id="d6047-109">macOS: [webového rozhraní API pomocí sady Visual Studio pro Mac](xref:tutorials/first-web-api-mac)</span><span class="sxs-lookup"><span data-stu-id="d6047-109">macOS: [Web API with Visual Studio for Mac](xref:tutorials/first-web-api-mac)</span></span>
* <span data-ttu-id="d6047-110">macOS, Linux, Windows: [webového rozhraní API pomocí Visual Studio Code](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="d6047-110">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

> [!NOTE]
> <span data-ttu-id="d6047-111">Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6047-111">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="d6047-112">Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span><span class="sxs-lookup"><span data-stu-id="d6047-112">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).</span></span>

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a><span data-ttu-id="d6047-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d6047-113">Prerequisites</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-windows.md)]

## <a name="create-the-project"></a><span data-ttu-id="d6047-114">Vytvoření projektu</span><span class="sxs-lookup"><span data-stu-id="d6047-114">Create the project</span></span>

<span data-ttu-id="d6047-115">Postupujte podle těchto kroků v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d6047-115">Follow these steps in Visual Studio:</span></span>

* <span data-ttu-id="d6047-116">Z **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="d6047-116">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d6047-117">Vyberte **webové aplikace ASP.NET Core** šablony.</span><span class="sxs-lookup"><span data-stu-id="d6047-117">Select the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="d6047-118">Pojmenujte projekt *TodoApi* a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="d6047-118">Name the project *TodoApi* and click **OK**.</span></span>
* <span data-ttu-id="d6047-119">V **nové webové aplikace ASP.NET Core – TodoApi** dialogového okna, vyberte verzi technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6047-119">In the **New ASP.NET Core Web Application - TodoApi** dialog, choose the ASP.NET Core version.</span></span> <span data-ttu-id="d6047-120">Vyberte **API** šablonu a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="d6047-120">Select the **API** template and click **OK**.</span></span> <span data-ttu-id="d6047-121">Proveďte **není** vyberte **povolit podporu Dockeru**.</span><span class="sxs-lookup"><span data-stu-id="d6047-121">Do **not** select **Enable Docker Support**.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="d6047-122">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="d6047-122">Launch the app</span></span>

<span data-ttu-id="d6047-123">V sadě Visual Studio stiskněte CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6047-123">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="d6047-124">Visual Studio spustí prohlížeč a přejde na `http://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="d6047-124">Visual Studio launches a browser and navigates to `http://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d6047-125">Chrome, Microsoft Edge a Firefox se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d6047-125">Chrome, Microsoft Edge, and Firefox display the following output:</span></span>

```json
["value1","value2"]
```

<span data-ttu-id="d6047-126">Pokud používáte Internet Explorer, budete vyzváni k uložení *values.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="d6047-126">If using Internet Explorer, you'll be prompted to save a *values.json* file.</span></span>

### <a name="add-a-model-class"></a><span data-ttu-id="d6047-127">Přidejte třídu modelu</span><span class="sxs-lookup"><span data-stu-id="d6047-127">Add a model class</span></span>

<span data-ttu-id="d6047-128">Model je objekt reprezentující data v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6047-128">A model is an object representing the data in the app.</span></span> <span data-ttu-id="d6047-129">V tomto případě jediný model je položky úkolu.</span><span class="sxs-lookup"><span data-stu-id="d6047-129">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="d6047-130">V Průzkumníku řešení klikněte pravým tlačítkem na projekt.</span><span class="sxs-lookup"><span data-stu-id="d6047-130">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="d6047-131">Vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d6047-131">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d6047-132">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="d6047-132">Name the folder *Models*.</span></span>

> [!NOTE]
> <span data-ttu-id="d6047-133">Třídy modelu můžete kamkoliv v projektu.</span><span class="sxs-lookup"><span data-stu-id="d6047-133">The model classes can go anywhere in the project.</span></span> <span data-ttu-id="d6047-134">*Modely* složky používají konvence pro třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="d6047-134">The *Models* folder is used by convention for model classes.</span></span>

<span data-ttu-id="d6047-135">V Průzkumníku řešení klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="d6047-135">In Solution Explorer, right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d6047-136">Název třídy *TodoItem* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="d6047-136">Name the class *TodoItem* and click **Add**.</span></span>

<span data-ttu-id="d6047-137">Aktualizace `TodoItem` třídy následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d6047-137">Update the `TodoItem` class with the following code:</span></span>

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="d6047-138">Vytvoří databázi `Id` při `TodoItem` se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="d6047-138">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="d6047-139">Vytvořte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="d6047-139">Create the database context</span></span>

<span data-ttu-id="d6047-140">*Kontext databáze* je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="d6047-140">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="d6047-141">Tato třída se vytvoří odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="d6047-141">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="d6047-142">V Průzkumníku řešení klikněte pravým tlačítkem myši *modely* a pak zvolte položku **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="d6047-142">In Solution Explorer, right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d6047-143">Název třídy *TodoContext* a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="d6047-143">Name the class *TodoContext* and click **Add**.</span></span>

<span data-ttu-id="d6047-144">Třída nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d6047-144">Replace the class with the following code:</span></span>

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a><span data-ttu-id="d6047-145">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="d6047-145">Add a controller</span></span>

<span data-ttu-id="d6047-146">V Průzkumníku řešení klikněte pravým tlačítkem myši *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="d6047-146">In Solution Explorer, right-click the *Controllers* folder.</span></span> <span data-ttu-id="d6047-147">Vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="d6047-147">Select **Add** > **New Item**.</span></span> <span data-ttu-id="d6047-148">V **přidat novou položku** dialogového okna, vyberte **třída Kontroleru rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="d6047-148">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span> <span data-ttu-id="d6047-149">Název třídy *TodoController*a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="d6047-149">Name the class *TodoController*, and click **Add**.</span></span>

![Přidání nové položky dialogové okno s kontrolerem v vyhledávací pole a webové rozhraní API kontroleru vybrané](first-web-api/_static/new_controller.png)

<span data-ttu-id="d6047-151">Třída nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d6047-151">Replace the class with the following code:</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a><span data-ttu-id="d6047-152">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="d6047-152">Launch the app</span></span>

<span data-ttu-id="d6047-153">V sadě Visual Studio stiskněte CTRL + F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6047-153">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="d6047-154">Visual Studio spustí prohlížeč a přejde na `http://localhost:<port>/api/values`, kde `<port>` je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="d6047-154">Visual Studio launches a browser and navigates to `http://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d6047-155">Přejděte `Todo` ovladač na `http://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="d6047-155">Navigate to the `Todo` controller at `http://localhost:<port>/api/todo`.</span></span>

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[jQuery](../includes/webApi/add-jquery.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

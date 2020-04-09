---
title: 'Výuka: Začínáme se stránkami Razor pages v ASP.NET Core'
author: rick-anderson
description: Tato série výukových programů ukazuje, jak používat Razor Pages v ASP.NET Core. Naučte se, jak vytvořit model, generovat kód pro stránky Razor, použít Entity Framework Core a SQL Server pro přístup k datům, přidat funkci vyhledávání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658541"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="49b5f-104">Výuka: Začínáme se stránkami Razor pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b5f-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="49b5f-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="49b5f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="49b5f-106">Toto je první kurz série, který učí základy budování webové aplikace ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="49b5f-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="49b5f-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="49b5f-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="49b5f-108">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="49b5f-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49b5f-109">Vytvořte webovou aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="49b5f-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="49b5f-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b5f-110">Run the app.</span></span>
> * <span data-ttu-id="49b5f-111">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-111">Examine the project files.</span></span>

<span data-ttu-id="49b5f-112">Na konci tohoto kurzu budete mít funkční webovou aplikaci Razor Pages, na které budete stavět v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="49b5f-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="49b5f-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="49b5f-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49b5f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49b5f-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49b5f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49b5f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49b5f-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="49b5f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="49b5f-118">Vytvoření webové aplikace Stránky Razor</span><span class="sxs-lookup"><span data-stu-id="49b5f-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49b5f-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49b5f-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49b5f-120">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="49b5f-121">Vytvořte novou ASP.NET základní webovou aplikaci a vyberte **možnost Další**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="49b5f-122">![nová ASP.NET základní webová aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="49b5f-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="49b5f-123">Název projektu **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="49b5f-124">Je důležité pojmenovat projekt *RazorPagesMovie,* aby se jmenné prostory shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="49b5f-125">![nová ASP.NET základní webová aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="49b5f-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="49b5f-126">V rozevíracím souboru **Webová aplikace**vyberte **ASP.NET jádra 3.1** a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nová ASP.NET základní webová aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="49b5f-128">Je vytvořen následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="49b5f-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="49b5f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49b5f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49b5f-131">Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="49b5f-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="49b5f-132">Změna adresáře`cd`( ), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="49b5f-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="49b5f-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="49b5f-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="49b5f-134">Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie.*</span><span class="sxs-lookup"><span data-stu-id="49b5f-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="49b5f-135">Příkaz `code` otevře složku *RazorPagesMovie* v aktuální instanci kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="49b5f-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="49b5f-136">Poté, co ikona plamene OmniSharp na stavovém řádku zezelenaje, dialogové okno požádá **požadované datové zdroje k sestavení a ladění v nástroji RazorPagesMovie chybí. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="49b5f-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="49b5f-137">Vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-137">Select **Yes**.</span></span>

  <span data-ttu-id="49b5f-138">Do kořenového adresáře projektu je přidán adresář *.vscode* obsahující soubory *launch.json* a *tasks.json.*</span><span class="sxs-lookup"><span data-stu-id="49b5f-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49b5f-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="49b5f-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49b5f-140">Vyberte **možnost Nové řešení souboru** > **New Solution**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-140">Select **File** > **New Solution**.</span></span>

![macOS Nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="49b5f-142">Vyberte **.NET Core** > **App** > **Web Application** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Dialogové okno Nový projekt](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="49b5f-144">V **dialogovém okně Konfigurovat novou webovou aplikaci** nastavte **cílovou architekturu** na **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![výběr macOS .NET Core 3.1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="49b5f-146">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![názevproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="49b5f-148">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="49b5f-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="49b5f-149">Prohlédněte si soubory projektu</span><span class="sxs-lookup"><span data-stu-id="49b5f-149">Examine the project files</span></span>

<span data-ttu-id="49b5f-150">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="49b5f-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="49b5f-151">Složka Stránky</span><span class="sxs-lookup"><span data-stu-id="49b5f-151">Pages folder</span></span>

<span data-ttu-id="49b5f-152">Obsahuje razor stránky a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="49b5f-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="49b5f-153">Každá stránka Razor Page je dvojicí souborů:</span><span class="sxs-lookup"><span data-stu-id="49b5f-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="49b5f-154">Soubor *.cshtml,* který obsahuje značky HTML s kódem Jazyka C# pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="49b5f-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="49b5f-155">Soubor *.cshtml.cs,* který obsahuje kód Jazyka C#, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="49b5f-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="49b5f-156">Podpůrné soubory mají názvy, které začínají podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="49b5f-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="49b5f-157">Například soubor *_Layout.cshtml* konfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="49b5f-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="49b5f-158">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="49b5f-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="49b5f-159">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="49b5f-160">wwwkořenová složka</span><span class="sxs-lookup"><span data-stu-id="49b5f-160">wwwroot folder</span></span>

<span data-ttu-id="49b5f-161">Obsahuje statické soubory, jako jsou soubory HTML, soubory JavaScript a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="49b5f-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="49b5f-162">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="49b5f-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="49b5f-163">appSettings.json</span></span>

<span data-ttu-id="49b5f-164">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="49b5f-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="49b5f-165">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="49b5f-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="49b5f-166">Program.cs</span></span>

<span data-ttu-id="49b5f-167">Obsahuje vstupní bod programu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-167">Contains the entry point for the program.</span></span> <span data-ttu-id="49b5f-168">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="49b5f-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="49b5f-169">Startup.cs</span></span>

<span data-ttu-id="49b5f-170">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="49b5f-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="49b5f-171">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="49b5f-172">Další kroky</span><span class="sxs-lookup"><span data-stu-id="49b5f-172">Next steps</span></span>

<span data-ttu-id="49b5f-173">Převést na další výukový program v sérii:</span><span class="sxs-lookup"><span data-stu-id="49b5f-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="49b5f-174">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="49b5f-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49b5f-175">Toto je první kurz série.</span><span class="sxs-lookup"><span data-stu-id="49b5f-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="49b5f-176">[Série](xref:tutorials/razor-pages/index) učí základy budování webové aplikace ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="49b5f-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="49b5f-177">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="49b5f-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="49b5f-178">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="49b5f-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49b5f-179">Vytvořte webovou aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="49b5f-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="49b5f-180">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b5f-180">Run the app.</span></span>
> * <span data-ttu-id="49b5f-181">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-181">Examine the project files.</span></span>

<span data-ttu-id="49b5f-182">Na konci tohoto kurzu budete mít funkční webovou aplikaci Razor Pages, na které budete stavět v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="49b5f-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="49b5f-184">Požadavky</span><span class="sxs-lookup"><span data-stu-id="49b5f-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49b5f-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49b5f-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="49b5f-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49b5f-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49b5f-187">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="49b5f-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="49b5f-188">Vytvoření webové aplikace Stránky Razor</span><span class="sxs-lookup"><span data-stu-id="49b5f-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49b5f-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49b5f-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49b5f-190">V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="49b5f-191">Vytvořte novou ASP.NET základní webovou aplikaci a vyberte **možnost Další**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![nová ASP.NET základní webová aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="49b5f-193">Název projektu **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="49b5f-194">Je důležité pojmenovat projekt *RazorPagesMovie,* aby se jmenné prostory shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![nová ASP.NET základní webová aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="49b5f-196">V rozevíracím souboru **Webová aplikace**vyberte **ASP.NET Core 2.2** a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![nová ASP.NET základní webová aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="49b5f-198">Je vytvořen následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="49b5f-198">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="49b5f-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49b5f-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="49b5f-201">Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="49b5f-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="49b5f-202">Změna adresáře`cd`( ), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="49b5f-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="49b5f-203">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="49b5f-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="49b5f-204">Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie.*</span><span class="sxs-lookup"><span data-stu-id="49b5f-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="49b5f-205">Příkaz `code` otevře složku *RazorPagesMovie* v aktuální instanci kódu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="49b5f-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="49b5f-206">Poté, co ikona plamene OmniSharp na stavovém řádku zezelenaje, dialogové okno požádá **požadované datové zdroje k sestavení a ladění v nástroji RazorPagesMovie chybí. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="49b5f-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="49b5f-207">Vyberte **ano**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-207">Select **Yes**.</span></span>

  <span data-ttu-id="49b5f-208">Do kořenového adresáře projektu je přidán adresář *.vscode* obsahující soubory *launch.json* a *tasks.json.*</span><span class="sxs-lookup"><span data-stu-id="49b5f-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49b5f-209">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="49b5f-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="49b5f-210">Vyberte **možnost Nové řešení souboru** > **New Solution**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-210">Select **File** > **New Solution**.</span></span>

![macOS Nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="49b5f-212">Vyberte **.NET Core** > **App** > **Web Application** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Dialogové okno Nový projekt](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="49b5f-214">V dialogovém **okně Konfigurovat nové webové rozhraní ASP.NET Core** nastavte **cílovou architekturu** na **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![výběr macOS .NET Core 3.0](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="49b5f-216">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="49b5f-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![názevproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="49b5f-218">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="49b5f-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49b5f-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49b5f-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="49b5f-220">Stisknutím kláves Ctrl+F5 spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="49b5f-221">Visual Studio spustí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="49b5f-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="49b5f-222">Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="49b5f-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="49b5f-223">To proto, `localhost` že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="49b5f-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="49b5f-224">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="49b5f-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="49b5f-225">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="49b5f-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="49b5f-226">Na domovské stránce aplikace vyberte **Přijmout,** chcete-li souhlasit se sledováním.</span><span class="sxs-lookup"><span data-stu-id="49b5f-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="49b5f-227">Tato aplikace nesleduje osobní údaje, ale šablona projektu obsahuje funkci souhlasu v případě, že ji potřebujete dodržovat obecné nařízení Evropské unie [o ochraně osobních údajů (GDPR).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="49b5f-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="49b5f-229">Následující obrázek znázorňuje aplikaci poté, co udělit souhlas se sledováním:</span><span class="sxs-lookup"><span data-stu-id="49b5f-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="49b5f-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49b5f-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="49b5f-232">Stisknutím **klávesCtrl-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="49b5f-233">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `http://localhost:5001`a přejde na .</span><span class="sxs-lookup"><span data-stu-id="49b5f-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="49b5f-234">Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="49b5f-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="49b5f-235">To proto, `localhost` že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="49b5f-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="49b5f-236">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="49b5f-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="49b5f-237">Na domovské stránce aplikace vyberte **Přijmout,** chcete-li souhlasit se sledováním.</span><span class="sxs-lookup"><span data-stu-id="49b5f-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="49b5f-238">Tato aplikace nesleduje osobní údaje, ale šablona projektu obsahuje funkci souhlasu v případě, že ji potřebujete dodržovat obecné nařízení Evropské unie [o ochraně osobních údajů (GDPR).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="49b5f-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="49b5f-240">Následující obrázek znázorňuje aplikaci poté, co udělit souhlas se sledováním:</span><span class="sxs-lookup"><span data-stu-id="49b5f-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49b5f-242">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="49b5f-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="49b5f-243">Stisknutím **tlačítka Cmd-Opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="49b5f-244">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `http://localhost:5001`a přejde na .</span><span class="sxs-lookup"><span data-stu-id="49b5f-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="49b5f-245">Na domovské stránce aplikace vyberte **Přijmout,** chcete-li souhlasit se sledováním.</span><span class="sxs-lookup"><span data-stu-id="49b5f-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="49b5f-246">Tato aplikace nesleduje osobní údaje, ale šablona projektu obsahuje funkci souhlasu v případě, že ji potřebujete dodržovat obecné nařízení Evropské unie [o ochraně osobních údajů (GDPR).](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="49b5f-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="49b5f-248">Následující obrázek znázorňuje aplikaci poté, co udělit souhlas se sledováním:</span><span class="sxs-lookup"><span data-stu-id="49b5f-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Domovská stránka nebo stránka Rejstříku](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="49b5f-250">Prohlédněte si soubory projektu</span><span class="sxs-lookup"><span data-stu-id="49b5f-250">Examine the project files</span></span>

<span data-ttu-id="49b5f-251">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="49b5f-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="49b5f-252">Složka Stránky</span><span class="sxs-lookup"><span data-stu-id="49b5f-252">Pages folder</span></span>

<span data-ttu-id="49b5f-253">Obsahuje razor stránky a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="49b5f-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="49b5f-254">Každá stránka Razor Page je dvojicí souborů:</span><span class="sxs-lookup"><span data-stu-id="49b5f-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="49b5f-255">Soubor *.cshtml,* který obsahuje značky HTML s kódem Jazyka C# pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="49b5f-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="49b5f-256">Soubor *.cshtml.cs,* který obsahuje kód Jazyka C#, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="49b5f-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="49b5f-257">Podpůrné soubory mají názvy, které začínají podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="49b5f-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="49b5f-258">Například soubor *_Layout.cshtml* konfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="49b5f-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="49b5f-259">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="49b5f-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="49b5f-260">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="49b5f-261">wwwkořenová složka</span><span class="sxs-lookup"><span data-stu-id="49b5f-261">wwwroot folder</span></span>

<span data-ttu-id="49b5f-262">Obsahuje statické soubory, jako jsou soubory HTML, soubory JavaScript a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="49b5f-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="49b5f-263">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="49b5f-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="49b5f-264">appSettings.json</span></span>

<span data-ttu-id="49b5f-265">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="49b5f-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="49b5f-266">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="49b5f-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="49b5f-267">Program.cs</span></span>

<span data-ttu-id="49b5f-268">Obsahuje vstupní bod programu.</span><span class="sxs-lookup"><span data-stu-id="49b5f-268">Contains the entry point for the program.</span></span> <span data-ttu-id="49b5f-269">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="49b5f-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="49b5f-270">Startup.cs</span></span>

<span data-ttu-id="49b5f-271">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas pro soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="49b5f-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="49b5f-272">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="49b5f-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49b5f-273">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="49b5f-273">Additional resources</span></span>

* [<span data-ttu-id="49b5f-274">Youtube verze tohoto výukového programu</span><span class="sxs-lookup"><span data-stu-id="49b5f-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="49b5f-275">Další kroky</span><span class="sxs-lookup"><span data-stu-id="49b5f-275">Next steps</span></span>

<span data-ttu-id="49b5f-276">Převést na další výukový program v sérii:</span><span class="sxs-lookup"><span data-stu-id="49b5f-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="49b5f-277">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="49b5f-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

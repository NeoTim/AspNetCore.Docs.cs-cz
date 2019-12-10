---
title: 'Kurz: Začínáme s Razor Pages v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor Pages v ASP.NET Core. Naučte se, jak vytvořit model, generovat kód pro stránky Razor, použít Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b651437b698d01310f90c5f14832616c1896e6c0
ms.sourcegitcommit: 4e3edff24ba6e43a103fee1b126c9826241bb37b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74959096"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="02599-104">Kurz: Začínáme s Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02599-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="02599-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02599-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="02599-106">Toto je první kurz série, který se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="02599-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="02599-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="02599-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="02599-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="02599-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="02599-109">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02599-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="02599-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02599-110">Run the app.</span></span>
> * <span data-ttu-id="02599-111">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="02599-111">Examine the project files.</span></span>

<span data-ttu-id="02599-112">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="02599-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="02599-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="02599-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02599-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02599-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02599-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02599-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02599-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="02599-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="02599-118">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="02599-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02599-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02599-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02599-120">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="02599-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="02599-121">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="02599-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="02599-122">![nové ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="02599-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="02599-123">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="02599-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="02599-124">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="02599-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="02599-125">![nové ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="02599-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="02599-126">V rozevíracím seznamu vyberte položku **ASP.NET Core 3,1** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="02599-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="02599-128">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="02599-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02599-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02599-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="02599-131">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="02599-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="02599-132">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="02599-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="02599-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="02599-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="02599-134">Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="02599-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="02599-135">Příkaz `code` otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="02599-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="02599-136">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="02599-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="02599-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="02599-137">Select **Yes**.</span></span>

  <span data-ttu-id="02599-138">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="02599-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02599-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="02599-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02599-140">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="02599-140">Select **File** > **New Solution**.</span></span>

![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="02599-142">Vyberte > **Další** **Webová aplikace > webové aplikace** **.NET Core** > **App** .</span><span class="sxs-lookup"><span data-stu-id="02599-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="02599-144">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílové** rozhraní **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="02599-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,0 – výběr](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="02599-146">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="02599-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="02599-148">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="02599-148">Open the project</span></span>

<span data-ttu-id="02599-149">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="02599-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="02599-150">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="02599-150">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="02599-151">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="02599-151">Examine the project files</span></span>

<span data-ttu-id="02599-152">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="02599-152">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="02599-153">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="02599-153">Pages folder</span></span>

<span data-ttu-id="02599-154">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="02599-154">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="02599-155">Každá stránka Razor Page je dvojicí souborů:</span><span class="sxs-lookup"><span data-stu-id="02599-155">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="02599-156">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="02599-156">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="02599-157">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="02599-157">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="02599-158">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="02599-158">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="02599-159">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="02599-159">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="02599-160">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="02599-160">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="02599-161">Další informace najdete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="02599-161">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="02599-162">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="02599-162">wwwroot folder</span></span>

<span data-ttu-id="02599-163">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="02599-163">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="02599-164">Další informace najdete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="02599-164">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="02599-165">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="02599-165">appSettings.json</span></span>

<span data-ttu-id="02599-166">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="02599-166">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="02599-167">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="02599-167">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="02599-168">Program.cs</span><span class="sxs-lookup"><span data-stu-id="02599-168">Program.cs</span></span>

<span data-ttu-id="02599-169">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="02599-169">Contains the entry point for the program.</span></span> <span data-ttu-id="02599-170">Další informace najdete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="02599-170">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="02599-171">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="02599-171">Startup.cs</span></span>

<span data-ttu-id="02599-172">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="02599-172">Contains code that configures app behavior.</span></span> <span data-ttu-id="02599-173">Další informace najdete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="02599-173">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="02599-174">Další kroky</span><span class="sxs-lookup"><span data-stu-id="02599-174">Next steps</span></span>

<span data-ttu-id="02599-175">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="02599-175">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="02599-176">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="02599-176">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02599-177">Toto je první kurz řady.</span><span class="sxs-lookup"><span data-stu-id="02599-177">This is the first tutorial of a series.</span></span> <span data-ttu-id="02599-178">[Série](xref:tutorials/razor-pages/index) se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="02599-178">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="02599-179">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="02599-179">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="02599-180">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="02599-180">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="02599-181">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02599-181">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="02599-182">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02599-182">Run the app.</span></span>
> * <span data-ttu-id="02599-183">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="02599-183">Examine the project files.</span></span>

<span data-ttu-id="02599-184">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="02599-184">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="02599-186">Požadavky</span><span class="sxs-lookup"><span data-stu-id="02599-186">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02599-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02599-187">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02599-188">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02599-188">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02599-189">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="02599-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="02599-190">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="02599-190">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02599-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02599-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02599-192">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="02599-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="02599-193">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="02599-193">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="02599-195">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="02599-195">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="02599-196">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="02599-196">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="02599-198">V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="02599-198">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="02599-200">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="02599-200">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02599-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02599-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="02599-203">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="02599-203">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="02599-204">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="02599-204">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="02599-205">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="02599-205">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="02599-206">Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="02599-206">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="02599-207">Příkaz `code` otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="02599-207">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="02599-208">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="02599-208">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="02599-209">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="02599-209">Select **Yes**.</span></span>

  <span data-ttu-id="02599-210">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="02599-210">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02599-211">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="02599-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="02599-212">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="02599-212">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="02599-213">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="02599-213">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="02599-214">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="02599-214">Open the project</span></span>

<span data-ttu-id="02599-215">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="02599-215">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="02599-216">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="02599-216">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02599-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02599-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02599-218">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="02599-218">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="02599-219">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02599-219">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="02599-220">V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="02599-220">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02599-221">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="02599-221">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="02599-222">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="02599-222">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="02599-223">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="02599-223">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="02599-224">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="02599-224">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="02599-225">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="02599-225">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="02599-227">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="02599-227">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02599-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02599-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="02599-230">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="02599-230">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="02599-231">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="02599-231">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="02599-232">V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="02599-232">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02599-233">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="02599-233">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="02599-234">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="02599-234">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="02599-235">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="02599-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="02599-236">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="02599-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="02599-238">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="02599-238">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02599-240">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="02599-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="02599-241">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="02599-241">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="02599-242">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="02599-242">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="02599-243">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="02599-243">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="02599-244">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="02599-244">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="02599-246">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="02599-246">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="02599-248">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="02599-248">Examine the project files</span></span>

<span data-ttu-id="02599-249">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="02599-249">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="02599-250">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="02599-250">Pages folder</span></span>

<span data-ttu-id="02599-251">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="02599-251">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="02599-252">Každá stránka Razor Page je dvojicí souborů:</span><span class="sxs-lookup"><span data-stu-id="02599-252">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="02599-253">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="02599-253">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="02599-254">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="02599-254">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="02599-255">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="02599-255">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="02599-256">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="02599-256">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="02599-257">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="02599-257">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="02599-258">Další informace najdete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="02599-258">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="02599-259">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="02599-259">wwwroot folder</span></span>

<span data-ttu-id="02599-260">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="02599-260">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="02599-261">Další informace najdete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="02599-261">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="02599-262">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="02599-262">appSettings.json</span></span>

<span data-ttu-id="02599-263">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="02599-263">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="02599-264">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="02599-264">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="02599-265">Program.cs</span><span class="sxs-lookup"><span data-stu-id="02599-265">Program.cs</span></span>

<span data-ttu-id="02599-266">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="02599-266">Contains the entry point for the program.</span></span> <span data-ttu-id="02599-267">Další informace najdete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="02599-267">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="02599-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="02599-268">Startup.cs</span></span>

<span data-ttu-id="02599-269">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="02599-269">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="02599-270">Další informace najdete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="02599-270">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02599-271">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="02599-271">Additional resources</span></span>

* [<span data-ttu-id="02599-272">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="02599-272">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="02599-273">Další kroky</span><span class="sxs-lookup"><span data-stu-id="02599-273">Next steps</span></span>

<span data-ttu-id="02599-274">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="02599-274">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="02599-275">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="02599-275">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

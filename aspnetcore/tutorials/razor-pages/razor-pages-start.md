---
title: 'Kurz: Začínáme s Razor Pages v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor Pages v ASP.NET Core. Naučte se, jak vytvořit model, generovat kód pro stránky Razor, použít Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: a8381dee05f267077a29999f3d8bbe6327c2b863
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116150"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="51199-104">Kurz: Začínáme s Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51199-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="51199-105">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="51199-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="51199-106">Toto je první kurz série, který se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="51199-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="51199-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="51199-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="51199-108">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="51199-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="51199-109">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51199-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="51199-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51199-110">Run the app.</span></span>
> * <span data-ttu-id="51199-111">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="51199-111">Examine the project files.</span></span>

<span data-ttu-id="51199-112">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="51199-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="51199-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="51199-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="51199-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51199-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="51199-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51199-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="51199-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="51199-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="51199-118">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="51199-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="51199-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51199-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="51199-120">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="51199-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="51199-121">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="51199-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="51199-122">![nové ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="51199-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="51199-123">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="51199-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="51199-124">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="51199-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="51199-125">![nové ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="51199-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="51199-126">V rozevíracím seznamu vyberte položku **ASP.NET Core 3,0** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="51199-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="51199-128">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="51199-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="51199-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51199-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="51199-131">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="51199-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="51199-132">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="51199-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="51199-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="51199-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="51199-134">Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="51199-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="51199-135">Příkaz `code` otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="51199-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="51199-136">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="51199-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="51199-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="51199-137">Select **Yes**.</span></span>

  <span data-ttu-id="51199-138">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="51199-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="51199-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="51199-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="51199-140">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="51199-140">Select **File** > **New Solution**.</span></span>

![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="51199-142">Vyberte > **Další** **Webová aplikace > webové aplikace** **.NET Core** > **App** .</span><span class="sxs-lookup"><span data-stu-id="51199-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="51199-144">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílové** rozhraní **.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="51199-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![macOS .NET Core 3,0 – výběr](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="51199-146">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="51199-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="51199-148">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="51199-148">Open the project</span></span>

<span data-ttu-id="51199-149">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="51199-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="51199-150">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="51199-150">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="51199-151">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="51199-151">Examine the project files</span></span>

<span data-ttu-id="51199-152">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="51199-152">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="51199-153">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="51199-153">Pages folder</span></span>

<span data-ttu-id="51199-154">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="51199-154">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="51199-155">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="51199-155">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="51199-156">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="51199-156">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="51199-157">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="51199-157">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="51199-158">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="51199-158">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="51199-159">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="51199-159">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="51199-160">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="51199-160">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="51199-161">Další informace najdete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="51199-161">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="51199-162">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="51199-162">wwwroot folder</span></span>

<span data-ttu-id="51199-163">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="51199-163">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="51199-164">Další informace najdete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="51199-164">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="51199-165">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="51199-165">appSettings.json</span></span>

<span data-ttu-id="51199-166">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="51199-166">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="51199-167">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="51199-167">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="51199-168">Program.cs</span><span class="sxs-lookup"><span data-stu-id="51199-168">Program.cs</span></span>

<span data-ttu-id="51199-169">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="51199-169">Contains the entry point for the program.</span></span> <span data-ttu-id="51199-170">Další informace najdete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="51199-170">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="51199-171">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="51199-171">Startup.cs</span></span>

<span data-ttu-id="51199-172">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="51199-172">Contains code that configures app behavior.</span></span> <span data-ttu-id="51199-173">Další informace najdete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="51199-173">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="51199-174">Další kroky</span><span class="sxs-lookup"><span data-stu-id="51199-174">Next steps</span></span>

<span data-ttu-id="51199-175">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="51199-175">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="51199-176">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="51199-176">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51199-177">Toto je první kurz řady.</span><span class="sxs-lookup"><span data-stu-id="51199-177">This is the first tutorial of a series.</span></span> <span data-ttu-id="51199-178">[Série](xref:tutorials/razor-pages/index) se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="51199-178">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="51199-179">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="51199-179">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="51199-180">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="51199-180">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="51199-181">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51199-181">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="51199-182">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51199-182">Run the app.</span></span>
> * <span data-ttu-id="51199-183">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="51199-183">Examine the project files.</span></span>

<span data-ttu-id="51199-184">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="51199-184">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="51199-186">Požadavky</span><span class="sxs-lookup"><span data-stu-id="51199-186">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="51199-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51199-187">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="51199-188">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51199-188">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="51199-189">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="51199-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="51199-190">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="51199-190">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="51199-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51199-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="51199-192">V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="51199-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="51199-193">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="51199-193">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="51199-195">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="51199-195">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="51199-196">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="51199-196">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="51199-198">V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="51199-198">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="51199-200">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="51199-200">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="51199-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51199-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="51199-203">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="51199-203">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="51199-204">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="51199-204">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="51199-205">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="51199-205">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="51199-206">Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="51199-206">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="51199-207">Příkaz `code` otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="51199-207">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="51199-208">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="51199-208">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="51199-209">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="51199-209">Select **Yes**.</span></span>

  <span data-ttu-id="51199-210">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="51199-210">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="51199-211">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="51199-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="51199-212">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="51199-212">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="51199-213">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="51199-213">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="51199-214">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="51199-214">Open the project</span></span>

<span data-ttu-id="51199-215">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="51199-215">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="51199-216">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="51199-216">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="51199-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51199-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="51199-218">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="51199-218">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="51199-219">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51199-219">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="51199-220">Panel Adresa zobrazuje `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="51199-220">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="51199-221">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="51199-221">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="51199-222">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="51199-222">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="51199-223">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="51199-223">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="51199-224">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="51199-224">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="51199-225">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="51199-225">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="51199-227">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="51199-227">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="51199-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51199-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="51199-230">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="51199-230">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="51199-231">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="51199-231">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="51199-232">Panel Adresa zobrazuje `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="51199-232">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="51199-233">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="51199-233">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="51199-234">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="51199-234">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="51199-235">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="51199-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="51199-236">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="51199-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="51199-238">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="51199-238">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="51199-240">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="51199-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="51199-241">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="51199-241">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="51199-242">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="51199-242">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="51199-243">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="51199-243">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="51199-244">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="51199-244">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="51199-246">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="51199-246">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="51199-248">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="51199-248">Examine the project files</span></span>

<span data-ttu-id="51199-249">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="51199-249">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="51199-250">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="51199-250">Pages folder</span></span>

<span data-ttu-id="51199-251">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="51199-251">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="51199-252">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="51199-252">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="51199-253">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="51199-253">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="51199-254">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="51199-254">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="51199-255">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="51199-255">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="51199-256">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="51199-256">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="51199-257">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="51199-257">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="51199-258">Další informace najdete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="51199-258">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="51199-259">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="51199-259">wwwroot folder</span></span>

<span data-ttu-id="51199-260">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="51199-260">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="51199-261">Další informace najdete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="51199-261">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="51199-262">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="51199-262">appSettings.json</span></span>

<span data-ttu-id="51199-263">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="51199-263">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="51199-264">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="51199-264">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="51199-265">Program.cs</span><span class="sxs-lookup"><span data-stu-id="51199-265">Program.cs</span></span>

<span data-ttu-id="51199-266">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="51199-266">Contains the entry point for the program.</span></span> <span data-ttu-id="51199-267">Další informace najdete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="51199-267">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="51199-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="51199-268">Startup.cs</span></span>

<span data-ttu-id="51199-269">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="51199-269">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="51199-270">Další informace najdete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="51199-270">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51199-271">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="51199-271">Additional resources</span></span>

* [<span data-ttu-id="51199-272">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="51199-272">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="51199-273">Další kroky</span><span class="sxs-lookup"><span data-stu-id="51199-273">Next steps</span></span>

<span data-ttu-id="51199-274">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="51199-274">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="51199-275">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="51199-275">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

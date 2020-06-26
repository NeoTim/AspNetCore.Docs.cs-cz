---
title: 'Kurz: Začínáme se Razor stránkami v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor stránky v ASP.NET Core. Naučte se, jak vytvořit model, vygenerovat kód pro Razor stránky, používat Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 97e3f60480bc8e7e88c8361e5b13f02d98765d9e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405299"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="2e364-104">Kurz: Začínáme se Razor stránkami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e364-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="2e364-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e364-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="2e364-106">Toto je první kurz série, který se učí základy vytvoření Razor webové aplikace ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="2e364-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="2e364-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="2e364-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="2e364-108">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="2e364-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e364-109">Vytvoření Razor webové aplikace Pages</span><span class="sxs-lookup"><span data-stu-id="2e364-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2e364-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2e364-110">Run the app.</span></span>
> * <span data-ttu-id="2e364-111">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="2e364-111">Examine the project files.</span></span>

<span data-ttu-id="2e364-112">Na konci tohoto kurzu budete mít Razor webovou aplikaci pracovní stránky, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="2e364-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2e364-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2e364-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e364-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e364-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e364-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e364-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e364-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2e364-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="2e364-118">Vytvoření Razor webové aplikace Pages</span><span class="sxs-lookup"><span data-stu-id="2e364-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e364-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e364-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e364-120">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="2e364-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2e364-121">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2e364-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="2e364-122">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="2e364-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="2e364-123">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="2e364-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2e364-124">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="2e364-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="2e364-125">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="2e364-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="2e364-126">V rozevíracím seznamu vyberte položku **ASP.NET Core 3,1** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2e364-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="2e364-128">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="2e364-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e364-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e364-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2e364-131">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2e364-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2e364-132">Přejděte do adresáře ( `cd` ), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="2e364-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2e364-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2e364-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2e364-134">`dotnet new`Příkaz vytvoří nový Razor projekt stránky ve složce *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="2e364-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2e364-135">`code`Příkaz otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2e364-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2e364-136">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="2e364-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2e364-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="2e364-137">Select **Yes**.</span></span>

  <span data-ttu-id="2e364-138">Do kořenového adresáře projektu se přidá adresář *. VSCode* , který obsahuje *launch.jsv* souborech a *tasks.jsna* nich.</span><span class="sxs-lookup"><span data-stu-id="2e364-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e364-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2e364-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2e364-140">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="2e364-140">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2e364-142">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web aplikace**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="2e364-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="2e364-143">V části verze 8,6 nebo novější vyberte webová aplikace **Webová aplikace a**  >  **aplikace**konzoly webové  >  **aplikace**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="2e364-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Výběr šablony webové aplikace v macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="2e364-145">Potvrďte následující konfigurace:</span><span class="sxs-lookup"><span data-stu-id="2e364-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="2e364-146">**Cílová architektura** je nastavená na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="2e364-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="2e364-147">**Ověřování** nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="2e364-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="2e364-148">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2e364-148">Select **Next**.</span></span>

  ![macOS .NET Core 3,1 – výběr](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="2e364-150">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2e364-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS název projektu](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2e364-152">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="2e364-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="2e364-153">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="2e364-153">Examine the project files</span></span>

<span data-ttu-id="2e364-154">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="2e364-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2e364-155">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="2e364-155">Pages folder</span></span>

<span data-ttu-id="2e364-156">Obsahuje Razor stránky a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="2e364-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2e364-157">Každá Razor stránka je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="2e364-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2e364-158">Soubor *. cshtml* , který obsahuje značky HTML s kódem jazyka C# pomocí Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="2e364-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2e364-159">Soubor *. cshtml.cs* , který obsahuje kód jazyka C#, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="2e364-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="2e364-160">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="2e364-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2e364-161">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="2e364-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2e364-162">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="2e364-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2e364-163">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2e364-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2e364-164">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="2e364-164">wwwroot folder</span></span>

<span data-ttu-id="2e364-165">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="2e364-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2e364-166">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="2e364-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2e364-167">appSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="2e364-167">appSettings.json</span></span>

<span data-ttu-id="2e364-168">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="2e364-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="2e364-169">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2e364-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2e364-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="2e364-170">Program.cs</span></span>

<span data-ttu-id="2e364-171">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="2e364-171">Contains the entry point for the program.</span></span> <span data-ttu-id="2e364-172">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="2e364-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2e364-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2e364-173">Startup.cs</span></span>

<span data-ttu-id="2e364-174">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e364-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="2e364-175">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="2e364-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2e364-176">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2e364-176">Next steps</span></span>

<span data-ttu-id="2e364-177">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="2e364-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2e364-178">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="2e364-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2e364-179">Toto je první kurz řady.</span><span class="sxs-lookup"><span data-stu-id="2e364-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="2e364-180">[Série](xref:tutorials/razor-pages/index) se učí základy vytvoření Razor webové aplikace ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="2e364-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="2e364-181">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="2e364-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="2e364-182">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="2e364-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e364-183">Vytvoření Razor webové aplikace Pages</span><span class="sxs-lookup"><span data-stu-id="2e364-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2e364-184">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2e364-184">Run the app.</span></span>
> * <span data-ttu-id="2e364-185">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="2e364-185">Examine the project files.</span></span>

<span data-ttu-id="2e364-186">Na konci tohoto kurzu budete mít Razor webovou aplikaci pracovní stránky, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="2e364-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2e364-188">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2e364-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e364-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e364-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e364-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e364-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e364-191">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2e364-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="2e364-192">Vytvoření Razor webové aplikace Pages</span><span class="sxs-lookup"><span data-stu-id="2e364-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e364-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e364-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e364-194">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="2e364-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="2e364-195">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2e364-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="2e364-197">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="2e364-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2e364-198">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="2e364-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="2e364-200">V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2e364-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="2e364-202">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="2e364-202">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e364-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e364-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2e364-205">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2e364-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2e364-206">Přejděte do adresáře ( `cd` ), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="2e364-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2e364-207">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2e364-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2e364-208">`dotnet new`Příkaz vytvoří nový Razor projekt stránky ve složce *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="2e364-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2e364-209">`code`Příkaz otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2e364-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2e364-210">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="2e364-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2e364-211">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="2e364-211">Select **Yes**.</span></span>

  <span data-ttu-id="2e364-212">Do kořenového adresáře projektu se přidá adresář *. VSCode* , který obsahuje *launch.jsv* souborech a *tasks.jsna* nich.</span><span class="sxs-lookup"><span data-stu-id="2e364-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e364-213">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2e364-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2e364-214">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="2e364-214">Select **File** > **New Solution**.</span></span>

![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2e364-216">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web aplikace**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="2e364-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="2e364-217">V části verze 8,6 nebo novější vyberte webová aplikace **Webová aplikace a**  >  **aplikace**konzoly webové  >  **aplikace**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="2e364-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="2e364-218">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílové** rozhraní **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="2e364-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,0 – výběr](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="2e364-220">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2e364-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2e364-222">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="2e364-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e364-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e364-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e364-224">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="2e364-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2e364-225">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2e364-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2e364-226">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2e364-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2e364-227">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="2e364-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2e364-228">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="2e364-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2e364-229">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="2e364-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="2e364-230">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="2e364-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2e364-231">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2e364-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2e364-233">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="2e364-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="2e364-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e364-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="2e364-236">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="2e364-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2e364-237">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="2e364-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="2e364-238">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2e364-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2e364-239">To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="2e364-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2e364-240">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="2e364-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="2e364-241">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="2e364-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2e364-242">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2e364-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2e364-244">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="2e364-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e364-246">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="2e364-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2e364-247">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="2e364-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2e364-248">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="2e364-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="2e364-249">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="2e364-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2e364-250">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2e364-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="2e364-252">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="2e364-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="2e364-254">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="2e364-254">Examine the project files</span></span>

<span data-ttu-id="2e364-255">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="2e364-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2e364-256">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="2e364-256">Pages folder</span></span>

<span data-ttu-id="2e364-257">Obsahuje Razor stránky a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="2e364-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2e364-258">Každá Razor stránka je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="2e364-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2e364-259">Soubor *. cshtml* , který obsahuje značky HTML s kódem jazyka C# pomocí Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="2e364-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2e364-260">Soubor *. cshtml.cs* , který obsahuje kód jazyka C#, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="2e364-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="2e364-261">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="2e364-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2e364-262">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="2e364-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2e364-263">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="2e364-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2e364-264">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2e364-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2e364-265">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="2e364-265">wwwroot folder</span></span>

<span data-ttu-id="2e364-266">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="2e364-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2e364-267">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="2e364-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2e364-268">appSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="2e364-268">appSettings.json</span></span>

<span data-ttu-id="2e364-269">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="2e364-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="2e364-270">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2e364-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2e364-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="2e364-271">Program.cs</span></span>

<span data-ttu-id="2e364-272">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="2e364-272">Contains the entry point for the program.</span></span> <span data-ttu-id="2e364-273">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="2e364-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2e364-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2e364-274">Startup.cs</span></span>

<span data-ttu-id="2e364-275">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="2e364-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="2e364-276">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="2e364-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e364-277">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2e364-277">Additional resources</span></span>

* [<span data-ttu-id="2e364-278">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="2e364-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="2e364-279">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2e364-279">Next steps</span></span>

<span data-ttu-id="2e364-280">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="2e364-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2e364-281">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="2e364-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

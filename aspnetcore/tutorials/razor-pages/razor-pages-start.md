---
title: 'Kurz: Začínáme se Razor stránkami v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte Razor , jak používat stránky v ASP.NET Core. Naučte se, jak vytvořit model, vygenerovat Razor kód pro stránky, používat Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8ed12b1778673962fe0b174e005bd6d8a7f54168
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774870"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="cfd17-104">Kurz: Začínáme s Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cfd17-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="cfd17-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cfd17-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="cfd17-106">Toto je první kurz série, který se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="cfd17-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="cfd17-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="cfd17-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="cfd17-108">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="cfd17-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cfd17-109">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfd17-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="cfd17-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfd17-110">Run the app.</span></span>
> * <span data-ttu-id="cfd17-111">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="cfd17-111">Examine the project files.</span></span>

<span data-ttu-id="cfd17-112">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="cfd17-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="cfd17-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="cfd17-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfd17-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfd17-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfd17-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfd17-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfd17-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cfd17-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="cfd17-118">Vytvoření webové aplikace Stránky Razor</span><span class="sxs-lookup"><span data-stu-id="cfd17-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfd17-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfd17-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cfd17-120">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="cfd17-121">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="cfd17-122">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="cfd17-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="cfd17-123">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="cfd17-124">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="cfd17-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="cfd17-125">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="cfd17-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="cfd17-126">V rozevíracím seznamu vyberte položku **ASP.NET Core 3,1** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="cfd17-128">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="cfd17-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfd17-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfd17-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cfd17-131">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cfd17-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="cfd17-132">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="cfd17-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="cfd17-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cfd17-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="cfd17-134">`dotnet new` Příkaz vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="cfd17-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="cfd17-135">`code` Příkaz otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cfd17-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="cfd17-136">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="cfd17-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="cfd17-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-137">Select **Yes**.</span></span>

  <span data-ttu-id="cfd17-138">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cfd17-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfd17-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cfd17-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cfd17-140">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-140">Select **File** > **New Solution**.</span></span>

![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="cfd17-142">Jako **Další**vyberte **webovou aplikaci** > **.NET Core** > **App** > .</span><span class="sxs-lookup"><span data-stu-id="cfd17-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="cfd17-144">V dialogovém okně **Konfigurace nové webové aplikace** nastavte **cílovou architekturu** na **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,1 – výběr](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="cfd17-146">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="cfd17-148">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="cfd17-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="cfd17-149">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="cfd17-149">Examine the project files</span></span>

<span data-ttu-id="cfd17-150">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="cfd17-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="cfd17-151">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="cfd17-151">Pages folder</span></span>

<span data-ttu-id="cfd17-152">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="cfd17-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="cfd17-153">Každá stránka Razor Page je dvojicí souborů:</span><span class="sxs-lookup"><span data-stu-id="cfd17-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="cfd17-154">Soubor *. cshtml* , který obsahuje značky HTML s kódem jazyka C# pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="cfd17-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="cfd17-155">Soubor *. cshtml.cs* , který obsahuje kód jazyka C#, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="cfd17-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="cfd17-156">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="cfd17-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="cfd17-157">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="cfd17-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="cfd17-158">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="cfd17-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="cfd17-159">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="cfd17-160">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="cfd17-160">wwwroot folder</span></span>

<span data-ttu-id="cfd17-161">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="cfd17-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="cfd17-162">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="cfd17-163">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="cfd17-163">appSettings.json</span></span>

<span data-ttu-id="cfd17-164">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="cfd17-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="cfd17-165">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="cfd17-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="cfd17-166">Program.cs</span></span>

<span data-ttu-id="cfd17-167">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="cfd17-167">Contains the entry point for the program.</span></span> <span data-ttu-id="cfd17-168">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="cfd17-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="cfd17-169">Startup.cs</span></span>

<span data-ttu-id="cfd17-170">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfd17-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="cfd17-171">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cfd17-172">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cfd17-172">Next steps</span></span>

<span data-ttu-id="cfd17-173">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="cfd17-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="cfd17-174">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="cfd17-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cfd17-175">Toto je první kurz řady.</span><span class="sxs-lookup"><span data-stu-id="cfd17-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="cfd17-176">[Série](xref:tutorials/razor-pages/index) se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="cfd17-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="cfd17-177">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="cfd17-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="cfd17-178">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="cfd17-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cfd17-179">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfd17-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="cfd17-180">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfd17-180">Run the app.</span></span>
> * <span data-ttu-id="cfd17-181">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="cfd17-181">Examine the project files.</span></span>

<span data-ttu-id="cfd17-182">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="cfd17-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="cfd17-184">Požadavky</span><span class="sxs-lookup"><span data-stu-id="cfd17-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfd17-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfd17-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfd17-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfd17-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfd17-187">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cfd17-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="cfd17-188">Vytvoření webové aplikace Stránky Razor</span><span class="sxs-lookup"><span data-stu-id="cfd17-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfd17-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfd17-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cfd17-190">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="cfd17-191">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="cfd17-193">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="cfd17-194">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="cfd17-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="cfd17-196">V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="cfd17-198">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="cfd17-198">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfd17-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfd17-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cfd17-201">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cfd17-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="cfd17-202">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="cfd17-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="cfd17-203">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cfd17-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="cfd17-204">`dotnet new` Příkaz vytvoří nový projekt Razor Pages ve složce *RazorPagesMovie* .</span><span class="sxs-lookup"><span data-stu-id="cfd17-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="cfd17-205">`code` Příkaz otevře složku *RazorPagesMovie* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cfd17-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="cfd17-206">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="cfd17-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="cfd17-207">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-207">Select **Yes**.</span></span>

  <span data-ttu-id="cfd17-208">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cfd17-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfd17-209">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cfd17-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cfd17-210">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-210">Select **File** > **New Solution**.</span></span>

![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="cfd17-212">Jako **Další**vyberte **webovou aplikaci** > **.NET Core** > **App** > .</span><span class="sxs-lookup"><span data-stu-id="cfd17-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![dialog pro nový projekt v macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="cfd17-214">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílové** rozhraní **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,0 – výběr](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="cfd17-216">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cfd17-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="cfd17-218">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="cfd17-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfd17-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfd17-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cfd17-220">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="cfd17-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="cfd17-221">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfd17-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="cfd17-222">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cfd17-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cfd17-223">Důvodem `localhost` je, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="cfd17-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="cfd17-224">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="cfd17-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="cfd17-225">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="cfd17-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="cfd17-226">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="cfd17-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="cfd17-227">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="cfd17-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="cfd17-229">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="cfd17-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="cfd17-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfd17-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="cfd17-232">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="cfd17-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="cfd17-233">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cfd17-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="cfd17-234">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="cfd17-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="cfd17-235">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="cfd17-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="cfd17-236">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="cfd17-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="cfd17-237">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="cfd17-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="cfd17-238">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="cfd17-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="cfd17-240">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="cfd17-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfd17-242">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cfd17-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="cfd17-243">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="cfd17-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="cfd17-244">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cfd17-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="cfd17-245">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="cfd17-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="cfd17-246">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="cfd17-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="cfd17-248">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="cfd17-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="cfd17-250">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="cfd17-250">Examine the project files</span></span>

<span data-ttu-id="cfd17-251">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="cfd17-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="cfd17-252">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="cfd17-252">Pages folder</span></span>

<span data-ttu-id="cfd17-253">Obsahuje Razor stránky a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="cfd17-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="cfd17-254">Každá Razor stránka je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="cfd17-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="cfd17-255">Soubor *. cshtml* , který obsahuje značky HTML s kódem jazyka C# Razor pomocí syntaxe.</span><span class="sxs-lookup"><span data-stu-id="cfd17-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="cfd17-256">Soubor *. cshtml.cs* , který obsahuje kód jazyka C#, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="cfd17-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="cfd17-257">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="cfd17-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="cfd17-258">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="cfd17-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="cfd17-259">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="cfd17-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="cfd17-260">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="cfd17-261">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="cfd17-261">wwwroot folder</span></span>

<span data-ttu-id="cfd17-262">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="cfd17-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="cfd17-263">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="cfd17-264">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="cfd17-264">appSettings.json</span></span>

<span data-ttu-id="cfd17-265">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="cfd17-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="cfd17-266">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="cfd17-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="cfd17-267">Program.cs</span></span>

<span data-ttu-id="cfd17-268">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="cfd17-268">Contains the entry point for the program.</span></span> <span data-ttu-id="cfd17-269">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="cfd17-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="cfd17-270">Startup.cs</span></span>

<span data-ttu-id="cfd17-271">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="cfd17-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="cfd17-272">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="cfd17-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cfd17-273">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cfd17-273">Additional resources</span></span>

* [<span data-ttu-id="cfd17-274">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="cfd17-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="cfd17-275">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cfd17-275">Next steps</span></span>

<span data-ttu-id="cfd17-276">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="cfd17-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="cfd17-277">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="cfd17-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

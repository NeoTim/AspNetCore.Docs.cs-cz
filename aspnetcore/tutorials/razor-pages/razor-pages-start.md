---
title: 'Kurz: Začínáme s Razor Pages v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor Pages v ASP.NET Core. Naučte se, jak vytvořit model, generovat kód pro stránky Razor, použít Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 67a5fcee0a37861fd39a018443edbc0b9e513213
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487676"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="bd3c3-104">Kurz: Začínáme s Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bd3c3-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="bd3c3-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd3c3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="bd3c3-106">Toto je první kurz série, který se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="bd3c3-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="bd3c3-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bd3c3-109">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="bd3c3-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-110">Run the app.</span></span>
> * <span data-ttu-id="bd3c3-111">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-111">Examine the project files.</span></span>

<span data-ttu-id="bd3c3-112">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="bd3c3-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="bd3c3-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd3c3-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd3c3-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd3c3-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd3c3-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd3c3-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bd3c3-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="bd3c3-118">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="bd3c3-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd3c3-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd3c3-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bd3c3-120">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bd3c3-121">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="bd3c3-122">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="bd3c3-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="bd3c3-123">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="bd3c3-124">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="bd3c3-125">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="bd3c3-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="bd3c3-126">V rozevíracím seznamu vyberte položku **ASP.NET Core 3,0** a potom vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="bd3c3-128">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd3c3-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd3c3-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bd3c3-131">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="bd3c3-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="bd3c3-132">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="bd3c3-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="bd3c3-134">Příkaz vytvoří nový projekt Razor Pages ve složce RazorPagesMovie. `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="bd3c3-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="bd3c3-135">Příkaz otevře složku RazorPagesMovie v aktuální instanci Visual Studio Code. `code`</span><span class="sxs-lookup"><span data-stu-id="bd3c3-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="bd3c3-136">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="bd3c3-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="bd3c3-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-137">Select **Yes**.</span></span>

  <span data-ttu-id="bd3c3-138">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="bd3c3-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd3c3-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bd3c3-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bd3c3-140">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-140">Select **File** > **New Solution**.</span></span>

![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="bd3c3-142">Jako **Další**vyberte **webovou aplikaci** > > .NETCore> App.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS dialogové okno nového projektu](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="bd3c3-144">V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** nastavte **cílové** rozhraní **.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![macOS .NET Core 3,0 – výběr](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="bd3c3-146">Pojmenujte projekt **RazorPagesMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="bd3c3-148">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="bd3c3-148">Open the project</span></span>

<span data-ttu-id="bd3c3-149">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="bd3c3-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="bd3c3-150">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="bd3c3-150">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd3c3-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd3c3-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bd3c3-152">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-152">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="bd3c3-153">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-153">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="bd3c3-154">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-154">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bd3c3-155">Důvodem `localhost` je, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-155">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="bd3c3-156">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-156">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="bd3c3-157">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-157">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd3c3-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd3c3-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="bd3c3-159">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="bd3c3-160">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="bd3c3-161">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bd3c3-162">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="bd3c3-163">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-163">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd3c3-164">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bd3c3-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="bd3c3-165">Stisknutím **kombinace kláves ALT-CMD-Enter** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-165">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="bd3c3-166">Můžete také přejít na panel nabídek a přejít na příkaz Spustit > Spustit bez ladění.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-166">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="bd3c3-167">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="bd3c3-168">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="bd3c3-168">Examine the project files</span></span>

<span data-ttu-id="bd3c3-169">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-169">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="bd3c3-170">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="bd3c3-170">Pages folder</span></span>

<span data-ttu-id="bd3c3-171">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-171">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="bd3c3-172">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-172">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="bd3c3-173">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-173">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="bd3c3-174">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-174">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="bd3c3-175">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-175">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="bd3c3-176">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-176">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="bd3c3-177">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-177">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="bd3c3-178">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-178">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="bd3c3-179">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="bd3c3-179">wwwroot folder</span></span>

<span data-ttu-id="bd3c3-180">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-180">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="bd3c3-181">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-181">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="bd3c3-182">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="bd3c3-182">appSettings.json</span></span>

<span data-ttu-id="bd3c3-183">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-183">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="bd3c3-184">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="bd3c3-185">Program.cs</span><span class="sxs-lookup"><span data-stu-id="bd3c3-185">Program.cs</span></span>

<span data-ttu-id="bd3c3-186">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-186">Contains the entry point for the program.</span></span> <span data-ttu-id="bd3c3-187">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-187">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="bd3c3-188">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="bd3c3-188">Startup.cs</span></span>

<span data-ttu-id="bd3c3-189">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-189">Contains code that configures app behavior.</span></span> <span data-ttu-id="bd3c3-190">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-190">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bd3c3-191">Další postup</span><span class="sxs-lookup"><span data-stu-id="bd3c3-191">Next steps</span></span>

<span data-ttu-id="bd3c3-192">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-192">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bd3c3-193">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="bd3c3-193">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd3c3-194">Toto je první kurz řady.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-194">This is the first tutorial of a series.</span></span> <span data-ttu-id="bd3c3-195">[Série](xref:tutorials/razor-pages/index) se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-195">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="bd3c3-196">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-196">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="bd3c3-197">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bd3c3-198">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-198">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="bd3c3-199">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-199">Run the app.</span></span>
> * <span data-ttu-id="bd3c3-200">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-200">Examine the project files.</span></span>

<span data-ttu-id="bd3c3-201">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-201">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="bd3c3-203">Požadavky</span><span class="sxs-lookup"><span data-stu-id="bd3c3-203">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd3c3-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd3c3-204">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd3c3-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd3c3-205">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd3c3-206">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bd3c3-206">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="bd3c3-207">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="bd3c3-207">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd3c3-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd3c3-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bd3c3-209">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-209">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="bd3c3-210">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-210">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="bd3c3-212">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-212">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="bd3c3-213">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-213">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="bd3c3-215">V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** a potom vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-215">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="bd3c3-217">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-217">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd3c3-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd3c3-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bd3c3-220">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="bd3c3-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="bd3c3-221">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-221">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="bd3c3-222">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-222">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="bd3c3-223">Příkaz vytvoří nový projekt Razor Pages ve složce RazorPagesMovie. `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="bd3c3-223">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="bd3c3-224">Příkaz otevře složku RazorPagesMovie v aktuální instanci Visual Studio Code. `code`</span><span class="sxs-lookup"><span data-stu-id="bd3c3-224">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="bd3c3-225">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="bd3c3-225">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="bd3c3-226">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-226">Select **Yes**.</span></span>

  <span data-ttu-id="bd3c3-227">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="bd3c3-227">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd3c3-228">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bd3c3-228">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bd3c3-229">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-229">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="bd3c3-230">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-230">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="bd3c3-231">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="bd3c3-231">Open the project</span></span>

<span data-ttu-id="bd3c3-232">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="bd3c3-232">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="bd3c3-233">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="bd3c3-233">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd3c3-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd3c3-234">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bd3c3-235">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-235">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="bd3c3-236">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="bd3c3-237">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-237">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bd3c3-238">Důvodem `localhost` je, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-238">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="bd3c3-239">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-239">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="bd3c3-240">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="bd3c3-241">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="bd3c3-242">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="bd3c3-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="bd3c3-244">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd3c3-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd3c3-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="bd3c3-247">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-247">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="bd3c3-248">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="bd3c3-249">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-249">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="bd3c3-250">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="bd3c3-251">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-251">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="bd3c3-252">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-252">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="bd3c3-253">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="bd3c3-253">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="bd3c3-255">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-255">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd3c3-257">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="bd3c3-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="bd3c3-258">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-258">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="bd3c3-259">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-259">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="bd3c3-260">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-260">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="bd3c3-261">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="bd3c3-261">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="bd3c3-263">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-263">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="bd3c3-265">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="bd3c3-265">Examine the project files</span></span>

<span data-ttu-id="bd3c3-266">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-266">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="bd3c3-267">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="bd3c3-267">Pages folder</span></span>

<span data-ttu-id="bd3c3-268">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-268">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="bd3c3-269">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-269">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="bd3c3-270">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-270">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="bd3c3-271">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-271">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="bd3c3-272">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-272">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="bd3c3-273">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-273">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="bd3c3-274">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-274">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="bd3c3-275">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-275">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="bd3c3-276">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="bd3c3-276">wwwroot folder</span></span>

<span data-ttu-id="bd3c3-277">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-277">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="bd3c3-278">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-278">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="bd3c3-279">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="bd3c3-279">appSettings.json</span></span>

<span data-ttu-id="bd3c3-280">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-280">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="bd3c3-281">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-281">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="bd3c3-282">Program.cs</span><span class="sxs-lookup"><span data-stu-id="bd3c3-282">Program.cs</span></span>

<span data-ttu-id="bd3c3-283">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-283">Contains the entry point for the program.</span></span> <span data-ttu-id="bd3c3-284">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-284">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="bd3c3-285">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="bd3c3-285">Startup.cs</span></span>

<span data-ttu-id="bd3c3-286">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-286">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="bd3c3-287">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bd3c3-287">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd3c3-288">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bd3c3-288">Additional resources</span></span>

* [<span data-ttu-id="bd3c3-289">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="bd3c3-289">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="bd3c3-290">Další kroky</span><span class="sxs-lookup"><span data-stu-id="bd3c3-290">Next steps</span></span>

<span data-ttu-id="bd3c3-291">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="bd3c3-291">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bd3c3-292">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="bd3c3-292">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

---
title: 'Kurz: Začínáme s Razor Pages v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor Pages v ASP.NET Core. Naučte se, jak vytvořit model, generovat kód pro stránky Razor, použít Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1605197188d97f27a884739a72400da2d5818b1a
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371997"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="9c695-104">Kurz: Začínáme s Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c695-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="9c695-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9c695-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="9c695-106">Toto je první kurz série, který se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9c695-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="9c695-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="9c695-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="9c695-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="9c695-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9c695-109">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c695-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="9c695-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c695-110">Run the app.</span></span>
> * <span data-ttu-id="9c695-111">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="9c695-111">Examine the project files.</span></span>

<span data-ttu-id="9c695-112">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="9c695-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="9c695-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9c695-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c695-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c695-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c695-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c695-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c695-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c695-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="9c695-118">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="9c695-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c695-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c695-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c695-120">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="9c695-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9c695-121">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="9c695-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="9c695-122">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="9c695-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="9c695-123">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="9c695-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="9c695-124">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="9c695-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="9c695-125">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="9c695-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="9c695-126">V rozevíracím seznamu vyberte položku **ASP.NET Core 3,0** a potom vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9c695-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="9c695-128">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="9c695-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c695-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c695-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9c695-131">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9c695-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="9c695-132">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="9c695-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="9c695-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9c695-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="9c695-134">Příkaz vytvoří nový projekt Razor Pages ve složce RazorPagesMovie.  `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="9c695-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="9c695-135">Příkaz otevře složku RazorPagesMovie v aktuální instanci Visual Studio Code.  `code`</span><span class="sxs-lookup"><span data-stu-id="9c695-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="9c695-136">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="9c695-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="9c695-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9c695-137">Select **Yes**.</span></span>

  <span data-ttu-id="9c695-138">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9c695-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c695-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c695-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9c695-140">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9c695-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="9c695-141">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9c695-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="9c695-142">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="9c695-142">Open the project</span></span>

<span data-ttu-id="9c695-143">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="9c695-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="9c695-144">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="9c695-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c695-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c695-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c695-146">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="9c695-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9c695-147">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c695-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="9c695-148">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="9c695-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9c695-149">Důvodem `localhost` je, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="9c695-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="9c695-150">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="9c695-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="9c695-151">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="9c695-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c695-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c695-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="9c695-153">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="9c695-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9c695-154">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9c695-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="9c695-155">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="9c695-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9c695-156">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="9c695-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="9c695-157">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="9c695-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c695-158">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c695-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="9c695-159">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="9c695-159">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9c695-160">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9c695-160">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="9c695-161">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="9c695-161">Examine the project files</span></span>

<span data-ttu-id="9c695-162">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="9c695-162">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="9c695-163">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="9c695-163">Pages folder</span></span>

<span data-ttu-id="9c695-164">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="9c695-164">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="9c695-165">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="9c695-165">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="9c695-166">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="9c695-166">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="9c695-167">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="9c695-167">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="9c695-168">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="9c695-168">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="9c695-169">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="9c695-169">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="9c695-170">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="9c695-170">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="9c695-171">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="9c695-171">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="9c695-172">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="9c695-172">wwwroot folder</span></span>

<span data-ttu-id="9c695-173">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="9c695-173">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="9c695-174">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="9c695-174">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="9c695-175">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="9c695-175">appSettings.json</span></span>

<span data-ttu-id="9c695-176">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="9c695-176">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="9c695-177">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9c695-177">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="9c695-178">Program.cs</span><span class="sxs-lookup"><span data-stu-id="9c695-178">Program.cs</span></span>

<span data-ttu-id="9c695-179">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="9c695-179">Contains the entry point for the program.</span></span> <span data-ttu-id="9c695-180">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9c695-180">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="9c695-181">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="9c695-181">Startup.cs</span></span>

<span data-ttu-id="9c695-182">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c695-182">Contains code that configures app behavior.</span></span> <span data-ttu-id="9c695-183">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="9c695-183">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9c695-184">Další postup</span><span class="sxs-lookup"><span data-stu-id="9c695-184">Next steps</span></span>

<span data-ttu-id="9c695-185">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="9c695-185">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9c695-186">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="9c695-186">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9c695-187">Toto je první kurz řady.</span><span class="sxs-lookup"><span data-stu-id="9c695-187">This is the first tutorial of a series.</span></span> <span data-ttu-id="9c695-188">[Série](xref:tutorials/razor-pages/index) se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9c695-188">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="9c695-189">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="9c695-189">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="9c695-190">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="9c695-190">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9c695-191">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c695-191">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="9c695-192">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c695-192">Run the app.</span></span>
> * <span data-ttu-id="9c695-193">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="9c695-193">Examine the project files.</span></span>

<span data-ttu-id="9c695-194">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="9c695-194">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="9c695-196">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9c695-196">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c695-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c695-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c695-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c695-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c695-199">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c695-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="9c695-200">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="9c695-200">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c695-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c695-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c695-202">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="9c695-202">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="9c695-203">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="9c695-203">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="9c695-205">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="9c695-205">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="9c695-206">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="9c695-206">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="9c695-208">V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** a potom vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9c695-208">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="9c695-210">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="9c695-210">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c695-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c695-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9c695-213">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9c695-213">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="9c695-214">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="9c695-214">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="9c695-215">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9c695-215">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="9c695-216">Příkaz vytvoří nový projekt Razor Pages ve složce RazorPagesMovie.  `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="9c695-216">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="9c695-217">Příkaz otevře složku RazorPagesMovie v aktuální instanci Visual Studio Code.  `code`</span><span class="sxs-lookup"><span data-stu-id="9c695-217">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="9c695-218">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="9c695-218">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="9c695-219">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="9c695-219">Select **Yes**.</span></span>

  <span data-ttu-id="9c695-220">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="9c695-220">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c695-221">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c695-221">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9c695-222">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9c695-222">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="9c695-223">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9c695-223">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="9c695-224">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="9c695-224">Open the project</span></span>

<span data-ttu-id="9c695-225">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="9c695-225">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="9c695-226">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="9c695-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c695-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c695-227">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c695-228">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="9c695-228">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9c695-229">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9c695-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="9c695-230">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="9c695-230">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9c695-231">Důvodem `localhost` je, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="9c695-231">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="9c695-232">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="9c695-232">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="9c695-233">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="9c695-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="9c695-234">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="9c695-234">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9c695-235">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="9c695-235">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="9c695-237">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="9c695-237">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c695-239">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c695-239">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="9c695-240">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="9c695-240">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9c695-241">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9c695-241">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="9c695-242">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="9c695-242">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9c695-243">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="9c695-243">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="9c695-244">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="9c695-244">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="9c695-245">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="9c695-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9c695-246">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="9c695-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="9c695-248">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="9c695-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c695-250">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="9c695-250">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="9c695-251">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="9c695-251">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9c695-252">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9c695-252">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="9c695-253">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="9c695-253">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9c695-254">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="9c695-254">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="9c695-256">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="9c695-256">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="9c695-258">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="9c695-258">Examine the project files</span></span>

<span data-ttu-id="9c695-259">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="9c695-259">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="9c695-260">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="9c695-260">Pages folder</span></span>

<span data-ttu-id="9c695-261">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="9c695-261">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="9c695-262">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="9c695-262">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="9c695-263">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="9c695-263">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="9c695-264">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="9c695-264">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="9c695-265">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="9c695-265">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="9c695-266">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="9c695-266">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="9c695-267">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="9c695-267">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="9c695-268">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="9c695-268">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="9c695-269">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="9c695-269">wwwroot folder</span></span>

<span data-ttu-id="9c695-270">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="9c695-270">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="9c695-271">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="9c695-271">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="9c695-272">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="9c695-272">appSettings.json</span></span>

<span data-ttu-id="9c695-273">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="9c695-273">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="9c695-274">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9c695-274">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="9c695-275">Program.cs</span><span class="sxs-lookup"><span data-stu-id="9c695-275">Program.cs</span></span>

<span data-ttu-id="9c695-276">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="9c695-276">Contains the entry point for the program.</span></span> <span data-ttu-id="9c695-277">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9c695-277">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="9c695-278">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="9c695-278">Startup.cs</span></span>

<span data-ttu-id="9c695-279">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="9c695-279">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="9c695-280">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="9c695-280">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9c695-281">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9c695-281">Additional resources</span></span>

* [<span data-ttu-id="9c695-282">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="9c695-282">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="9c695-283">Další postup</span><span class="sxs-lookup"><span data-stu-id="9c695-283">Next steps</span></span>

<span data-ttu-id="9c695-284">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="9c695-284">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9c695-285">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="9c695-285">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
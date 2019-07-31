---
title: 'Kurz: Začínáme s Razor Pages v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor Pages v ASP.NET Core. Naučte se, jak vytvořit model, generovat kód pro stránky Razor, použít Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 57a10895c718c539ece280afcb27cb4033c7fb45
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682796"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="f8b44-104">Kurz: Začínáme s Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8b44-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="f8b44-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8b44-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="f8b44-106">Toto je první kurz série, který se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8b44-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="f8b44-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="f8b44-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="f8b44-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="f8b44-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f8b44-109">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8b44-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="f8b44-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8b44-110">Run the app.</span></span>
> * <span data-ttu-id="f8b44-111">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-111">Examine the project files.</span></span>

<span data-ttu-id="f8b44-112">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="f8b44-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="f8b44-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f8b44-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8b44-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8b44-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f8b44-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8b44-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f8b44-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f8b44-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="f8b44-118">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8b44-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8b44-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8b44-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8b44-120">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f8b44-121">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="f8b44-122">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="f8b44-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="f8b44-123">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="f8b44-124">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="f8b44-125">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="f8b44-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="f8b44-126">V rozevíracím seznamu vyberte položku **ASP.NET Core 3,0** a potom vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="f8b44-128">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="f8b44-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f8b44-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8b44-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f8b44-131">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f8b44-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="f8b44-132">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="f8b44-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="f8b44-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="f8b44-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="f8b44-134">Příkaz vytvoří nový projekt Razor Pages ve složce RazorPagesMovie. `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="f8b44-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="f8b44-135">Příkaz otevře složku RazorPagesMovie v aktuální instanci Visual Studio Code. `code`</span><span class="sxs-lookup"><span data-stu-id="f8b44-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="f8b44-136">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="f8b44-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="f8b44-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-137">Select **Yes**.</span></span>

  <span data-ttu-id="f8b44-138">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f8b44-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f8b44-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f8b44-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f8b44-140">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f8b44-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="f8b44-141">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8b44-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="f8b44-142">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="f8b44-142">Open the project</span></span>

<span data-ttu-id="f8b44-143">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="f8b44-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="f8b44-144">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="f8b44-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8b44-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8b44-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8b44-146">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="f8b44-147">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8b44-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f8b44-148">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8b44-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f8b44-149">Důvodem `localhost` je, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="f8b44-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="f8b44-150">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="f8b44-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="f8b44-151">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="f8b44-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f8b44-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8b44-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="f8b44-153">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="f8b44-154">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f8b44-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="f8b44-155">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8b44-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f8b44-156">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="f8b44-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f8b44-157">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="f8b44-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f8b44-158">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f8b44-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f8b44-159">Stisknutím **kombinace kláves ALT-CMD-Enter** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-159">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="f8b44-160">Můžete také přejít na panel nabídek a přejít na příkaz Spustit > Spustit bez ladění.</span><span class="sxs-lookup"><span data-stu-id="f8b44-160">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="f8b44-161">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f8b44-161">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="f8b44-162">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="f8b44-162">Examine the project files</span></span>

<span data-ttu-id="f8b44-163">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="f8b44-163">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="f8b44-164">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="f8b44-164">Pages folder</span></span>

<span data-ttu-id="f8b44-165">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="f8b44-165">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="f8b44-166">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="f8b44-166">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="f8b44-167">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="f8b44-167">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="f8b44-168">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="f8b44-168">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="f8b44-169">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="f8b44-169">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="f8b44-170">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="f8b44-170">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="f8b44-171">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="f8b44-171">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="f8b44-172">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-172">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="f8b44-173">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="f8b44-173">wwwroot folder</span></span>

<span data-ttu-id="f8b44-174">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="f8b44-174">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="f8b44-175">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-175">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="f8b44-176">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="f8b44-176">appSettings.json</span></span>

<span data-ttu-id="f8b44-177">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="f8b44-177">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="f8b44-178">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-178">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="f8b44-179">Program.cs</span><span class="sxs-lookup"><span data-stu-id="f8b44-179">Program.cs</span></span>

<span data-ttu-id="f8b44-180">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="f8b44-180">Contains the entry point for the program.</span></span> <span data-ttu-id="f8b44-181">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-181">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="f8b44-182">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="f8b44-182">Startup.cs</span></span>

<span data-ttu-id="f8b44-183">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8b44-183">Contains code that configures app behavior.</span></span> <span data-ttu-id="f8b44-184">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-184">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f8b44-185">Další kroky</span><span class="sxs-lookup"><span data-stu-id="f8b44-185">Next steps</span></span>

<span data-ttu-id="f8b44-186">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="f8b44-186">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f8b44-187">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="f8b44-187">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8b44-188">Toto je první kurz řady.</span><span class="sxs-lookup"><span data-stu-id="f8b44-188">This is the first tutorial of a series.</span></span> <span data-ttu-id="f8b44-189">[Série](xref:tutorials/razor-pages/index) se učí základy vytváření ASP.NET Core webové aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8b44-189">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="f8b44-190">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="f8b44-190">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="f8b44-191">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="f8b44-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f8b44-192">Vytvořte Razor Pages webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8b44-192">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="f8b44-193">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8b44-193">Run the app.</span></span>
> * <span data-ttu-id="f8b44-194">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-194">Examine the project files.</span></span>

<span data-ttu-id="f8b44-195">Na konci tohoto kurzu budete mít funkční Razor Pages webovou aplikaci, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="f8b44-195">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="f8b44-197">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f8b44-197">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8b44-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8b44-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f8b44-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8b44-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f8b44-200">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f8b44-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="f8b44-201">Vytvoření webové aplikace v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8b44-201">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8b44-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8b44-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8b44-203">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-203">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="f8b44-204">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-204">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="f8b44-206">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-206">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="f8b44-207">Je důležité pojmenovat projekt *RazorPagesMovie* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-207">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="f8b44-209">V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** a potom vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-209">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="f8b44-211">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="f8b44-211">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f8b44-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8b44-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f8b44-214">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f8b44-214">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="f8b44-215">Přejděte do adresáře (`cd`), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="f8b44-215">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="f8b44-216">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="f8b44-216">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="f8b44-217">Příkaz vytvoří nový projekt Razor Pages ve složce RazorPagesMovie. `dotnet new`</span><span class="sxs-lookup"><span data-stu-id="f8b44-217">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="f8b44-218">Příkaz otevře složku RazorPagesMovie v aktuální instanci Visual Studio Code. `code`</span><span class="sxs-lookup"><span data-stu-id="f8b44-218">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="f8b44-219">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou **, dialogové okno požádá o požadované prostředky k sestavení a ladění chybí v ' RazorPagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="f8b44-219">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="f8b44-220">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="f8b44-220">Select **Yes**.</span></span>

  <span data-ttu-id="f8b44-221">Do kořenového adresáře projektu se přidá adresář *. VSCode* obsahující soubory *Launch. JSON* a *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="f8b44-221">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f8b44-222">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f8b44-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f8b44-223">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f8b44-223">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="f8b44-224">Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření projektu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8b44-224">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="f8b44-225">Otevřít projekt</span><span class="sxs-lookup"><span data-stu-id="f8b44-225">Open the project</span></span>

<span data-ttu-id="f8b44-226">V aplikaci Visual Studio vyberte **soubor > otevřít**a potom vyberte soubor *RazorPagesMovie. csproj* .</span><span class="sxs-lookup"><span data-stu-id="f8b44-226">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="f8b44-227">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="f8b44-227">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8b44-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8b44-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8b44-229">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-229">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="f8b44-230">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8b44-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f8b44-231">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8b44-231">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f8b44-232">Důvodem `localhost` je, že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="f8b44-232">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="f8b44-233">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="f8b44-233">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="f8b44-234">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="f8b44-234">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="f8b44-235">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="f8b44-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="f8b44-236">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f8b44-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="f8b44-238">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="f8b44-238">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f8b44-240">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8b44-240">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="f8b44-241">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-241">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="f8b44-242">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f8b44-242">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="f8b44-243">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="f8b44-243">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f8b44-244">To je proto `localhost` , že se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="f8b44-244">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f8b44-245">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="f8b44-245">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="f8b44-246">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="f8b44-246">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="f8b44-247">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f8b44-247">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="f8b44-249">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="f8b44-249">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f8b44-251">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f8b44-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f8b44-252">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="f8b44-252">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="f8b44-253">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f8b44-253">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="f8b44-254">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="f8b44-254">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="f8b44-255">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f8b44-255">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="f8b44-257">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="f8b44-257">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="f8b44-259">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="f8b44-259">Examine the project files</span></span>

<span data-ttu-id="f8b44-260">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="f8b44-260">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="f8b44-261">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="f8b44-261">Pages folder</span></span>

<span data-ttu-id="f8b44-262">Obsahuje stránky Razor a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="f8b44-262">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="f8b44-263">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="f8b44-263">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="f8b44-264">Soubor *. cshtml* , který obsahuje značku HTML s C# kódem pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="f8b44-264">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="f8b44-265">Soubor *. cshtml.cs* obsahující C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="f8b44-265">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="f8b44-266">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="f8b44-266">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="f8b44-267">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="f8b44-267">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="f8b44-268">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="f8b44-268">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="f8b44-269">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-269">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="f8b44-270">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="f8b44-270">wwwroot folder</span></span>

<span data-ttu-id="f8b44-271">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="f8b44-271">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="f8b44-272">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-272">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="f8b44-273">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="f8b44-273">appSettings.json</span></span>

<span data-ttu-id="f8b44-274">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="f8b44-274">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="f8b44-275">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-275">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="f8b44-276">Program.cs</span><span class="sxs-lookup"><span data-stu-id="f8b44-276">Program.cs</span></span>

<span data-ttu-id="f8b44-277">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="f8b44-277">Contains the entry point for the program.</span></span> <span data-ttu-id="f8b44-278">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-278">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="f8b44-279">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="f8b44-279">Startup.cs</span></span>

<span data-ttu-id="f8b44-280">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas s soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="f8b44-280">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="f8b44-281">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f8b44-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8b44-282">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f8b44-282">Additional resources</span></span>

* [<span data-ttu-id="f8b44-283">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="f8b44-283">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="f8b44-284">Další postup</span><span class="sxs-lookup"><span data-stu-id="f8b44-284">Next steps</span></span>

<span data-ttu-id="f8b44-285">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="f8b44-285">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f8b44-286">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="f8b44-286">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

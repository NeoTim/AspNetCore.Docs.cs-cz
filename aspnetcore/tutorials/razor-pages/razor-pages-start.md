---
title: 'Kurz: Začínáme se Razor stránkami v ASP.NET Core'
author: rick-anderson
description: V této sérii kurzů se dozvíte, jak používat Razor stránky v ASP.NET Core. Naučte se, jak vytvořit model, vygenerovat kód pro Razor stránky, používat Entity Framework Core a SQL Server pro přístup k datům, přidat funkce hledání, přidat ověření vstupu a použít migrace k aktualizaci modelu.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 5fb841de2fa9a04cf05aaf08f255041ee1952638
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021091"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="50d2f-104">Kurz: Začínáme se Razor stránkami v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50d2f-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="50d2f-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="50d2f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="50d2f-106">Toto je první kurz série, který se učí základy vytvoření Razor webové aplikace ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="50d2f-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="50d2f-107">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="50d2f-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="50d2f-108">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="50d2f-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="50d2f-109">Vytvoření Razor webové aplikace Pages</span><span class="sxs-lookup"><span data-stu-id="50d2f-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="50d2f-110">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="50d2f-110">Run the app.</span></span>
> * <span data-ttu-id="50d2f-111">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="50d2f-111">Examine the project files.</span></span>

<span data-ttu-id="50d2f-112">Na konci tohoto kurzu budete mít Razor webovou aplikaci pracovní stránky, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="50d2f-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="50d2f-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="50d2f-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="50d2f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50d2f-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="50d2f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="50d2f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="50d2f-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="50d2f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="50d2f-118">Vytvoření Razor webové aplikace Pages</span><span class="sxs-lookup"><span data-stu-id="50d2f-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="50d2f-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50d2f-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="50d2f-120">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="50d2f-121">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="50d2f-122">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="50d2f-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="50d2f-123">Pojmenujte projekt \*\* Razor PagesMovie\*\*.</span><span class="sxs-lookup"><span data-stu-id="50d2f-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="50d2f-124">Je důležité pojmenovat projekt \* Razor PagesMovie\* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="50d2f-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="50d2f-125">![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="50d2f-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="50d2f-126">V rozevíracím seznamu vyberte položku **ASP.NET Core 3,1** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="50d2f-128">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="50d2f-128">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="50d2f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="50d2f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="50d2f-131">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="50d2f-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="50d2f-132">Přejděte do adresáře ( `cd` ), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="50d2f-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="50d2f-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="50d2f-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="50d2f-134">`dotnet new`Příkaz vytvoří nový Razor projekt stránky ve složce \* Razor PagesMovie\* .</span><span class="sxs-lookup"><span data-stu-id="50d2f-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="50d2f-135">`code`Příkaz otevře složku \* Razor PagesMovie\* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="50d2f-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="50d2f-136">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' Razor PagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="50d2f-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="50d2f-137">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-137">Select **Yes**.</span></span>

  <span data-ttu-id="50d2f-138">Do kořenového adresáře projektu se přidá adresář *. VSCode* , který obsahuje *launch.jsv* souborech a *tasks.jsna* nich.</span><span class="sxs-lookup"><span data-stu-id="50d2f-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="50d2f-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="50d2f-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="50d2f-140">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-140">Select **File** > **New Solution**.</span></span>

  ![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="50d2f-142">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web aplikace**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="50d2f-143">V části verze 8,6 nebo novější vyberte webová aplikace **Webová aplikace a**  >  **aplikace**konzoly webové  >  **aplikace**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Výběr šablony webové aplikace v macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="50d2f-145">V dialogovém okně **Konfigurace nové webové aplikace** :</span><span class="sxs-lookup"><span data-stu-id="50d2f-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="50d2f-146">Potvrďte, že **ověřování** je nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="50d2f-147">Pokud se zobrazí možnost výběru **cílové platformy**, vyberte nejnovější verzi 3. x.</span><span class="sxs-lookup"><span data-stu-id="50d2f-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="50d2f-148">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-148">Select **Next**.</span></span>

* <span data-ttu-id="50d2f-149">Pojmenujte projekt \*\* Razor PagesMovie\*\*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS název projektu](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="50d2f-151">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="50d2f-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="50d2f-152">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="50d2f-152">Examine the project files</span></span>

<span data-ttu-id="50d2f-153">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="50d2f-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="50d2f-154">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="50d2f-154">Pages folder</span></span>

<span data-ttu-id="50d2f-155">Obsahuje Razor stránky a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="50d2f-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="50d2f-156">Každá Razor stránka je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="50d2f-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="50d2f-157">Soubor *. cshtml* , který obsahuje značky HTML s kódem jazyka C# pomocí Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="50d2f-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="50d2f-158">Soubor *. cshtml.cs* , který obsahuje kód jazyka C#, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="50d2f-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="50d2f-159">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="50d2f-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="50d2f-160">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="50d2f-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="50d2f-161">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="50d2f-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="50d2f-162">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="50d2f-163">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="50d2f-163">wwwroot folder</span></span>

<span data-ttu-id="50d2f-164">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="50d2f-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="50d2f-165">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="50d2f-166">appSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="50d2f-166">appSettings.json</span></span>

<span data-ttu-id="50d2f-167">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="50d2f-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="50d2f-168">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="50d2f-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="50d2f-169">Program.cs</span></span>

<span data-ttu-id="50d2f-170">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="50d2f-170">Contains the entry point for the program.</span></span> <span data-ttu-id="50d2f-171">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="50d2f-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="50d2f-172">Startup.cs</span></span>

<span data-ttu-id="50d2f-173">Obsahuje kód, který konfiguruje chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="50d2f-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="50d2f-174">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="50d2f-175">Další kroky</span><span class="sxs-lookup"><span data-stu-id="50d2f-175">Next steps</span></span>

<span data-ttu-id="50d2f-176">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="50d2f-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="50d2f-177">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="50d2f-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="50d2f-178">Toto je první kurz řady.</span><span class="sxs-lookup"><span data-stu-id="50d2f-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="50d2f-179">[Série](xref:tutorials/razor-pages/index) se učí základy vytvoření Razor webové aplikace ASP.NET Core Pages.</span><span class="sxs-lookup"><span data-stu-id="50d2f-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="50d2f-180">Na konci série budete mít aplikaci, která spravuje databázi filmů.</span><span class="sxs-lookup"><span data-stu-id="50d2f-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="50d2f-181">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="50d2f-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="50d2f-182">Vytvoření Razor webové aplikace Pages</span><span class="sxs-lookup"><span data-stu-id="50d2f-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="50d2f-183">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="50d2f-183">Run the app.</span></span>
> * <span data-ttu-id="50d2f-184">Prověřte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="50d2f-184">Examine the project files.</span></span>

<span data-ttu-id="50d2f-185">Na konci tohoto kurzu budete mít Razor webovou aplikaci pracovní stránky, kterou vytvoříte v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="50d2f-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="50d2f-187">Požadavky</span><span class="sxs-lookup"><span data-stu-id="50d2f-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="50d2f-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50d2f-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="50d2f-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="50d2f-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="50d2f-190">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="50d2f-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="50d2f-191">Vytvoření Razor webové aplikace Pages</span><span class="sxs-lookup"><span data-stu-id="50d2f-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="50d2f-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50d2f-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="50d2f-193">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="50d2f-194">Vytvořte novou ASP.NET Core webovou aplikaci a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="50d2f-196">Pojmenujte projekt \*\* Razor PagesMovie\*\*.</span><span class="sxs-lookup"><span data-stu-id="50d2f-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="50d2f-197">Je důležité pojmenovat projekt \* Razor PagesMovie\* , aby se obory názvů shodovaly při kopírování a vkládání kódu.</span><span class="sxs-lookup"><span data-stu-id="50d2f-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/config.png)

* <span data-ttu-id="50d2f-199">V rozevíracím seznamu vyberte položku **ASP.NET Core 2,2** **a potom**vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová ASP.NET Core webové aplikace](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="50d2f-201">Vytvoří se následující počáteční projekt:</span><span class="sxs-lookup"><span data-stu-id="50d2f-201">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="50d2f-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="50d2f-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="50d2f-204">Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="50d2f-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="50d2f-205">Přejděte do adresáře ( `cd` ), který bude obsahovat projekt.</span><span class="sxs-lookup"><span data-stu-id="50d2f-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="50d2f-206">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="50d2f-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="50d2f-207">`dotnet new`Příkaz vytvoří nový Razor projekt stránky ve složce \* Razor PagesMovie\* .</span><span class="sxs-lookup"><span data-stu-id="50d2f-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="50d2f-208">`code`Příkaz otevře složku \* Razor PagesMovie\* v aktuální instanci Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="50d2f-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="50d2f-209">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' Razor PagesMovie '. Přidat je?**</span><span class="sxs-lookup"><span data-stu-id="50d2f-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="50d2f-210">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-210">Select **Yes**.</span></span>

  <span data-ttu-id="50d2f-211">Do kořenového adresáře projektu se přidá adresář *. VSCode* , který obsahuje *launch.jsv* souborech a *tasks.jsna* nich.</span><span class="sxs-lookup"><span data-stu-id="50d2f-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="50d2f-212">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="50d2f-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="50d2f-213">Vyberte **soubor** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-213">Select **File** > **New Solution**.</span></span>

![macOS nové řešení](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="50d2f-215">V Visual Studio pro Mac starší než verze 8,6 vyberte možnost Webová aplikace **.NET Core**  >  **App**  >  **Web aplikace**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="50d2f-216">V části verze 8,6 nebo novější vyberte webová aplikace **Webová aplikace a**  >  **aplikace**konzoly webové  >  **aplikace**  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="50d2f-217">V dialogovém okně **Konfigurace nové webové aplikace** :</span><span class="sxs-lookup"><span data-stu-id="50d2f-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="50d2f-218">Potvrďte, že **ověřování** je nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="50d2f-219">Pokud se zobrazí možnost výběru **cílové platformy**, vyberte nejnovější verzi 2. x.</span><span class="sxs-lookup"><span data-stu-id="50d2f-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="50d2f-220">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-220">Select **Next**.</span></span>

* <span data-ttu-id="50d2f-221">Pojmenujte projekt \*\* Razor PagesMovie\*\*a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="50d2f-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="50d2f-223">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="50d2f-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="50d2f-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50d2f-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="50d2f-225">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="50d2f-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="50d2f-226">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="50d2f-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="50d2f-227">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="50d2f-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="50d2f-228">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="50d2f-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="50d2f-229">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="50d2f-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="50d2f-230">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="50d2f-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="50d2f-231">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="50d2f-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="50d2f-232">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="50d2f-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="50d2f-234">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="50d2f-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="50d2f-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="50d2f-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="50d2f-237">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="50d2f-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="50d2f-238">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="50d2f-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="50d2f-239">Panel Adresa se zobrazí `localhost:port#` a ne něco jako `example.com` .</span><span class="sxs-lookup"><span data-stu-id="50d2f-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="50d2f-240">To je proto, že `localhost` se jedná o standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="50d2f-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="50d2f-241">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="50d2f-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="50d2f-242">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="50d2f-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="50d2f-243">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="50d2f-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="50d2f-245">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="50d2f-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="50d2f-247">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="50d2f-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="50d2f-248">Stisknutím klávesy **cmd a opt-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="50d2f-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="50d2f-249">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="50d2f-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="50d2f-250">Na domovské stránce aplikace vyberte **přijmout** pro vyjádření souhlasu se sledováním.</span><span class="sxs-lookup"><span data-stu-id="50d2f-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="50d2f-251">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci souhlasu pro případ, že ji budete potřebovat pro splnění Obecné nařízení o ochraně osobních údajů Evropské unie [(GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="50d2f-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="50d2f-253">Následující obrázek zobrazí aplikaci po poskytnutí souhlasu se sledováním:</span><span class="sxs-lookup"><span data-stu-id="50d2f-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Stránka domů nebo index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="50d2f-255">Prověřte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="50d2f-255">Examine the project files</span></span>

<span data-ttu-id="50d2f-256">Tady je přehled hlavních složek projektu a souborů, se kterými budete pracovat v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="50d2f-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="50d2f-257">Složka stránky</span><span class="sxs-lookup"><span data-stu-id="50d2f-257">Pages folder</span></span>

<span data-ttu-id="50d2f-258">Obsahuje Razor stránky a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="50d2f-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="50d2f-259">Každá Razor stránka je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="50d2f-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="50d2f-260">Soubor *. cshtml* , který obsahuje značky HTML s kódem jazyka C# pomocí Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="50d2f-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="50d2f-261">Soubor *. cshtml.cs* , který obsahuje kód jazyka C#, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="50d2f-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="50d2f-262">Podpůrné soubory mají názvy začínající podtržítkem.</span><span class="sxs-lookup"><span data-stu-id="50d2f-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="50d2f-263">Například soubor *_Layout. cshtml* nakonfiguruje prvky uživatelského rozhraní společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="50d2f-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="50d2f-264">Tento soubor nastaví navigační nabídku v horní části stránky a oznámení o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="50d2f-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="50d2f-265">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="50d2f-266">Složka wwwroot</span><span class="sxs-lookup"><span data-stu-id="50d2f-266">wwwroot folder</span></span>

<span data-ttu-id="50d2f-267">Obsahuje statické soubory, jako jsou soubory HTML, JavaScriptové soubory a soubory CSS.</span><span class="sxs-lookup"><span data-stu-id="50d2f-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="50d2f-268">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="50d2f-269">appSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="50d2f-269">appSettings.json</span></span>

<span data-ttu-id="50d2f-270">Obsahuje konfigurační data, například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="50d2f-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="50d2f-271">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="50d2f-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="50d2f-272">Program.cs</span></span>

<span data-ttu-id="50d2f-273">Obsahuje vstupní bod pro program.</span><span class="sxs-lookup"><span data-stu-id="50d2f-273">Contains the entry point for the program.</span></span> <span data-ttu-id="50d2f-274">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="50d2f-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="50d2f-275">Startup.cs</span></span>

<span data-ttu-id="50d2f-276">Obsahuje kód, který konfiguruje chování aplikace, například zda vyžaduje souhlas cookie s.</span><span class="sxs-lookup"><span data-stu-id="50d2f-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="50d2f-277">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="50d2f-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50d2f-278">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="50d2f-278">Additional resources</span></span>

* [<span data-ttu-id="50d2f-279">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="50d2f-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="50d2f-280">Další kroky</span><span class="sxs-lookup"><span data-stu-id="50d2f-280">Next steps</span></span>

<span data-ttu-id="50d2f-281">Přejděte k dalšímu kurzu v řadě:</span><span class="sxs-lookup"><span data-stu-id="50d2f-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="50d2f-282">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="50d2f-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

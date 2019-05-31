---
title: 'Kurz: Začínáme se stránkami Razor v ASP.NET Core'
author: rick-anderson
description: Tato série kurzů ukazuje, jak používat v ASP.NET Core Razor Pages. Zjistěte, jak vytvořit model, generování kódu pro stránky Razor, použít pro přístup k datům Entity Framework Core a SQL Server, vyhledávání, přidat ověřování vstupu a použití migrace k aktualizaci modelu.
ms.author: riande
ms.date: 05/30/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: e9f11f68aa138ab74a0ffbbd0e32067bc984606d
ms.sourcegitcommit: 9ae1fd11f39b0a72b2ae42f0b450345e6e306bc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66415663"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="c0c96-104">Kurz: Začínáme se stránkami Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0c96-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="c0c96-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0c96-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c0c96-106">Toto je první kurz z řady.</span><span class="sxs-lookup"><span data-stu-id="c0c96-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="c0c96-107">[Série](xref:tutorials/razor-pages/index) vás naučí základy vytváření webové aplikace ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c0c96-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="c0c96-108">Na konci série budete mít aplikaci, která spravuje databázi videa.</span><span class="sxs-lookup"><span data-stu-id="c0c96-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="c0c96-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="c0c96-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c0c96-110">Vytvoření webové aplikace stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="c0c96-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="c0c96-111">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c0c96-111">Run the app.</span></span>
> * <span data-ttu-id="c0c96-112">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="c0c96-112">Examine the project files.</span></span>

<span data-ttu-id="c0c96-113">Na konci tohoto kurzu budete mít funkční webová aplikace Razor Pages, na kterém budete stavět v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="c0c96-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="c0c96-115">Vytvoření webové aplikace stránky Razor</span><span class="sxs-lookup"><span data-stu-id="c0c96-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c0c96-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c96-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c0c96-117">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="c0c96-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="c0c96-118">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c0c96-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="c0c96-119">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="c0c96-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="c0c96-120">Je důležité projekt pojmenujte *RazorPagesMovie* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.</span><span class="sxs-lookup"><span data-stu-id="c0c96-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="c0c96-122">Vyberte **2.2 technologie ASP.NET Core** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="c0c96-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="c0c96-124">Následující počáteční projekt je vytvořen:</span><span class="sxs-lookup"><span data-stu-id="c0c96-124">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c0c96-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0c96-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c0c96-127">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c0c96-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c0c96-128">Přejděte do adresáře (`cd`) která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="c0c96-128">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="c0c96-129">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c0c96-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="c0c96-130">`dotnet new` Příkaz vytvoří nový projekt v Razor Pages *RazorPagesMovie* složky.</span><span class="sxs-lookup"><span data-stu-id="c0c96-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="c0c96-131">`code` Příkaz otevře *RazorPagesMovie* složku v aktuální instanci aplikace Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c0c96-131">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="c0c96-132">Po stavový řádek OmniSharp bezpečnostní opatření ikona se změní na zelenou, dialogové okno s dotazem **'RazorPagesMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="c0c96-132">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="c0c96-133">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="c0c96-133">Select **Yes**.</span></span>

  <span data-ttu-id="c0c96-134">A *.vscode* adresáře, který obsahuje *launch.json* a *tasks.json* soubory, se přidá do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="c0c96-134">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c0c96-135">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c0c96-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c0c96-136">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c0c96-136">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="c0c96-137">Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření projektu pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="c0c96-137">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="c0c96-138">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="c0c96-138">Open the project</span></span>

<span data-ttu-id="c0c96-139">Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *RazorPagesMovie.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="c0c96-139">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c0c96-140">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="c0c96-140">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c0c96-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c96-141">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c0c96-142">Stisknutím kláves Ctrl + F5 ke spuštění bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="c0c96-142">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c0c96-143">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c0c96-143">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c0c96-144">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c0c96-144">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c0c96-145">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="c0c96-145">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c0c96-146">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="c0c96-146">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="c0c96-147">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="c0c96-147">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="c0c96-148">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="c0c96-148">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c0c96-149">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c0c96-149">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c0c96-151">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="c0c96-151">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c0c96-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0c96-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="c0c96-154">Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="c0c96-154">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c0c96-155">Spustí Visual Studio Code [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c0c96-155">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="c0c96-156">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c0c96-156">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c0c96-157">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="c0c96-157">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c0c96-158">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="c0c96-158">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="c0c96-159">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="c0c96-159">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c0c96-160">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c0c96-160">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c0c96-162">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="c0c96-162">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c0c96-164">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c0c96-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c0c96-165">Stisknutím klávesy **Cmd optimalizované F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="c0c96-165">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c0c96-166">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c0c96-166">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="c0c96-167">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="c0c96-167">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c0c96-168">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c0c96-168">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="c0c96-170">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="c0c96-170">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="c0c96-172">Zkontrolujte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="c0c96-172">Examine the project files</span></span>

<span data-ttu-id="c0c96-173">Tady je přehled hlavní projekt složek a souborů, které budete pracovat v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="c0c96-173">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c0c96-174">Složka stránek</span><span class="sxs-lookup"><span data-stu-id="c0c96-174">Pages folder</span></span>

<span data-ttu-id="c0c96-175">Obsahuje Razor pages a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="c0c96-175">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="c0c96-176">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="c0c96-176">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="c0c96-177">A *.cshtml* soubor, který obsahuje kód HTML s C# kód používající syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="c0c96-177">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="c0c96-178">A *. cshtml.cs* soubor, který obsahuje C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="c0c96-178">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="c0c96-179">Podpůrné soubory mají názvy, které začínají znakem podtržítka.</span><span class="sxs-lookup"><span data-stu-id="c0c96-179">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c0c96-180">Například *_Layout.cshtml* soubor nastaví prvky uživatelského rozhraní, které jsou společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="c0c96-180">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c0c96-181">Tento soubor nastaví navigační nabídce v horní části stránky a o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="c0c96-181">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c0c96-182">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c0c96-182">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c0c96-183">složku Wwwroot</span><span class="sxs-lookup"><span data-stu-id="c0c96-183">wwwroot folder</span></span>

<span data-ttu-id="c0c96-184">Obsahuje statické soubory, jako jsou soubory HTML, soubory jazyka JavaScript a soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="c0c96-184">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c0c96-185">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c0c96-185">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c0c96-186">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="c0c96-186">appSettings.json</span></span>

<span data-ttu-id="c0c96-187">Obsahuje konfigurační data, jako je například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="c0c96-187">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="c0c96-188">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c0c96-188">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c0c96-189">Program.cs</span><span class="sxs-lookup"><span data-stu-id="c0c96-189">Program.cs</span></span>

<span data-ttu-id="c0c96-190">Obsahuje vstupní bod programu.</span><span class="sxs-lookup"><span data-stu-id="c0c96-190">Contains the entry point for the program.</span></span> <span data-ttu-id="c0c96-191">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="c0c96-191">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c0c96-192">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c0c96-192">Startup.cs</span></span>

<span data-ttu-id="c0c96-193">Obsahuje kód, který nakonfiguruje chování aplikace, jako je například jestli vyžaduje souhlas pro soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="c0c96-193">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="c0c96-194">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c0c96-194">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0c96-195">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c0c96-195">Additional resources</span></span>

* [<span data-ttu-id="c0c96-196">Verzi tohoto kurzu na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="c0c96-196">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="c0c96-197">Další kroky</span><span class="sxs-lookup"><span data-stu-id="c0c96-197">Next steps</span></span>

<span data-ttu-id="c0c96-198">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="c0c96-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c0c96-199">Vytvoření webové aplikace stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="c0c96-199">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="c0c96-200">Spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c0c96-200">Ran the app.</span></span>
> * <span data-ttu-id="c0c96-201">Prozkoumat soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="c0c96-201">Examined the project files.</span></span>

<span data-ttu-id="c0c96-202">Přejděte k dalšímu kurzu v této sérii:</span><span class="sxs-lookup"><span data-stu-id="c0c96-202">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c0c96-203">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="c0c96-203">Add a model</span></span>](xref:tutorials/razor-pages/model)

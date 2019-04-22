---
title: 'Kurz: Začínáme se stránkami Razor v ASP.NET Core'
author: rick-anderson
description: Tato série kurzů ukazuje, jak používat v ASP.NET Core Razor Pages. Zjistěte, jak vytvořit model, generování kódu pro stránky Razor, použít pro přístup k datům Entity Framework Core a SQL Server, vyhledávání, přidat ověřování vstupu a použití migrace k aktualizaci modelu.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1d264ca4a605d8291e273a8f054c92e7eefa5548
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468844"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="772d3-104">Kurz: Začínáme se stránkami Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="772d3-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="772d3-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="772d3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="772d3-106">Toto je první kurz z řady.</span><span class="sxs-lookup"><span data-stu-id="772d3-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="772d3-107">[Série](xref:tutorials/razor-pages/index) vás naučí základy vytváření webové aplikace ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="772d3-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="772d3-108">Na konci série budete mít aplikaci, která spravuje databázi videa.</span><span class="sxs-lookup"><span data-stu-id="772d3-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="772d3-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="772d3-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="772d3-110">Vytvoření webové aplikace stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="772d3-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="772d3-111">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="772d3-111">Run the app.</span></span>
> * <span data-ttu-id="772d3-112">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="772d3-112">Examine the project files.</span></span>

<span data-ttu-id="772d3-113">Na konci tohoto kurzu budete mít funkční webové aplikace stránky Razor, na kterém budete stavět v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="772d3-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="772d3-115">Vytvoření webové aplikace stránky Razor</span><span class="sxs-lookup"><span data-stu-id="772d3-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="772d3-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="772d3-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="772d3-117">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="772d3-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="772d3-118">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="772d3-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="772d3-119">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="772d3-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="772d3-120">Je důležité projekt pojmenujte *RazorPagesMovie* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.</span><span class="sxs-lookup"><span data-stu-id="772d3-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="772d3-122">Vyberte **2.2 technologie ASP.NET Core** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="772d3-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="772d3-124">Následující počáteční projekt je vytvořen:</span><span class="sxs-lookup"><span data-stu-id="772d3-124">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="772d3-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="772d3-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="772d3-127">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="772d3-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="772d3-128">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="772d3-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="772d3-129">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="772d3-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="772d3-130">`dotnet new` Příkaz vytvoří nový projekt v Razor Pages *RazorPagesMovie* složky.</span><span class="sxs-lookup"><span data-stu-id="772d3-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="772d3-131">`code` Příkaz otevře *RazorPagesMovie* složky v nové instanci sady Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="772d3-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="772d3-132">Zobrazí se dialogové okno s **'RazorPagesMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="772d3-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="772d3-133">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="772d3-133">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="772d3-134">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="772d3-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="772d3-135">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="772d3-135">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="772d3-136">Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření projektu pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="772d3-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="772d3-137">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="772d3-137">Open the project</span></span>

<span data-ttu-id="772d3-138">Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *RazorPagesMovie.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="772d3-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="772d3-139">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="772d3-139">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="772d3-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="772d3-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="772d3-141">Stisknutím kláves Ctrl + F5 ke spuštění bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="772d3-141">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="772d3-142">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="772d3-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="772d3-143">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="772d3-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="772d3-144">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="772d3-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="772d3-145">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="772d3-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="772d3-146">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="772d3-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="772d3-147">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="772d3-147">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="772d3-148">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="772d3-148">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="772d3-150">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="772d3-150">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="772d3-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="772d3-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="772d3-153">Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="772d3-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="772d3-154">Spustí Visual Studio Code [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="772d3-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="772d3-155">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="772d3-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="772d3-156">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="772d3-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="772d3-157">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="772d3-157">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="772d3-158">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="772d3-158">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="772d3-159">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="772d3-159">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="772d3-161">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="772d3-161">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="772d3-163">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="772d3-163">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="772d3-164">Stisknutím klávesy **Cmd optimalizované F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="772d3-164">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="772d3-165">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="772d3-165">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="772d3-166">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="772d3-166">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="772d3-167">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="772d3-167">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="772d3-169">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="772d3-169">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="772d3-171">Zkontrolujte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="772d3-171">Examine the project files</span></span>

<span data-ttu-id="772d3-172">Tady je přehled hlavní projekt složek a souborů, které budete pracovat v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="772d3-172">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="772d3-173">Složka stránek</span><span class="sxs-lookup"><span data-stu-id="772d3-173">Pages folder</span></span>

<span data-ttu-id="772d3-174">Obsahuje Razor pages a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="772d3-174">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="772d3-175">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="772d3-175">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="772d3-176">A *.cshtml* soubor, který obsahuje kód HTML s C# kód používající syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="772d3-176">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="772d3-177">A *. cshtml.cs* soubor, který obsahuje C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="772d3-177">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="772d3-178">Podpůrné soubory mají názvy, které začínají znakem podtržítka.</span><span class="sxs-lookup"><span data-stu-id="772d3-178">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="772d3-179">Například *_Layout.cshtml* soubor nastaví prvky uživatelského rozhraní, které jsou společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="772d3-179">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="772d3-180">Tento soubor nastaví navigační nabídce v horní části stránky a o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="772d3-180">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="772d3-181">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="772d3-181">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="772d3-182">složku Wwwroot</span><span class="sxs-lookup"><span data-stu-id="772d3-182">wwwroot folder</span></span>

<span data-ttu-id="772d3-183">Obsahuje statické soubory, jako jsou soubory HTML, soubory jazyka JavaScript a soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="772d3-183">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="772d3-184">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="772d3-184">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="772d3-185">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="772d3-185">appSettings.json</span></span>

<span data-ttu-id="772d3-186">Obsahuje konfigurační data, jako je například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="772d3-186">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="772d3-187">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="772d3-187">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="772d3-188">Program.cs</span><span class="sxs-lookup"><span data-stu-id="772d3-188">Program.cs</span></span>

<span data-ttu-id="772d3-189">Obsahuje vstupní bod programu.</span><span class="sxs-lookup"><span data-stu-id="772d3-189">Contains the entry point for the program.</span></span> <span data-ttu-id="772d3-190">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="772d3-190">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="772d3-191">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="772d3-191">Startup.cs</span></span>

<span data-ttu-id="772d3-192">Obsahuje kód, který nakonfiguruje chování aplikace, jako je například jestli vyžaduje souhlas pro soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="772d3-192">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="772d3-193">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="772d3-193">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="772d3-194">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="772d3-194">Additional resources</span></span>

* [<span data-ttu-id="772d3-195">Verzi tohoto kurzu na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="772d3-195">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="772d3-196">Další kroky</span><span class="sxs-lookup"><span data-stu-id="772d3-196">Next steps</span></span>

<span data-ttu-id="772d3-197">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="772d3-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="772d3-198">Vytvoření webové aplikace stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="772d3-198">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="772d3-199">Spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="772d3-199">Ran the app.</span></span>
> * <span data-ttu-id="772d3-200">Prozkoumat soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="772d3-200">Examined the project files.</span></span>

<span data-ttu-id="772d3-201">Přejděte k dalšímu kurzu v této sérii:</span><span class="sxs-lookup"><span data-stu-id="772d3-201">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="772d3-202">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="772d3-202">Add a model</span></span>](xref:tutorials/razor-pages/model)

---
title: 'Kurz: Začínáme se stránkami Razor v ASP.NET Core'
author: rick-anderson
description: Tato série kurzů ukazuje, jak používat v ASP.NET Core Razor Pages. Zjistěte, jak vytvořit model, generování kódu pro stránky Razor, použít pro přístup k datům Entity Framework Core a SQL Server, vyhledávání, přidat ověřování vstupu a použití migrace k aktualizaci modelu.
ms.author: riande
ms.date: 06/3/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b0cbb4dda5328d305686f21a0b3e8f8e83575a39
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815265"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="83d2c-104">Kurz: Začínáme se stránkami Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83d2c-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="83d2c-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83d2c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="83d2c-106">Toto je první kurz z řady.</span><span class="sxs-lookup"><span data-stu-id="83d2c-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="83d2c-107">[Série](xref:tutorials/razor-pages/index) vás naučí základy vytváření webové aplikace ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="83d2c-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="83d2c-108">Na konci série budete mít aplikaci, která spravuje databázi videa.</span><span class="sxs-lookup"><span data-stu-id="83d2c-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="83d2c-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="83d2c-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="83d2c-110">Vytvoření webové aplikace stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="83d2c-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="83d2c-111">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="83d2c-111">Run the app.</span></span>
> * <span data-ttu-id="83d2c-112">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="83d2c-112">Examine the project files.</span></span>

<span data-ttu-id="83d2c-113">Na konci tohoto kurzu budete mít funkční webová aplikace Razor Pages, na kterém budete stavět v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="83d2c-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="83d2c-115">Požadavky</span><span class="sxs-lookup"><span data-stu-id="83d2c-115">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d2c-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d2c-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d2c-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d2c-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d2c-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="83d2c-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="83d2c-119">Vytvoření webové aplikace stránky Razor</span><span class="sxs-lookup"><span data-stu-id="83d2c-119">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d2c-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d2c-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d2c-121">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="83d2c-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="83d2c-122">Vytvořit novou webovou aplikaci ASP.NET Core a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="83d2c-122">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="83d2c-124">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="83d2c-124">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="83d2c-125">Je důležité projekt pojmenujte *RazorPagesMovie* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.</span><span class="sxs-lookup"><span data-stu-id="83d2c-125">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="83d2c-127">Vyberte **2.2 technologie ASP.NET Core** v rozevírací nabídce, **webovou aplikaci**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="83d2c-127">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="83d2c-129">Následující počáteční projekt je vytvořen:</span><span class="sxs-lookup"><span data-stu-id="83d2c-129">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d2c-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d2c-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83d2c-132">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="83d2c-132">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="83d2c-133">Přejděte do adresáře (`cd`) která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="83d2c-133">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="83d2c-134">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="83d2c-134">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="83d2c-135">`dotnet new` Příkaz vytvoří nový projekt v Razor Pages *RazorPagesMovie* složky.</span><span class="sxs-lookup"><span data-stu-id="83d2c-135">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="83d2c-136">`code` Příkaz otevře *RazorPagesMovie* složku v aktuální instanci aplikace Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="83d2c-136">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="83d2c-137">Po stavový řádek OmniSharp bezpečnostní opatření ikona se změní na zelenou, dialogové okno s dotazem **'RazorPagesMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="83d2c-137">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="83d2c-138">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="83d2c-138">Select **Yes**.</span></span>

  <span data-ttu-id="83d2c-139">A *.vscode* adresáře, který obsahuje *launch.json* a *tasks.json* soubory, se přidá do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="83d2c-139">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d2c-140">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="83d2c-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="83d2c-141">Z terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="83d2c-141">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="83d2c-142">Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření projektu pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="83d2c-142">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="83d2c-143">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="83d2c-143">Open the project</span></span>

<span data-ttu-id="83d2c-144">Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *RazorPagesMovie.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="83d2c-144">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="83d2c-145">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="83d2c-145">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83d2c-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83d2c-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83d2c-147">Stisknutím kláves Ctrl + F5 ke spuštění bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="83d2c-147">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="83d2c-148">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="83d2c-148">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="83d2c-149">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="83d2c-149">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="83d2c-150">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="83d2c-150">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="83d2c-151">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="83d2c-151">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="83d2c-152">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="83d2c-152">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="83d2c-153">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="83d2c-153">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="83d2c-154">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="83d2c-154">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="83d2c-156">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="83d2c-156">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83d2c-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83d2c-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="83d2c-159">Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="83d2c-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="83d2c-160">Spustí Visual Studio Code [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="83d2c-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="83d2c-161">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="83d2c-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="83d2c-162">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="83d2c-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="83d2c-163">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="83d2c-163">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="83d2c-164">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="83d2c-164">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="83d2c-165">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="83d2c-165">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="83d2c-167">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="83d2c-167">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83d2c-169">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="83d2c-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="83d2c-170">Stisknutím klávesy **Cmd optimalizované F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="83d2c-170">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="83d2c-171">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="83d2c-171">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="83d2c-172">Na domovské stránce aplikace vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="83d2c-172">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="83d2c-173">Tato aplikace nesleduje osobní údaje, ale šablona projektu zahrnuje funkci pro vyjádření souhlasu v případě, že potřebujete v souladu s Evropské unie [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="83d2c-173">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="83d2c-175">Následující obrázek znázorňuje aplikaci po udělení souhlasu doplňku sledování:</span><span class="sxs-lookup"><span data-stu-id="83d2c-175">The following image shows the app after you give consent to tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="83d2c-177">Zkontrolujte soubory projektu</span><span class="sxs-lookup"><span data-stu-id="83d2c-177">Examine the project files</span></span>

<span data-ttu-id="83d2c-178">Tady je přehled hlavní projekt složek a souborů, které budete pracovat v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="83d2c-178">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="83d2c-179">Složka stránek</span><span class="sxs-lookup"><span data-stu-id="83d2c-179">Pages folder</span></span>

<span data-ttu-id="83d2c-180">Obsahuje Razor pages a podpůrné soubory.</span><span class="sxs-lookup"><span data-stu-id="83d2c-180">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="83d2c-181">Každá stránka Razor je pár souborů:</span><span class="sxs-lookup"><span data-stu-id="83d2c-181">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="83d2c-182">A *.cshtml* soubor, který obsahuje kód HTML s C# kód používající syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="83d2c-182">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="83d2c-183">A *. cshtml.cs* soubor, který obsahuje C# kód, který zpracovává události stránky.</span><span class="sxs-lookup"><span data-stu-id="83d2c-183">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="83d2c-184">Podpůrné soubory mají názvy, které začínají znakem podtržítka.</span><span class="sxs-lookup"><span data-stu-id="83d2c-184">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="83d2c-185">Například *_Layout.cshtml* soubor nastaví prvky uživatelského rozhraní, které jsou společné pro všechny stránky.</span><span class="sxs-lookup"><span data-stu-id="83d2c-185">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="83d2c-186">Tento soubor nastaví navigační nabídce v horní části stránky a o autorských právech v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="83d2c-186">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="83d2c-187">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="83d2c-187">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="83d2c-188">složku Wwwroot</span><span class="sxs-lookup"><span data-stu-id="83d2c-188">wwwroot folder</span></span>

<span data-ttu-id="83d2c-189">Obsahuje statické soubory, jako jsou soubory HTML, soubory jazyka JavaScript a soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="83d2c-189">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="83d2c-190">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="83d2c-190">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="83d2c-191">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="83d2c-191">appSettings.json</span></span>

<span data-ttu-id="83d2c-192">Obsahuje konfigurační data, jako je například připojovací řetězce.</span><span class="sxs-lookup"><span data-stu-id="83d2c-192">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="83d2c-193">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="83d2c-193">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="83d2c-194">Program.cs</span><span class="sxs-lookup"><span data-stu-id="83d2c-194">Program.cs</span></span>

<span data-ttu-id="83d2c-195">Obsahuje vstupní bod programu.</span><span class="sxs-lookup"><span data-stu-id="83d2c-195">Contains the entry point for the program.</span></span> <span data-ttu-id="83d2c-196">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="83d2c-196">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="83d2c-197">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="83d2c-197">Startup.cs</span></span>

<span data-ttu-id="83d2c-198">Obsahuje kód, který nakonfiguruje chování aplikace, jako je například jestli vyžaduje souhlas pro soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="83d2c-198">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="83d2c-199">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="83d2c-199">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83d2c-200">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="83d2c-200">Additional resources</span></span>

* [<span data-ttu-id="83d2c-201">Verzi tohoto kurzu na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="83d2c-201">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="83d2c-202">Další postup</span><span class="sxs-lookup"><span data-stu-id="83d2c-202">Next steps</span></span>

<span data-ttu-id="83d2c-203">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="83d2c-203">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="83d2c-204">Vytvoření webové aplikace stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="83d2c-204">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="83d2c-205">Spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="83d2c-205">Ran the app.</span></span>
> * <span data-ttu-id="83d2c-206">Prozkoumat soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="83d2c-206">Examined the project files.</span></span>

<span data-ttu-id="83d2c-207">Přejděte k dalšímu kurzu v této sérii:</span><span class="sxs-lookup"><span data-stu-id="83d2c-207">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="83d2c-208">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="83d2c-208">Add a model</span></span>](xref:tutorials/razor-pages/model)

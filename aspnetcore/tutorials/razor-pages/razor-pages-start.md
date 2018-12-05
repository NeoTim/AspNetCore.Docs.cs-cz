---
title: Začínáme se stránkami Razor v ASP.NET Core
author: rick-anderson
monikerRange: '>= aspnetcore-2.2'
description: Tato série kurzů ukazuje, jak používat v ASP.NET Core Razor Pages. Zjistěte, jak vytvořit model, generování kódu pro stránky Razor, použít pro přístup k datům Entity Framework Core a SQL Server, vyhledávání, přidat ověřování vstupu a použití migrace k aktualizaci modelu.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1152ebfcee48a46ecd28c941fce32d3fc1e05c41
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861625"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="eb7dc-104">Kurz: Začínáme se stránkami Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb7dc-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="eb7dc-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eb7dc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="eb7dc-106">V tomto kurzu se naučíte se základy vytváření webové aplikace ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-106">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="eb7dc-107">Aplikaci spravuje databáze filmů produktů.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-107">The app manages a database of movie titles.</span></span> <span data-ttu-id="eb7dc-108">Získáte informace o následujících postupech:</span><span class="sxs-lookup"><span data-stu-id="eb7dc-108">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eb7dc-109">Vytvoření webové aplikace stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="eb7dc-110">Přidat a generování uživatelského rozhraní modelu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-110">Add and scaffold a model.</span></span>
> * <span data-ttu-id="eb7dc-111">Práce s databází.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-111">Work with a database.</span></span>
> * <span data-ttu-id="eb7dc-112">Přidáte vyhledávání a ověřování.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-112">Add search and validation.</span></span>

<span data-ttu-id="eb7dc-113">Na konci máte aplikaci, která můžete spravovat a zobrazit film názvy položek.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-113">At the end, you have an app that can manage and display movie titles items.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="eb7dc-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="eb7dc-114">Prerequisites</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="eb7dc-115">Vytvoření webové aplikace Razor</span><span class="sxs-lookup"><span data-stu-id="eb7dc-115">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eb7dc-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb7dc-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eb7dc-117">Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="eb7dc-118">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="eb7dc-119">Pojmenujte projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="eb7dc-120">Je důležité projekt pojmenujte *RazorPagesMovie* tak obory názvů budou porovnávány názvy při zkopírujete/vložíte kód.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy/paste code.</span></span>
 <span data-ttu-id="eb7dc-121">![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="eb7dc-121">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>

* <span data-ttu-id="eb7dc-122">Vyberte **2.2 technologie ASP.NET Core** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![Nová webová aplikace ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="eb7dc-124">Následující počáteční projekt je vytvořen:</span><span class="sxs-lookup"><span data-stu-id="eb7dc-124">The following starter project is created:</span></span>

  ![Průzkumník řešení](razor-pages-start/_static/se2.2.png)

* <span data-ttu-id="eb7dc-126">Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-126">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="eb7dc-127">Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-127">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="eb7dc-128">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-128">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eb7dc-129">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-129">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="eb7dc-130">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-130">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="eb7dc-131">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-131">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="eb7dc-132">Na předchozím obrázku číslo portu je 5001.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-132">In the preceding image, the port number is 5001.</span></span> <span data-ttu-id="eb7dc-133">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-133">When you run the app, you'll see a different port number.</span></span>

  <span data-ttu-id="eb7dc-134">Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-134">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="eb7dc-135">Mnoho vývojářů dávají přednost používání režimu bez ladění a aktualizujte stránku, podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-135">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eb7dc-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb7dc-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eb7dc-137">Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="eb7dc-137">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="eb7dc-138">Změňte adresář (`cd`) do složky, která bude obsahovat projektu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-138">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="eb7dc-139">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="eb7dc-139">Run the following command:</span></span>

   ```console
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

  * <span data-ttu-id="eb7dc-140">Zobrazí se dialogové okno s **'RazorPagesMovie' chybí požadované prostředky pro sestavení a ladění. Přidat?**</span><span class="sxs-lookup"><span data-stu-id="eb7dc-140">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>  <span data-ttu-id="eb7dc-141">Vyberte **Ano**</span><span class="sxs-lookup"><span data-stu-id="eb7dc-141">Select **Yes**</span></span>

  * <span data-ttu-id="eb7dc-142">`dotnet new webapp -o RazorPagesMovie`: vytvoří nový projekt v Razor Pages *RazorPagesMovie* složky.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-142">`dotnet new webapp -o RazorPagesMovie`: creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="eb7dc-143">`code -r RazorPagesMovie`: Načte *RazorPagesMovie.csproj* soubor projektu ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-143">`code -r RazorPagesMovie`: Loads the *RazorPagesMovie.csproj* project file in Visual Studio Code.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="eb7dc-144">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="eb7dc-144">Launch the app</span></span>

* <span data-ttu-id="eb7dc-145">Stisknutím klávesy **Ctrl-F5** spustit bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-145">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="eb7dc-146">Visual Studio Code spuštění spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-146">Visual Studio Code starts starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="eb7dc-147">Zobrazí se panel Adresa `localhost:port:5001` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-147">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="eb7dc-148">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-148">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="eb7dc-149">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-149">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="eb7dc-150">Spuštění aplikace s **Ctrl + F5** (bez ladění režim) umožňuje provádět změny kódu, uložte soubor, aktualizujte prohlížeč a zobrazení změn kódu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-150">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="eb7dc-151">Mnoho vývojářů dávají přednost používání režimu bez ladění a aktualizujte stránku, podívejte se na změny.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-151">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eb7dc-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="eb7dc-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb7dc-153">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="eb7dc-153">From a terminal, run the following commands:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

<span data-ttu-id="eb7dc-154">Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) k vytvoření a spuštění projektu pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-154">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create and run a Razor Pages project.</span></span> <span data-ttu-id="eb7dc-155">Otevřete prohlížeč na http://localhost:5000 zobrazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-155">Open a browser to http://localhost:5000 to view the application.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="eb7dc-156">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="eb7dc-156">Open the project</span></span>

<span data-ttu-id="eb7dc-157">Stisknutím kláves Ctrl + C vypnout aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-157">Press Ctrl+C to shut down the application.</span></span>

<span data-ttu-id="eb7dc-158">Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *RazorPagesMovie.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-158">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="eb7dc-159">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="eb7dc-159">Launch the app</span></span>

<span data-ttu-id="eb7dc-160">Vyberte **spuštění > Spustit bez ladění** aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-160">Select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="eb7dc-161">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí se prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-161">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

* <span data-ttu-id="eb7dc-162">Vyberte **přijmout** souhlas sledování.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-162">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="eb7dc-163">Tato aplikace nesleduje osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-163">This app doesn't track personal information.</span></span> <span data-ttu-id="eb7dc-164">Tento kód vygenerovanou šablonu obsahuje prostředky, které vám pomohou splnit [obecného Regulation (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="eb7dc-164">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="eb7dc-166">Následující obrázek znázorňuje aplikaci po přijetí sledování:</span><span class="sxs-lookup"><span data-stu-id="eb7dc-166">The following image shows the app after accepting tracking:</span></span>

  ![Index nebo Domovská stránka](razor-pages-start/_static/home2.2.png)

## <a name="project-files-and-folders"></a><span data-ttu-id="eb7dc-168">Projektových souborů a složek</span><span class="sxs-lookup"><span data-stu-id="eb7dc-168">Project files and folders</span></span>

<span data-ttu-id="eb7dc-169">Následující tabulka uvádí soubory a složky v projektu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-169">The following table lists the files and folders in the project.</span></span> <span data-ttu-id="eb7dc-170">V tuto chvíli v tomto kurzu *Startup.cs* soubor je nejdůležitější pochopit.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-170">At this point in the tutorial, the *Startup.cs* file is the most important to understand.</span></span> <span data-ttu-id="eb7dc-171">Není nutné zkontrolovat každý odkaz níže uvedené.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-171">You don't need to review each link provided below.</span></span> <span data-ttu-id="eb7dc-172">Odkazy jsou uvedeny jako odkaz, pokud potřebujete další informace o souboru nebo složky v projektu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-172">The links are provided as a reference when you need more information on a file or folder in the project.</span></span>

| <span data-ttu-id="eb7dc-173">Soubor nebo složku</span><span class="sxs-lookup"><span data-stu-id="eb7dc-173">File or folder</span></span>              | <span data-ttu-id="eb7dc-174">Účel</span><span class="sxs-lookup"><span data-stu-id="eb7dc-174">Purpose</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eb7dc-175">*wwwroot*</span><span class="sxs-lookup"><span data-stu-id="eb7dc-175">*wwwroot*</span></span> | <span data-ttu-id="eb7dc-176">Obsahuje statické soubory.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-176">Contains static files.</span></span> <span data-ttu-id="eb7dc-177">Zobrazit [statické soubory](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="eb7dc-177">See [Static files](xref:fundamentals/static-files).</span></span> |
| <span data-ttu-id="eb7dc-178">*Stránky*</span><span class="sxs-lookup"><span data-stu-id="eb7dc-178">*Pages*</span></span> | <span data-ttu-id="eb7dc-179">Složka pro [stránky Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="eb7dc-179">Folder for [Razor Pages](xref:razor-pages/index).</span></span> |
| <span data-ttu-id="eb7dc-180">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="eb7dc-180">*appsettings.json*</span></span> | [<span data-ttu-id="eb7dc-181">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="eb7dc-181">Configuration</span></span>](xref:fundamentals/configuration/index) |
| <span data-ttu-id="eb7dc-182">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="eb7dc-182">*Program.cs*</span></span> | <span data-ttu-id="eb7dc-183">[Hostitelé](xref:fundamentals/host/index) aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-183">[Hosts](xref:fundamentals/host/index) the ASP.NET Core app.</span></span>|
| <span data-ttu-id="eb7dc-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="eb7dc-184">*Startup.cs*</span></span> | <span data-ttu-id="eb7dc-185">Nakonfiguruje služby a kanál žádosti.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-185">Configures services and the request pipeline.</span></span> <span data-ttu-id="eb7dc-186">Zobrazit [spuštění](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="eb7dc-186">See [Startup](xref:fundamentals/startup).</span></span>|

### <a name="the-pages-folder"></a><span data-ttu-id="eb7dc-187">Složka stránek</span><span class="sxs-lookup"><span data-stu-id="eb7dc-187">The Pages folder</span></span>

<span data-ttu-id="eb7dc-188">*_Layout.cshtml* soubor obsahuje společné elementy HTML (skripty a šablony stylů) a nastaví rozložení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-188">The *_Layout.cshtml* file contains common HTML elements (scripts and stylesheets) and sets the layout for the application.</span></span> <span data-ttu-id="eb7dc-189">Například když kliknete na **RazorPagesMovie**, **Domů**, nebo **ochrany osobních údajů**, se zobrazí stejné prvky.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-189">For example, when you click on **RazorPagesMovie**, **Home**, or **Privacy**, you see the same elements.</span></span> <span data-ttu-id="eb7dc-190">Společné prvky patří navigační nabídce na horní a záhlaví v dolní části okna.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-190">The common elements include the navigation menu on the top and the header on the bottom of the window.</span></span> <span data-ttu-id="eb7dc-191">Zobrazit [rozložení](xref:mvc/views/layout) Další informace.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-191">See [Layout](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="eb7dc-192">*_ViewImports.cshtml* soubor obsahuje direktivy Razor, které jsou importovány do každé stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-192">The *_ViewImports.cshtml* file contains Razor directives that are imported into each Razor Page.</span></span> <span data-ttu-id="eb7dc-193">Zobrazit [direktivy import sdílených](xref:mvc/views/layout#importing-shared-directives) Další informace.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-193">See [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives) for more information.</span></span>

<span data-ttu-id="eb7dc-194">*Soubor _ViewStart.cshtml* nastaví stránky Razor `Layout` použít vlastnost *_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-194">The *_ViewStart.cshtml* sets the Razor Pages `Layout` property to use the *_Layout.cshtml* file.</span></span> <span data-ttu-id="eb7dc-195">Zobrazit [rozložení](xref:mvc/views/layout) Další informace.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-195">See [Layout](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="eb7dc-196">*_ValidationScriptsPartial.cshtml* soubor obsahuje odkaz na [jQuery](https://jquery.com/) skripty pro ověření.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-196">The *_ValidationScriptsPartial.cshtml* file provides a reference to [jQuery](https://jquery.com/) validation scripts.</span></span> <span data-ttu-id="eb7dc-197">Když `Create` a `Edit` stránky jsou přidány později v tomto kurzu *_ValidationScriptsPartial.cshtml* soubor se použije.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-197">When the `Create` and `Edit` pages are added later in the tutorial, the *_ValidationScriptsPartial.cshtml* file will be used.</span></span>

<span data-ttu-id="eb7dc-198">`Index`, `Error`, a `Privacy` stránky jsou k dispozici:</span><span class="sxs-lookup"><span data-stu-id="eb7dc-198">`Index`, `Error`, and `Privacy` pages are provided to:</span></span>

* <span data-ttu-id="eb7dc-199">`Index`: Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-199">`Index`: Start an app.</span></span>
* <span data-ttu-id="eb7dc-200">`Error`: Zobrazí informace o chybě.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-200">`Error`: Display error information.</span></span>
* <span data-ttu-id="eb7dc-201">`Privacy`: Zadejte podrobnosti o zásadách ochrany osobních údajů webu.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-201">`Privacy`: Specify details about the site's privacy policy.</span></span>

<span data-ttu-id="eb7dc-202">Pro účely tohoto kurzu nejsou použity předchozí stránky.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-202">For this tutorial, the preceding pages are not used.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eb7dc-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb7dc-203">Visual Studio</span></span>](#tab/visual-studio)

<a name="f7"></a>
### <a name="use-f7-to-toggle-between-a-razor-page-and-the-pagemodel"></a><span data-ttu-id="eb7dc-204">Použít F7 přepínat mezi stránky Razor a PageModel</span><span class="sxs-lookup"><span data-stu-id="eb7dc-204">Use F7 to toggle between a Razor Page and the PageModel</span></span>

<span data-ttu-id="eb7dc-205">F7 přepíná mezi stránky Razor (*\*.cshtml* soubor) a C# souboru (*\*. cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="eb7dc-205">F7 toggles between a Razor Page (*\*.cshtml* file) and the C# file (*\*.cshtml.cs*).</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eb7dc-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb7dc-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- TODO review  Need something in these tabs -->

<span data-ttu-id="eb7dc-207">Podle konvence, stránka Razor (*\*.cshtml* soubor) a přidružené `PageModel` mají stejný název kořenového souboru.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-207">By convention, the Razor Page (*\*.cshtml* file) and the associated `PageModel` have the same root file name.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eb7dc-208">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="eb7dc-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb7dc-209">Podle konvence, stránka Razor (*\*.cshtml* soubor) a přidružené `PageModel` mají stejný název kořenového souboru.</span><span class="sxs-lookup"><span data-stu-id="eb7dc-209">By convention, the Razor Page (*\*.cshtml* file) and the associated `PageModel` have the same root file name.</span></span>

---

> [!div class="step-by-step"]
> [<span data-ttu-id="eb7dc-210">Další krok: Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="eb7dc-210">Next: Adding a model</span></span>](xref:tutorials/razor-pages/model)